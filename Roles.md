### Role Protocols

Role protocols only include records with application record types. They transfer essentially all data using streams.

역할 프로토콜에는 애플리케이션 레코드 유형의 레코드만 포함됩니다. 그들은 본질적으로 스트림을 사용하여 모든 데이터를 전송합니다.

To make the protocols reliable and to simplify application programming, role protocols are designed to use nearly sequential marshalling. In a protocol with strictly sequential marshalling, the application receives its first input, then its second, etc. until it has received them all. Similarly, the application sends its first output, then its second, etc. until it has sent them all. Inputs are not interleaved with each other, and outputs are not interleaved with each other.

프로토콜을 안정적으로 만들고 응용 프로그램 프로그래밍을 단순화하기 위해 역할 프로토콜은 거의 순차 마샬링을 사용하도록 설계되었습니다. 엄격한 순차 마샬링이 있는 프로토콜에서 애플리케이션은 첫 번째 입력을 받은 다음 두 번째 등을 모두 수신할 때까지 받습니다. 마찬가지로 응용 프로그램은 첫 번째 출력을 보낸 다음 두 번째 출력 등을 모두 보낼 때까지 보냅니다. 입력은 서로 인터리브되지 않고 출력은 서로 인터리브되지 않습니다.

The sequential marshalling rule is too restrictive for some FastCGI roles, because CGI programs can write to both stdout and stderr without timing restrictions. So role protocols that use both FCGI_STDOUT and FCGI_STDERR allow these two streams to be interleaved.

순차 마샬링 규칙은 일부 FastCGI 역할에 대해 너무 제한적입니다. CGI 프로그램은 타이밍 제한 없이 stdout 및 stderr 모두에 쓸 수 있기 때문입니다. 따라서 FCGI_STDOUT 및 FCGI_STDERR을 모두 사용하는 역할 프로토콜은 이 두 스트림이 인터리브되도록 허용합니다.

All role protocols use the FCGI_STDERR stream just the way stderr is used in conventional applications programming: to report application-level errors in an intelligible way. Use of the FCGI_STDERR stream is always optional. If an application has no errors to report, it sends either no FCGI_STDERR records or one zero-length FCGI_STDERR record.

모든 역할 프로토콜은 기존 응용 프로그램 프로그래밍에서 stderr이 사용되는 방식으로 FCGI_STDERR 스트림을 사용하여 응용 프로그램 수준 오류를 이해할 수 있는 방식으로 보고합니다. FCGI_STDERR 스트림의 사용은 항상 선택 사항입니다. 애플리케이션에 보고할 오류가 없으면 FCGI_STDERR 레코드를 전송하지 않거나 길이가 0인 FCGI_STDERR 레코드를 하나 보냅니다.

When a role protocol calls for transmitting a stream other than FCGI_STDERR, at least one record of the stream type is always transmitted, even if the stream is empty.

역할 프로토콜이 FCGI_STDERR 이외의 스트림 전송을 요청하는 경우 스트림이 비어 있더라도 스트림 유형의 적어도 하나의 레코드가 항상 전송됩니다.

Again in the interests of reliable protocols and simplified application programming, role protocols are designed to be nearly request-response. In a truly request-response protocol, the application receives all of its input records before sending its first output record. Request-response protocols don't allow pipelining.

신뢰할 수 있는 프로토콜과 단순화된 응용 프로그램 프로그래밍을 위해 역할 프로토콜은 거의 요청-응답으로 설계되었습니다. 진정한 요청-응답 프로토콜에서 애플리케이션은 첫 번째 출력 레코드를 보내기 전에 모든 입력 레코드를 수신합니다. 요청-응답 프로토콜은 파이프라이닝을 허용하지 않습니다.

The request-response rule is too restrictive for some FastCGI roles; after all, CGI programs aren't restricted to read all of stdin before starting to write stdout. So some role protocols allow that specific possibility. First the application receives all of its inputs except for a final stream input. As the application begins to receive the final stream input, it can begin writing its output.

요청-응답 규칙은 일부 FastCGI 역할에 대해 너무 제한적입니다. 결국, CGI 프로그램은 stdout 쓰기를 시작하기 전에 모든 stdin을 읽도록 제한되지 않습니다. 따라서 일부 역할 프로토콜은 특정 가능성을 허용합니다. 먼저 애플리케이션은 최종 스트림 입력을 제외한 모든 입력을 수신합니다. 애플리케이션이 최종 스트림 입력을 수신하기 시작하면 출력 쓰기를 시작할 수 있습니다.

When a role protocol uses FCGI_PARAMS to transmit textual values, such as the values that CGI programs obtain from environment variables, the length of the value does not include the terminating null byte, and the value itself does not include a null byte. An application that needs to provide environ(7) format name-value pairs must insert an equal sign between the name and value and append a null byte after the value.

