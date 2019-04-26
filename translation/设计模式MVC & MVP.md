# 设计模式MVC & MVP

## MVC

## MVP

问：网络请求应在放在哪一层？

答：网络请求得到的是Model的内容，从类的设计角度，所以应该是Model类做这个事情。比如User发起网络数据来请求本身的内容。

MVP是MVC的衍生，在MVP中，Model和View是通过接口来进行交互