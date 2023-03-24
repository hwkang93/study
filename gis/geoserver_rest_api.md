# Java 에서 GeoSever 로 API 요청 날리는 예제 소스코드


요청 URL + .format (xml, json 등)


```java
public class GeoServerAPI {

    public static void main(String[] args) {
        Url url = new Url("http://localhost:8080/geoserver/rest/workspaces.json");
        HttpURLConnection conn = url.openConnection();
        conn.setReqeustProperty("Authorization", getGeoServerAuth());
        conn.setRequestMethod("GET");
        conn.setDoInput(true);
        conn.setDoOutput(true);

        InputStream is = conn.getInputStream();
        
    }
    
    public static String getGeoServerAuth() {
        String auth = GEOSERVER_ID + ":" + GEOSERVER_PASSWORD;
        byte[] encodedAuth = Base64.getEncoder().encode(auth.getBytes(StandardCharsets.UTF_8));
        String authHeaderValue = "basic " + new String(encodedAuth);

        return authHeaderValue;
    }
}
```