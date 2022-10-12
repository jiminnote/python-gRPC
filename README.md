# RPC
**Remote Procedure Call(원격 프로시저 호출)**
별도의 원격 제어를 위한 코딩없이 다른 주소 공간에서 함수나 프로시저를 실행할 수 있게 하는 통신 기술
일반적인 프로세스는 자신의 주소공간 안에 존재하는 함수만 호출하여 실행 가능하지만
RPC는 네트워크를 통한 메시징을 수행하기 때문에 함수 또는 프로시저가 로컬 위치에 있든, 원격 위치에 있든 상관없이 동일하게 수행 가능

# python-gRPC

![image](https://user-images.githubusercontent.com/95075455/195239844-9491dbfb-df9b-44b4-ab5f-5828a0faad59.png)

```
pip3 install --upgrade pip
pip3 install grpcio
pip3 install grpcio-tools
pip3 install --upgrade google-api-python-client --ignore-installed
```
* `greet.proto`생성 후 `/python-gRPC` 경로에서 protocol buffers 컴파일 진행
```
python3 -m grpc_tools.protoc -I=./ --python_out=./ --grpc_python_out=./ ./greet.proto
```

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

## proto 파일
### syntax
`greet.proto`
```
syntax = "proto3"; 
```
`syntax`버전을 명시하는 구문으로 `proto2`나 `proto3`중에서 적용
대부분 `proto3`을 쓰지만 이 부분이 명시되어있지 않을 경우 default 값인 `proto2`로 적용되니 명시하는게 좋다.

### service - rpc
```
service Greeter {
	// Unary
	rpc SayHello (HelloRequest) returns (HelloReply);

	// Server Streaming
	rpc ParrotSaysHello (HelloRequest) returns (stream HelloReply);
	
	// Client Streaming
	rpc ChattyClientSaysHello (stream HelloRequest) returns (DelayedReply);

	// Both Streaming
	rpc InteractingHello (stream HelloRequest) returns (stream HelloReply);
}
```

### Field types
```
message HelloRequest {
  string name = 1;
  string greeting = 2;
}
```
* double, float : 실수
* int32, int64 : 정수 자료형 / 음수에는 비효율적
* uint32, uint64 : 부호없는 정수
* sint32, sint64 : 부호있는 정수
* fixed32, fixed64 : 항상 4, 8바이트. 값이 2^28, 2^56보다 크면 효율적
* sfixed32, sfixed64 : 항상 4, 8바이트
* bool : 부울
* string : UTF-8 혹은 7bit ASCII 
* bytes : 임의의 바이트 배열 사용가능

### Tag
```
message HelloRequest {
  string name = 1;
  string greeting = 2;
}
```
각 변수 내의 숫자는 바이너리 형식의 필드를 구분하기 위해 사용하는 태그
* 1부터 2^29까지사용가능 / 9000~19999는 protocol buffers 구현을 위해 사용하고 있어 유저는 사용 불가
* 태그 값 중 1~15까지는 1바이트로 인코딩하고 이후 숫자들은 2바이트 이상으로 인코딩하기 때문에 자주 쓸 필드에 사용하는게 좋다.


### Field rules
```
message HelloReply {
  string message = 1;
}
```
* required : 이 필드를 반드시 1개 가진다.
* optional : 이 필드를 가지지 않거나 1개 가진다.
* repeated : 이 필드를 0개 이상 가진다.(연산이 비효율적이라고 한다)
optional을 베이스로 하되 해당 값이 없을 시 default값을 넣어주는 식.



