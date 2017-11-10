---
layout:     post   				    # 使用的布局（不需要改）
title:      测试一下 				# 标题 
subtitle:   from hackerrank #副标题
date:       2017-11-10 				# 时间
author:     Manlin 						# 作者
header-img: img/post-bg-2015.jpg 	#这篇文章标题背景图片
catalog: true 						# 是否归档
tags:								#标签
    - 生活
---


```python
import random
N=4
V=[4,0,4,3]
P=[2,2,0,4]
score=0

def Go(step,energy):
    global score   #一定要有，不然会报错 “UnboundLocalError: local variable 'score' referenced before assignment”
    if step==N:
        score=score+V[N-1]
        print(score)
        return
    else:
#         way=random.randint(1,2)
#         if way==1:
#             score=score+V[step-1]
#         else:
#             energy=P[step-1]
#         if energy>0:
#             score=Go(step+1,energy-1)
#         else:
#             return

# Go(1,0)

```

    7



```python
# Q1: 输出list，计算
n = int(input())
print(i**2 for i in range(n))  #错，因为结果应该是数组，少了【】
print([i for i in range(n)])   #对，记得要加方括号变成数组
```

    3
    <generator object <genexpr> at 0x109d1ee08>
    [0, 1, 2]



```python
# Q2: 输出不带空格的list
if __name__ == '__main__':
    n = int(input())
    for i in range(n): 
        print(i+1,end="") # end=""不会换新的一行或者加空格
```

    3
    123


```python
# Q3: 每行一个command
import random
L=[]
N = int(input())


for row in range(N):
    inputs = input().split()           #不知道input有多少个的情况下，可以分类用len（）
    if len(inputs)==1:
        command =inputs[0]
    if len(inputs)==2:
        command = inputs[0]
        e = int(inputs[1])
    if len(inputs)==3:
        command = inputs[0]
        i = int(inputs[1])
        e = int(inputs[2])
        
    if command=="insert":
       L.insert(i,e)
    elif command=="remove":
       L.remove(e)
    elif command=="append":
       L.append(e)
    elif command=="sort":
       L.sort()
    elif command=="pop":
       L.pop()
    elif command=="reverse":
       L.reverse()  
    elif command=="print":   # 记得if后要加冒号，不然一直会提醒invalid syntax
        print(L)
```

    1
    print
    []



```python
# Q4: 把input给hash一下
n = int(input())
integer_list = map(int, input().split())  #map(function to apply, list of input)
integer_tuple = tuple(integer_list)  #list 不能hash， tuple可以
print(hash(integer_tuple))
```

    2
    2 3
    3713082714463740756



```python
# Q5: 输入xyz，输出加起来不为n的所有组合，并排序
x = int(input())
y = int(input())
z = int(input())
n = int(input())
print(list([i,j,k] for i in range(x+1) for j in range(y+1) for k in range(z+1) if (i+j+k !=n))) 
        #要转成list再print，不然有generator object <genexpr> at 0x105738728的问题
```

    1
    1
    1
    2
    [[0, 0, 0], [0, 0, 1], [0, 1, 0], [1, 0, 0], [1, 1, 1]]



```python
# Q6: 输入n个-100～100间的数，找出第二名
n = int(input()) 
arr = list(map(int, input().split()))  # 记得把map的结果转成list，不然报错File "solution.py", line 6, in 
secondlarge=-100
for i in range(n):
    if secondlarge<arr[i]<max(arr):
        secondlarge=arr[i]
print(secondlarge)     
```

    5
    1 2 3 4 5
    4



```python
#Q7：找出倒数第二名所有学生,并按字母顺序排列 {q:1,w:2,e:3,r:2}--output：r，w
#方法1

N=int(input())

students=[]              #list要append list
for i in range(N):
    name = input()
    score = float(input())
    students.append([name,score]) #list要append list
    
students.sort(key=lambda x:x[1])     #list 排序的方法！！！！

t=0
for i in range(N):
    if students[i][1]==students[0][1]:
        t=t+1             # 不一定只有一个最后一名

second = []               #记得先定义list            
for i in range(N):
    if i+1>t and students[i][1]==students[t][1]:
        second.append(students[i])
second.sort(key=lambda x:x[0])
for i in range(len(second)):     #循环记得用range
    print(second[i][0])

    
#方法2如下：！！！
```

    4
    q
    1
    w
    2
    e
    3
    r
    2
    r
    w



```python
#Q7：找出倒数第二名所有学生
#方法2
a = [[input(), float(input())] for i in range(int(input()))] #range 的input是最先输入的。其次才是name，然后是float
s = sorted(set([x[1] for x in a])) #set（）不能用于嵌套的列表。先用set找出不重复，再用sorted排序，
for name in sorted(x[0] for x in a if x[1] == s[1]): #先找出第二名的所有x，再把名字排序
    print(name)

#sort彻底改变原list
```

    4
    q
    1
    w
    2
    e
    3
    r
    2
    r
    w



```python
# Q8: 输入n个学生（name 分数 分数 分数）格式的资料，再输入学生名字查平均分
if __name__ == '__main__':
    n = int(input())
    student_marks = {}
    for _ in range(n):
        name, *line = input().split()      #把第一个跟其他分开 str跟list
        scores = list(map(float, line))
        student_marks[name] = scores
    query_name = input()
    print('%.2f'%(sum(student_marks[query_name])/3))  #'%.2f'%()需要加括号

```

    2
    q 1 2 3
    w 2 3 4
    q
    2.00



```python
# 字符串输出
s="hello"
print(len("s"),s[1])
```

    1 e



```python
# 字符串分离成一个字符跟一串字符，用星号
*name, line = input().split() 
print(line)
```

    1 2 3 4 5
    5



