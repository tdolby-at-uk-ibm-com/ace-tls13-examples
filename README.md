# ace-tls13-examples
Examples of using ACE with TLSv1.3

These examples call a TLSv1.3-only service on the internet (`tls13.1d.pw`) to test TLSv1.3 connectivity.

## Build and test

Clone this repo locally, using either the git command or the eGit plugin (included with ACE v12).

Deploy the applications using either mqsicreateworkdir/mqsibar or to an integration node, and then use curl or a browser 
to trigger the flows. Four flow URLs exist currently:

- `/usingTLSHTTPRequest` for a flow that specifies "TLS" on the HTTPRequest node
- `/explicitTLS12HTTPRequest` for a flow that specifies "TLSv1.2" on the HTTPRequest node
- `/explicitTLS13HTTPRequest` for a flow that specifies "TLSv1.3" on the HTTPRequest node
- `/usingTLS13HTTPRequestLE` for a flow that specifies "TLSv1.1" on the HTTPRequest node and then overrides it in the LocalEnvironment to use "TLSv1.3"

The last two are equivalent, and test results for the last flow are not shown.

## Expected results with ACE 12.0.4

Success for "TLS" and "TLSv1.3", with a failure for "TLSv1.2" due to the server not supporting it.

#### TLS
```
tdolby:/$ curl http://localhost:7800/usingTLSHTTPRequest
{"connectResult":"success"}
```
#### TLSv1.3
```
tdolby:/$ curl http://localhost:7800/explicitTLS13HTTPRequest 
{"connectResult":"success"}
```
#### TLS v1.2
```
tdolby:/$ curl http://localhost:7800/explicitTLS12HTTPRequest
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<SOAP-ENV:Body>
<SOAP-ENV:Fault>
<faultcode>SOAP-ENV:Server</faultcode>
<faultstring>BIP3113E: Exception detected in message flow ExplicitTLS12HTTPRequest (integration node integration_server) </faultstring>
<faultactor>http://localhost:7800/explicitTLS12HTTPRequest</faultactor>
<detail><text>Exception. BIP2230E: Error detected whilst processing a message in node &apos;ExplicitTLS12HTTPRequest.HTTP Request&apos;. :
/build/jenkins_swg/slot0/product-build/WMB/src/WebServices/WSLibrary/ImbWSRequestNode.cpp: 787: ImbWSRequestNode::evaluate: ComIbmWSRequestNode: ExplicitTLS12HTTPRequest#FCMComposite_1_3
BIP3162S: An HTTP error occurred. The HTTP Request-Line was: &apos;POST / HTTP/1: /build/jenkins_swg/slot0/product-build/WMB/src/WebServices/WSLibrary/ImbWSRequestNode.cpp: 699: ImbWSRequestNode::evaluate: : 
BIP3152S: Socket error detected whilst invoking Web service located at host tls13.1d.pw, port 443, path /. :
/build/jenkins_swg/slot0/product-build/WMB/src/WebServices/WSBase/ImbWSRequest.cpp: 723: Imb::WSRequest::makeWSRequest: : 
BIP3165S: An error occurred whilst performing an SSL socket operation. Operation: connect. Error Text: 
javax.net.ssl.SSLHandshakeException: Received fatal alert: protocol_version. :
/build/jenkins_swg/slot0/product-build/WMB/src/WebServices/WSBase/Socket.cpp: 1320: Imb::SocketJNIManager::handleGeneralJavaException: :
</text></detail>
</SOAP-ENV:Fault>
</SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```

## Expected results with ACE 11.0.0.9

Failure in all cases, with "TLSv1.3" failing due to not being supported by the JVM and the other two failing due to trying to use TLSv1.2.


