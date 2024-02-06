## Execute main method
```kotlin
task runLogging(type: JavaExec) {  
	group = "Execution"  
	description = "Run the main class with JavaExecTask"  
	classpath = sourceSets.main.runtimeClasspath  
	main = 'sandbox.cooldirectory.CoolClass' // package path  
}
```
