## Writing to file

```java
@Test  
public void testWrite(  
        @TempDir Path nestedPath,  
        @TempDir Path simplePath  
) throws Exception {
	// prepare  
	String fileName = "text.txt";  
	Path newNestedPath = nestedPath.resolve("dirA/dirB");  
	Files.createDirectories(newNestedPath);  
	Files.createFile(newNestedPath.resolve(fileName));  
	
	Files.createFile(simplePath.resolve(fileName));  
	Main main = new Main();
	  
	main.writeToFiles(nestedPath.toString() + "/", simplePath.toString() + "/");
}
```