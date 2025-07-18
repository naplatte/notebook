### 1.print

```python
print("Hello World!")

#输出多个字符串
print("apple" + "banana")

#输出计算结果
print(1 + 2)

#配合强转
print(int('2')+3)
print(float('1.2')+1)
```

### 2.基本数学运算

```python
#幂次方

#2的三次方
print(2**3)

#除法取整
print(9 // 4)

#python3中，整数相除的结果是浮点数
print(7 / 5) #1.4
```

### 3.变量

无数据类型，直接赋值

```python
x = 10
print(x)

y = 12 + 3
print(y)

z = "Hello Python"
print(z)

**#一次性定义多个
a,b,c = 1,2,3
print(a,b,c)**
```

### 4.while循环

```python
x = 1
while x < 10 :
    print(x)
    x+=1
```

### 5.for循环

```python
example_list = [1,2,3,4,5,6,7,8,9]
for i in example_list:
    print(i)
    print("还在循环之内")
```

range(beg,end,x)函数

- beg是开始
- end是结尾
- x是步长

range(1,9)表示从1到8，注意取不到9，**左闭右开**；

```python
example_list = [1,2,3,4,5,6,7,8,9]
for i in range(1,9,3):
    print(i)
```

### 6.if - elif - else

```python
x = 2
y = 2
if x < y:
    print("x < y")
elif x == y:
    print("x = y")
else:
    print("x > y")
```

### 7.函数

- 函数定义&传参

```python
def func(a,b):
    c = a + b
    print(c)

func(2,3)
```

- 默认参数：在传参时就赋予默认值，但在调用时也可以更改
- **默认参数之后不可以跟随非默认参数**，即**非默认参数放在前面，默认参数放在后面**；

```python
def buy_cars(price,brand = "su7",color = "blue",is_second_hand = True):
    print('price:',price,
          'brand:',brand,
          'color:',color,
          'is_second_hand:',is_second_hand)

buy_cars(280000)
buy_cars(300000,"BMW")
```

- 返回值

```python
def func(a):
    return a+2
print(func(2))
```

- 全局变量：函数外定义，一般大写

```python
X = 100
def func(a):
    return a + X

print(X)
print(func(100))
```

### 8.文件读写

- open(),write(),close()

```python
text = "This is my first file\\nThis is next row\\nThis is third row"

my_file = open("my_file.txt",'w')
my_file.write(text)
my_file.close()
```

- 在一个文件后追加内容

```python
append_text = '\\nThis is appended file'

my_file = open('my_file.txt','a')#'a'表示append追加
my_file.write(append_text)
my_file.close()
```

- 读文件

```python
file = open('my_file.txt','r')#'r'表示read。读文件
out = file.read()
print(out)
```

- readline和readlines
  - 前者是一行一行读，后者是读全部行

```python
file = open('my_file.txt','r')#'r'表示read。读文件
out1 = file.readline()
out2 = file.readline()
print(out1,out2)

out = file.readlines();
print(out)
```

### 9.类

- 类的定义

```python
class Calculator:
    name = 'Good Calculator'
    price = 18
    def add(self,x,y):
        return x + y

calc = Calculator()
result = calc.add(1,4)
print(result)
```

- 初始函数

```python
class Calculator:
    def __init__(self,name,price):
        self.name = name
        self.price = price

calc = Calculator('Good Calculator',18)

print(calc.name)
print(calc.price)
```

- 类的继承：创建MyData类继承Dataset类

```python
class MyData(Dataset):
	def __init__(self):
```

### 10.Input

```python
a_input = input('请输入一个数字:')
print(a_input)
```

input()的返回值是string类型

```python
a_input = input('请输入一个数字:')

if a_input == '1':#注意这里是字符串类型，不是整数类型
    print(1)
elif a_input == '2':
    print(2)
else:
    print('other')
```

### 11.元组，列表

- 元组：tuple
- 列表：list

```python
a_tuple = (1,2,3,4,5)
another_tuple = 1,2,3

a_list = [4,5,6,7]
len(a_list) #列表长度
a_list[2] = 11 #赋值

for i in a_list:
    print(i)

for i in range(len(a_tuple)):
    print(a_tuple[i])
```

- append()、insert()、remove()、index()、count()、sort()

```python
a_list = [4,5,6,7]

a_list.append(9) #增添一个元素
a_list.insert(3,'x')

a_list.remove(5) #删除列表中第一个出现的5
print(a_list[3]) #打印下标为3的值
print(a_list[-1]) #打印列表中最后一个元素
print(a_list[0:3]) #打印下标0-3的值
print(a_list[:4]) #打印下标0-4（0可省略）
print(a_list[3:]) #打印下标3到最后

print(a_list.index(4))#第一次出现值为4的下标
print(a_list.count('x')) #列表中x的个数

a_list.sort() #升序排序
a_list.sort(reverse=True) #降序排序
```

