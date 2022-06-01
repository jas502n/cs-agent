# 0xagent

CobaltStrike 4.0 - 4.5 Patch

```java
private static final void A() {
    RuntimeMXBean var0 = ManagementFactory.getRuntimeMXBean();
    List var1 = var0.getInputArguments();
    Iterator var2 = var1.iterator();
    while(var2.hasNext()) {
        String var3 = (String)var2.next();
        if (var3 != null && var3.toLowerCase().contains("-javaagent:")) {
            System.exit(0);
        }
    }
}
```

```java
// java.lang.management.RuntimeMXBean#getInputArguments
public java.util.List<String> getInputArguments();

// sun.management.RuntimeImpl#getInputArguments
private List<String> vmArgs = null;

public synchronized List<String> getVmArguments() {
        if (this.vmArgs == null) {
            String[] var1 = this.getVmArguments0();
            List var2 = var1 != null && var1.length != 0 ? Arrays.asList(var1) : Collections.emptyList();
            this.vmArgs = Collections.unmodifiableList(var2);
        }

        return this.vmArgs;
    }
// sun.management.VMManagement#getVmArguments
List<String> getVmArguments();

// sun.management.VMManagementImpl#getVmArguments
public synchronized List<String> getVmArguments() {
        if (this.vmArgs == null) {
            String[] var1 = this.getVmArguments0();
            List var2 = var1 != null && var1.length != 0 ? Arrays.asList(var1) : Collections.emptyList();
            this.vmArgs = Collections.unmodifiableList(var2);
        }

        return this.vmArgs;
    }
```

Patch:
```
// sun.management.VMManagementImpl#getVmArguments
public synchronized List getVmArguments() {
    ArrayList var1 = new ArrayList();
    var1.add("-XX:+AggressiveHeap");
    var1.add("-XX:+UseParallelGC");
    return var1;
}
```

Changed from [CSAgent](https://github.com/Twi1ight/CSAgent). review by [dust-life](https://github.com/dust-life).

The key for 4.5 is not available here, Just a loader.

## features

- Check the file hash from CS official Website
- Patch javaagemt detection
- Patch Authorization
- Patch Checksum8
- Patch profile saving feature, so that your configuration information will not be saved in `.aggressor.prop`, preventing information leakage by countermeasures.

Just that's all.

**Tips**
- Using jdk8 will make the startup time as long as 10-15s
- Versions after using jdk8 will start immediately
- If you want to use the `checksum8` feature, name the profile `c3.profile`.

## Usage
![image](https://user-images.githubusercontent.com/19629138/167308302-f8f89594-73d9-4205-b13c-d188692e9c61.png)

e.g: 4.4 key

Client
```
java -XX:ParallelGCThreads=4 -XX:+AggressiveHeap -XX:+UseParallelGC -Xms512M -Xmx1024M -javaagent:0xagent.jar=5e98194a01c6b48fa582a6a9fcbb92d6 -jar cobaltstrike.jar
```
![image](https://user-images.githubusercontent.com/19629138/167308485-c28a66f9-ba90-43dc-b1eb-70a47f803a39.png)


Teamserver
```
java -XX:ParallelGCThreads=4 -Dcobaltstrike.server_port=59850 -Djavax.net.ssl.keyStore=./xxxx.store -Djavax.net.ssl.keyStorePassword=xxxxxx -server -XX:+AggressiveHeap -XX:+UseParallelGC -javaagent:0xagent.jar=5e98194a01c6b48fa582a6a9fcbb92d6 -classpath ./cobaltstrike.jar server.TeamServer $*
```

