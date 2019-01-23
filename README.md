
# Proxy SDK usage instructions  
[![stable](https://img.shields.io/badge/stable-stable-green.svg)](https://github.com/snail007/goproxy-sdk/) [LICENSE:SSPL](/LICENSE) [![download_count](https://img.shields.io/github/downloads/snail007/goproxy-sdk/total.svg?style=plastic)](https://github.com/snail007/goproxy-sdk/releases) [![download](https://img.shields.io/github/release/snail007/goproxy-sdk.svg?style=plastic)](https://github.com/snail007/goproxy-sdk/releases)  
  
[Download](https://github.com/snail007/goproxy-sdk/releases)  

The following platforms are supported:
- Android, `.arr` library
- IOS, `.framework` library
- Windows, `.dll` library
- Linux, `.so` library
- MacOS, `.dylib` library

proxy uses gombile to compile a copy of go code into an sdk library that can be called directly from the android and ios platforms, 
It also provides sdk support for linux and windows, MacOS,based on these libraries, APP developers can easily develop various forms of proxy tools.    

# The following sub-platform describes the use of the SDK

## Android SDK
  
The sdk form provided in the Android system is a suffix.aar class library files, development time only need to ARR class library files into the android project can be. 

### Android-SDK usage examples

#### 1. Importing packages

```java
import snail007.proxy.Porxy
```

#### 2. Start a service

```java
String serviceID="http01";// Here serviceID is a custom unique identifier string, ensure that each start of the service is not the same
String serviceArgs="http -p :8080";
String err=Proxy.start(serviceID,serviceArgs);
if (!err.isEmpty()){
    // Failed to start
    System.out.println("start fail,error:"+err);
}else{
    // Successful launch
}
```

#### 3. Stop service

```java
String serviceID="http01";
Proxy.stop(serviceID);
// Stop over.

```

## IOS SDK
   
The sdk form provided in IOS is a suffix.framework Class Library folder, the development of the class library files only need to be introduced into the project, and then call the method.  

### IOS-SDK usage examples

#### Importing packages

```objc
#import <Proxy/Proxy.objc.h>
```

#### 2. Start a service

```objc
-(IBAction)doStart:(id)sender
{
	// Here serviceID is a custom unique identifier string, guaranteed to be different for each started service
	NSString *serviceID = @"http01";
    NSString *serviceArgs = @"http -p :8080";
    NSString *error = ProxyStart(serviceID,serviceArgs);
    
    if (error != nil && error.length > 0)
    {
        NSLog(@"start error %@",error);
    }else{
        NSLog(@"Successful launch");
    }
}
```

#### 3. Stop service

```objc
-(IBAction)doStop:(id)sender
{
    NSString *serviceID = @"http01";
    ProxyStop(serviceID);
    // Stop over.
}
```


## Windows SDK
 
The sdk form provided in the Windows system is a suffix.when developing,you only need to load the dll class library file, and then call the method. 

### Windows-SDK usage instance

C++examples, do not need to include header files, only need to load proxy-sdk.dll can, ieshims.dll needed and proxy-sdk.dll together.  
Author: [yjbdsky](https://github.com/yjbdsky)     

```cpp
#include <stdio.h>
#include<stdlib.h>
#include <string.h>
#include<pthread.h>
#include<Windows.h>

#ifdef __cplusplus
extern "C" {
#endif

typedef char *(*GOSTART)(char *s);
typedef char *(*GOSTOP)(char *s);
typedef int(*GOISRUN)(char *s);
HMODULE GODLL = LoadLibrary("proxy-sdk.dll");

char * Start(char * p0,char * p1)
{
	if (GODLL != NULL)
	{
		GOSTART gostart = *(GOSTART)(GetProcAddress(GODLL, "Start"));
		if (gostart != NULL){
			printf("%s:%s\n",p0, p1);
			char *ret = gostart(p0,p1);
			return ret;
		}
	}
	return "Cannot Find dll";
}
char * Stop(char * p)
{
	if (GODLL != NULL)
	{
		GOSTOP gostop = *(GOSTOP)(GetProcAddress(GODLL, "Stop"));
		if (gostop != NULL){
			printf("%s\n", p);
			char *ret = gostop(p);
			return ret;
		}
	}
	return "Cannot Find dll";
}

int main()
{
	// Here p0 is a custom unique identifier string, guaranteed to be different for each started service
	char *p0 = "http01";
	char *p1 = "http -t tcp -p :38080";
	printf("This is demo application.\n");
	// Start the service, returns an empty string description starts successfully;returns a non-empty string description fails to start, the returned string is the cause of the error
	printf("start result %s\n", Start(p0,p1));
	// Stop service, no return value
	Stop(p0);
	return 0;
}


#ifdef __cplusplus
}
#endif
```

C++ Example 2, move step：[GoProxyForC](https://github.com/SuperPowerLF2/GoProxyForC)   

## Linux SDK
  
The sdk form provided in the Linux system is a suffix.so library files, development time only need to load the so library, you can call the method.

### Linux-SDK usage examples
The SDK that is used below Linux is the so file i.e. libproxy-sdk. so,write a simple example of a C program that calls the method inside the so library. 

`vi test-proxy.c`  

```c
#include <stdio.h>
#include "libproxy-sdk.h"

int main() {
     printf("This is demo application.\n");
	 // Here p0 is a custom unique identifier string, guaranteed to be different for each started service
	 char *p0 = "http01";
     char *p1 = "http -t tcp -p :38080";
     // Start the service, returns an empty string description starts successfully;returns a non-empty string description fails to start, the returned string is the cause of the error
     printf("start result %s\n",Start(p0,p1));
     // Stop service, no return value
     Stop(p0);
     return 0;
}
```

#### Compile test-proxy.c ####  
`export LD_LIBRARY_PATH=./ && gcc -o test-proxy test-proxy.c libproxy-sdk.so`  

#### Execution ####  
`./test-proxy`  

## MacOS SDK

The sdk form provided in the MacOS system is a suffix.dylib library files, development time only need to load so library, you can call the method.   

### MacOS-SDK usage instance
The sdk used below for MacOS is the dylib file i.e. libproxy-sdk.dylib, write a simple Obj-C program example, call dylib library inside the method.

```objc
#import "libproxy-sdk.h"
-(IBAction)doStart:(id)sender
{
    char *result =  Start("http01", "http -t tcp -p :38080");
    
    if (result)
    {
        printf("started");
    }else{
        printf("not started");
    }
  
}
-(IBAction)doStop:(id)sender
{
     Stop("http01");

}
```

### About the service

There are 11 types of proxy services:  

```shell
http  
socks  
sps  
tcp  
udp  
bridge  
server  
client  
tbridge  
tserver  
tclient  
```

When the service starts,if there is a service running with the same ID, then the previous service will be stopped and the previous service will be overwritten later.  

So make sure that the first ID parameter is unique every time you start the service.  

The specific usage and parameters of these services can be found in [proxy manual](https://github.com/snail007/goproxy/blob/master/README.md) the SDK service does not support the manual inside: -- daemon and -- forever parameters.


