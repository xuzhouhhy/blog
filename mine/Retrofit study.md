#Retrofit
[官网](https://square.github.io/retrofit/)
##介绍
Use annotations to describe the HTTP request:

URL parameter replacement and query parameter support
Object conversion to request body (e.g., JSON, protocol buffers)
Multipart request body and file upload
翻译：使用注解描述HTTP请求：
 1. URL参数替换、支持查询参数
 2. 类转换成请求的类型，如JSON、protocli buffers等
 3. 多部分组成的请求体和文件上传

##API Declaration 
 接口方法的注解和他的参数表明一个请求是怎样被处理的
###REQUEST METHOD
每一个方法必须有一个HTTP主街，用来提供请求方法和relative URL。有五个build-in主街：GET,POST,PUT,DELETE,HEAD。注解中的资源有独特的relative URL