```python
# Q9：大小写转换
def swap_case(s):
    s = s.swapcase()    # 要用等式。只用s.swapcase()不会改变s
    return s           #return 后要+s
swap_case(input("输入一句话："))
```

    输入一句话：Hello world！





    'hELLO WORLD！'




```python
# Q10: 输入一句话，先分解，再用-连起来
def split_and_join(line):
    a=line.split(" ")
    line= "-".join(a)
    return line
line=input("输入一句话：")
print(split_and_join(line))
```

    输入一句话：I am Manlin!
    I-am-Manlin!



```python
# Q11: 输入姓名，输出Hello XX XX! You just delved into python.
# 方法1:
def print_full_name(a, b):
    s = ["Hello"," ",a," ",b,"! ","You just delved into python."]  ## 不这么写的话感叹号前面总是多出一个空格
    for i in range(len(s)):
        print(s[i],end="")          #跟Q2一样，输出没有空格没有换行
print_full_name(input("First name:"),input("Last name:"))   #为什么结果前面没有“out”，而Q9有？
```

    First name:Manlin
    Last name:Cai
    Hello Manlin Cai! You just delved into python.


```python
# 方法2: Smart！！！！
def print_full_name(a, b):
    print("Hello %s %s! You just delved into python." % (a,b))
print_full_name(input("First name:"),input("Last name:"))
```

    First name:Manlin
    Last name:Cai
    Hello Manlin Cai! You just delved into python.



```python
# 方法3：
def print_full_name(a, b):
    print ("Hello " + a + " " + b + "! You just delved into python.")  #用+号就可以无缝衔接
print_full_name(input("First name:"),input("Last name:"))
```

    First name:Manlin
    Last name:Cai
    Hello Manlin Cai! You just delved into python.



```python
# Q11方法2中 %s的用法
# 把%()里的元素转换成相应的格式，组成一个字符串
"%s:%d"%("Price",100)
```




    'Price:100'




```python
# Q12: 输入一串字符串，一个index，及一个character，把原来index对应的字符换掉
def mutate_string(string, position, character):
    List = list(string)             
    List[position]= character        #原始字符串string[i]能出结果，但是不能修改，必须改成list之后才可以修改。
    string = "".join(List)           #用“”重新连起来，无空格
    return string
if __name__ == '__main__':
    s = input("输入字符串：")
    i, c = input("index 空格 character:").split()
    s_new = mutate_string(s, int(i), c)
    print(s_new)
```

    输入字符串：Manlim Cai
    index 空格 character:5 n
    Manlin Cai



```python
# 逻辑值也可以sum()
s=[False,False]
p=[False,True]
print(sum(s)) #不能用s.sum(),列表没有这种用法
print(sum(p))
```

    0
    1



```python
# Q13: 输入一个字符串，看有没有包含任何以下各种值：
# 方法1
s = list(input("输入字符串："))
print(bool(sum([s[i].isalnum() for i in range(len(s))]))) #数字或字母
print(bool(sum([s[i].isalpha() for i in range(len(s))]))) #字母
print(bool(sum([s[i].isdigit() for i in range(len(s))]))) #数字
print(bool(sum([s[i].islower() for i in range(len(s))]))) #小写字母
print(bool(sum([s[i].isupper() for i in range(len(s))]))) #大写字母
```

    输入字符串：qA2
    True
    True
    True
    True
    True



```python
# 方法2:
S = input("输入字符串：")
print(any([char.isalnum() for char in S]))  #用any 等于 bool(sum())
print(any([char.isalpha() for char in S]))  
print(any([char.isdigit() for char in S]))
print(any([char.islower() for char in S]))
print(any([char.isupper() for char in S]))  # i.islower() for i in string!!!!!!!!Smart!!!
```

    输入字符串：qA2
    True
    True
    True
    True
    True



```python
# 调节字符串位置
width = 20
print('HackerRank'.ljust(width,'-'))
print('HackerRank'.center(width,'-'))
print('HackerRank'.rjust(width,'-'))
```

    HackerRank----------
    -----HackerRank-----
    ----------HackerRank



```python
## join  Q10用到
import string
alpha = string.ascii_lowercase

n = int(input())
L = []
for i in range(n):
    s = "-".join(alpha[i:n])
    L.append((s[::-1]+s[1:]).center(4*n-3, "-")) #先写出中间行，接着append下半部分
print('\n'.join(L[:0:-1]+L))        #把下半部分颠倒一下，用换行符join,中间用+号
```

    5
    --------e--------
    ------e-d-e------
    ----e-d-c-d-e----
    --e-d-c-b-c-d-e--
    e-d-c-b-a-b-c-d-e
    --e-d-c-b-c-d-e--
    ----e-d-c-d-e----
    ------e-d-e------
    --------e--------



```python
#.title()会把123abc变成123Abc。。 
#.capitalize()只会把第一个是字母的字符串首字母大写
```


```python
# Q14: 小黄人拼字：随意输入一个字符串，如BANANA，以辅音开头的所有子集给stuart加一分（可以重复加分），以原音开头的所有子集给kevin加一分
def minion_game(string):
    kevin=0
    stuart=0
    vowels="AEIOUaeiou"
    
    for i in range(len(string)): 
        if string[i] in vowels:
            kevin += (len(string)-i)          #找出规律比程序重要
        else:
            stuart += (len(string)-i)          #可以不用括号
    
    if kevin>stuart:
        print("Kevin",kevin)
    elif stuart>kevin:
        print("Stuart",stuart)
    else:
        print("Draw")    

minion_game(input("输入一个字符串："))
```

    输入一个字符串：BANANA
    Stuart 12



```python

```
