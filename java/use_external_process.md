# Java 에서 외부 프로세스 실행하기

## java.lang.Process 예시

```java
class Executor {
    public void exec(String inputFileName, String outputFileName) throws IOException, InterruptedException {
        Runtime runtime = Runtime.getRuntime();
        Process process = null;
        BufferedReader bufferedReader = null;

        try {
            String command = "cp " + inputFileName + " " + outputFileName;
            String[] commands = {"/bin/sh", "-c", command};

            process = runtime.exec(commands);
            process.waitFor(10000, TimeUnit.MILLISECONDS);

            bufferedReader = new BufferedReader(new InputStreamReader(process.getInputStream()));
            String response;
            while((response = bufferedReader.readLine()) != null) {
                logger.info(response);
            }

            bufferedReader = new BufferedReader(new InputStreamReader(process.getErrorStream()));
            String errResponse;
            while((errResponse = bufferedReader.readLine()) != null) {
                logger.error(errResponse);
            }
        } catch (IOException e) {
            e.printStackTrace();
            throw e;
        } finally {
            bufferedReader.close();
            process.destroy();
        }
    }
}
```

## com.jcraft.jsch 예시

```java
class Executor {
    public void exec(String inputFileName, String outputFileName) {
        Session jschSession = null;

        try {
            JSch jsch = new JSch();
            jschSession = jsch.getSession("userId", "1.1.1.1", "8080");

            // authenticate using password
            jschSession.setPassword("userPassword");
            // 10 seconds timeout session
            jschSession.connect(10000);

            ChannelExec channelExec = (ChannelExec) jschSession.openChannel("exec");

            String commandString = "cp " + inputFileName + " " + outputFileName;
            
            // run a shell script
            channelExec.setCommand(commandString);
            InputStream in = channelExec.getExtInputStream();

            // 5 seconds timeout channel
            channelExec.connect(cps.REFINESERVICE_CHANNEL_TIMEOUT);

            // read the result from remote server
            byte[] tmp = new byte[1024];
            do {
                while (in.available() > 0) {
                    int i = in.read(tmp, 0, 1024);
                    if (i < 0)
                        break;
                    logger.debug(new String(tmp, 0, i, "euc-kr"));
                }
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException ee) {
                    logger.debug("Exception occured at sleep.");
                }
            } while (!channelExec.isClosed() || in.available() > 0);

            channelExec.disconnect();
        } catch (JSchException e) {
            throw e;
        } catch (IOException e) {
            throw e;
        } catch (Exception e) {
            throw e;
        } finally {
            if (jschSession != null) {
                jschSession.disconnect();
            }
        }
    }
}

```

## Reference

https://d2.naver.com/helloworld/1113548