역할 프로토콜이 FCGI_PARAMS를 사용하여 CGI 프로그램이 환경 변수에서 얻은 값과 같은 텍스트 값을 전송하는 경우 값의 길이에는 종료 널 바이트가 포함되지 않으며 값 자체에는 널 바이트가 포함되지 않습니다. Environ(7) 형식 이름-값 쌍을 제공해야 하는 애플리케이션은 이름과 값 사이에 등호를 삽입하고 값 뒤에 널 바이트를 추가해야 합니다.

Role protocols do not support the non-parsed header feature of CGI. FastCGI applications set response status using the Status and Location CGI headers.

역할 프로토콜은 CGI의 구문 분석되지 않은 헤더 기능을 지원하지 않습니다. FastCGI 응용 프로그램은 상태 및 위치 CGI 헤더를 사용하여 응답 상태를 설정합니다.


### Responder

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


### Authorizer

An Authorizer FastCGI application receives all the information associated with an HTTP request and generates an authorized/unauthorized decision. In case of an authorized decision the Authorizer can also associate name-value pairs with the HTTP request; when giving an unauthorized decision the Authorizer sends a complete response to the HTTP client.

Authorizer FastCGI 애플리케이션은 HTTP 요청과 관련된 모든 정보를 수신하고 승인/비승인 결정을 생성합니다. 승인된 결정의 경우 Authorizer는 이름-값 쌍을 HTTP 요청과 연결할 수도 있습니다. 승인되지 않은 결정을 내릴 때 Authorizer는 HTTP 클라이언트에 완전한 응답을 보냅니다.

Since CGI/1.1 defines a perfectly good way to represent the information associated with an HTTP request, Authorizers use the same representation:

CGI/1.1은 HTTP 요청과 관련된 정보를 표현하는 완벽하게 좋은 방법을 정의하므로 Authorizers는 동일한 표현을 사용합니다.

The Authorizer application receives HTTP request information from the Web server on the FCGI_PARAMS stream, in the same format as a Responder. The Web server does not send CONTENT_LENGTH, PATH_INFO, PATH_TRANSLATED, and SCRIPT_NAME headers.

Authorizer 애플리케이션은 응답자와 동일한 형식으로 FCGI_PARAMS 스트림의 웹 서버로부터 HTTP 요청 정보를 수신합니다. 웹 서버는 CONTENT_LENGTH, PATH_INFO, PATH_TRANSLATED 및 SCRIPT_NAME 헤더를 보내지 않습니다.

The Authorizer application sends stdout and stderr data in the same manner as a Responder. The CGI/1.1 response status specifies the disposition of the request. If the application sends status 200 (OK), the Web server allows access. Depending upon its configuration the Web server may proceed with other access checks, including requests to other Authorizers.

Authorizer 애플리케이션은 응답자와 동일한 방식으로 stdout 및 stderr 데이터를 보냅니다. CGI/1.1 응답 상태는 요청 처리를 지정합니다. 응용 프로그램이 상태 200(OK)을 보내면 웹 서버가 액세스를 허용합니다. 구성에 따라 웹 서버는 다른 권한 부여자에 대한 요청을 포함하여 다른 액세스 검사를 진행할 수 있습니다.

An Authorizer application's 200 response may include headers whose names are prefixed with Variable-. These headers communicate name-value pairs from the application to the Web server. For instance, the response header

Authorizer 애플리케이션의 200 응답에는 Variable-가 접두사로 붙은 이름의 헤더가 포함될 수 있습니다. 이러한 헤더는 응용 프로그램에서 웹 서버로 이름-값 쌍을 전달합니다. 예를 들어 응답 헤더

        Variable-AUTH_METHOD: database lookup
        
transmits the value "database lookup" with name AUTH-METHOD. The server associates such name-value pairs with the HTTP request and includes them in subsequent CGI or FastCGI requests performed in processing the HTTP request. When the application gives a 200 response, the server ignores response headers whose names aren't prefixed with Variable- prefix, and ignores any response content.

이름이 AUTH-METHOD인 "데이터베이스 조회" 값을 전송합니다. 서버는 이러한 이름-값 쌍을 HTTP 요청과 연결하고 HTTP 요청을 처리할 때 수행되는 후속 CGI 또는 FastCGI 요청에 포함합니다. 응용 프로그램이 200 응답을 제공하면 서버는 이름에 Variable- 접두사가 붙지 않은 응답 헤더를 무시하고 응답 내용을 무시합니다.

For Authorizer response status values other than "200" (OK), the Web server denies access and sends the response status, headers, and content back to the HTTP client.

"200"(OK) 이외의 Authorizer 응답 상태 값의 경우 웹 서버는 액세스를 거부하고 응답 상태, 헤더 및 콘텐츠를 HTTP 클라이언트로 다시 보냅니다.

### Filter

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
