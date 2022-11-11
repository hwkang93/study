# Request 접속자 정보 조회하는 예제 소스코드

신규 프로젝트할 때마다 사용하는 소스코드인 것 같아서 작성해놓음

### IP

```java
class RequestUtils {
    public String getIp(HttpServletRequest request) {
        String ip = request.getHeader("X-FORWARDED-FOR");
        
        if (ip == null || ip.length() == 0) {
            ip = request.getHeader("Proxy-Client-IP");
        }
        if (ip == null || ip.length() == 0) {
            ip = request.getHeader("WL-Proxy-Client-IP");  // 웹로직
        }
        if (ip == null || ip.length() == 0) {
            ip = request.getRemoteAddr() ;
        }
        
        return ip;
    }
}


```

### Agent
```java
class RequestUtils {
    public static String getAgent(HttpServletRequest request) {
        return request.getHeader("User-Agent");
    }
}
```
### Browser

```java
class RequestUtils {
    public static String getAgent(HttpServletRequest request) {
        String agent = getAgent(request);
        String browser = "";

        if (agent != null) {
            if (agent.indexOf("Trident") > -1) {
                browser = "MSIE";
            } else if (agent.indexOf("Chrome") > -1) {
                browser = "Chrome";
            } else if (agent.indexOf("Opera") > -1) {
                browser = "Opera";
            } else if (agent.indexOf("iPhone") > -1 && agent.indexOf("Mobile") > -1) {
                browser = "iPhone";
            } else if (agent.indexOf("Android") > -1 && agent.indexOf("Mobile") > -1) {
                browser = "Android";
            }
        }
        
        return browser;
    }
}
```