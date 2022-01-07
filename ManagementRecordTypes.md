### 4.1 FCGI_GET_VALUES, FCGI_GET_VALUES_RESULT

The Web server can query specific variables within the application. The server will typically perform a query on application startup in order to to automate certain aspects of system configuration.

웹 서버는 응용 프로그램 내의 특정 변수를 쿼리할 수 있습니다. 서버는 일반적으로 시스템 구성의 특정 측면을 자동화하기 위해 응용 프로그램 시작 시 쿼리를 수행합니다.

The application receives a query as a record {FCGI_GET_VALUES, 0, ...}. The contentData portion of a FCGI_GET_VALUES record contains a sequence of name-value pairs with empty values.

애플리케이션은 쿼리를 {FCGI_GET_VALUES, 0, ...} 레코드로 수신합니다. FCGI_GET_VALUES 레코드의 contentData 부분에는 값이 비어 있는 이름-값 쌍의 시퀀스가 포함되어 있습니다.

The application responds by sending a record {FCGI_GET_VALUES_RESULT, 0, ...} with the values supplied. If the application doesn't understand a variable name that was included in the query, it omits that name from the response.

애플리케이션은 제공된 값과 함께 {FCGI_GET_VALUES_RESULT, 0, ...} 레코드를 전송하여 응답합니다. 애플리케이션이 쿼리에 포함된 변수 이름을 이해하지 못하는 경우 응답에서 해당 이름을 생략합니다.

FCGI_GET_VALUES is designed to allow an open-ended set of variables. The initial set provides information to help the server perform application and connection management:

FCGI_GET_VALUES는 개방형 변수 집합을 허용하도록 설계되었습니다. 초기 세트는 서버가 애플리케이션 및 연결 관리를 수행하는 데 도움이 되는 정보를 제공합니다.

FCGI_MAX_CONNS: The maximum number of concurrent transport connections this application will accept, e.g. "1" or "10".

FCGI_MAX_CONNS: 이 애플리케이션이 수락할 최대 동시 전송 연결 수입니다. "1" 또는 "10".

FCGI_MAX_REQS: The maximum number of concurrent requests this application will accept, e.g. "1" or "50".

FCGI_MAX_REQS: 이 애플리케이션이 수락할 최대 동시 요청 수입니다. "1" 또는 "50".

FCGI_MPXS_CONNS: "0" if this application does not multiplex connections (i.e. handle concurrent requests over each connection), "1" otherwise.

FCGI_MPXS_CONNS: 이 응용 프로그램이 연결을 다중화하지 않는 경우(즉, 각 연결에 대한 동시 요청 처리) "0", 그렇지 않은 경우 "1".

An application may receive a FCGI_GET_VALUES record at any time. The application's response should not involve the application proper but only the FastCGI library.

애플리케이션은 언제든지 FCGI_GET_VALUES 레코드를 수신할 수 있습니다. 응용 프로그램의 응답은 적절한 응용 프로그램이 아니라 FastCGI 라이브러리만 포함해야 합니다.

### 4.2 FCGI_UNKNOWN_TYPE

The set of management record types is likely to grow in future versions of this protocol. To provide for this evolution, the protocol includes the FCGI_UNKNOWN_TYPE management record. 

관리 레코드 유형 집합은 이 프로토콜의 향후 버전에서 증가할 가능성이 있습니다. 이러한 발전을 제공하기 위해 프로토콜에는 FCGI_UNKNOWN_TYPE 관리 레코드가 포함됩니다.

When an application receives a management record whose type T it does not understand, the application responds with {FCGI_UNKNOWN_TYPE, 0, {T}}.

애플리케이션이 유형 T를 이해할 수 없는 관리 레코드를 수신하면 애플리케이션은 {FCGI_UNKNOWN_TYPE, 0, {T}}로 응답합니다.

The contentData component of a FCGI_UNKNOWN_TYPE record has the form:

FCGI_UNKNOWN_TYPE 레코드의 contentData 구성 요소 형식은 다음과 같습니다.


        typedef struct {
            unsigned char type;    
            unsigned char reserved[7];
        } FCGI_UnknownTypeBody;


The type component is the type of the unrecognized management record.

유형 구성 요소는 인식할 수 없는 관리 레코드의 유형입니다.
