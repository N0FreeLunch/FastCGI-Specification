### 5.1 FCGI_BEGIN_REQUEST

The Web server sends a FCGI_BEGIN_REQUEST record to start a request.

웹 서버는 FCGI_BEGIN_REQUEST 레코드를 보내 요청을 시작합니다.

The contentData component of a FCGI_BEGIN_REQUEST record has the form:

FCGI_BEGIN_REQUEST 레코드의 contentData 구성 요소 형식은 다음과 같습니다.

        typedef struct {
            unsigned char roleB1;
            unsigned char roleB0;
            unsigned char flags;
            unsigned char reserved[5];
        } FCGI_BeginRequestBody;

The role component sets the role the Web server expects the application to play. The currently-defined roles are:

역할 구성 요소는 웹 서버에서 응용 프로그램이 수행할 것으로 기대하는 역할을 설정합니다. 현재 정의된 역할은 다음과 같습니다.

FCGI_RESPONDER
FCGI_AUTHORIZER
FCGI_FILTER

Roles are described in more detail in Section 6 below.

역할은 아래 섹션 6에 자세히 설명되어 있습니다.

The flags component contains a bit that controls connection shutdown:

flags 구성 요소에는 연결 종료를 제어하는 비트가 포함되어 있습니다.

flags & FCGI_KEEP_CONN: If zero, the application closes the connection after responding to this request. If not zero, the application does not close the connection after responding to this request; the Web server retains responsibility for the connection.

flags & FCGI_KEEP_CONN: 0이면 응용 프로그램이 이 요청에 응답한 후 연결을 닫습니다. 0이 아니면 애플리케이션은 이 요청에 응답한 후 연결을 닫지 않습니다. 웹 서버는 연결에 대한 책임이 있습니다.

### 5.2 Name-Value Pair Stream: FCGI_PARAMS
### 5.2 네임-벨류 쌍 스트림 : FCGI_PARAMS

FCGI_PARAMS is a stream record type used in sending name-value pairs from the Web server to the application. The name-value pairs are sent down the stream one after the other, in no specified order.

FCGI_PARAMS는 웹 서버에서 응용 프로그램으로 이름-값 쌍을 보내는 데 사용되는 스트림 레코드 유형입니다. 이름-값 쌍은 지정된 순서 없이 차례로 스트림 아래로 전송됩니다.

### 5.3 Byte Streams: FCGI_STDIN, FCGI_DATA, FCGI_STDOUT, FCGI_STDERR
### 5.3 바이트 스트림 : FCGI_STDIN, FCGI_DATA, FCGI_STDOUT, FCGI_STDERR

FCGI_STDIN is a stream record type used in sending arbitrary data from the Web server to the application. FCGI_DATA is a second stream record type used to send additional data to the application.

FCGI_STDIN은 웹 서버에서 응용 프로그램으로 임의의 데이터를 보내는 데 사용되는 스트림 레코드 유형입니다. FCGI_DATA는 응용 프로그램에 추가 데이터를 보내는 데 사용되는 두 번째 스트림 레코드 유형입니다.

FCGI_STDOUT and FCGI_STDERR are stream record types for sending arbitrary data and error data respectively from the application to the Web server.

FCGI_STDOUT 및 FCGI_STDERR은 각각 임의의 데이터와 오류 데이터를 응용 프로그램에서 웹 서버로 보내기 위한 스트림 레코드 유형입니다.

### 5.4 FCGI_ABORT_REQUEST

The Web server sends a FCGI_ABORT_REQUEST record to abort a request. After receiving {FCGI_ABORT_REQUEST, R}, the application responds as soon as possible with {FCGI_END_REQUEST, R, {FCGI_REQUEST_COMPLETE, appStatus}}. This is truly a response from the application, not a low-level acknowledgement from the FastCGI library.

웹 서버는 요청을 중단하기 위해 FCGI_ABORT_REQUEST 레코드를 보냅니다. {FCGI_ABORT_REQUEST, R}를 수신한 후 애플리케이션은 가능한 한 빨리 {FCGI_END_REQUEST, R, {FCGI_REQUEST_COMPLETE, appStatus}}로 응답합니다. 이것은 FastCGI 라이브러리의 저수준 승인이 아니라 애플리케이션의 진정한 응답입니다.