#### TLS
```
[kenya:/] curl http://localhost:7800/usingTLSHTTPRequest
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<SOAP-ENV:Body>
<SOAP-ENV:Fault>
<faultcode>SOAP-ENV:Server</faultcode>
<faultstring>BIP3113E: Exception detected in message flow UsingTLSHTTPRequest (integration node integration_server) </faultstring>
<faultactor>http://localhost:7800/usingTLSHTTPRequest</faultactor>
<detail><text>Exception. BIP2230E: Error detected whilst processing a message in node &apos;UsingTLSHTTPRequest.HTTP Request&apos;. :
/jenkins/slot0/product-build/WMB/src/WebServices/WSLibrary/ImbWSRequestNode.cpp: 806: ImbWSRequestNode::evaluate: ComIbmWSRequestNode: UsingTLSHTTPRequest#FCMComposite_1_3
: /jenkins/slot0/product-build/WMB/src/WebServices/WSLibrary/ImbWSRequestNode.cpp: 718: ImbWSRequestNode::evaluate: : 
BIP3152S: Socket error detected whilst invoking Web service located at host &apos;tls13.1d.pw&apos;, port 443, path &apos;/&apos;. :
/jenkins/slot0/product-build/WMB/src/WebServices/WSBase/ImbWSRequest.cpp: 678: Imb::WSRequest::makeWSRequest: : 
BIP3165S: An error occurred whilst performing an SSL socket operation. Operation: &apos;connect&apos;. Error Text:
&apos;javax.net.ssl.SSLException: Received fatal alert: protocol_version&apos;. :
/jenkins/slot0/product-build/WMB/src/WebServices/WSBase/Socket.cpp: 1266: Imb::SocketJNIManager::handleGeneralJavaException: :
</text></detail>
</SOAP-ENV:Fault>
</SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```
#### TLSv1.3
```
[kenya:/] curl http://localhost:7800/explicitTLS13HTTPRequest
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<SOAP-ENV:Body>
<SOAP-ENV:Fault>
<faultcode>SOAP-ENV:Server</faultcode>
<faultstring>BIP3113E: Exception detected in message flow ExplicitTLS13HTTPRequest (integration node integration_server) </faultstring>
<faultactor>http://localhost:7800/explicitTLS13HTTPRequest</faultactor>
<detail><text>Exception. BIP2230E: Error detected whilst processing a message in node &apos;ExplicitTLS13HTTPRequest.HTTP Request&apos;. :
/jenkins/slot0/product-build/WMB/src/WebServices/WSLibrary/ImbWSRequestNode.cpp: 806: ImbWSRequestNode::evaluate: ComIbmWSRequestNode: ExplicitTLS13HTTPRequest#FCMComposite_1_3
: /jenkins/slot0/product-build/WMB/src/WebServices/WSLibrary/ImbWSRequestNode.cpp: 718: ImbWSRequestNode::evaluate: : 
BIP3152S: Socket error detected whilst invoking Web service located at host &apos;tls13.1d.pw&apos;, port 443, path &apos;/&apos;. :
/jenkins/slot0/product-build/WMB/src/WebServices/WSBase/ImbWSRequest.cpp: 678: Imb::WSRequest::makeWSRequest: : 
BIP3165S: An error occurred whilst performing an SSL socket operation. Operation: &apos;setSSLOptions&apos;. Error Text: 
&apos;java.security.NoSuchAlgorithmException: TLSv1.3 SSLContext not available&apos;. :
/jenkins/slot0/product-build/WMB/src/WebServices/WSBase/Socket.cpp: 1266: Imb::SocketJNIManager::handleGeneralJavaException: :
</text></detail>
</SOAP-ENV:Fault>
</SOAP-ENV:Body>
</SOAP-ENV:Envelope>

```
#### TLS v1.2
```
[kenya:/] curl http://localhost:7800/explicitTLS12HTTPRequest
<?xml version="1.0" encoding="UTF-8"?>
<SOAP-ENV:Envelope xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<SOAP-ENV:Body>
<SOAP-ENV:Fault>
<faultcode>SOAP-ENV:Server</faultcode>
<faultstring>BIP3113E: Exception detected in message flow ExplicitTLS12HTTPRequest (integration node integration_server) </faultstring>
<faultactor>http://localhost:7800/explicitTLS12HTTPRequest</faultactor>
<detail><text>Exception. BIP2230E: Error detected whilst processing a message in node &apos;ExplicitTLS12HTTPRequest.HTTP Request&apos;. :
/jenkins/slot0/product-build/WMB/src/WebServices/WSLibrary/ImbWSRequestNode.cpp: 806: ImbWSRequestNode::evaluate: ComIbmWSRequestNode: ExplicitTLS12HTTPRequest#FCMComposite_1_3
: /jenkins/slot0/product-build/WMB/src/WebServices/WSLibrary/ImbWSRequestNode.cpp: 718: ImbWSRequestNode::evaluate: : 
BIP3152S: Socket error detected whilst invoking Web service located at host &apos;tls13.1d.pw&apos;, port 443, path &apos;/&apos;. :
/jenkins/slot0/product-build/WMB/src/WebServices/WSBase/ImbWSRequest.cpp: 678: Imb::WSRequest::makeWSRequest: : 
BIP3165S: An error occurred whilst performing an SSL socket operation. Operation: &apos;connect&apos;. Error Text:
&apos;javax.net.ssl.SSLException: Received fatal alert: protocol_version&apos;. :
/jenkins/slot0/product-build/WMB/src/WebServices/WSBase/Socket.cpp: 1266: Imb::SocketJNIManager::handleGeneralJavaException: :
</text></detail>
</SOAP-ENV:Fault>
</SOAP-ENV:Body>
</SOAP-ENV:Envelope>
```
