A Filter FastCGI application receives all the information associated with an HTTP request, plus an extra stream of data from a file stored on the Web server, and generates a "filtered" version of the data stream as an HTTP response.

Filter FastCGI 응용 프로그램은 HTTP 요청과 관련된 모든 정보와 웹 서버에 저장된 파일의 추가 데이터 스트림을 수신하고 데이터 스트림의 "필터링된" 버전을 HTTP 응답으로 생성합니다.

A Filter is similar in functionality to a Responder that takes a data file as a parameter. The difference is that with a Filter, both the data file and the Filter itself can be access controlled using the Web server's access control mechanisms, while a Responder that takes the name of a data file as a parameter must perform its own access control checks on the data file.

필터는 데이터 파일을 매개변수로 사용하는 응답자와 기능면에서 유사합니다. 차이점은 필터를 사용하면 데이터 파일과 필터 자체가 웹 서버의 액세스 제어 메커니즘을 사용하여 액세스 제어될 수 있지만 데이터 파일의 이름을 매개변수로 사용하는 응답자는 자체 액세스 제어 검사를 수행해야 한다는 것입니다. 데이터 파일.

The steps taken by a Filter are similar to those of a Responder. The server presents the Filter with environment variables first, then standard input (normally form POST data), finally the data file input:

필터가 취하는 단계는 응답자의 단계와 유사합니다. 서버는 먼저 환경 변수가 있는 필터를 제공한 다음 표준 입력(일반적으로 POST 데이터 형식), 마지막으로 데이터 파일 입력을 제공합니다.

Like a Responder, the Filter application receives name-value pairs from the Web server over FCGI_PARAMS. Filter applications receive two Filter-specific variables: FCGI_DATA_LAST_MOD and FCGI_DATA_LENGTH.

응답자와 마찬가지로 필터 응용 프로그램은 FCGI_PARAMS를 통해 웹 서버에서 이름-값 쌍을 받습니다. 필터 애플리케이션은 두 개의 필터 관련 변수인 FCGI_DATA_LAST_MOD 및 FCGI_DATA_LENGTH를 수신합니다.

Next the Filter application receives CGI/1.1 stdin data from the Web server over FCGI_STDIN. The application receives at most CONTENT_LENGTH bytes from this stream before receiving the end-of-stream indication. (The application receives less than CONTENT_LENGTH bytes only if the HTTP client fails to provide them, e.g. because the client crashed.)

그런 다음 필터 응용 프로그램은 FCGI_STDIN을 통해 웹 서버에서 CGI/1.1 stdin 데이터를 수신합니다. 애플리케이션은 스트림 끝 표시를 수신하기 전에 이 스트림에서 최대 CONTENT_LENGTH바이트를 수신합니다. (예: 클라이언트 충돌로 인해 HTTP 클라이언트가 제공하지 못한 경우에만 애플리케이션이 CONTENT_LENGTH바이트 미만을 수신합니다.)

Next the Filter application receives the file data from the Web server over FCGI_DATA. This file's last modification time (expressed as an integer number of seconds since the epoch January 1, 1970 UTC) is FCGI_DATA_LAST_MOD; the application may consult this variable and respond from a cache without reading the file data. The application reads at most FCGI_DATA_LENGTH bytes from this stream before receiving the end-of-stream indication.

그런 다음 필터 응용 프로그램은 FCGI_DATA를 통해 웹 서버에서 파일 데이터를 수신합니다. 이 파일의 마지막 수정 시간(1970년 1월 1일 UTC 이후 정수 초로 표시)은 FCGI_DATA_LAST_MOD입니다. 응용 프로그램은 이 변수를 참조하고 파일 데이터를 읽지 않고 캐시에서 응답할 수 있습니다. 애플리케이션은 스트림 끝 표시를 수신하기 전에 이 스트림에서 최대 FCGI_DATA_LENGTH바이트를 읽습니다.

The Filter application sends CGI/1.1 stdout data to the Web server over FCGI_STDOUT, and CGI/1.1 stderr data over FCGI_STDERR. The application sends these concurrently, not one after the other. The application must wait to finish reading FCGI_STDIN before it begins writing FCGI_STDOUT and FCGI_STDERR, but it needn't finish reading from FCGI_DATA before it begins writing these two streams.

필터 응용 프로그램은 FCGI_STDOUT을 통해 웹 서버에 CGI/1.1 stdout 데이터를 보내고 FCGI_STDERR을 통해 CGI/1.1 stderr 데이터를 보냅니다. 응용 프로그램은 이러한 항목을 차례로 보내지 않고 동시에 보냅니다. 애플리케이션은 FCGI_STDOUT 및 FCGI_STDERR 쓰기를 시작하기 전에 FCGI_STDIN 읽기를 완료할 때까지 기다려야 하지만 이 두 스트림 쓰기를 시작하기 전에 FCGI_DATA에서 읽기를 마칠 필요는 없습니다.

After sending all its stdout and stderr data, the application sends a FCGI_END_REQUEST record. The application sets the protocolStatus component to FCGI_REQUEST_COMPLETE and the appStatus component to the status code that a similar CGI program would have returned via the exit system call.

모든 stdout 및 stderr 데이터를 보낸 후 애플리케이션은 FCGI_END_REQUEST 레코드를 보냅니다. 응용 프로그램은 protocolStatus 구성 요소를 FCGI_REQUEST_COMPLETE로 설정하고 appStatus 구성 요소를 유사한 CGI 프로그램이 종료 시스템 호출을 통해 반환한 상태 코드로 설정합니다.

A Filter should compare the number of bytes received on FCGI_STDIN with CONTENT_LENGTH and on FCGI_DATA with FCGI_DATA_LENGTH. If the numbers don't match and the Filter is a query, the Filter response should provide an indication that data is missing. If the numbers don't match and the Filter is an update, the Filter should abort the update.

필터는 FCGI_STDIN에서 수신된 바이트 수를 CONTENT_LENGTH와 비교하고 FCGI_DATA에서 수신한 바이트 수를 FCGI_DATA_LENGTH와 비교해야 합니다. 숫자가 일치하지 않고 필터가 쿼리인 경우 필터 응답은 데이터가 누락되었다는 표시를 제공해야 합니다. 숫자가 일치하지 않고 필터가 업데이트인 경우 필터는 업데이트를 중단해야 합니다.
