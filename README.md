# High Memory Usage in Java 21

This project will showcase the increased memory usage in **Java 21** compared to **Java 8**, using the *Spring Pet Clinic* sample Java project from Spring. We will build two Docker images—one with Java 8 and another with Java 21—and run them to compare memory usage. The images will include **jattach** to track native memory usage. Since the base OS is Alpine, we cannot run `jcmd` directly, hence the need for **jattach**.

## How to Reproduce the Issue

1. **Build the Docker images** using the following commands:

   ```bash
   docker build -t java21-spring -f DockerfileJava21 .
   docker build -t java8-spring -f DockerfileJava8 .
   ```

2. **Run two containers** with the following commands:

    ```bash 
    docker run --memory=1g -p 8081:8080 java21-spring java -XX:NativeMemoryTracking=summary -Xms256m -Xmx512m -jar /app/spring-petclinic.jar
    docker run --memory=1g -p 8080:8080 java8-spring java -XX:NativeMemoryTracking=summary -Xms256m -Xmx512m -jar /app/spring-petclinic.jar
    ```

3. **Monitor metrics** using the `docker stats` command.

4. **Track memory usage** by running the following commands:

    ```bash
    docker exec -it <CONTAINER_ID> sh
    cd /
    ./jattach 1 jcmd VM.native_memory
    ```

## Docker Stats Output

![Docker Stats](./Docker%20Stats.png "Docker Stats")

## Native Memory Tracking Output

### Java 8

```bash
Native Memory Tracking:

Total: reserved=1929874KB, committed=393190KB
-                 Java Heap (reserved=524288KB, committed=262208KB)
                            (mmap: reserved=524288KB, committed=262208KB) 
 
-                     Class (reserved=1099014KB, committed=57566KB)
                            (classes #10367)
                            (malloc=1286KB #10537) 
                            (mmap: reserved=1097728KB, committed=56280KB) 
 
-                    Thread (reserved=31968KB, committed=31968KB)
                            (thread #32)
                            (stack: reserved=31829KB, committed=31829KB)
                            (malloc=106KB #186) 
                            (arena=32KB #58)
 
-                      Code (reserved=253118KB, committed=20810KB)
                            (malloc=3518KB #5282) 
                            (mmap: reserved=249600KB, committed=17292KB) 
 
-                        GC (reserved=1738KB, committed=890KB)
                            (malloc=26KB #174) 
                            (mmap: reserved=1712KB, committed=864KB) 
 
-                  Compiler (reserved=159KB, committed=159KB)
                            (malloc=24KB #469) 
                            (arena=135KB #7)
 
-                  Internal (reserved=1844KB, committed=1844KB)
                            (malloc=1812KB #12019) 
                            (mmap: reserved=32KB, committed=32KB) 
 
-                    Symbol (reserved=15183KB, committed=15183KB)
                            (malloc=11915KB #122017) 
                            (arena=3268KB #1)
 
-    Native Memory Tracking (reserved=2366KB, committed=2366KB)
                            (malloc=7KB #90) 
                            (tracking overhead=2359KB)
 
-               Arena Chunk (reserved=196KB, committed=196KB)
                            (malloc=196KB) 

```

### Java 21

```bash
Native Memory Tracking:

(Omitting categories weighting less than 1KB)

Total: reserved=2044186KB, committed=424038KB
       malloc: 42596KB #337605
       mmap:   reserved=2001590KB, committed=381442KB

-                 Java Heap (reserved=524288KB, committed=262208KB)
                            (mmap: reserved=524288KB, committed=262208KB) 
 
-                     Class (reserved=1050068KB, committed=13012KB)
                            (classes #17813)
                            (  instance classes #16553, array classes #1260)
                            (malloc=1492KB #45190) (peak=1496KB #45058) 
                            (mmap: reserved=1048576KB, committed=11520KB) 
                            (  Metadata:   )
                            (    reserved=131072KB, committed=69376KB)
                            (    used=68992KB)
                            (    waste=384KB =0.55%)
                            (  Class space:)
                            (    reserved=1048576KB, committed=11520KB)
                            (    used=11130KB)
                            (    waste=390KB =3.38%)
 
-                    Thread (reserved=31927KB, committed=2831KB)
                            (thread #32)
                            (stack: reserved=31826KB, committed=2730KB)
                            (malloc=66KB #191) (peak=79KB #207) 
                            (arena=34KB #60) (peak=2322KB #38)
 
-                      Code (reserved=249888KB, committed=24908KB)
                            (malloc=2200KB #9067) (at peak) 
                            (mmap: reserved=247688KB, committed=22708KB) 
 
-                        GC (reserved=1734KB, committed=882KB)
                            (malloc=22KB #80) (peak=250KB #137) 
                            (mmap: reserved=1712KB, committed=860KB) 
 
-                  Compiler (reserved=238KB, committed=238KB)
                            (malloc=74KB #835) (peak=117KB #857) 
                            (arena=164KB #4) (peak=53061KB #30)
 
-                  Internal (reserved=863KB, committed=863KB)
                            (malloc=827KB #25496) (peak=835KB #25298) 
                            (mmap: reserved=36KB, committed=36KB) 
 
-                     Other (reserved=24KB, committed=24KB)
                            (malloc=24KB #3) (peak=58KB #5) 
 
-                    Symbol (reserved=30116KB, committed=30116KB)
                            (malloc=28254KB #234525) (at peak) 
                            (arena=1862KB #1) (at peak)
 
-    Native Memory Tracking (reserved=5362KB, committed=5362KB)
                            (malloc=87KB #1569) (peak=87KB #1568) 
                            (tracking overhead=5275KB)
 
-        Shared class space (reserved=16384KB, committed=11996KB, readonly=0KB)
                            (mmap: reserved=16384KB, committed=11996KB) 
 
-               Arena Chunk (reserved=2KB, committed=2KB)
                            (malloc=2KB #116) (peak=55748KB #1365) 
 
-                    Module (reserved=127KB, committed=127KB)
                            (malloc=127KB #3158) (at peak) 
 
-                 Safepoint (reserved=8KB, committed=8KB)
                            (mmap: reserved=8KB, committed=8KB) 
 
-           Synchronization (reserved=1751KB, committed=1751KB)
                            (malloc=1751KB #17227) (at peak) 
 
-            Serviceability (reserved=17KB, committed=17KB)
                            (malloc=17KB #9) (peak=17KB #11) 
 
-                 Metaspace (reserved=131387KB, committed=69691KB)
                            (malloc=315KB #118) (at peak) 
                            (mmap: reserved=131072KB, committed=69376KB) 
 
-      String Deduplication (reserved=1KB, committed=1KB)
                            (malloc=1KB #8) (at peak) 

```