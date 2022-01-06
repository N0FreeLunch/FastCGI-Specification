## 3. Protocol Basics

### 3.1 Notation

We use C language notation to define protocol message formats. All structure elements are defined in terms of the unsigned char type, and are arranged so that an ISO C compiler lays them out in the obvious manner, with no padding. The first byte defined in the structure is transmitted first, the second byte second, etc.

프로토콜 메시지 형식을 정의하기 위해 C 언어 표기법을 사용합니다. 모든 구조 요소는 unsigned char 유형으로 정의되며 ISO C 컴파일러가 패딩 없이 명확한 방식으로 배치하도록 배열됩니다. 구조에 정의된 첫 번째 바이트가 먼저 전송되고 두 번째 바이트가 두 번째로 전송되는 식입니다.

We use two conventions to abbreviate our definitions.

정의를 축약하기 위해 두 가지 규칙을 사용합니다.

First, when two adjacent structure components are named identically except for the suffixes "B1" and "B0," it means that the two components may be viewed as a single number, computed as B1<<8 + B0. The name of this single number is the name of the components, minus the suffixes. This convention generalizes in an obvious way to handle numbers represented in more than two bytes.

첫째, 접미사 "B1"과 "B0"을 제외하고 두 개의 인접한 구조 구성요소가 동일하게 명명될 때, 이는 두 구성요소를 B1<<8 + B0으로 계산되는 단일 숫자로 볼 수 있음을 의미합니다. 이 단일 숫자의 이름은 접미사를 뺀 구성 요소의 이름입니다. 이 규칙은 2바이트 이상으로 표현되는 숫자를 처리하기 위해 분명한 방식으로 일반화됩니다.

Second, we extend C structs to allow the form

둘째, C 구조체를 확장하여 다음 형식을 허용합니다.

        struct {
            unsigned char mumbleLengthB1;
            unsigned char mumbleLengthB0;
            ... /* other stuff */
            unsigned char mumbleData[mumbleLength];
        };
        
meaning a structure of varying length, where the length of a component is determined by the values of the indicated earlier component or components.

구성 요소의 길이는 표시된 이전 구성 요소 또는 구성 요소의 값에 의해 결정되는 다양한 길이의 구조를 의미합니다.



### 3.2 Accepting Transport Connections
### 3.2 전송 연결 수락

A FastCGI application calls accept() on the socket referred to by file descriptor FCGI_LISTENSOCK_FILENO to accept a new transport connection. If the accept() succeeds, and the FCGI_WEB_SERVER_ADDRS environment variable is bound, the application application immediately performs the following special processing:
FCGI_WEB_SERVER_ADDRS: The value is a list of valid IP addresses for the Web server.

FastCGI 응용 프로그램은 파일 설명자 FCGI_LISTENSOCK_FILENO가 참조하는 소켓에서 accept()를 호출하여 새 전송 연결을 수락합니다. accept()가 성공하고 FCGI_WEB_SERVER_ADDRS 환경 변수가 바인딩되면 애플리케이션 애플리케이션은 즉시 다음과 같은 특수 처리를 수행합니다. FCGI_WEB_SERVER_ADDRS: 값은 웹 서버의 유효한 IP 주소 목록입니다.

