## Debugging
Append to command line arg
`-Dtests.additional.jvmargs="-Xdebug -Xnoagent -Xrunjdwp:transport=dt_socket,server=y,suspend=y,address=9000"`

## ByteBuffer
Need rewound before being read

## File
#### Read from file
```java
try {
	Path path = Paths.get('dir','to');
    File file = new File(path);
    InputStream inStream = new FileInputStream(file);
    stdOut = new BufferedReader(new InputStreamReader(process.getInputStream()));

    String line = null;
    while ((line = stdOut.readLine()) != null) {
        LOG.error(line);
    }
}
```
## Process
### Read from process
```java
try {
    Process process = Runtime.getRuntime().exec(command);
    stdOut = new BufferedReader(new InputStreamReader(process.getInputStream()));
    stdErr = new BufferedReader(new InputStreamReader(process.getErrorStream()));


    String line = null;
    while ((line = stdOut.readLine()) != null) {
        LOG.error(line);
    }
    while ((line = stdErr.readLine()) != null) {
        LOG.error(line);
    }
}
```