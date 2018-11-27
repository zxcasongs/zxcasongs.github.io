random模块

import random
print(random.random())
print(random.randint(0,1))  [0,1]
print(random.randrange(0,3)) [0,3)
print(random.choice('hello'))

生成验证码
import  random
randmchoice=''
for i in range(4):
    x=random.randint(0,9)
    randmchoice+=str(x)
print(randmchoice)

字母加数字 随机验证码





