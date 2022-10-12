# python-gRPC

## 실행 과정
```
$ python3 greet_server.py
```
```
$ python3 greet_client.py
1. SayHello - Unary
2. ParrotSaysHello - Server Side Streaming
3. ChattyClientSaysHello - Client Side Streaming
4. InteractingHello - Both Streaming
Which rpc would you like to make: 
```
### SayHello Request:
greet_client.py
`if rpc_call == "1":`
```
# server

SayHello Request Made:
name: "Annie"
greeting: "Hi"
```
```
# client

Which rpc would you like to make: 1
SayHello Response Received:
message: "Hi Annie"
```

### ParrotSaysHello Request:
greet_client.py
`if rpc_call == "2":`
```
# server

ParrotSaysHello Request Made:
name: "Jimin"
greeting: "Hello"
```
```
# client

Which rpc would you like to make: 2
ParrotSaysHello Response Received:
message: "1 Hello Jimin"

ParrotSaysHello Response Received:
message: "2 Hello Jimin"

ParrotSaysHello Response Received:
message: "3 Hello Jimin"

ParrotSaysHello Response Received:
message: "4 Hello Jimin"

ParrotSaysHello Response Received:
message: "5 Hello Jimin"
```
**서버에서 클라이언트로 response**
0.5초마다 1번씩 총 5번 response


### ChattyClientSaysHello Request:
greet_client.py
`if rpc_call == "3":`
```
# client

Which rpc would you like to make: 3
Please enter a name (or nothing to stop chatting):
```
이름 입력 input
```
# client

Please enter a name (or nothing to stop chatting): jiminnote
Please enter a name (or nothing to stop chatting): jiminnote
Please enter a name (or nothing to stop chatting): 
```
**client에서 서버로 request**
```
# server

ChattyClientSaysHello Request Made:
name: "jiminnote"
greeting: "Hello"

ChattyClientSaysHello Request Made:
name: "jiminnote"
greeting: "Hello"
```

```
# client

Please enter a name (or nothing to stop chatting): jiminnote
Please enter a name (or nothing to stop chatting): jiminnote
Please enter a name (or nothing to stop chatting): 
ChattyClientSaysHello Response Received:
message: "You have sent 2 messages. Please expect a delayed response."
request {
  name: "jiminnote"
  greeting: "Hello"
}
request {
  name: "jiminnote"
  greeting: "Hello"
}
```
입력 없이 `enter`하면 채팅이 끝남
채팅이 끝나야 몇 개의 message를 보냈는지 어떤 message인지 출력

### InteractingHello Request:
greet_client.py
`if rpc_call == "4":`
```
# client

Which rpc would you like to make: 4
Please enter a name (or nothing to stop chatting): jiminnote
InteractingHello Response Received: 
message: "Hello jiminnote"

Please enter a name (or nothing to stop chatting): jiminnote
InteractingHello Response Received: 
message: "Hello jiminnote"

Please enter a name (or nothing to stop chatting): 
```
요청하면 바로바로 응답을 보여줌
```
# server

InteractingHello Request Made:
name: "jiminnote"
greeting: "Hello"

InteractingHello Request Made:
name: "jiminnote"
greeting: "Hello"
```