- 多维列表

```python
a_list = [ [1,2,3],
           [4,5,6],
           [7,8,9]]

print(a_list[2][1])
```

### 12.字典

- 键值对key - value；
- 无序

```python
d = {'apple':1,'banana':2,'orange':3}
print(d)

del d['banana']
print(d)

d['pear'] = [1,2,3] #在字典中插入元素，字典中可以嵌套任何数据类型
print(d)
```

### 13.import模块

自己创建的py文件也可以被import

```python
import time

import time as t #别名，之后可以直接用t来代替time

from time import time* #引用time中的所有函数，且在调用时不需要加time.
```

- break和continue
  - break：跳出本层全部循环；
  - continue：跳出本轮循环，再进入下一轮循环；

### 14.异常处理

- try：需要执行的代码；
- except：发生异常时执行的代码；
- else：未发生异常时执行的代码；
- finally：不管有没有异常都会执行的代码；

```python
try:
    file = open('test','r+')
except Exception as e:
    print("This is no file named as test")
    response = input("do you want to creat this file?")
    if response == 'y':
        file = open('test','w')
    else:
        pass
else: #即未发生异常时需要做的
    file.write('ssss')
finally:
    print('这句话，无论异常是否发生都会执行。')
```

### 15.zip、lambda和map

- zip()：将多个对象中的元素按顺序配对；
- map()：将一个函数应用到一个或多个可迭代对象的每个元素上，返回一个由函数应用结果组成的可迭代对象；

```python
a = [1,2,3]
b = [4,5,6]
zip(a,b)
print(list(zip(a,b))) #[(1, 4), (2, 5), (3, 6)]

func = lambda x,y:x+y
print(func(2,3))

a = list(map(func,[2],[3])) #list类型
print(a)
```

### 16.浅复制&深复制

```python
import copy

a = [1,2,3]
b =a
print(id(a),id(b)) #inode相同（同一块磁盘块），a改变自己的内容，b也跟着改变（写同一块空间）

a = [1,2,[3,4]]
c = copy.copy(a)
print(id(a),id(c)) #inode不同
a[1] = 22
a[2][0] = 33
print(c[1])
print(c[2][0]) #浅复制：a改最浅层内容，c不会跟着改；a改深层次内容时，c跟着改

d = copy.deepcopy(a)
print(id(a),id(d)) #inode不同
a[1] = 22
a[2][0] = 33
print(d[1])
print(d[2][0]) #深复制：a改内容，b跟着改，无论浅层深层
```

### 17.numpy

- array()、shape()、dtype()、矩阵运算
- 一维：向量，二维：矩阵，三维及以上：张量 or 多维数组

```python
import numpy as np

x = np.array([1,2,3])
y = np.array([3,4,5])
print(x + y) #数组中各元素进行运算，需保证两个数组的元素个数相同

print(x / 2) #[0.5 1.  1.5]，即数组x中的每个元素做除2运算

A = np.array([[1,2],[3,4]]) #矩阵
print(A.shape) #查看矩阵A的形状
print(A.dtype) #查看矩阵元素的数据类型
print(A * 10) #对矩阵同样也可以进行运算
```

- 广播：使得不同形状的数组之间也可以进行运算

```python
A = np.array([[1,2],[3,4]])
B = np.array([10,20])
print(A * B) #[[10 40]
             #[30 80]]
```

- unique方法
  - arr：输入的数组
  - return_index：若为true，则返回唯一值在原数组的索引
  - return_counts：若为true，则返回每个唯一值出现的次数
  - axis：指定去重的维度，None默认在展开的一维数组去重

```python
numpy.unique(arr, return_index=False, return_counts=False, axis=None)
```

- vstack方法
  - 合并元组/列表中的所有数据为一个大数组
  - **np.vstack(list)**
- 其他遇到的方法

| astype(np.int)           | 类型转换为int                |
| ------------------------ | ---------------------------- |
| np.exp(-x)               | 即e的-x次方                  |
| np.arange(-2.0,3.0,0.1)  | 在-2到3的范围内，以0.1为单位 |
| np.dot(A,B)              | 矩阵乘法AB                   |
| np.ndim(A)               | 矩阵A的维数                  |
| np.random.choice(600,10) | 从0-599中随机选择10个数字    |

### 18.matplolib

- 绘制图形

