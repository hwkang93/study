
### pom.xml

```xml
<dependency>
    <groupId>org.mitre.dsmiley.httpproxy</groupId>
    <artifactId>smiley-http-proxy-servlet</artifactId>
    <version>1.12.1</version>
</dependency>
```

### ApiGatewayServlet.java

```java
package apigw;

import egovframework.com.cmm.service.EgovProperties;
import lombok.extern.slf4j.Slf4j;

import org.apache.http.HttpRequest;
import org.apache.http.HttpResponse;
import org.apache.http.client.utils.URIUtils;
import org.mitre.dsmiley.httpproxy.ProxyServlet;
import org.springframework.http.HttpHeaders;
import org.springframework.util.StringUtils;

import javax.servlet.ServletException;
import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import java.io.IOException;
import java.io.InputStream;
import java.net.URI;

@Slf4j
public class ApiGatewayServlet extends ProxyServlet {
	private static final long serialVersionUID = 1L;
	private final String TOKEN_EXPIRED_MESSAGE = "{\"message\":\"expired\"}";

	@Override
	protected void copyRequestHeaders(HttpServletRequest servletRequest, HttpRequest proxyRequest) {
		String authorization = this.getAuthorization(cookie);
		proxyRequest.setHeader(HttpHeaders.AUTHORIZATION, authorization);

		super.copyRequestHeaders(servletRequest, proxyRequest);
	}

	@Override
	protected HttpResponse doExecute(HttpServletRequest servletRequest, HttpServletResponse servletResponse,
			HttpRequest proxyRequest) throws IOException {

		return super.doExecute(servletRequest, servletResponse, proxyRequest);
	}



	@Override
    protected void initTarget() throws ServletException {
        this.targetUri = EgovProperties.getPropertyCommon("api.url");
        if (this.targetUri == null) {
            throw new ServletException("targetUri is required.");
        } else {
            try {
                this.targetUriObj = new URI(this.targetUri);
            } catch (Exception var2) {
                throw new ServletException("Trying to process targetUri init parameter: " + var2, var2);
            }

            this.targetHost = URIUtils.extractHost(this.targetUriObj);
        }
    }



	@Override
	protected void copyResponseEntity(HttpResponse proxyResponse, HttpServletResponse servletResponse,
			HttpRequest proxyRequest, HttpServletRequest servletRequest) throws IOException {
		super.copyResponseEntity(proxyResponse, servletResponse, proxyRequest, servletRequest);

		String responseMessage = this.getResponseMessage(proxyResponse);
		log.debug(" :: Http Status Code => " + proxyResponse.getStatusLine().getStatusCode());

		if(TOKEN_EXPIRED_MESSAGE.equals(responseMessage)) {
			log.debug("Token is expired. Reissue token");
			refreshToken();
		}
	}

	private Cookie getCookie(HttpServletRequest request, String cookieName) {
		for(Cookie cookie : request.getCookies()) {
			if(cookieName.equals(cookie.getName())) {
				return cookie;
			}
		}

		return null;
	}

	private String getAuthorization(Cookie authCookie) {
		if(authCookie == null) {
			return "";
		}

		String token = "Bearer " + authCookie.getValue();

		return token;
	}

	private String getResponseMessage(HttpResponse response) {
		String message = "";
		try (InputStream is = response.getEntity().getContent())  {
			message = is.toString();
		} catch (UnsupportedOperationException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		}

		return message;
	}
}

```