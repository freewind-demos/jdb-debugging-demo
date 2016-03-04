Remote debugging with command line tool `jdb`
=============================================

Normally we use IDE for remote debugging a Java application, but it's also possible to use a command line tool to do this.

There is a `jdb` tool included in the JDK: http://docs.oracle.com/javase/7/docs/technotes/tools/windows/jdb.html

Server side
------------

We should compile and run the Java file first.

When compiling the Java source, we need to pass `-g` to keep local varible names for debugging (otherwise the names will be changed to some shorter names).

```
javac -g HelloWorld.java
```

Then run it with enabling remote debugging:

```
java -Xdebug -Xrunjdwp:transport=dt_socket,server=y,address=9999 HelloWorld
```

It:

- listenning on port `9999`
- won't run any code until client connect (`server=y`)

Client side
-----------

```
$ jdb -attach 9999
Set uncaught java.lang.Throwable
Set deferred uncaught java.lang.Throwable
Initializing jdb ...
>
VM Started: No frames on the current call stack

```

Also we can speicify the ip to connect a remote host:

```
jdb -attach <some-ip>:<port>
```

Set a breakpoint:

```
main[1] stop at HelloWorld:4
Deferring breakpoint HelloWorld:4.
It will be set after the class is loaded.
```

Run to the (next) breakpoint:

```
main[1] next
> Set deferred breakpoint HelloWorld:4

Breakpoint hit: "thread=main", HelloWorld.main(), line=4 bci=3
4           name = "BBB";
```

Print a varible value:

```
main[1] print name
 name = "AAA"
```

Run to the next line:

```
main[1] step
>
Step completed: "thread=main", HelloWorld.main(), line=5 bci=6
5           name = "CCC";
```

Print the `name` variable again:

```
main[1] print name
 name = "BBB"
```

Next breakpoint (actually run to end now):

```
main[1] next
>
Step completed: "thread=main", HelloWorld.main(), line=6 bci=9
6           System.out.println(name);
```

Show threads:

```
main[1] threads
Group system:
  (java.lang.ref.Reference$ReferenceHandler)0x14d Reference Handler cond. waiting
  (java.lang.ref.Finalizer$FinalizerThread)0x14c  Finalizer         cond. waiting
  (java.lang.Thread)0x14b                         Signal Dispatcher running
Group main:
  (java.lang.Thread)0x1                           main              running
main[1]
```