```python
import matplotlib.pyplot as plt
import numpy as np

x = np.arange(0,6,0.1) #以0.1为单位，生成0到6的数据
y = np.sin(x)

plt.plot(x,y)
plt.show()
x = np.arange(0,6,0.1) #以0.1为单位，生成0到6的数据
y1 = np.sin(x)
y2 = np.cos(x)

#绘制图形
plt.plot(x,y1,label = "sin")
plt.plot(x,y2,linestyle = "--",label = "cos") #虚线绘制
plt.xlabel("x") #x轴标签
plt.ylabel("y") #y轴标签
plt.title('sin & cos') #标题
plt.legend() #添加图例
plt.show()
```

![image](https://raw.githubusercontent.com/sleepyDev0x/Pictures/main/412e427ca197886b476d46f72d4f8773.png)	

- 显示图像

```python
import matplotlib.pyplot as plt
from matplotlib.image import imread

img = imread("D:\\【哲风壁纸】间谍过家家-阿尼亚.png")
plt.imshow(img)
plt.show()
```

![image](https://raw.githubusercontent.com/sleepyDev0x/Pictures/main/d0f4fbf16d338cd0a0cd8b5241293d7f.png)

### 19.科学计数法

1. 正指数：
   - `1e3` 表示 103=1000103=1000。
   - `2e6` 表示 2×106=2,000,0002×106=2,000,000。
2. 负指数：
   - `1e-3` 表示 10−3=0.00110−3=0.001。
   - `5e-6` 表示 5×10−6=0.0000055×10−6=0.000005。
3. 小数部分：
   - `1.23e4` 表示 1.23×104=12,3001.23×104=12,300。
   - `4.56e-2` 表示 4.56×10−2=0.04564.56×10−2=0.0456

### 20.关于类中的self

```python
class Affine:
    def __init__(self,W,b):
        self.W = W
        self.b = b
        self.x = None
        self.dW = None
        self.db = None
```

- `self` 是 Python 类中指向 **当前对象实例** 的引用。在类的方法（如 `__init__`、`forward`、`backward`）中，所有对实例属性和方法的访问都必须通过 `self`。
- 在 C++ 中，`this` 是一个指针，可以隐式使用（例如直接写 `W` 而不是 `this->W`），但在 Python 中必须显式使用 `self`。

### 21.OS模块

最常用的子模块：**os.path**

- exists(path)检查该路径的文件/目录是否存在

```python
os.path.exists("./dataset")
```

- isfile(path)判断该路径是否是文件

```python
os.path.isfile("./dataset")
```

- isdir(path)判断是否为目录
- basename(path)返回不包含所在目录的文件名

```python
os.path.basename("dir/dir1/file.txt")
#返回file.txt
```

- dirname(path)返回文件所在目录

```python
os.path.dirname("dir1/dir2/file.ext")
#返回'dir1/dir2'
```

- split(path)返回一个元组，第一个元素为目录，第二个元素为文件名

```python
os.path.split("dir1/dir2/file.ext")
#返回('dir1/dir2', 'file.ext')
```

- join(path,*paths)拼接路径

```python
os.path.join("dir/dir1","file.txt")
```

- 目录操作
  - listdir(path=’ ‘)返回一个列表，列表为**给定目录下所有文件和子目录**
- 目录操作&其他接口详见https://zhuanlan.zhihu.com/p/82029511

### 22.sys模块

- sys模块是与Python解释器交互的一个接口
- 该模块提供对解释器使用或维护的一些变量的访问和获取
- sys.path
  - 返回的是一个列表，列表第一个元素是当前文件路径
- https://zhuanlan.zhihu.com/p/338154218

### 23.Pandas（Python data analysis）

- 基本数据类型：dataframe和series
  - datafream：多行多列
  - series：多行单列
- 读取数据

```python
pd.read_csv
```

- .values()：将 DataFrame或 Series转换为 NumPy 数组
  - **DataFrame**：将 DataFrame 转换为一个二维 NumPy 数组
  - **Series**：将 Series 转换为一维 NumPy 数组

### 24.切片

- 基本切片

```python
list = [0,1,2,3,4,5,6,7,8,9]

list[2,5]#索引2-5（不包含5，左闭右开）即2,3,4
list[:5]#索引0-5（依然左闭右开）即0,1,2,3,4
list[5:]#索引5-末尾,即5,6,7,8,9
list[:]#所有元素
```

- 使用步长，负索引：表示从后向前计数

```python
list[::2]#每个两个元素提取一个,即0,2,4,6,8
list[1::2]#从索引1开始，每隔两个元素提取一个,即1,3,5,7,9
list[::-1]#从后向前每隔一个元素提取一个,即9 - 0
list[::-2]#从后向前每隔两个元素提取一个,即9,7,5,3,1
```

- 二维矩阵的切片

```python
data[i,j]#常规获取元素方法，获取第i行，第j列的元素
data[i:]#获取第i行所有元素
data[:j]#获取第j列所有元素
```