```python
#-回车输入空异常
#未输入任何参数直接运行异常
#-未达到字符串要求不返回内容
#-若output未返回内容，则手动返回内容
#-client退出关键字
import commands,sys,logging
from socket import *
from time import sleep

def getlog(arg):
  log=logging.getLogger()
  log.setLevel(logging.INFO)
  fh=logging.FileHandler("/tmp/py-cmd.log",mode="w")
  log.addHandler(fh)
  log.info(arg)

def Server(port=9527):
  server=socket()
  server.bind(("",int(port)))
  server.setsockopt(SOL_SOCKET,SO_REUSEADDR,1)
  server.listen(5)
  while 1:
    c,addr=server.accept()
    print("connected from:",addr)
    while 2:
      data=c.recv(1024)
      if data == "qs":
        c.close()
        server.close()
        sys.exit("user exit")
      if data:
        status,output=commands.getstatusoutput(data)
        getlog(data)
        if len(output) == 0:
          c.send(str(status))
        else:
          print(output)
          c.send(output)
      else:
        print("disconnected from:",addr)
        c.close()
        break
  server.close()

def Client(server_ip,port=9527):
  client=socket()
  client.connect((server_ip,int(port)))
  while 1:
    msg=input("cmd>>")
    if msg =="qc":
      client.close()
      sys.exit(0)
    if len(msg) != 0:
      client.send(msg)
      sleep(0.5)
      data=client.recv(1024)
    if not data:
      client.close()
      sys.exit(0)
    print("origin data:",data)
    print(data)

try:
  if sys.argv[1] =="-s":
    Server(*sys.argv[2:])
  elif sys.argv[1]=="-c":
    Client(*sys.argv[2:])
except:
  print('''
  for server_side:
   xx.py -s [port]
  for client_side:
   xx.py -c <server_ip> <port>
  ''')
```
