图例：

- 蓝线 表示公有继承；
- 绿线 表示保护继承；
- 红线 表示私有继承；
- 黄色虚线 表示模版类实例化；
- 紫色虚线 表示类内部定义了该类变量。

使用一个generic hyper-graph 结构来表达一个优化问题。

类`OptimizableGraph` 定义在`optimizable_graph.h` 中。两个内部类`OptimizableGraph::Vertex` 和`OptimizableGraph::Edge` 来表示节点和超边。

系统也提供了一些模版类来协助扩展。

- `BaseVertex`：模版参数为**优化变量维度**和**数据类型**。需要定义读写函数；函数`void oplus(double* v)` 则是节点的更新函数，值存储在变量`_estimate` (类型为给定的数据类型) 中；函数`void setToOriginImpl()` 将节点的值进行重置。定义节点时，利用`setEstimate(type)` 函数来设定初始值；`setId(int)` 定义节点编号。
- `BaseUnaryEdge`：模版参数为观测值的**维度**，**类型**和**节点类型**。需要重定义读写函数；可以有初始化函数；函数`void linearizeOplus()` 计算雅可比矩阵；`void computeError()` 函数用以计算误差，观测值存储在`_measurement` 中，误差存储在`_error` 中，节点存储在`_vertices[]` 中。定义边式，利用`setId(int)` 来定义边的编号（决定了在H矩阵中的位置）；`setMeasurement(type)` 函数来定义观测值；`setVertex(int, vertex)` 来定义节点；`setInformation()` 来定义协方差矩阵的逆。
- `BaseBinaryEdge`：二元变。比一元边需要多设定一个节点类型。`_vertices[]` 的大小为2，存储顺序和调用`setVertex(int, vertex)` 是设定的`int` 有关（0 或1）。
- `BaseMultiEdge`.

