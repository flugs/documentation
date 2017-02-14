# Flugs Server
Flugs supports several types of HTTP servers.

## HTTP Server


## HTTPS Server

### 
### Automatic HTTPS
Flugs automatically enables HTTPS for your application, given that some
reasonable criteria are met:

 - The host is non-empty and not localhost or an IP address
 - The port is not explicitly 80
 - The scheme is not explicitly http
 - TLS is not turned off
 - Certificates and keys are not provided by you
 - Flugs is able to bind to ports 80 and 443


## Local Socket
