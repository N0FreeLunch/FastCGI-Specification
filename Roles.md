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
