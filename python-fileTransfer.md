```python
#-*-coding:utf-8-*-

#自定义服务端端口号，要发送的文件
#根据命令行参数判断为服务端还是客户端-s -c
#xx.py -s <filename> <port>
#xx.py -c <filename> <server_ip> <server_port>
#后期更新
#1.服务端不退出，只要是给出路径的请求都给与响应
#2.Windows下的共享，图形界面

from socket import *
import sys,os
def show_process():
  size=[]
  for i in sys.argv[2:]:
    os.path.getsize(i)
    
def Args():
  list=[]
  for i in range sys.argv[2:]:
    a=[]
    if os.path.exists(i):
      a=i.split("/") #切出文件名发送给Client
      list.append(a[-1])
  return list

def Server(fname,port=9527):
  server=socket()
  server.bind(("",int(port)))
  server.listen(5)
  c,addr=server.accept()
  c.send(fname.encode())
  f=open(fname,"rb")
  while 1:
    data=f.read(1024)
    if not data:
      break
    c.send(data)
  server.close()
  f.close()
  c.close()

def Client(fname="",server_ip="",server_port=9527):
  client=socket()
  client.connect((server_ip,int(server_port)))
  if len(fname) == 0: #没有收到传参则使用服务端发来的文件名
    fname=client.recv(1024)
  f=open(fname.decode(),"wb")
  while 1:
    data=client.recv(1024)
    if not data:
      break
    f.write(data)
  f.close()
  client.close()

def main():
  if sys.argv[1] == "-s":
    Server(*sys.argv[2:])
  elif sys.argv[1] == "-c":
    Client(*sys.argv[2:])
try:
  main()
except (IndexError):
  print('''
作为服务端语法：#xx.py -s <filename> <port>
接收端语法：#xx.py -c  <server_ip> <server_port>
  '''
  )
```
