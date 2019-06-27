# eclipse

## Referenced file contains errors (http://www.springframework.org/schema/beans/spring-beans-3.0.xsd). For more information, right click on the

 message in the Problems View and select "Show Details..."

这是由于缓冲造成的,里面有相关的配置和当前定义冲突,解决办法是打开Preferences -> General -> Network Connections -> Cache,然后删除里面的缓存信息.