A Web server aborts a FastCGI request when an HTTP client closes its transport connection while the FastCGI request is running on behalf of that client. The situation may seem unlikely; most FastCGI requests will have short response times, with the Web server providing output buffering if the client is slow. But the FastCGI application may be delayed communicating with another system, or performing a server push.

웹 서버는 HTTP 클라이언트가 해당 클라이언트를 대신하여 FastCGI 요청이 실행되는 동안 전송 연결을 닫으면 FastCGI 요청을 중단합니다. 상황이 가능성이 없어 보일 수 있습니다. 대부분의 FastCGI 요청은 응답 시간이 짧으며 클라이언트가 느린 경우 웹 서버가 출력 버퍼링을 제공합니다. 그러나 FastCGI 응용 프로그램은 다른 시스템과의 통신이나 서버 푸시 수행이 지연될 수 있습니다.

When a Web server is not multiplexing requests over a transport connection, the Web server can abort a request by closing the request's transport connection. But with multiplexed requests, closing the transport connection has the unfortunate effect of aborting all the requests on the connection.

웹 서버가 전송 연결을 통해 요청을 다중화하지 않는 경우 웹 서버는 요청의 전송 연결을 닫아 요청을 중단할 수 있습니다. 그러나 다중화된 요청의 경우 전송 연결을 닫으면 연결에 대한 모든 요청이 중단되는 불행한 효과가 있습니다.

### 5.5 FCGI_END_REQUEST

The application sends a FCGI_END_REQUEST record to terminate a request, either because the application has processed the request or because the application has rejected the request.

애플리케이션이 요청을 처리했거나 애플리케이션이 요청을 거부했기 때문에 애플리케이션은 FCGI_END_REQUEST 레코드를 전송하여 요청을 종료합니다.

The contentData component of a FCGI_END_REQUEST record has the form:

FCGI_END_REQUEST 레코드의 contentData 구성 요소 형식은 다음과 같습니다.

        typedef struct {
            unsigned char appStatusB3;
            unsigned char appStatusB2;
            unsigned char appStatusB1;
            unsigned char appStatusB0;
            unsigned char protocolStatus;
            unsigned char reserved[3];
        } FCGI_EndRequestBody;

The appStatus component is an application-level status code. Each role documents its usage of appStatus.

appStatus 구성 요소는 응용 프로그램 수준 상태 코드입니다. 각 역할은 appStatus의 사용을 문서화합니다.

The protocolStatus component is a protocol-level status code; the possible protocolStatus values are:

protocolStatus 구성 요소는 프로토콜 수준 상태 코드입니다. 가능한 protocolStatus 값은 다음과 같습니다.

FCGI_REQUEST_COMPLETE: normal end of request.

FCGI_REQUEST_COMPLETE: 정상적인 요청 종료.

FCGI_CANT_MPX_CONN: rejecting a new request. This happens when a Web server sends concurrent requests over one connection to an application that is designed to process one request at a time per connection.

FCGI_CANT_MPX_CONN: 새 요청을 거부합니다. 이것은 웹 서버가 연결당 한 번에 하나의 요청을 처리하도록 설계된 응용 프로그램에 하나의 연결을 통해 동시 요청을 보낼 때 발생합니다.

FCGI_OVERLOADED: rejecting a new request. This happens when the application runs out of some resource, e.g. database connections.

FCGI_OVERLOADED: 새 요청을 거부합니다. 이것은 응용 프로그램에서 일부 리소스가 부족할 때 발생합니다. 데이터베이스 연결.

FCGI_UNKNOWN_ROLE: rejecting a new request. This happens when the Web server has specified a role that is unknown to the application.

FCGI_UNKNOWN_ROLE: 새 요청을 거부합니다. 이것은 웹 서버가 응용 프로그램에 알려지지 않은 역할을 지정했을 때 발생합니다.
