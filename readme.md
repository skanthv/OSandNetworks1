# stdin, stdout, stderr
os maintains standard streams :stdin, stdout,stderr. when you write print() stmt it writes to stdout, then OS handles display on screen. when you write input stmt it gets from stdin stream, to which you wrote input with keyboard.. 

Transport means communication mechanism between client and server
whereas OSI Layer 4 transport layer is more about application level protocol for msg exchange.

# MCP Transport types i.e. communication mechanisms are

Transport method    Application Presentation        Session         Transport   Network Datalink            Physical
STDIO               STDIO       JSON serialization  N/A             N/A         N/A     N/A                 Process pipes
HTTP/SSE/WS         HTTP        JSON serialization  HTTP Session    TCP         IP      Network interface   Networkcabls/wireles


## stdio
1)When you have local desktop apps, vs code extensions, command line tools
you can use process based communication via stdin/ stdout. this is only for single user
which is bidireectional and faster
vscode extension with mcp server : here extension spawns python mcp server as child process.
when you have a desktop app with background processing, you want desktop app to process files and show progress, so use stdio for commands, sse for progress updates, so in this case stdio and sse hybrid way of comms.
This uses application and process communication . it bypasses network layers 2-4. it uses interprocess communication IPC. the protocol is direct stdin/stdout pipes

Here is a basic example of client-server communication over stdio in Python, using the principle behind the MCP stdio transport: 

the client launches the server as a subprocess and communicates by writing to its stdin and reading from its stdout.
Client (stdio_client.py)
This client starts the server subprocess and communicates using stdin/stdout:
python
import subprocess
proc = subprocess.Popen(
    ["python", "stdio_server.py"],
    stdin=subprocess.PIPE,
    stdout=subprocess.PIPE,
    stderr=subprocess.PIPE,
    text=True,
)
#Send a message
#The client communicates via the server's stdin and receives output from stdout; both must use consistent encoding (text=True or #proper encoding/decoding).
proc.stdin.write("hello stdio\n")
#Every message sent to the server should end with a newline (\n) to ensure the server reads it correctly.
#Always flush the input buffer (stdin.flush()) after writing to ensure the server receives the message immediately.
proc.stdin.flush()
#Get the response
response = proc.stdout.readline().strip()
print("Server responded:", response)  # Expected: "HELLO STDIO"
#End the server
proc.stdin.close()
proc.terminate()

Server (stdio_server.py)
This server simply echoes lines sent to it, upper-cased:
python
import sys
for line in sys.stdin:
    response = line.strip().upper()
#The server must flush outputs (print(..., flush=True)) so that responses go to the client without buffering delays.
    print(response, flush=True)


This pattern scales up to protocols (like MCP), where messages can be line-delimited JSON objects for structured communication.​
This is the essence of stdio transport: spawning a process, and communicating via pipes for simple and fast local integration.
## http
2)when you have web apps, microservices, cross platform api's you need request and response web communication, so use HTTP protocol which allows that. this is for multi users.
when you are creating a web based ai assistant, browser makes api calls to mcp server, and they can be stateless, need to be scalable, work across internet, so use http.
when you are doing ci/cd pipeline with mcp tools, then the build server calls mcp tools via REST API, so you need stateless, reliable, way of callign and it should handle failures well, so use HTTP

it uses all 7 OSI layers, no layer is bypassed, the prtocol is http with text/event-stream, and mechanism is persistent http connection

import requests
resp = requests.post("https://api.example.com/foo", json={"key": "value"})
print(resp.json())
Request: client sends a full HTTP request, server replies with a complete response.server completes the response before returning



## streamable http
2.1) streamable http
Server streams multiple chunks over a single HTTP connection. Often used with chunked transfer-encoding or similar streaming semantics.


In client-side Python:
use stream=True on requests and iterate with for chunk in response.iter_content()
Request is as usual (POST or GET), but the response is sent in chunks.

In server-side :(Python, FastAPI example):
from fastapi.responses import StreamingResponse
@app.post("/api/stream/")
async def stream():
    async def event_generator():
        yield "chunk 1\n"
        yield "chunk 2\n"
    return StreamingResponse(event_generator(), media_type="text/plain")

## sse
3)when you want live dashboards, notifications, progress updates, you want unidirectional streaming of data from server to client, so use Server sent events SSE
when you are coding realtime code analysis dashboard, you need realtime updates from server to dashboard, live code quality metrics streaming to dashboard, so use sse.
Unidirectional stream from server to client over HTTP. Text/event-stream media type is used, and each message is typically separated by double newlines.

Client: typically a browser's EventSource API; Python clients read lines and parse as SSE events

Python, FastAPI server example:
from fastapi.responses import StreamingResponse
@app.get("/events")
async def events():
    async def event_generator():
        yield "data: message 1\n\n"
        yield "data: message 2\n\n"
    return StreamingResponse(event_generator(), media_type="text/event-stream")

## websockets 
4) when you want collaborative apps, gaming, chat systems where bidrectional message streams are needed but over the web plus real time communication, then you need websockets.
when you have multiple users editing the same document and you need live updates, use websockets
Persistent, bidirectional TCP connection established by upgrading an HTTP handshake.
Both client and server can send messages at any time; messages are not limited to text (can be binary)

