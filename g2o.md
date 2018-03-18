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


```c++
#include "g2o/core/base_vertex.h"
#include "g2o/core/base_binary_vertex.h"

class myVertex: public g2::BaseVertex<Dim, Type>
{
    public:
    EIGEN_MAKE_ALIGNED_OPERATOR_NEW
    
    myVertex(){}
    
    virtual void read(std::istream& is) {}
    virtual void write(std::ostream& os) const {}
    
    virtual void setOriginImpl()
    {
        _estimate = Type();
    }
    virtual void oplusImpl(const double* update) override
    {
        _estimate += /*update*/;
    }
}

class myEdge: public g2o::BaseBinaryEdge<errorDim, errorType, Vertex1Type, Vertex2Type>
{
    public:
    EIGEN_MAKE_ALIGNED_OPERATOR_NEW
    
    myEdge(){}
    
    virtual bool read(istream& in) {}
    virtual bool write(ostream& out) cout {}
    
    virtual void computeError() override
    {
        // ...
        _error = _measurement - Something;
    }
    
    virtual void linearizeOplus() override
    {
        _jacobianOplusXi(pos, pos) = something;
        // ...
        
        /*
        _jocobianOplusXj(pos, pos) = something;
        ...
        */
    }
    
    private:
    // data
}
```



最小二乘中的一个问题就是求解线性系统$\mathbf{H} \Delta \mathbf{x} = -\mathbf{b}$，为此，g2o 提供了`Solver` 类。但利用`Solver` 类进行优化需要设置很多参数。所以g2o 提供了模版类`BlockSolver<>` （内置了`SparseBlockMatrix<> ` 类）来简化流程。`BlockSolver<>` 可以进行Schur 消元并利用另一个模版类`LinearSolver<>` 来求解。g2o 提供了多种线形求解器，包括CSparse, CHOLMOD 等等。

一般初始化流程如下：

```c++
#include <g2o/core/base_vertex.h>
#include <g2o/core/base_binary_edge.h> // base_unary_edge.h
#include <g2o/core/block_solver.h>
#include <g2o/core/optimization_algorithm_levenberg.h>
#include <g2o/core/optimization_algorithm_gauss_newton.h>
#include <g2o/core/optimization_algorithm_dogleg.h>
#include <g2o/solvers/dense/linear_solver_dense.h>

int main(int argc char** argv)
{
    typedef g2o::BlockSolver<g2o::BlockSolverTraits<poseDim, landmarkDim> > Block;
    
    // 线形方程求解器
    // linear solver using dense cholesky decompositio
    Block::LinearSolverType* linearSolver = new g2o::LinearSolverDense<Block::PoseMatrixType>(); //稠密增量方程
    
    // linear solver which uses the sparse Cholesky solver from Eigen
    // Block::LinearSolverType* linearSolver = new g2o::LinearSolverEigen<Block::PoseMatrixType>();
    
    // linear solver which uses CSparse
    // Block::LinearSolverType* linearSolver = new g2o::LinearSolverCSparse<Block::PoseMatrixType>();
    
    // basic solver for Ax = b which has to reimplemented for different linear algebra libraries
    // Block::LinearSolverType* linearSolver = new g2o::LinearSolverCholmod<Block::PoseMatrixType>();
    
    Block* solver_ptr = new Block(linearSolver); // 矩阵块求解器，设置线形求解器
    
    // 梯度下降法
    g2o::optimizationAlgorithmLevenberg* solver = new g2o::OptimizationAlgorithmLenvnberg(solver_ptr);
    
    // g2o::optimizationAlgorithmGaussNewton* solver = new g2o::OptimizationAlgorithmGaussNewton(solver_ptr);
    
    //g2o::optimizationAlgorithmGaussDogleg* solver = new g2o::OptimizationAlgorithmDogleg(solver_ptr);
    
    g2o::SparseOptimizer optimizer; // 图模型
    
    optimizer.setAlgorithm(solver); // 设置求解器
    // optimizer.setVerbose(true); // 打开调试输出
    
    // vertex
    g2o::VertexSE3Expmap* pose = new g2o::VeretxSE3Expmap(); // 相机位姿
    pose->setId(0);
    pose->setEstimate(/*something*/);
    optimizer.addVertex(pose);
    
    int index = 1;
    for (const Point p : points) // 伪码
    {
        g2o::PointType* point = new g2o::pointType(); // 伪码
        point->setId(index++);
        point->setEstimate(/*something*/);
        // point->setMarginalized(true); // 该点在解方程时进行Schur消元
        
        optimizer->addVertex(point);
    }
    
    // parameter: camera intrinsics 可有可无
    g2o::CameraParameters* camera = new g2o::CameraParameters(/*K*/);
    camera->setId(0);
    optimizer.addParameter(camera);
    
    // edges
    index = 1;
    for (const Point2d p : point_2d) // 伪码
    {
        g2o::EdgeType* edge = new g2o::EdgeType(); // 伪码
        edge->setId(index);
        // huber loss, 默认为不用设置
        /*
        if (robustify)
        {
            g2o::RobustKernelHuber* rk = new g2o::RobustKernelHuber;
            rk->setDelta(1.0);
            edge->setRobustKernel(rk);
        }
        */
        edge->setVertex(0, dynamic_cast<g2o::PointType*>(optimizer.vertex(index))); // 二元边，第一个顶点为节点
        edge->setVertex(1, pose); // 第二个顶点为相机
        
        edge->setMeasurement(/*something*/);
        // edge->setParameterId(0, 0);
        edge->setInformation(/*Identity*/); // 协方差矩阵之逆
        
        optimizer.addEdge(edge);
        index++;
    }
    
    
    optimizer.setVerbose(true);
    optimizer.initializeOptimization();
    optimizer.optimize(/*iteration times*/);
    return 0;
}
```