If FCGI_WEB_SERVER_ADDRS was bound, the application checks the peer IP address of the new connection for membership in the list. If the check fails (including the possibility that the connection didn't use TCP/IP transport), the application responds by closing the connection.

FCGI_WEB_SERVER_ADDRS가 바인딩된 경우 애플리케이션은 목록의 구성원 자격에 대해 새 연결의 피어 IP 주소를 확인합니다. 확인이 실패하면(연결이 TCP/IP 전송을 사용하지 않았을 가능성 포함) 응용 프로그램은 연결을 닫아 응답합니다.

FCGI_WEB_SERVER_ADDRS is expressed as a comma-separated list of IP addresses. Each IP address is written as four decimal numbers in the range [0..255] separated by decimal points. So one legal binding for this variable is FCGI_WEB_SERVER_ADDRS=199.170.183.28,199.170.183.71.

FCGI_WEB_SERVER_ADDRS는 쉼표로 구분된 IP 주소 목록으로 표시됩니다. 각 IP 주소는 소수점으로 구분된 [0..255] 범위의 4개의 십진수로 작성됩니다. 따라서 이 변수에 대한 법적 바인딩은 FCGI_WEB_SERVER_ADDRS=199.170.183.28,199.170.183.71입니다.

An application may accept several concurrent transport connections, but it need not do so.
응용 프로그램은 여러 개의 동시 전송 연결을 수락할 수 있지만 그렇게 할 필요는 없습니다.

### 3.3 Records
### 3.3 기록

Applications execute requests from a Web server using a simple protocol. Details of the protocol depend upon the application's role, but roughly speaking the Web server first sends parameters and other data to the application, then the application sends result data to the Web server, and finally the application sends the Web server an indication that the request is complete.

응용 프로그램은 간단한 프로토콜을 사용하여 웹 서버의 요청을 실행합니다. 프로토콜의 세부 사항은 응용 프로그램의 역할에 따라 다르지만 대략적으로 말하면 웹 서버가 먼저 매개변수 및 기타 데이터를 응용 프로그램에 보낸 다음 응용 프로그램이 웹 서버에 결과 데이터를 보내고 마지막으로 응용 프로그램이 웹 서버에 요청에 대한 표시를 보냅니다. 완료되었습니다.

All data that flows over the transport connection is carried in FastCGI records. FastCGI records accomplish two things. First, records multiplex the transport connection between several independent FastCGI requests. This multiplexing supports applications that are able to process concurrent requests using event-driven or multi-threaded programming techniques. Second, records provide several independent data streams in each direction within a single request. This way, for instance, both stdout and stderr data can pass over a single transport connection from the application to the Web server, rather than requiring separate connections.

전송 연결을 통해 흐르는 모든 데이터는 FastCGI 레코드로 전달됩니다. FastCGI 레코드는 두 가지를 수행합니다. 첫째, 레코드는 여러 개의 독립적인 FastCGI 요청 간의 전송 연결을 다중화합니다. 이 멀티플렉싱은 이벤트 기반 또는 다중 스레드 프로그래밍 기술을 사용하여 동시 요청을 처리할 수 있는 응용 프로그램을 지원합니다. 둘째, 레코드는 단일 요청 내에서 각 방향으로 여러 개의 독립적인 데이터 스트림을 제공합니다. 예를 들어 이러한 방식으로 stdout 및 stderr 데이터는 별도의 연결을 요구하지 않고 단일 전송 연결을 통해 응용 프로그램에서 웹 서버로 전달할 수 있습니다.

        typedef struct {
            unsigned char version;
            unsigned char type;
            unsigned char requestIdB1;
            unsigned char requestIdB0;
            unsigned char contentLengthB1;
            unsigned char contentLengthB0;
            unsigned char paddingLength;
            unsigned char reserved;
            unsigned char contentData[contentLength];
            unsigned char paddingData[paddingLength];
        } FCGI_Record;
        
        
A FastCGI record consists of a fixed-length prefix followed by a variable number of content and padding bytes. A record contains seven components:

FastCGI 레코드는 고정 길이 접두어와 가변 수의 콘텐츠 및 패딩 바이트로 구성됩니다. 레코드에는 다음과 같은 7가지 구성 요소가 있습니다.

- version: Identifies the FastCGI protocol version. This specification documents FCGI_VERSION_1.

- 버전: FastCGI 프로토콜 버전을 식별합니다. 이 사양은 FCGI_VERSION_1을 문서화합니다.

- type: Identifies the FastCGI record type, i.e. the general function that the record performs. Specific record types and their functions are detailed in later sections.

- 유형: FastCGI 레코드 유형, 즉 레코드가 수행하는 일반 기능을 식별합니다. 특정 레코드 유형 및 해당 기능은 이후 섹션에서 자세히 설명합니다.

- requestId: Identifies the FastCGI request to which the record belongs.

- requestId: 레코드가 속한 FastCGI 요청을 식별합니다.

- contentLength: The number of bytes in the contentData component of the record.

- contentLength: 레코드의 contentData 구성 요소에 있는 바이트 수입니다.

- paddingLength: The number of bytes in the paddingData component of the record.

- paddingLength: 레코드의 paddingData 구성 요소에 있는 바이트 수입니다.

- contentData: Between 0 and 65535 bytes of data, interpreted according to the record type.

- contentData: 0에서 65535바이트 사이의 데이터로, 레코드 유형에 따라 해석됩니다.

- paddingData: Between 0 and 255 bytes of data, which are ignored.

- paddingData: 0에서 255바이트 사이의 데이터로 무시됩니다.

We use a relaxed C struct initializer syntax to specify constant FastCGI records. We omit the version component, ignore padding, and treat requestId as a number. Thus {FCGI_END_REQUEST, 1, {FCGI_REQUEST_COMPLETE,0}} is a record with type == FCGI_END_REQUEST, requestId == 1, and contentData == {FCGI_REQUEST_COMPLETE,0}.

우리는 일정한 FastCGI 레코드를 지정하기 위해 편안한 C struct 이니셜라이저 구문을 사용합니다. 버전 구성 요소를 생략하고 패딩을 무시하며 requestId를 숫자로 취급합니다. 따라서 {FCGI_END_REQUEST, 1, {FCGI_REQUEST_COMPLETE,0}}은 유형 == FCGI_END_REQUEST, requestId == 1, contentData == {FCGI_REQUEST_COMPLETE,0}인 레코드입니다.

#### Padding

The protocol allows senders to pad the records they send, and requires receivers to interpret the paddingLength and skip the paddingData. Padding allows senders to keep data aligned for more efficient processing. Experience with the X window system protocols shows the performance benefit of such alignment.

이 프로토콜을 통해 발신자는 자신이 보내는 레코드를 채울 수 있으며 수신자는 paddingLength를 해석하고 paddingData를 건너뛸 수 있습니다. 패딩을 사용하면 발신자가 데이터를 보다 효율적으로 처리할 수 있도록 정렬된 상태로 유지할 수 있습니다. X 윈도우 시스템 프로토콜에 대한 경험은 이러한 정렬의 성능 이점을 보여줍니다.

We recommend that records be placed on boundaries that are multiples of eight bytes. The fixed-length portion of a FCGI_Record is eight bytes.

레코드는 8바이트의 배수인 경계에 배치하는 것이 좋습니다. FCGI_Record의 고정 길이 부분은 8바이트입니다.

#### Managing Request IDs
#### 요청 ID 관리

The Web server re-uses FastCGI request IDs; the application keeps track of the current state of each request ID on a given transport connection. A request ID R becomes active when the application receives a record {FCGI_BEGIN_REQUEST, R, ...} and becomes inactive when the application sends a record {FCGI_END_REQUEST, R, ...} to the Web server.
While a request ID R is inactive, the application ignores records with requestId == R, except for FCGI_BEGIN_REQUEST records as just described.

웹 서버는 FastCGI 요청 ID를 재사용합니다. 애플리케이션은 지정된 전송 연결에서 각 요청 ID의 현재 상태를 추적합니다. 요청 ID R은 애플리케이션이 {FCGI_BEGIN_REQUEST, R, ...} 레코드를 수신할 때 활성화되고 애플리케이션이 웹 서버에 {FCGI_END_REQUEST, R, ...} 레코드를 보낼 때 비활성화됩니다.
요청 ID R이 비활성 상태인 동안 애플리케이션은 방금 설명한 FCGI_BEGIN_REQUEST 레코드를 제외하고 requestId == R인 레코드를 무시합니다.

The Web server attempts to keep FastCGI request IDs small. That way the application can keep track of request ID states using a short array rather than a long array or a hash table. An application also has the option of accepting only one request at a time. In this case the application simply checks incoming requestId values against the current request ID.

웹 서버는 FastCGI 요청 ID를 작게 유지하려고 합니다. 그런 식으로 애플리케이션은 긴 배열이나 해시 테이블이 아닌 짧은 배열을 사용하여 요청 ID 상태를 추적할 수 있습니다. 응용 프로그램에는 한 번에 하나의 요청만 수락하는 옵션도 있습니다. 이 경우 애플리케이션은 단순히 현재 요청 ID에 대해 들어오는 requestId 값을 확인합니다.

#### Types of Record Types
#### 레코드 유형의 유형

There are two useful ways of classifying FastCGI record types.
FastCGI 레코드 유형을 분류하는 두 가지 유용한 방법이 있습니다.

The first distinction is between management records and application records. A management record contains information that is not specific to any Web server request, such as information about the protocol capabilities of the application. An application record contains information about a particular request, identified by the requestId component.

첫 번째 차이점은 관리 기록과 신청 기록 사이입니다. 관리 레코드에는 응용 프로그램의 프로토콜 기능에 대한 정보와 같이 웹 서버 요청과 관련이 없는 정보가 포함되어 있습니다. 애플리케이션 레코드에는 requestId 구성 요소로 식별되는 특정 요청에 대한 정보가 포함됩니다.

Management records have a requestId value of zero, also called the null request ID. Application records have a nonzero requestId.

관리 레코드는 null 요청 ID라고도 하는 0의 requestId 값을 갖습니다. 애플리케이션 레코드에 0이 아닌 requestId가 있습니다.

The second distinction is between discrete and stream records. A discrete record contains a meaningful unit of data all by itself. A stream record is part of a stream, i.e. a series of zero or more non-empty records (length != 0) of the stream type, followed by an empty record (length == 0) of the stream type. The contentData components of a stream's records, when concatenated, form a byte sequence; this byte sequence is the value of the stream. Therefore the value of a stream is independent of how many records it contains or how its bytes are divided among the non-empty records.

두 번째 차이점은 이산 레코드와 스트림 레코드 간의 차이입니다. 불연속 레코드는 그 자체로 의미 있는 데이터 단위를 포함합니다. 스트림 레코드는 스트림의 일부입니다. 즉, 스트림 유형의 빈 레코드(길이 == 0)가 뒤따르는 0개 이상의 비어 있지 않은 레코드(길이 != 0)의 시리즈입니다. 스트림 레코드의 contentData 구성 요소는 연결될 때 바이트 시퀀스를 형성합니다. 이 바이트 시퀀스는 스트림의 값입니다. 따라서 스트림의 값은 포함된 레코드 수 또는 바이트가 비어 있지 않은 레코드 간에 분할되는 방식과 무관합니다.

These two classifications are independent. Among the record types defined in this version of the FastCGI protocol, all management record types are also discrete record types, and nearly all application record types are stream record types. But three application record types are discrete, and nothing prevents defining a management record type that's a stream in some later version of the protocol.

이 두 분류는 독립적입니다. 이 버전의 FastCGI 프로토콜에 정의된 레코드 유형 중 모든 관리 레코드 유형도 개별 레코드 유형이며 거의 모든 애플리케이션 레코드 유형은 스트림 레코드 유형입니다. 그러나 세 가지 애플리케이션 레코드 유형은 별개이며 프로토콜의 일부 이후 버전에서 스트림인 관리 레코드 유형을 정의하는 것을 방해하는 것은 없습니다.

### 3.4 Name-Value Pairs
### 3.4 이름-값 쌍

In many of their roles, FastCGI applications need to read and write varying numbers of variable-length values. So it is useful to adopt a standard format for encoding a name-value pair.

많은 역할에서 FastCGI 응용 프로그램은 다양한 수의 가변 길이 값을 읽고 써야 합니다. 따라서 이름-값 쌍을 인코딩하기 위해 표준 형식을 채택하는 것이 유용합니다.

FastCGI transmits a name-value pair as the length of the name, followed by the length of the value, followed by the name, followed by the value. Lengths of 127 bytes and less can be encoded in one byte, while longer lengths are always encoded in four bytes:

FastCGI는 이름의 길이, 값의 길이, 이름, 값의 순으로 이름-값 쌍을 전송합니다. 127바이트 이하의 길이는 1바이트로 인코딩할 수 있지만 더 긴 길이는 항상 4바이트로 인코딩됩니다.

        typedef struct {
            unsigned char nameLengthB0;  /* nameLengthB0  >> 7 == 0 */
            unsigned char valueLengthB0; /* valueLengthB0 >> 7 == 0 */
            unsigned char nameData[nameLength];
            unsigned char valueData[valueLength];
        } FCGI_NameValuePair11;

        typedef struct {
            unsigned char nameLengthB0;  /* nameLengthB0  >> 7 == 0 */
            unsigned char valueLengthB3; /* valueLengthB3 >> 7 == 1 */
            unsigned char valueLengthB2;
            unsigned char valueLengthB1;
            unsigned char valueLengthB0;
            unsigned char nameData[nameLength];
            unsigned char valueData[valueLength
                    ((B3 & 0x7f) << 24) + (B2 << 16) + (B1 << 8) + B0];
        } FCGI_NameValuePair14;

        typedef struct {
            unsigned char nameLengthB3;  /* nameLengthB3  >> 7 == 1 */
            unsigned char nameLengthB2;
            unsigned char nameLengthB1;
            unsigned char nameLengthB0;
            unsigned char valueLengthB0; /* valueLengthB0 >> 7 == 0 */
            unsigned char nameData[nameLength
                    ((B3 & 0x7f) << 24) + (B2 << 16) + (B1 << 8) + B0];
            unsigned char valueData[valueLength];
        } FCGI_NameValuePair41;

        typedef struct {
            unsigned char nameLengthB3;  /* nameLengthB3  >> 7 == 1 */
            unsigned char nameLengthB2;
            unsigned char nameLengthB1;
            unsigned char nameLengthB0;
            unsigned char valueLengthB3; /* valueLengthB3 >> 7 == 1 */
            unsigned char valueLengthB2;
            unsigned char valueLengthB1;
            unsigned char valueLengthB0;
            unsigned char nameData[nameLength
                    ((B3 & 0x7f) << 24) + (B2 << 16) + (B1 << 8) + B0];
            unsigned char valueData[valueLength
                    ((B3 & 0x7f) << 24) + (B2 << 16) + (B1 << 8) + B0];
        } FCGI_NameValuePair44;
        
        
The high-order bit of the first byte of a length indicates the length's encoding. A high-order zero implies a one-byte encoding, a one a four-byte encoding.
This name-value pair format allows the sender to transmit binary values without additional encoding, and enables the receiver to allocate the correct amount of storage immediately even for large values.

길이의 첫 번째 바이트의 상위 비트는 길이의 인코딩을 나타냅니다. 상위 0은 1바이트 인코딩, 1은 4바이트 인코딩을 의미합니다.
이 이름-값 쌍 형식을 통해 발신자는 추가 인코딩 없이 이진 값을 전송할 수 있으며 수신자는 큰 값에 대해서도 즉시 정확한 양의 저장 공간을 할당할 수 있습니다.

### 3.5 Closing Transport Connections
### 3.5 전송 연결 닫기

The Web server controls the lifetime of transport connections. The Web server can close a connection when no requests are active. Or the Web server can delegate close authority to the application (see FCGI_BEGIN_REQUEST). In this case the application closes the connection at the end of a specified request.

웹 서버는 전송 연결의 수명을 제어합니다. 웹 서버는 활성 요청이 없을 때 연결을 닫을 수 있습니다. 또는 웹 서버가 응용 프로그램에 닫기 권한을 위임할 수 있습니다(FCGI_BEGIN_REQUEST 참조). 이 경우 애플리케이션은 지정된 요청이 끝나면 연결을 닫습니다.

This flexibility accommodates a variety of application styles. Simple applications will process one request at a time and accept a new transport connection for each request. More complex applications will process concurrent requests, over one or multiple transport connections, and will keep transport connections open for long periods of time.

이러한 유연성은 다양한 애플리케이션 스타일을 수용합니다. 단순 응용 프로그램은 한 번에 하나의 요청을 처리하고 각 요청에 대해 새 전송 연결을 수락합니다. 더 복잡한 응용 프로그램은 하나 이상의 전송 연결을 통해 동시 요청을 처리하고 오랜 기간 동안 전송 연결을 열어 둡니다.

A simple application gets a significant performance boost by closing the transport connection when it has finished writing its response. The Web server needs to control the connection lifetime for long-lived connections.

간단한 응용 프로그램은 응답 작성을 마쳤을 때 전송 연결을 닫음으로써 상당한 성능 향상을 얻습니다. 웹 서버는 수명이 긴 연결에 대한 연결 수명을 제어해야 합니다.

When an application closes a connection or finds that a connection has closed, the application initiates a new connection.

응용 프로그램이 연결을 닫거나 연결이 닫혔다는 것을 발견하면 응용 프로그램은 새 연결을 시작합니다.
