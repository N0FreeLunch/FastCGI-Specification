National Center for Supercomputer Applications, The Common Gateway Interface, version CGI/1.1.

D.R.T. Robinson, The WWW Common Gateway Interface Version 1.1, Internet-Draft, 15 February 1996.

A. Table: Properties of the record types

A. 표: 레코드 유형의 속성

The following chart lists all of the record types and indicates these properties of each:

다음 차트는 모든 레코드 유형을 나열하고 각각의 이러한 속성을 나타냅니다.

WS->App: records of this type can only be sent by the Web server to the application. Records of other types can only be sent by the application to the Web server.

WS->앱: 이 유형의 레코드는 웹 서버에서만 애플리케이션으로 보낼 수 있습니다. 다른 유형의 레코드는 응용 프로그램에서 웹 서버로만 보낼 수 있습니다.

management: records of this type contain information that is not specific to a Web server request, and use the null request ID. Records of other types contain request-specific information, and cannot use the null request ID.

관리: 이 유형의 레코드에는 웹 서버 요청과 관련이 없는 정보가 포함되어 있으며 null 요청 ID를 사용합니다. 다른 유형의 레코드에는 요청별 정보가 포함되어 있으며 null 요청 ID를 사용할 수 없습니다.

stream: records of this type form a stream, terminated by a record with empty contentData. Records of other types are discrete; each carries a meaningful unit of data.

스트림: 이 유형의 레코드는 스트림을 형성하고 빈 contentData가 있는 레코드로 종료됩니다. 다른 유형의 레코드는 개별적입니다. 각각은 의미 있는 데이터 단위를 전달합니다.

                               WS->App   management  stream

        FCGI_GET_VALUES           x          x
        FCGI_GET_VALUES_RESULT               x
        FCGI_UNKNOWN_TYPE                    x

        FCGI_BEGIN_REQUEST        x
        FCGI_ABORT_REQUEST        x
        FCGI_END_REQUEST
        FCGI_PARAMS               x                    x
        FCGI_STDIN                x                    x
        FCGI_DATA                 x                    x
        FCGI_STDOUT                                    x 
        FCGI_STDERR                                    x     


B. Typical Protocol Message Flow

B. 일반적인 프로토콜 메시지 흐름

Additional notational conventions for the examples:

예제에 대한 추가 표기 규칙:

The contentData of stream records (FCGI_PARAMS, FCGI_STDIN, FCGI_STDOUT, and FCGI_STDERR) is represented as a character string. A string ending in " ... " is too long to display, so only a prefix is shown.
Messages sent to the Web server are indented with respect to messages received from the Web server.

스트림 레코드(FCGI_PARAMS, FCGI_STDIN, FCGI_STDOUT, FCGI_STDERR)의 contentData는 문자열로 표현된다. " ... "로 끝나는 문자열은 너무 길어서 표시할 수 없으므로 접두사만 표시됩니다.
웹 서버로 보낸 메시지는 웹 서버에서 받은 메시지와 관련하여 들여쓰기됩니다.

Messages are shown in the time sequence experienced by the application.

메시지는 애플리케이션에서 경험한 시간 순서로 표시됩니다.

1. A simple request with no data on stdin, and a successful response:

1. stdin에 대한 데이터가 없는 간단한 요청과 성공적인 응답:

{FCGI_BEGIN_REQUEST,   1, {FCGI_RESPONDER, 0}}
{FCGI_PARAMS,          1, "\013\002SERVER_PORT80\013\016SERVER_ADDR199.170.183.42 ... "}
{FCGI_PARAMS,          1, ""}
{FCGI_STDIN,           1, ""}

    {FCGI_STDOUT,      1, "Content-type: text/html\r\n\r\n<html>\n<head> ... "}
    {FCGI_STDOUT,      1, ""}
    {FCGI_END_REQUEST, 1, {0, FCGI_REQUEST_COMPLETE}}
    
2. Similar to example 1, but this time with data on stdin. The Web server chooses to send the parameters using more FCGI_PARAMS records than before:

