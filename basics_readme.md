# Non-blocking I/O - Event Loop - coroutine, async, await
- in asyncio concept is used
- event loop is the scheduler of coroutines
- when you mark something function as async it is a coroutine
- eventloop will schedule the couroutine and let it run until it hits await stmt
- the coroutine once it hits await, will yield control to event loop
- event loop then schedules other tasks to run. while that task is waiting to complete.
- once that task si complete, then event loop lets it resume operation
- so when a function  is awaiting , other tasks can run , so it exhibits non-blocking I/O behavior

# within the computer - process, threads - pipes
a process will have memory allocated to it
some steps in the process can run in parallel and once they finish may be only sequential execution has to happen and then again few tasks can be done in parallel.. whenever there are things that can be done in parallel, we can schedule them to run as threads.
threads are things running within the process, using the same memory, using different cores of the cpu
each process will have 3 pipes..stdin, stdout, stderr.
Shell is a software used to interact with OS
when we want 2 processes to communicate with each other, and we run some command in shell, it uses OS commands and makes a connection between stdout pipe (aka channel) of one process to the stdin pipe of the other process.

# between computers - sockets , TCP, UDP , localhost 
when we want one process in one computer to communicate with another process in another computer (generally called cliet on one computer and server on other) then OS on both computers creates a socket between those 2 computers. the type of socket can be TCP (which means sending data until it is guaranteed to be delivered and ensuring it is sent in the same order etc..)..then it is called TCP socket or TCP connection..
the type of socket can be UDP ( send data and dont bother to check if it was actually received at the destination socket)
when we say localhost, i.e. ip address 127.0.0.1, the os creates both sockets on the same computer and one connected to client process, other connected to server process but both on teh same machine.
In UNIX , it does this sockets between processes to make them talk to each other.
============
# between computers - TCP, HTTP, REST, GRAPHQL
- client and server agreeing to follow TCP means creating socket of that type, which means they both agree to follow rules where they will ensure reliable delivery of data, ensure order is followed, verification is done at the other end, retry if needed etc. we dont care about the data format of what is being sent in this case. so TCP is protocol of sendign data and is at Network layer of OSI model of communication.
- HTTP means caring about the data format..ensuring that it is like a request, having a header, use standard methods like GET/POST/PUT/DELETE, standard status codes, body, type etc.. client sending data in a certain format, server sending data in a certain format using that TCP connection... so HTTP is a protocol for data format.. and is at application layer of OSI model of communication..
- REST means not just the data format , but also deciding what can be requested and what will be sent, like dont just ask for anything and send everything ..ask for /users and server should provide that api and when asked, provide only users data and nothing else.. so that it is organized..this is called organizing api's at resource level. also ensuring the request response is not dependent on remembering the previous requests..so it is STATELESS.. so REST (representational state transfer) is an architecture style of communication..so this is also at application layer..
- GRAPHQL is another architecture style that tells clietn to ask what exactly they want , dont just ask /users.. ask exaclty i want this users all data across orders, complaints, stores etc.. and thee server to first gather all their data , cleanup, avoid sending unnecessary data and then send the only required data 


# between computers - HTTP/1, HTTP/2
- HTTP is a way of sending a request and getting a response.. and there is one TCP connection socket created and closed for that purpose.
- HTTP/2 is a way of using that single TCP connection socket and sending multiple requests and getting responses to those requests. so the format of data sent and received is different when client and server follow HTTP/2 rules which then allow single connection to be used by multiple clients .. so this does multiplexing of connection.

# between computers - HTTP, persistent HTTP
- in general HTTP format of sending data over TCP, the socket created of TCP type will not have the keepalive option set..so it is closed after response.
- in persistent HTTP, we ensure that the keepalive option is set, so that the connection remains open, so that it can handle multiple requests and responses.
- so whether you use keepalive set or when you use HTTP/2 in both cases keepalive is set so that the TCP type socket connection is kept open for multiple requests and responses.


I wrote below temrs and words in some ranodm order, if it makes sense group them and sort in a logical order..so that one should know the above terms first before they can understand the below ones..
I want to know for each of these words, what is the scenario in which this term is needed, what role needs it, how will they create, how will it appear or how can they see it, what are its characteristics, what can they do with it.., what category of term is it, if it is a spec or protocol, in which scenario is it needed, who needs it, what are the basic rules it says, what are its implementation examples, . DONT USE COMPLEX LANGUAGE. USE SIMPLE TERMS, WITH ANALOGIES AND GIVE ME CLEAR MENTAL MODELS TO UNDERSTAND, RECALL EASILY. 
# process

# server process

# client process

# child process

# import sys

# import asyncio

# import threading

# list
lines[-1] # lastline

# import socket
socket.socket(socket.AF_INET,socket.SOCK_STREAM)
socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR)
socket.bind
socket.sendall
socket.listen
socket.accept
socket.recv().decode(utf-8)
# import requests


# import urllib.request, urllib.parse

# request.get, ..

# import json
request =json.loads(line)
json.dumps(response)
JSON.parse(event.data) ..may be js


# =, == ,===


# date
datetime.now().isoformat()

# event handling

# EventSource
EventSource('/mcp-stream')
# GET , POST

# VS code extension 'spawns' python mcp server as child process

# sub process

# inter process communication

# class ..def __init__():

# stdin/stdout/stderr pipes
print(..,file=sys.stderr)
for line in sys.stdin
line.strip
print(..,flush=True)
# socket

# list of dictionaries  handling
[
    {
        ..,{}
    },{}
]
# text/event-stream

# TCP

# HTTP

# REST

# persistent http connection


# Streamable HTTP


# HTTP/1.1 over TCP

# HTTP/2 over TCP

# SSE
# Websocket over TCP