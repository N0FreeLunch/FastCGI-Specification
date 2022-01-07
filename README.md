1. [ntroduction](./Introduction.md)
3. [nitial Process State](./InitialProcessState.md)
  2.1 Argument list
  2.2 File descriptors
  2.3 Environment variables
  2.4 Other state
4. [Protocol Basics](./ProtocolBasics.md)
  3.1 Notation
  3.2 Accepting Transport Connections
  3.3 Records
  3.4 Name-Value Pairs
  3.5 Closing Transport Connections
5. [Management Record Types](./ManagementRecordTypes.md)
  4.1 FCGI_GET_VALUES, FCGI_GET_VALUES_RESULT
  4.2 FCGI_UNKNOWN_TYPE
6. [Application Record Types](./ApplicationRecordTypes.md)
  5.1 FCGI_BEGIN_REQUEST
  5.2 Name-Value Pair Streams: FCGI_PARAMS, FCGI_RESULTS
  5.3 Byte Streams: FCGI_STDIN, FCGI_DATA, FCGI_STDOUT, FCGI_STDERR
  5.4 FCGI_ABORT_REQUEST
  5.5 FCGI_END_REQUEST
7. Roles(./Roles.md)
  6.1 Role Protocols
  6.2 Responder
  6.3 Authorizer
  6.4 Filter
8. Errors(./Errors.md)
9. Types and Constants(./TypesAndConstants.md)
10. References(./References.md)
  A. Table: Properties of the record types
  B. Typical Protocol Message Flow


## Reference
- FastCGI : http://www.mit.edu/~yandros/doc/specs/fcgi-spec.html
- ngx_http_fastcgi_module : http://nginx.org/en/docs/http/ngx_http_fastcgi_module.html#fastcgi_keep_conn
