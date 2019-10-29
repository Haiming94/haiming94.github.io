





### 基于torch的两种CNN(卷积神经网络)的实现方法

最近在完成一个`Multi-task Learning`任务时，很想尝试一下`pytorch`的实现方法，因此研究了一下`pytorch`的相关代码。不过也有很多以外的收获，`pytorch`的机制和`tensorflow`确实有很大不同。

------

`pytorch`实现有两个实现`CNN`的函数类：

- torch.nn.xx
- torch.nn.functional.xx

简单理解：`torch.nn.xx`不需要传入参数，而是直接使用就好，完全是封装好了，传入`input`即可；而`torch.nn.functional.xx`则不同，需要传入用户定义好的模型参数`weight`。

------

下列代码说明均来自[pytorch中文文档](<https://pytorch-cn.readthedocs.io/zh/latest/>)

1.`torch.nn.xx`的卷积操作(有多种卷积：一维、二维、三维等)：

<!--一维卷积操作-->

`torch.nn.Conv1d(in_channels, out_channels, kernel_size, stride=1, padding=0, dilation=1, groups=1, bias=True)`

**Parameters:**

- in_channels(`int`) – 输入信号的通道
- out_channels(`int`) – 卷积产生的通道
- kerner_size(`int` or `tuple`) - 卷积核的尺寸
- stride(`int` or `tuple`, `optional`) - 卷积步长
- padding (`int` or `tuple`, `optional`)- 输入的每一条边补充0的层数
- dilation(`int` or `tuple`, `optional``) – 卷积核元素之间的间距
- groups(`int`, `optional`) – 从输入通道到输出通道的阻塞连接数
- bias(`bool`, `optional`) - 如果`bias=True`，添加偏置

**shape:**

输入: (N,C_in,L_in) 
输出: (N,C_out,L_out) 
输入输出的计算方式： 
$$
L_{out}=floor((L_{in}+2*padding-dilation*(kernerl\_size-1)-1)/stride+1)
$$
**变量**

weight(`tensor`) - 卷积的权重，大小是(`out_channels`, `in_channels`, `kernel_size`) 
bias(`tensor`) - 卷积的偏置系数，大小是（`out_channel`）

**example**

```python3
m = nn.Conv1d(16, 33, 3, stride=2)
input = autograd.Variable(torch.randn(20, 16, 50))
output = m(input)
```

<!--二维卷积操作-->

`torch.nn.Conv2d(in_channels, out_channels, kernel_size, stride=1, padding=0, dilation=1, groups=1, bias=True)`

**Parameters:**

- in_channels(`int`) – 输入信号的通道
- out_channels(`int`) – 卷积产生的通道
- kerner_size(`int` or `tuple`) - 卷积核的尺寸
- stride(`int` or `tuple`, `optional`) - 卷积步长
- padding(`int` or `tuple`, `optional`) - 输入的每一条边补充0的层数
- dilation(`int` or `tuple`, `optional`) – 卷积核元素之间的间距
- groups(`int`, `optional`) – 从输入通道到输出通道的阻塞连接数
- bias(`bool`, `optional`) - 如果`bias=True`，添加偏置

**Shape:**

input: (N,C_in,H_in,W_in) 
output: (N,C_out,H_out,W_out)
$$
H_{out}=floor((H_{in}+2padding[0]-dilation[0](kernerl\_size[0]-1)-1)/stride[0]+1)
$$

$$
W_{out}=floor((W_{in}+2padding[1]-dilation[1](kernerl\_size[1]-1)-1)/stride[1]+1)
$$

**变量：**

weight(`tensor`) - 卷积的权重，大小是(`out_channels`, `in_channels`,`kernel_size`)
bias(`tensor`) - 卷积的偏置系数，大小是（`out_channel`）

**example:**

```python3
>>> # With square kernels and equal stride
>>> m = nn.Conv2d(16, 33, 3, stride=2)
>>> # non-square kernels and unequal stride and with padding
>>> m = nn.Conv2d(16, 33, (3, 5), stride=(2, 1), padding=(4, 2))
>>> # non-square kernels and unequal stride and with padding and dilation
>>> m = nn.Conv2d(16, 33, (3, 5), stride=(2, 1), padding=(4, 2), dilation=(3, 1))
>>> input = autograd.Variable(torch.randn(20, 16, 50, 100))
>>> output = m(input)
```

------

2.`torch.nn.functional.xx`同样对应着多种类型的卷积操作，但是最大的区别还是体现在在参数上。

<!--一维卷积-->

`torch.nn.functional.conv1d(input, weight, bias=**None**, stride=1, padding=0, dilation=1, groups=1)`

**参数：**

- **input** – 输入张量的形状 (minibatch x in_channels x iW) 
- **weight** – 过滤器的形状 (out_channels, in_channels, kW) 
- **bias** – 可选偏置的形状 (out_channels) 
- **stride** – 卷积核的步长，默认为1

**example:**

```python3
>>> filters = autograd.Variable(torch.randn(33, 16, 3))
>>> inputs = autograd.Variable(torch.randn(20, 16, 50))
>>> F.conv1d(inputs, filters)
```

<!--二维卷积-->

`torch.nn.functional.conv2d(input, weight, bias=**None**, stride=1, padding=0, dilation=1, groups=1)`

**参数：**

- **input** – 输入张量 (minibatch x in_channels x iH x iW) 
- **weight** – 过滤器张量 (out_channels, in_channels/groups, kH, kW) 
- **bias** – 可选偏置张量 (out_channels) 
- **stride**– 卷积核的步长，可以是单个数字或一个元组 (sh x sw)。默认为1 
- **padding** – 输入上隐含零填充。可以是单个数字或元组。 默认值：0 
- **groups** – 将输入分成组，in_channels应该被组数除尽

**example:**

```python3
>>> # With square kernels and equal stride
>>> filters = autograd.Variable(torch.randn(8,4,3,3))
>>> inputs = autograd.Variable(torch.randn(1,4,5,5))
>>> F.conv2d(inputs, filters, padding=1)
```



------

#### 基于上述两种方法分别完成多层卷积类

1.基于`torch.nn.xx`实现：

```python3
class nn_CNN(nn.Module):
    def __init__(self):
        super(CNN, self).__init__()
        
        self.cnn1 = nn.Conv2d(in_channels=1,  out_channels=16, kernel_size=5,padding=0)
        self.relu1 = nn.ReLU()
        self.maxpool1 = nn.MaxPool2d(kernel_size=2)
        
        self.cnn2 = nn.Conv2d(in_channels=16, out_channels=32, kernel_size=5,  padding=0)
        self.relu2 = nn.ReLU()
        self.maxpool2 = nn.MaxPool2d(kernel_size=2)
        
        self.linear1 = nn.Linear(4 * 4 * 32, 10)
        
    def forward(self, x):
        x = x.view(x.size(0), -1)
        out = self.maxpool1(self.relu1(self.cnn1(x)))
        out = self.maxpool2(self.relu2(self.cnn2(out)))
        out = self.linear1(out.view(x.size(0), -1))
        return out
```

2.基于`torch.nn.functional.xx`实现：

```python3
class CNN(nn.Module):
    def __init__(self):
        super(CNN, self).__init__()
        
        self.cnn1_weight = nn.Parameter(torch.rand(16, 1, 5, 5))
        self.bias1_weight = nn.Parameter(torch.rand(16))
        
        self.cnn2_weight = nn.Parameter(torch.rand(32, 16, 5, 5))
        self.bias2_weight = nn.Parameter(torch.rand(32))
        
        self.linear1_weight = nn.Parameter(torch.rand(4 * 4 * 32, 10))
        self.bias3_weight = nn.Parameter(torch.rand(10))
        
    def forward(self, x):
        x = x.view(x.size(0), -1)
        out = F.conv2d(x, self.cnn1_weight, self.bias1_weight)
        out = F.conv2d(x, self.cnn2_weight, self.bias2_weight)
        out = F.linear(x, self.linear1_weight, self.bias3_weight)
        return out
```

### 总结

1.两者的调用方式不同。

使用`torch.nn.xx`时，需要提前传入超参数，然后再将数据以函数调用的方式输入；而`torch.nn.functional.xx`要同时传入数据和`weight、bias`等。

2.`torch.nn.xx`能够放在`torch.nn.Sequential`里，而`torch.nn.functional.xxx`不行。

3.`torch.nn.functional.xxx`需要自己定义`weight`,每次调用时都需要手动传入`weight`,而`torch.nn.xxx`则不用。

4.关于`dropout`,推荐使用`torch.nn.xxx`。因为一般情况下只有训练时才用`dropout`,在`eval`不需要`dropout`。使用`torch.nn.Dropout`,在调用`model.eval()`后，模型的`dropout`层都关闭，但用`torch.nn.functional.dropout`，在调用`model.eval()`后不会关闭`dropout`.

5.有一种情况用`torch.nn.functional.xxx`会更好，即如果行为相同，但参数矩阵相同的两个`layer`共享参数，可直接多次调用nn的Module。但若行为不同，比如想让两个dilation不同但kernel相同的卷积层共享参数，就可用到`torch.nn.functional.xxx`,例如：

```python3
import torch
import torch.nn as nn
import torch.nn.functional as F

class Model(nn.Module):
    def __init__(self):
        super(Model, self).__init__()

        self.weight = nn.Parameter(torch.Tensor(10,10,3,3))
    
    def forward(self, x):
        x_1 = F.conv2d(x, self.weight,dilation=1, padding=1)
        x_2 = F.conv2d(x, self.weight,dilation=2, padding=2)
        return x_1 + x_2
```

在构建模型框架时（`torch.nn.Module`）可用`torch.nn.xxx`，在训练模型时可用`torch.nn.functional.xxx`。另外，如果涉及到参数计算的，那用`torch.nn.xxx`.；若不需要涉及更新参数，只是一次性计算，那用`torch.nn.functional.xxx`。

#### 参考链接

[pytorch：nn与nn.functional的区别](<https://www.jianshu.com/p/7bb495573cb9>)