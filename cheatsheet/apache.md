# Apache Cheatsheet

## LogFormat

```shell
%a:          Remote IP-address
%A:          Local IP-address
%B:          Bytes sent, excluding HTTP headers.
%b:          Bytes sent, excluding HTTP headers. In CLF format. i.e. a '-' rather than a 0 when no bytes are sent.
%c:          Connection status when response was completed.
             'X' = connection aborted before the response completed.
             '+' = connection may be kept alive after the response is sent.
             '-' = connection will be closed after the response is sent.
%{FOOBAR}e:  The contents of the environment variable FOOBAR
%f:          Filename
%h:          Remote host
%H           The request protocol
%{Foobar}i:  The contents of Foobar: header line(s) in the request
             sent to the server.
%l:          Remote logname (from identd, if supplied)
%m           The request method
%{Foobar}n:  The contents of note "Foobar" from another module.
%{Foobar}o:  The contents of Foobar: header line(s) in the reply.
%p:          The canonical Port of the server serving the request
%P:          The process ID of the child that serviced the request.
%q           The query string (prepended with a ? if a query string exists, otherwise an empty string)
%r:          First line of request
%s:          Status.  For requests that got internally redirected, this is the status of the *original* request --- %...>s for the last.
%t:          Time, in common log format time format (standard english format)
%{format}t:  The time, in the form given by format, which should be in strftime(3) format. (potentially localized)
%T:          The time taken to serve the request, in seconds.
%u:          Remote user (from auth; may be bogus if return status (%s) is 401)
%U:          The URL path requested, not including any query string.
%v:          The canonical ServerName of the server serving the request.
%V:          The server name according to the UseCanonicalName setting.
```
