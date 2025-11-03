https://en.wikipedia.org/wiki/Extended_Kalman_filter
预测流程`predict` 
1. 准备控制量prepareControl(const double referenceTime, const double delta)
	根据目标速度，设置对应的加速度和限副
	--->controlUpdateVector_
	- **加速度/减速度参数**:
    - `accelerationLimits_` / `decelerationLimits_`: 物理限制。机器人的电机不可能产生无限大的加速度。这些参数设置了加速度和减速度的**最大值（绝对值）**，用于限幅。
    - `accelerationGains_` / `decelerationGains_`: 增益系数。它们决定了“速度误差”有多大比例被转化为加速度指令。可以看作是 **P-控制器** 的比例系数。
    - 这个计算出的 `controlAcceleration_` 随后会被送入 `predict` 函数，用于驱动状态转移方程，从而预测机器人在下一个时刻的状态。这使得滤波器的预测更加准确，因为它融合了来自控制源（如里程计）的意图信息。
2. 构建状态转移矩阵**transferFunction_** 
	码基于物理运动学模型，推导出从 `[k]` 时刻到 `[k+1]` 时刻的状态转移关系。
3. 构建状态转移雅可比矩阵**transferFunctionJacobian_** 
	`transferFunctionJacobian_ = transferFunction_`
4. 应用控制项（加速度）
	将控制输入积分到状态中，更新角速度和线性加速度。
	`state_(...) += controlAcceleration(...) * delta`
5. 执行状态预测
	`state_ = transferFunction_ * state_`
6. 执行协方差预测！！！！！！！！！！！！！！！！
	--->先验估计误差协方差矩阵（表示当前状态预测值的不确定性）
	`estimateErrorCovariance_ = (J * P * J^T) + (Q * Δt)` 

校正流程`correct` 
	输入：`measurement`(观测值、协方差、更新向量等)
	输出：修正后的`state_`和`estimateErrorCovatiance_`
1. 确定更新维度
	取出每个观测量中当前有效维度的索引，排除nan和inf
		--->`updateIndices`
2. 构建子矩阵
	`stateSubset` 待更新的状态子集(x)
	`measurementSubset` 观测值子集(z)
	`measurementCovarianceSubset` 观测噪声协方差子集(R)（表示传感器测量本身的不确定性）
	`stateToMeasurementSubset` 观测矩阵子集(H)，这里它是一个**选择矩阵**，因为观测通常直接对应状态（如 GPS 的 X 直接对应状态的 X），所以 `H` 矩阵在对应位置上是 1，其余为 0。
3. 处理观测异常值
	检查nan/inf并排除
	检查负协方差       ---> 取绝对值
	检查极小协方差  --->  取1e-9
4. 计算卡尔曼增益(K)
		权值矩阵，决定我们更相信预测还是观测
	 $PH^T$将状态预测不确定$P$映射到观测空间
		$P$代表预测方差，即`estimateErrorCovariance_` 
		维度是 `(状态维度 x 观测维度)`。它本质上表示了状态误差和观测误差之间的**协方差**。
	$S^{-1} = (HPH^T + R)^{-1}$ **总的不确定性** $S = 预测误差(HPH^T) + 观测噪声误差(R)$   
		`hphrInv = (stateToMeasurementSubset * pht + measurementCovarianceSubset).inverse()` 
		$HPH^T$表示预测值在观测空间的不确定性
		$R$代表传感器的噪声，即measurementCovarianceSubset观测噪声协方差
	$K = (PH^T) S^{-1}$ 
		的计算公式可以理解为：$K = \frac{\text{状态和观测的协方差}(PH^T)}{\text{总的不确定性}(S)}$ 
		若预测值不确定($P$值很大)，传感器非常精确($R$值很小)，则$S \approx R$ 小，--->$K$ 值大，更信任观测值。
		若预测值精确($P$值小)，传感器噪声大($R$值很大)，则$S$大，--->$K$值小，更信任预测值。
	$y = z - H\hat{x}$ 计算新息
		**观测值与预测值在观测空间中的差值**，它告诉我们当前的预测和实际的测量相差多少，在后续会与卡尔曼增益$K$相乘用于校正
		`innovationSubset = (measurementSubset - stateSubset);` 
	![[卡尔曼增益的计算流程.png]] 
 5. 马氏距离检测
	 若新息误差过大，则跳过校正，防止滤波器被污染
6. 更新状态估计
	$x=x+K*innovation$ 
	`state_.noalias() += kalmanGainSubset * innovationSubset;`
	$innovation$ 新息，即观测到的状态增量
7. 根新估计误差协方差！！！！！！！！！！！！
	`estimateErrorCovariance_` 
	约瑟夫形式，保证协方差对称正定
		$P_{new} = (I - KH) P (I - KH)^T + KRK^T$
	1. 增益残差$(I-KH)$ 
		矩阵 $(I - KH)$ 反映了校正行为对不确定性的影响。卡尔曼增益 $K$ 决定了我们从观测中汲取多少信息，$(I - KH)$ 则相应地表示剩余的不确定性。
	2. $(I - KH) P (I - KH)^T$ 
		用增益残差来变换先验协方差 $P$。它代表了经过观测校正后，从先验不确定性 $P$ 中减少的那部分不确定性。
	3. $+KRK^T$ 
		部分引入了由于传感器噪声 $R$ 所带来的新的不确定性。虽然观测校正减少了不确定性，但传感器本身并不完美，它的噪声会通过卡尔曼增益 $K$ 被引入到状态估计中
总结：
	卡尔曼滤波的目标是估计运动状态$\mathbf{x}$ 
	通过维护预测、观测、卡尔曼增益来对估计状态进行更新
	其中
		预测对状态 $\mathbf{x}$ 的操作：
			$\mathbf{\hat{x}}_{k|k-1} = f(\mathbf{\hat{x}}_{k-1|k-1}, \mathbf{u}_k)$ 
			`state_ = transferFunction_ * state_`
		在观测中计算卡尔曼增益$K$，并根据观测增量$innovation$对状态 $\mathbf{x}$ 进行校准
			$x=x+K*innovation$ 
			$状态(x) = 状态(x) + 卡尔曼增益(K) * 观测增量(innovation)$ 

代码流程及参数与数据传递[[数据传递]] 