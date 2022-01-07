A Responder FastCGI application has the same purpose as a CGI/1.1 program: It receives all the information associated with an HTTP request and generates an HTTP response.

응답자 FastCGI 응용 프로그램은 CGI/1.1 프로그램과 동일한 목적을 가지고 있습니다. HTTP 요청과 관련된 모든 정보를 수신하고 HTTP 응답을 생성합니다.

It suffices to explain how each element of CGI/1.1 is emulated by a Responder:

응답자가 CGI/1.1의 각 요소를 에뮬레이트하는 방법을 설명하는 것으로 충분합니다.

The Responder application receives CGI/1.1 environment variables from the Web server over FCGI_PARAMS.

응답자 응용 프로그램은 FCGI_PARAMS를 통해 웹 서버에서 CGI/1.1 환경 변수를 받습니다.

Next the Responder application receives CGI/1.1 stdin data from the Web server over FCGI_STDIN. The application receives at most CONTENT_LENGTH bytes from this stream before receiving the end-of-stream indication. (The application receives less than CONTENT_LENGTH bytes only if the HTTP client fails to provide them, e.g. because the client crashed.)

다음으로 응답자 응용 프로그램은 FCGI_STDIN을 통해 웹 서버에서 CGI/1.1 stdin 데이터를 수신합니다. 애플리케이션은 스트림 끝 표시를 수신하기 전에 이 스트림에서 최대 CONTENT_LENGTH바이트를 수신합니다. (예: 클라이언트 충돌로 인해 HTTP 클라이언트가 제공하지 못한 경우에만 애플리케이션이 CONTENT_LENGTH바이트 미만을 수신합니다.)

The Responder application sends CGI/1.1 stdout data to the Web server over FCGI_STDOUT, and CGI/1.1 stderr data over FCGI_STDERR. The application sends these concurrently, not one after the other. The application must wait to finish reading FCGI_PARAMS before it begins writing FCGI_STDOUT and FCGI_STDERR, but it needn't finish reading from FCGI_STDIN before it begins writing these two streams.

응답자 응용 프로그램은 FCGI_STDOUT을 통해 웹 서버에 CGI/1.1 stdout 데이터를 보내고 FCGI_STDERR을 통해 CGI/1.1 stderr 데이터를 보냅니다. 응용 프로그램은 이러한 항목을 차례로 보내지 않고 동시에 보냅니다. 애플리케이션은 FCGI_STDOUT 및 FCGI_STDERR 쓰기를 시작하기 전에 FCGI_PARAMS 읽기를 완료할 때까지 기다려야 하지만 이 두 스트림 쓰기를 시작하기 전에 FCGI_STDIN에서 읽기를 마칠 필요는 없습니다.

After sending all its stdout and stderr data, the Responder application sends a FCGI_END_REQUEST record. The application sets the protocolStatus component to FCGI_REQUEST_COMPLETE and the appStatus component to the status code that the CGI program would have returned via the exit system call.

모든 stdout 및 stderr 데이터를 보낸 후 응답자 애플리케이션은 FCGI_END_REQUEST 레코드를 보냅니다. 응용 프로그램은 protocolStatus 구성 요소를 FCGI_REQUEST_COMPLETE로 설정하고 appStatus 구성 요소를 CGI 프로그램이 종료 시스템 호출을 통해 반환했을 상태 코드로 설정합니다.

A Responder performing an update, e.g. implementing a POST method, should compare the number of bytes received on FCGI_STDIN with CONTENT_LENGTH and abort the update if the two numbers are not equal.

업데이트를 수행하는 응답자, 예: POST 메서드를 구현하는 경우 FCGI_STDIN에서 수신된 바이트 수를 CONTENT_LENGTH와 비교하고 두 숫자가 같지 않으면 업데이트를 중단해야 합니다.
