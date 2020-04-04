```python
import sys,os
from socket import *

def file_server(port=9527):
  server=socket()
  server.bind(("",int(port)))
  server.listen(5)
  c,addr=server.accept()
  filename=c.recv(1024)
  if os.path.exists(filename):
    file=open(filename,"rb")
    while 1:
      data=file.read(1024)
      if not data:
        break
      c.send(data)
    file.close()
    print("file transfer ok")
  else:
    c.send("file not found")
  server.close()
  c.close()

def file_client(server_ip="",server_port=9527):
  client=socket()
  list=[]
  client.connect((server_ip,int(server_port)))
  filename=input("file to receive:")
  client.send(filename)
  list=filename.split("/")
  f=open(list[-1],"wb")
  while 1:
    data=client.recv(1024)
    if not data:
      break
    f.write(data)
  f.close()
  client.close()

def main():
  if sys.argv[1] == "-s":
    file_server(sys.argv[2])
  elif sys.argv[1] == "-c":
    file_client(*sys.argv[2:])
try:
  main()
except (IndexError):
  print('''
作为服务端语法：#xx.py -s <port>
接收端语法：#xx.py -c  <server_ip> <server_port>
  '''
  )

```
