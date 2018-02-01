# Fixing Service Hosting Security Errors

When self-hosting services, especially in development scenarios using the development service host, there are several reasons why one might experience security errors. This usually has to do with the environment already having HTTP handlers listening on port 80, such as a development web server or IIS being active on a developer’s machine.

## General Security Errors

There are several ways to fix general security errors that indicate a conflicting HTTP listener. It is possible to configure the system so certain URLs are excluded by other listeners. This is mostly overkill however. The easiest way to fix these problems is to simply run Visual Studio with Administrator rights, which simply allows taking over those URLs while the development host is up and running, with no ill effects or system changes once the host shuts down. 

## Skype Problems

Sometimes, running Visual Studio as Admin does not fix the problem. We have discovered that this is often due to Skype running on the same machine (this problem may or may not be Windows 8 specific… we have only seen it on Windows 8).

Here is an example of what this may look like: 

![](Fixing%20Service%20Host%20Security%20Errors/Fixing%20Service%20Hosting%20Security%20Errors1.jpg)

If this occurs while already running as Admin, or on a machine that seemingly shouldn’t have anything conflicting running, you can run the following command at the command prompt to find out a bit more:

```
netstat -o -n -a | findstr 0.0:80 
```

This will give you the Process ID of what is running on port 80.  Armed with that information you can look in Task Manager to identify the culprit. The reason is likely that Skype now defaults to using port 80 and 443 for incoming connections. You can turn it off (it requires a restart of Skype) in the options:

![](Fixing%20Service%20Host%20Security%20Errors/Fixing%20Service%20Hosting%20Security%20Errors2.jpg)

Voila! This should fix the problem.
