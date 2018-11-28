python时间戳

time,datetime模块

time.time()获得当前时间戳
time.localtime()获得当地时间时间
time.gmtime()获得utc当地时间
time.strftime()将一个元祖格式时间转化为指定格式
x=time.localtime()
time.strftime("%Y-%m-%d %H:%M:%S",x)


datetime
print(datetime.datetime.now())#当前时间
print(datetime.datetime.now()+datetime.timedelta(3))三天后
print(datetime.datetime.now()+datetime.timedelta(-3))三天前
print(datetime.datetime.now()+datetime.timedelta(hours=3))往后三小时




