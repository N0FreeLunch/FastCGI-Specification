A FastCGI application exits with zero status to indicate that it terminated on purpose, e.g. in order to perform a crude form of garbage collection. A FastCGI application that exits with nonzero status is assumed to have crashed. How a Web server or other application manager responds to applications that exit with zero or nonzero status is outside the scope of this specification.

FastCGI 응용 프로그램은 의도적으로 종료되었음을 나타내기 위해 0 상태로 종료됩니다. 조잡한 형태의 가비지 수집을 수행하기 위해. 0이 아닌 상태로 종료되는 FastCGI 응용 프로그램은 충돌한 것으로 간주됩니다. 웹 서버나 다른 응용 프로그램 관리자가 0 또는 0이 아닌 상태로 종료되는 응용 프로그램에 응답하는 방법은 이 사양의 범위를 벗어납니다.

A Web server can request that a FastCGI application exit by sending it SIGTERM. If the application ignores SIGTERM the Web server can resort to SIGKILL.

웹 서버는 SIGTERM을 전송하여 FastCGI 응용 프로그램 종료를 요청할 수 있습니다. 응용 프로그램이 SIGTERM을 무시하면 웹 서버는 SIGKILL에 의존할 수 있습니다.

FastCGI applications report application-level errors with the FCGI_STDERR stream and the appStatus component of the FCGI_END_REQUEST record. In many cases an error will be reported directly to the user via the FCGI_STDOUT stream.

FastCGI 응용 프로그램은 FCGI_STDERR 스트림과 FCGI_END_REQUEST 레코드의 appStatus 구성 요소를 사용하여 응용 프로그램 수준 오류를 보고합니다. 많은 경우에 오류는 FCGI_STDOUT 스트림을 통해 사용자에게 직접 보고됩니다.

On Unix, applications report lower-level errors, including FastCGI protocol errors and syntax errors in FastCGI environment variables, to syslog. Depending upon the severity of the error, the application may either continue or exit with nonzero status.

Unix에서 응용 프로그램은 FastCGI 환경 변수의 FastCGI 프로토콜 오류 및 구문 오류를 비롯한 하위 수준 오류를 syslog에 보고합니다. 오류의 심각도에 따라 응용 프로그램이 계속되거나 0이 아닌 상태로 종료될 수 있습니다.
