## Transmission Control Protocol

TCP stands for Transmission Control Protocol. TCP is a communication standard that enables application programs and computing devices to exchange messages over a network. TCP establishes a connection between the source and destination before it starts transmitting the data. It breaks large amounts of data into smaller packets while ensuring data integrity.

## TCP server in python

 We import `socket` and `threading` module. The `socket` module provides various objects, constants , functions and related exceptions for building full-fledged network applications including client and server programs. The `threading` module allows a program to run multiple operations concurrently in the same process space ...

```python
import socket
import threading
```

We create a socket object with the the `AF_INET` and `SOCK_STREAM` parameters. `AF_INET` specifies the address family that our socket will use and in this case, it is Internet Protocol Version 4 address. `SOCK_STREAM` shows that the socket object will use TCP.

```python
#create TCP/IP socket
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
```

We define the `IP` and the port number for the server and we pass these arguments to the socket. This tells the socket object to listen on this `IP` address and port number. 

```python
server_ip = "0.0.0.0"
server_port = 9999

#Bind socket to port
sock.bind((server_ip,server_port))
```

This sets the number of maximum connections that can be connected to the server.in this case, the the server can only have a maximum of 5 connections.

```python
#Listen for incomming connections
sock.listen(5)
```

Once a client has established a connection to the server, we keep the client's socket in the `connection` variable and the connection details in the `client_addr` variable. `client_addr[0]` stores the IP address of the client and `client_addr[1]` stores the port that the client used to establish a connection to the server.

For each client connection, we spin up a thread object that calls the `handle_client` function and we pass in the `connection` variable as the argument to the function.

```python
while True:
    #wait for connection
    connection,client_addr = sock.accept()
    print("[*] Accepted connection from: %s:%d" %(client_addr[0],client_addr[1]))

    client_handler =  threading.Thread(target=handle_client, args=(connection,))
    client_handler.start()
```

The `handle_client` function receives what the client socket sent and it sends back an `ACK!` message to the client to acknowledge that the message was received.

Then it closes the connection.

```python
def handle_client(client_socket):

    request = client_socket.recv(1024)

    print("[*] Recieved: %s" %request)

    #send acknowldgement
    data = "ACK!".encode('utf-8')
    client_socket.send(data)

    client_socket.close()
```

Here is the full code for the `tcp` server in python.

```python
import socket
import threading

#create TCP/IP socket
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

server_ip = "0.0.0.0"
server_port = 9999

#Bind socket to port
sock.bind((server_ip,server_port))

#Listen for incomming connections
sock.listen(5)

print ("[*] Listening on %s:%d" %(server_ip,server_port))

def handle_client(client_socket):

    request = client_socket.recv(1024)

    print("[*] Recieved: %s" %request)

    #send acknowldgement
    data = "ACK!".encode('utf-8')
    client_socket.send(data)

    client_socket.close()
while True:
    #wait for connection
    connection,client_addr = sock.accept()
    print("[*] Accepted connection from: %s:%d" %(client_addr[0],client_addr[1]))

    client_handler =  threading.Thread(target=handle_client, args=(connection,))
    client_handler.start()

```

Let's run the server.

```bash
python3 tcp_server.py 
[*] Listening on 0.0.0.0:9999
```

The server is listening on `0.0.0.0:9999` and is ready for any incoming connections. 

##TCP client in python

Creating a `tcp` client is the same as creating the `tcp` server with only a few changes. Here the client connects to the server running on `0.0.0.0:9999` and sends a `Hello World!` message. Then, it prints the response it receives from the server.

```python
import socket

target_ip = "0.0.0.0"
target_port = 9999

#create a socket object
client = socket.socket(socket.AF_INET,socket.SOCK_STREAM)

#connect the client
client.connect((target_ip,target_port))

#send data
data = "Hello World!".encode('utf-8')
client.send(data)

#recieve data
response = client.recv(1024)

print(response)
```

Let's run the client in another shell.

```bash
python3 tcp_client.py 
b'ACK!'
```

Let's go back to the server and this is now the output of the server.

```bash
python3 tcp_server.py 
[*] Listening on 0.0.0.0:9999
[*] Accepted connection from: 127.0.0.1:55760
[*] Recieved: b'Hello World!'

```

If you stop the server and run it again, you will get an error about the `address already being in use`. To fix this run `pkill -9 python3` and then you should now be able to run the server again.

We have successfully built a `tcp` server and a `tcp` client in python.

