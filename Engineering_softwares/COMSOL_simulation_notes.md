## 结构振动的仿真分析
### 1. 结构动力学
- 与弹性力、粘滞力，以及外部负载相比，惯性力十分显著
    >$m\ddot{u} + c\dot{u} + ku = f(t)$   
    >惯性力 粘滞力  弹性力  载荷

### 2. 分类
![本地路径](-comsol_pics\categories_of_structural_mechanics.png "结构动力学的分类")

### 3. 研究类型
- 涉及到的频域计算均为线性扰动，即简谐振动

### 4. 频域概述
$$M\ddot{u} + C\dot{u} + Ku = f(t)$$
$$f(t) = \tilde{f}e^{i\omega t} \qquad u(t) = \tilde{u}e^{i\omega t}$$
$$ (-\omega^2M + i\omega C + K)\tilde{u} = \tilde{f} $$
$$\tilde{u} = u_r + iu_i $$
- 在一定频率下的稳定状态解
    - 频率响应
    - 特征频率分析
- 激励和响应为简谐式
    - 线性
    - 扰动
- 所有均按复数计算，给出幅值和相位信息
- 材料属性和载荷可以按复数计算
    - 任何地方都可以输入复数
    - 一些节点含有相位子节点

### 5. 特征频率分析
- 无外加载荷的自由简谐振动
    - 对各个$\omega$(特征频率)，会有一个对应的振型/模态
        >$(-\omega^2M + i\omega C + K)\tilde{u} = 0$
- 可计算过阻尼特征频率
    - 无专用求解器；阻尼效应会被自动计入模型
    - 包含阻尼的特征频率一般为复数特征频率
    - 特征模态可能是复数形式
- 载荷效应被忽略，除非他们能提供一定刚度或自拟（如取决于位移或速度）
    - 如跟随载荷（压力）
- 非零预位移被设为零
- 特征模态振幅是任意的，不是准确的振幅
    - 可选用三种不同的缩放方式（均方根、最大值、质量矩阵）
    - 若要知道准确的振幅，可在计算出的频率附近做扫描计算（扫频）
### 6. 特征频率结果
- 计算组中的表格
    - 特征频率
        - $\omega_r$ -> 频率
        - $\omega_i$ -> 阻尼
    - 相对阻尼
        - $\zeta \approx \omega_i / \omega_r$
    - 品质因子
        - $Q = 1/(2\zeta)$
- 当打开模型时，需要更新表格
    - 更新步骤：设置 -> 计算组 -> 计算
- 参与因子
    - 质量可通过各模态反应
### 7. 频率响应
- 对一组简谐载荷的稳态响应
- 通常按“频率扫描”计算
- 载荷和材料属性可设为频率相关
    - 使用变量名freq
    - 模态叠加（分析）时假定材料属性频率独立
- 当绘制变量$v$时，实际上展示的是该复数的实部
    - 实部给出当前相位角下的值
    - 相位角通过数据集设定
    - 算子real(), imag(), abs(), 及arg()常用来提取特定部分或值
- 通常我们对复制abs(v)更感兴趣
- 使用从频域到时域FFT研究步骤从频率结果创建时域解
- 频响分别两个频率下的结果（幅值和相位）
- 为了找到组合响应峰值需要转换到时域

### 8. 广义周期性激励
- 计算载荷的FFT
    - 需要一个辅助全局DOF 'q'以及时域解
- 对所有主要频率计算扫频
    - 载荷表达式依赖freq
- 通过逆FFT再度转换到时域解
### 9. 时域分析
- 非稳态问题
- 非线性问题
    - 例外：模态叠加（瞬态模态）
    - 如碰撞、非线性载荷
- 对动力学问题***广义$\alpha$求解器是***首选
    - 设置方法：瞬态求解器里 -> 时间步进 -> 广义$\alpha$
    - BDF具有更高数值阻尼
- 隐式求解器
    - 可采用较大时间步长
    - 并非特别适用于冲击与波传播；每计算步长计算量较大
- 自动时间步长控制
    - 容差常需要收紧
- 结果须对许多时间步做存储
- 降低文件大小
    - 不要存储时间导数
    - 不要存储反作用力
    - 仅存储所选结果
    - 使用探针
### 10. 预应力分析
- 拉伸应力通常会提高特征频率（应力强化）
    - 此时要先做稳态频率分析，然后再做频域扰动
- 预应力分析要求有前一阶段的应力状态
    - 通常为稳态解
    - 也可只是初始应力
- 预应力研究包含2或3个研究步骤
    1. 稳态分析（稳态，特征频率）
    2. 特征频率或频域分析（稳态，频域扰动）
    3. 可能的模态叠加（稳态，特征频率，瞬态稳态）
- 在预应力分析中，有必要将稳态载荷与附加简谐载荷分开
    - 选择谐波扰动加载频域扰动步骤载荷
    - 指示标志（~）会出现在节点图标上
    - 对约束，谐波扰动为子节点
    - 选择谐波扰动相当于使用linper()算子
### 11. 谐波扰动
- 谐波扰动用于：
    - 频域，预应力
    - 频域，模态
    - 频域，模态，预应力
- 不可用于
    - 频域
- 谐波扰动在将线性设为线性扰动下会被激活
    - 可在默认研究步骤基础上取舍
- 对扰动解，所有结果设置都将有下拉菜单 计算表达式
    - 可能会对预载荷解预计扰动解的组合感兴趣
### 12. 模态叠加
- 响应被计算为一系列特征模态的叠加
- 自由度数量与所用特征模态数量相同，通常为10-100
- 由于频率信息已知，频域计算过特别
- 阻尼可对各模态分别预先设定

## 气动声学
### 1. 气动声学
- 对流声学（载流噪声）
- 扰动方程
- 输入
    - 材料属性
    - 稳态背景流
- FEM或dG-FEM稳定型方程
- 假设
    - 线性声学
    - 非流致噪声
    - 包含不同程度细节的对流声学
### 2. Navier-Stokes方程
![本地路径](comsol_pics\navier-stokes_equation.png)