2. 예제 1과 유사하지만 이번에는 stdin에 대한 데이터를 사용합니다. 웹 서버는 이전보다 더 많은 FCGI_PARAMS 레코드를 사용하여 매개변수를 전송하도록 선택합니다.

{FCGI_BEGIN_REQUEST,   1, {FCGI_RESPONDER, 0}}
{FCGI_PARAMS,          1, "\013\002SERVER_PORT80\013\016SER"}
{FCGI_PARAMS,          1, "VER_ADDR199.170.183.42 ... "}
{FCGI_PARAMS,          1, ""}
{FCGI_STDIN,           1, "quantity=100&item=3047936"}
{FCGI_STDIN,           1, ""}

    {FCGI_STDOUT,      1, "Content-type: text/html\r\n\r\n<html>\n<head> ... "}
    {FCGI_STDOUT,      1, ""}
    {FCGI_END_REQUEST, 1, {0, FCGI_REQUEST_COMPLETE}}
    
3. Similar to example 1, but this time the application detects an error. The application logs a message to stderr, returns a page to the client, and returns non-zero exit status to the Web server. The application chooses to send the page using more FCGI_STDOUT records:

예제 1과 유사하지만 이번에는 애플리케이션이 오류를 감지합니다. 응용 프로그램은 stderr에 메시지를 기록하고 클라이언트에 페이지를 반환하고 웹 서버에 0이 아닌 종료 상태를 반환합니다. 애플리케이션은 더 많은 FCGI_STDOUT 레코드를 사용하여 페이지를 전송하도록 선택합니다.

{FCGI_BEGIN_REQUEST,   1, {FCGI_RESPONDER, 0}}
{FCGI_PARAMS,          1, "\013\002SERVER_PORT80\013\016SERVER_ADDR199.170.183.42 ... "}
{FCGI_PARAMS,          1, ""}
{FCGI_STDIN,           1, ""}

    {FCGI_STDOUT,      1, "Content-type: text/html\r\n\r\n<ht"}
    {FCGI_STDERR,      1, "config error: missing SI_UID\n"}
    {FCGI_STDOUT,      1, "ml>\n<head> ... "}
    {FCGI_STDOUT,      1, ""}
    {FCGI_STDERR,      1, ""}
    {FCGI_END_REQUEST, 1, {938, FCGI_REQUEST_COMPLETE}}
    
4. Two instances of example 1, multiplexed onto a single connection. The first request is more difficult than the second, so the application finishes the requests out of order:

4. 단일 연결로 다중화된 예 1의 두 인스턴스. 첫 번째 요청은 두 번째 요청보다 더 어렵기 때문에 애플리케이션이 요청을 순서 없이 완료합니다.

{FCGI_BEGIN_REQUEST,   1, {FCGI_RESPONDER, FCGI_KEEP_CONN}}
{FCGI_PARAMS,          1, "\013\002SERVER_PORT80\013\016SERVER_ADDR199.170.183.42 ... "}
{FCGI_PARAMS,          1, ""}
{FCGI_BEGIN_REQUEST,   2, {FCGI_RESPONDER, FCGI_KEEP_CONN}}
{FCGI_PARAMS,          2, "\013\002SERVER_PORT80\013\016SERVER_ADDR199.170.183.42 ... "}
{FCGI_STDIN,           1, ""}

    {FCGI_STDOUT,      1, "Content-type: text/html\r\n\r\n"}

{FCGI_PARAMS,          2, ""}
{FCGI_STDIN,           2, ""}

    {FCGI_STDOUT,      2, "Content-type: text/html\r\n\r\n<html>\n<head> ... "}
    {FCGI_STDOUT,      2, ""}
    {FCGI_END_REQUEST, 2, {0, FCGI_REQUEST_COMPLETE}}
    {FCGI_STDOUT,      1, "<html>\n<head> ... "}
    {FCGI_STDOUT,      1, ""}
    {FCGI_END_REQUEST, 1, {0, FCGI_REQUEST_COMPLETE}}
