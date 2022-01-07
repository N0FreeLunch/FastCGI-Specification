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
