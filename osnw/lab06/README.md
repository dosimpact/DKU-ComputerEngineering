## 목표

IPC 에는 파이프, 유닉스 소켓 등의 방법이 있다.
파이프를 이용해서 Inter process communication을 해보자.

서버프로세스에서 클라이언트의 접속이 오면
자식 프로세스로 fork 하여 클라이언트와 통신한다.

이때, 클라이언트로부터 자식 프로세스가 받은 문자열은 부모 프로세스로 전달하여,
문자열을 합치고 다시 부모 프로세스가 받아서 이를 클라이언트로 전달한다.

- 이를 정리하면 다음의 과정으로 구현 한다.

1. 서버 실행
2. 클라이언트 접속
3. 서버에서 fork 하여 클라이언트와 통신
4. 클라이언트의 메시지를 read
5. 부모 서버 프로세스로 IPC로 전달
6. 부모 서버 프로세스는 각 자식 서버 프로세스가 받은 문자열을 취합
7. 이를 다시 자식 서버 프로세스로 전달
8. 자식 서버 프로세스는 클라이언트로 메시지를 write

구현 과정이 복잡할 수 있으니 프로세스별 역할을 정리하면 각자의 책임 로직이 보인다.

- 프로세스별 역할 정리

1. 부모 서버 프로세스

- 클라이언트의 접속을 승인 하고 프로세스를 Fork 한다.
- IPC를 통해 자식 프로세스로 온 데이터를 취합하고 다시 보낸다.

2. 자식 서버 프로세스

- 클라이언트의 메시지를 받는다.
- IPC를 통해 부모 프로세스로 받은 메시지를 보낸다.
- IPC를 통해 부모 프로세스로부터 취합된 데이터를 받는다.
- 클라이언트에게 취합된 메시지를 보낸다.

3. 클라이언트 프로세스

- 서버로 접속한다.
- 데이터를 보낸다.
- 취합된 데이터를 받는다.

## 예제 코드 실행법

gcc ./echo_client_pipe.c -o echo_client_pipe
gcc ./echo_server_pipe.c -o echo_server_pipe

## dist 코드 컴파일

gcc ./echo_server_fork.c -o echo_server_fork
gcc ./echo_client.c -o echo_client