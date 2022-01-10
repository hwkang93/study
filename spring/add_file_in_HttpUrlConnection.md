# HttpUrlConnection 객체에 파일 넣기 예제 소스코드

```java
public class HttpFileUtils {
    private static final String NEW_LINE = "\r\n";
    private static final String TWO_HYPHEN = "--";

    public static void setFileInfoToConnection(HttpURLConnection connection, MultipartFile multipartFile, String boundary) throws IOException {
        DataOutputStream dos = new DataOutputStream(connection.getOutputStream());

        final String filename = multipartFile.getOriginalFilename();
        final String description = multipartFile.getResource().getDescription();

        dos.writeBytes(TWO_HYPHEN + boundary + NEW_LINE);
        dos.writeBytes("Content-Disposition: form-data; name=\"description\"" + NEW_LINE + NEW_LINE);
        dos.writeBytes(description + NEW_LINE);

        dos.writeBytes(TWO_HYPHEN + boundary + TWO_HYPHEN);
        dos.writeBytes("Content-Disposition: form-data; name=\"file\"; filename=\"" + filename + "\"" + NEW_LINE + NEW_LINE);
        dos.write(multipartFile.getBytes());
        dos.writeBytes(NEW_LINE);

        dos.writeBytes(TWO_HYPHEN + boundary + TWO_HYPHEN + NEW_LINE);
        dos.flush();
    }
}
```

