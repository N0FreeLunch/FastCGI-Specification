FastCGI is an open extension to CGI that provides high performance for all Internet applications without the penalties of Web server APIs.

FastCGI는 웹 서버 API의 불이익 없이 모든 인터넷 응용 프로그램에 고성능을 제공하는 CGI의 개방형 확장입니다.

This specification has narrow goal: to specify, from an application perspective, the interface between a FastCGI application and a Web server that supports FastCGI. Many Web server features related to FastCGI, e.g. application management facilities, have nothing to do with the application to Web server interface, and are not described here.

이 사양은 좁은 목표를 가지고 있습니다. 응용 프로그램 관점에서 FastCGI 응용 프로그램과 FastCGI를 지원하는 웹 서버 간의 인터페이스를 지정하는 것입니다. FastCGI와 관련된 많은 웹 서버 기능(예: 응용 프로그램 관리 기능)은 웹 서버 인터페이스에 대한 응용 프로그램과 아무 관련이 없으므로 여기에서는 설명하지 않습니다.

This specification is for Unix (more precisely, for POSIX systems that support Berkeley Sockets). The bulk of the specification is a simple communications protocol that is independent of byte ordering and will extend to other systems.

이 사양은 Unix용입니다(더 정확하게는 Berkeley 소켓을 지원하는 POSIX 시스템용). 사양의 대부분은 바이트 순서와 무관하고 다른 시스템으로 확장되는 간단한 통신 프로토콜입니다.

We'll introduce FastCGI by comparing it with conventional Unix implementations of CGI/1.1. FastCGI is designed to support long-lived application processes, i.e. application servers. That's a major difference compared with conventional Unix implementations of CGI/1.1, which construct an application process, use it respond to one request, and have it exit.

FastCGI를 CGI/1.1의 기존 Unix 구현과 비교하여 소개합니다. FastCGI는 수명이 긴 응용 프로그램 프로세스, 즉 응용 프로그램 서버 를 지원하도록 설계되었습니다 . 이는 애플리케이션 프로세스를 구성하고 하나의 요청에 응답하고 종료하도록 하는 CGI/1.1의 기존 Unix 구현과 비교할 때 큰 차이점입니다.

The initial state of a FastCGI process is more spartan than the initial state of a CGI/1.1 process, because the FastCGI process doesn't begin life connected to anything. It doesn't have the conventional open files stdin, stdout, and stderr, and it doesn't receive much information through environment variables. The key piece of initial state in a FastCGI process is a listening socket, through which it accepts connections from a Web server.

FastCGI 프로세스의 초기 상태는 CGI/1.1 프로세스의 초기 상태보다 스파르타적입니다. FastCGI 프로세스는 어떤 것과도 연결된 상태로 시작하지 않기 때문입니다. 기존의 열린 파일 stdin , stdout 및 stderr 이 없으며 환경 변수를 통해 많은 정보를 수신하지 않습니다. FastCGI 프로세스에서 초기 상태의 핵심 부분은 웹 서버에서 연결을 수락하는 수신 소켓입니다.

After a FastCGI process accepts a connection on its listening socket, the process executes a simple protocol to receive and send data. The protocol serves two purposes. First, the protocol multiplexes a single transport connection between several independent FastCGI requests. This supports applications that are able to process concurrent requests using event-driven or multi-threaded programming techniques. Second, within each request the protocol provides several independent data streams in each direction. This way, for instance, both stdout and stderr data pass over a single transport connection from the application to the Web server, rather than requiring separate pipes as with CGI/1.1.

FastCGI 프로세스가 수신 소켓에서 연결을 수락한 후 프로세스는 데이터를 수신 및 전송하기 위해 간단한 프로토콜을 실행합니다. 프로토콜은 두 가지 용도로 사용됩니다. 첫째, 프로토콜은 여러 개의 독립적인 FastCGI 요청 간에 단일 전송 연결을 다중화합니다. 이는 이벤트 기반 또는 다중 스레드 프로그래밍 기술을 사용하여 동시 요청을 처리할 수 있는 애플리케이션을 지원합니다. 둘째, 각 요청 내에서 프로토콜은 각 방향으로 여러 개의 독립적인 데이터 스트림을 제공합니다. 예를 들어 이러한 방식으로 stdout 및 stderr 데이터는 CGI/1.1에서와 같이 별도의 파이프가 필요하지 않고 단일 전송 연결을 통해 응용 프로그램에서 웹 서버로 전달됩니다.

A FastCGI application plays one of several well-defined roles. The most familiar is the Responder role, in which the application receives all the information associated with an HTTP request and generates an HTTP response; that's the role CGI/1.1 programs play. A second role is Authorizer, in which the application receives all the information associated with an HTTP request and generates an authorized/unauthorized decision. A third role is Filter, in which the application receives all the information associated with an HTTP request, plus an extra stream of data from a file stored on the Web server, and generates a "filtered" version of the data stream as an HTTP response. The framework is extensible so that more FastCGI can be defined later.

FastCGI 응용 프로그램은 잘 정의된 여러 역할 중 하나를 수행합니다 . 가장 친숙한 역할은 애플리케이션이 HTTP 요청과 관련된 모든 정보를 수신하고 HTTP 응답을 생성하는 응답자 역할입니다. 그것이 CGI/1.1 프로그램이 하는 역할입니다. 두번째 역할은 인증 자 애플리케이션은 HTTP 요청과 관련된 모든 정보를 수신하고,인가 / 비인가 결정을 생성하는. 세 번째 역할은 필터입니다., 여기서 응용 프로그램은 HTTP 요청과 관련된 모든 정보와 웹 서버에 저장된 파일의 추가 데이터 스트림을 수신하고 데이터 스트림의 "필터링된" 버전을 HTTP 응답으로 생성합니다. 프레임워크는 나중에 더 많은 FastCGI를 정의할 수 있도록 확장 가능합니다.

In the remainder of this specification the terms "FastCGI application," "application process," or "application server" are abbreviated to "application" whenever that won't cause confusion.

이 사양의 나머지 부분에서 "FastCGI 응용 프로그램", "응용 프로그램 프로세스" 또는 "응용 프로그램 서버"라는 용어는 혼동을 일으키지 않을 때마다 "응용 프로그램"으로 축약됩니다.
