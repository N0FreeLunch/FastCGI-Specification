2.1 Argument list
By default the Web server creates an argument list containing a single element, the name of the application, taken to be the last component of the executable's path name. The Web server may provide a way to specify a different application name, or a more elaborate argument list.
Note that the file executed by the Web server might be an interpreter file (a text file that starts with the characters #!), in which case the application's argument list is constructed as described in the execve manpage.

기본적으로 웹 서버는 실행 파일 경로 이름의 마지막 구성 요소로 간주되는 응용 프로그램 이름이라는 단일 요소를 포함하는 인수 목록을 만듭니다. 웹 서버는 다른 응용 프로그램 이름이나 보다 정교한 인수 목록을 지정하는 방법을 제공할 수 있습니다.

웹 서버가 실행하는 파일은 인터프리터 파일(문자 #!로 시작하는 텍스트 파일)일 수 있으며, 이 경우 응용 프로그램의 인수 목록은 execve 맨페이지에 설명된 대로 구성됩니다.

2.2 File descriptors
The Web server leaves a single file descriptor, FCGI_LISTENSOCK_FILENO, open when the application begins execution. This descriptor refers to a listening socket created by the Web server.
FCGI_LISTENSOCK_FILENO equals STDIN_FILENO. The standard descriptors STDOUT_FILENO and STDERR_FILENO are closed when the application begins execution. A reliable method for an application to determine whether it was invoked using CGI or FastCGI is to call getpeername(FCGI_LISTENSOCK_FILENO), which returns -1 with errno set to ENOTCONN for a FastCGI application.

웹 서버는 응용 프로그램이 실행을 시작할 때 열린 단일 파일 설명자 FCGI_LISTENSOCK_FILENO를 남겨둡니다. 이 디스크립터는 웹 서버에 의해 생성된 수신 소켓을 참조합니다.

FCGI_LISTENSOCK_FILENO는 STDIN_FILENO와 같습니다. 표준 설명자 STDOUT_FILENO 및 STDERR_FILENO는 응용 프로그램이 실행을 시작할 때 닫힙니다. 응용 프로그램이 CGI 또는 FastCGI를 사용하여 호출되었는지 여부를 결정하는 신뢰할 수 있는 방법은 getpeername(FCGI_LISTENSOCK_FILENO)을 호출하는 것입니다. 이는 FastCGI 응용 프로그램에 대해 errno가 ENOTCONN으로 설정된 상태에서 -1을 반환합니다.

The Web server's choice of reliable transport, Unix stream pipes (AF_UNIX) or TCP/IP (AF_INET), is implicit in the internal state of the FCGI_LISTENSOCK_FILENO socket.

웹 서버가 선택하는 안정적인 전송, Unix 스트림 파이프(AF_UNIX) 또는 TCP/IP(AF_INET)는 FCGI_LISTENSOCK_FILENO 소켓의 내부 상태에 암시적입니다.

2.3 Environment variables
The Web server may use environment variables to pass parameters to the application. This specification defines one such variable, FCGI_WEB_SERVER_ADDRS; we expect more to be defined as the specification evolves. The Web server may provide a way to bind other environment variables, such as the PATH variable.

웹 서버는 환경 변수를 사용하여 매개변수를 애플리케이션에 전달할 수 있습니다. 이 사양은 그러한 변수 중 하나인 FCGI_WEB_SERVER_ADDRS를 정의합니다. 사양이 발전함에 따라 더 많은 것이 정의될 것으로 기대합니다. 웹 서버는 PATH 변수와 같은 다른 환경 변수를 바인딩하는 방법을 제공할 수 있습니다.

2.4 Other state
The Web server may provide a way to specify other components of an application's initial process state, such as the priority, user ID, group ID, root directory, and working directory of the process.

웹 서버는 우선 순위, 사용자 ID, 그룹 ID, 루트 디렉터리 및 프로세스의 작업 디렉터리와 같은 응용 프로그램의 초기 프로세스 상태의 다른 구성 요소를 지정하는 방법을 제공할 수 있습니다.