this also uses all 7 osi layers, websocket protocol over TCP, upgraded HTTP connection.

Python, WebSockets client example:
import websockets
import asyncio

async def chat():
    async with websockets.connect("ws://example.com/ws") as websocket:
        await websocket.send("hello")
        response = await websocket.recv()
        print(response)
asyncio.run(chat())

## summary
websockets and sse provide realtime
stdio and websockets are bidirectional
stdio is immediate, but http is on-demand
http, sse, websockets work over networks but stdio is local only
stdio is simplest but websocket is most complex
you want to handle comms in the same laptop, use stdio




# connect via stdio vs http -diff?
stdio =easiest way for 2 local processes (mcp client which is claude or cursor or windsurf) and mcp server hosted locally to communicate; does not need sockets,http; lightweight. good for testing in dev 
once connected mcp server lists the tools available

## using stdio (i.e. running mcp server on your local machine for testing)?
mcpservers 
    flight-mcp 
        command :python
        args :[flight-mcp-server.py]
        env [APIKEY:Value]

start mcp server locally
python3 flight-mcp.py 
or npx flight-mcp
configure the mcp client to connect to it

## using http 
### (i.e. running your own instance of mcp server to be shared by different clients)
mcpservers 
    flight-mcp 
        url: http://localhost:3000/mcp
        headers:
            APIKEY: value
### to connect to a mcp server hosted remotely by someone
mcpservers 
    flight-mcp 
        url: https://joyair.com/mcp
        headers:
            APIKEY: value
in this case the mcp client connects to mcp server via http , and the url points to the remote mcp server, but you need to take care of authN,authZ, data privacy,Trust of the external server as you are sending data externally to a 3rd party



# why MCP
lets say you are building an app to book flight tickets.
you need to know the flight companies api server that provided those api's 
then directly call those api's in your code to book tickets

instead if there was another  server that  provided those api's and you connect to that , discover api's available and call them.

## That server can be hosted locally in your company networ, in that case you can connect to it via stdio or http
Configuring mcp server locally:
if the tool like cursor/windsurf/claudecode , they have mcp config in a file mcp.json
 which has list of mcp servers, command to start the server, env variables to pass 
 the IDE's like claudecode, cursor etc, start the mcp server in stdio mode



## or that server could be  hosted by the flight company on their systems..in that case you connect to it via http


# MCP
a list of rules that mcp client and mcp server should follow so that they can talk to each other.
and thereby help to extend the functionality of the LLM ..by providing external knowledge or resources, external tools through with it can take actions, externally provided detailed prompts which are very well written that the client can use when asking server so that it can respond better in terms of picking the right tool..

# MCP server can provide resources 

Resources aka knowledge bases or rules for various decisions or processes. 
Resources:
uri, name, title, description, mimetype

uri= a https://.. link to a file OR , file:///...md , OR a git repository file


# MCP Server can provide better defined prompts that users can use
instead of users using a prompt like : find me a flight to bangalore
you can then use the server defined prompt like 
you are a travel assistant, whenthe user asks about flights, you must call the search_flights tool with origin destination and date.
format all dates as yyyy--mm-dd .be helpful and concise.

Prompts:
name, title, description, arguments (name,description, required)...

# MCP server can define tools 
should define tools indicating various details some of which are
Tool: name, title, description, input schema (type=object, properties: from-type,desc, to-type,desc, date-type,desc,format, required=[from,to,date])

# jsonrpc 2.0 way of communication between mcp client and mcp server
JSON-RPC 2.0 Protocol
client should send request to server and the request should have
jsonrpc:2.0, method = the method to call, params : the input params to the method, an id number 
Server should respond in this format : jsonrpc:2.0, result:, error : and same id number

stateless

upto us to decide how to transmit the data 
mcp supports http, stdio as ways to transport data
other ways: tcp,udp,unix sockets,message q (am not clear why these were even discussed when mcp does not support them???)

# sdk's to build mcp servers and clients
https://modelcontextprotocol.io/docs/sdk

# official MCP python SDK
fastmcp​

pip install "mcp[cli]"

Successfully installed annotated-types-0.7.0 anyio-4.11.0 attrs-25.4.0 certifi-2025.10.5 click-8.3.0 colorama-0.4.6 h11-0.16.0 httpcore-1.0.9 httpx-0.28.1 httpx-sse-0.4.3 idna-3.11 jsonschema-4.25.1 jsonschema-specifications-2025.9.1 markdown-it-py-4.0.0 

mcp-1.19.0 

mdurl-0.1.2 

pydantic-2.12.3 pydantic-core-2.41.4 pydantic-settings-2.11.0 

pygments-2.19.2 python-dotenv-1.1.1 python-multipart-0.0.20 pywin32-311 referencing-0.37.0 rich-14.2.0 rpds-py-0.28.0 shellingham-1.5.4 sniffio-1.3.1 sse-starlette-3.0.2 starlette-0.48.0 typer-0.20.0 typing-extensions-4.15.0 typing-inspection-0.4.2 

uvicorn-0.38.0 ??
