# Project README file

## Proj Design

### Warm-Up1: Echo

Basically in this code, the client will send a message to the server, and the server will echo back whatever it reveiced from the client.

* **Client**: In initialization, the client should be able to set host name, port number and message from CMD args. After getting all the info the client will create a socket and try to connect to the server. If success, then the client send a message and wait for the return and print, otherwise will report error.

* **Server**: In initialization, the server should be able to set, port number and max pending number from CMD args. After getting all the info the server will create socket, bind to the host, and set up listening queues. Whenever the server receives a connection request from the client, it will accept and get the corresponding message. Then the server will circle back and echo the message back to the client before the connection end.

### Warm-Up2: File Transferring

This proj is similar to the last one, while it will read from a file and send to the client. The client should write the message from the server into a file.

* **Client**: The initialization is simliar. The different part is when receiving a message, the message might be broken up and splitted into several pieces, so the client need to keep receiving all the messages until nothing more arrives. And everytime the client receive something, it should write into the message. 

* **Server**: The initialization is simliar. When getting a new request, the server will open the file and start read and transfer. The message could be splitted so it needs to keep transfer until nothing more from the file. After all the message been transferred, the server will notifu the client that it's been completed.

### Part 1

We need to build the client-server which has the communination based on GERFILE protocal. The client could get files from server.

* **Client**: The client will make a GETFILE request. For each request, we initial a instance of gfcrequest_t. The main logic for receiving and writing in is gfc_perform function. One thing we need to notice is we need to check tte header and identify the real header by the end marker `\r\n\r\n`. The status from the server could be OK, FILE_NOT_FOUND, INVALID and ERROR. If OK, which means we are receiving messages correctly, then we can open a file and write the message. Otherwise report the issue correspondingly.

* **Client**: The server will get request and send the file, with the header to the client. For each request, we will use gfcontext_t to save the context related info and gfserver_t to save server related info. The main logic fir transferring is in gfserver_serve function, in which we check the request first. A valid request should have scheme = 'GEFILE', method = 'GET', a valid path. If the request is malformed, the server notifies the client by sending message `GETFILE INVALID \r\n\r\n`. Other wise just call the hander function to do the job.

### Part 2

In this part we need to implement a boss-worker pattern. So we need to use steque to store the requests and info. For the server, we use two numbers to track on the number of finished and pending requests, while in the server we use one number to track on the number of tacks in the queue. For both server and client, the worker will wait when the steque is empty and the boss will notify them when new requests come.


## Flow of Control

### Echo


* **Client**: create socket -> ask for connection -> send data ->  receive data and output -> close

* **Server**:create socket -> bind to port -> listen -> accept connection from client -> receive data -> send data back to the client -> close(loop)

### File Transferring

* **Client**: create socket -> ask for connection -> receive data -> write file -> if not finished receiving, continue, else close

* **Server**:create socket -> bind to port -> listen -> accept connection from client -> read files -> send data -> if not EDF, continue read file, else close(loop)

### Part1&2

The second part has the same flow as the first one, but in the multi thread version.

* **Client**: create socket -> ask for connection -> send request -> receive header -> check if header valid -> if valid, receive and write file, else close

* **Server**: create socket -> bind to port -> listen -> accept connection from client -> receive request ->check the header -> if legal, send header and file, else send invalid header -> close

## Implementation and Test

### Echo and File Transfer: The Echo code follows https://github.com/zx1986/xSinppet/tree/master/unix-socket-practice while the File Transfer is just slightly different. Since the process is straight forward I just use 1 self defined case to test.

### Part1 and Part2: The idea of implementation has been illustrated. I use some of the binaries files to test from Piazza.

## References
- https://github.com/zx1986/xSinppet/tree/master/unix-socket-practice

- http://beej.us/guide/bgnet/

- http://www.cs.rpi.edu/~moorthy/Courses/os98/Pgms/socket.html

- https://www.geeksforgeeks.org/socket-programming-cc/

- https://www.geeksforgeeks.org/thread-functions-in-c-c/



