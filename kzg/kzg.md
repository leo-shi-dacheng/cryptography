KZG 多项式承诺方案详解
# 1. 基础概念
## 1.1 什么是 KZG 承诺？
KZG (Kate-Zaverucha-Goldberg) 承诺是一种多项式承诺方案，允许：
- 对一个多项式进行简短的承诺
- 证明多项式在某点的取值
- 验证这个取值的正确性
1.2 核心组件
- 多项式 (Polynomial)
    - 形如 ```f(x) = ax² + bx + c``` 的数学表达式
    - 在代码中用系数数组表示：```[c, b, a]```
- 承诺 (Commitment)
    - 多项式的"指纹"
    - 是椭圆曲线上的一个点
- 证明 (Proof)
    - 包含多项式在某点的值
    - 包含一个证明值，用于验证该值的正确性
# 2. 数学原理
## 2.1 基本步骤
1. Setup (可信设置)
- 选择随机值 τ
- 计算 ```[G, τG, τ²G, ..., τⁿG] 和 [H, τH]```
2. Commit (生成承诺)
- 对于多项式 ```f(x) = Σ cᵢxⁱ```
- 承诺 ```C = Σ(cᵢ * τⁱG)```
3. CreateProof (生成证明)
- 计算 f(z)
- 计算商多项式 ```q(x) = (f(x) - f(z))/(x - z)```
- 生成商多项式的承诺作为证明
## 2.2 示例计算
- 以 ```f(x) = 1 + 2x + 3x²``` 为例：
```
在 x = 2 时：
f(2) = 1 + 2(2) + 3(2²) = 17

商多项式 q(x) = (f(x) - 17)/(x - 2)
               = (3x² + 2x + 1 - 17)/(x - 2)
               = 3x + 8
```
# 3. 代码实现
## 3.1 主要数据结构
```go
type KZG struct {
    G1Powers  []bn254.G1Affine // [G, τG, τ²G, ..., τⁿG]
    G2Powers  []bn254.G2Affine // [H, τH]
    MaxDegree int
    Modulus   *big.Int
}

type Polynomial struct {
    Coefficients []fr.Element
}

type Commitment struct {
    Value bn254.G1Affine
}

type Proof struct {
    Value   fr.Element
    ProofG1 bn254.G1Affine
}
```
## 3.2 核心函数
1. ```Setup```
- 生成可信设置参数
- 计算必要的椭圆曲线点序列
2. ```Commit```
- 接收多项式系数
- 生成承诺值
3. ```CreateProof```
- 计算多项式在指定点的值
- 生成证明值
4. ```Verify```
- 验证证明的正确性
- 使用配对运算进行验证
# 4. 应用场景
## 4.1 零知识证明系统
- 用于 ```Plonk```、```Sonic``` 等协议
- 提供简短的证明大小
## 4.2 区块链扩容
- 用于 rollup 方案
- 数据可用性采样
# 5. 安全性考虑
## 5.1 可信设置
- τ 值必须安全生成和销毁
- 通常通过多方计算仪式生成
## 5.2 密码学假设
- 基于椭圆曲线离散对数难题
- 基于双线性配对的安全性

# 6. 常见问题
## 6.1 性能考虑
- Setup 阶段计算量大
- 验证速度相对较快
- 承诺和证明大小都是常数级
## 6.2 局限性
- 需要可信设置
- 对量子计算不安全
- 设置大小与多项式度成正比
7. 总结
- KZG 承诺方案是现代零知识证明系统的重要组成部分，它提供了：
- 简短的承诺大小
- 高效的验证过程
- 可组合性
- 适用于各种应用场景