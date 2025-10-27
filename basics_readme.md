# Non-blocking I/O - Event Loop - coroutine, async, await
in asyncio concept is used
event loop is the scheduler of coroutines
when you mark something function as async it is a coroutine
eventloop will schedule the couroutine and let it run until it hits await stmt
the coroutine once it hits await, will yield control to event loop
event loop then schedules other tasks to run. while that task is waiting to complete.
once that task si complete, then event loop lets it resume operation
so when a function  is awaiting , other tasks can run , so it exhibits non-blocking I/O behavior

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