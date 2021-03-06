# HttpMockServer

Read about what is Mock server and its usecases at <a href="https://www.mock-server.com/#what-is-mockserver">here</a>.<BR>
But my aim is to keep it simple so that QA engineers can setup integration tests; with zero code at both the Client and Server; only with configuration changes.

The application is made with spring boot, so easy to start <BR>
Build instructions to run this application is blow:

1. mvn clean install
2. java -jar ../.m2/repository/com/example/httpMockServer/0.0.1-SNAPSHOT/httpMockServer-0.0.1-SNAPSHOT.jar src/main/java/com/mock/HttpResponseMockApplication  
  
This server is a BLACK BOX, user need not to know the language it is implemented, since they were never going to make changes to it.
Whereas end user will be going to create request config file for each different request at the same system it is running.

Similarly the client side also no code changes required. There is only three config changes required at client.
1. Mock server host
2. Mock server port
3. Request config file location at Mock server in HTTP Header.

For each request has a request config file location at mock server.
Mock server loads this config, and it understads the what response to be returned, response headers to be added for the that request via config file and build those 
and return it back to the request. 
  
Request config file which contains mainly three sections:
1.   serverConfig    : Server related properties, 
     e.g.) if you want delay the response for sometime, or schedule a callback, or every Nth request should refer different payload.
2.   responseConfig  : Response related properties, e.g.) Response content file location, response headers
3.   callbackConfig  : If there is a callback needed for your request, those information goes here.
  
  
<PRE>  
{
  "responseConfig": {
    "headers": {
      "Content-Type": "application/json"
    },
    "responseFilePath": "/tmp/httpResponseMock/helloworld.response.config"
  },
  "serverConfig" : {},
  "callbackConfig" : {}
}
</PRE>  

<code>responseFilePath</code> is the file that contains payload to be returned for the request.

So all we need now is to add request config file path as header "Http-Mock-Config-File" like below
  
<PRE>   
  curl -X GET \
  http://localhost:8080/helloworld \
  -H 'http-mock-config-file: /tmp/httpResponseMock/helloworld.config' \
  
  
  curl -X GET \
  http://localhost:8080/healthcheck \
  -H 'postman-token: d0855a3f-ceef-4496-698e-6da0835c0c91' \
  
  curl -X GET \
  http://localhost:8080/version \
  -H 'postman-token: 91e7b8a4-422e-13c1-589e-7aa8d85fc28b'
</PRE>    
  
In future the request config file configuration will support callback payload, proxy configuration and many more.
The request config file is json file and its payload syntax is defined in this project.

Each request config file will create a session object to store session specific information, e.g.) You want to return different response for every fourth request.

In future we will add template engine to fill dynamic values in the response via request OR from DB (file system specific).
But I will ensure only configuration changes and no code changes at either side.
   
[![Run in Postman](https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/ed54024760a89d8b405e)
