rdhttp
======

RDHTTP is HTTP client library for iOS. It is based on Apple's NSURLConnection but much easier to use and 
ready for real world tasks. 

The library was designed as a simple, self-contained solution (just RDHTTP.h and RDHTTP.m). 
It is reasonably low-level and does not contain any features unrelated to HTTP (JSON, XML, SOAP, ...).

The API is inspired by now unsupported ASIHTTPRequest with few conceptual changes: blocks instead of delegates/selectors,
request/operation/response separation, complete absense of synchronous calls.

## TODO 
Currently the library is in development. Following tasks are active: 

* More unit tests 
* Actual usage examples
* <del>Tested and good 'cancel' method</del>
* <del>RFC 2616 redirect behaviour</del>
* <del>Generating basic authorizaion header</del>
* <del>better GCD / threading options</del>
* <del>NSOperation methods</del>
* <del>Submit any binary to App Store (private API test)</del>
* <del>Documentation</del>
* Use library in production code for 100000+ users (done with small subset of features)



## Features

* Blocks-oriented asynchronous API
* Easy access to HTTP request / response fields 
* HTTP errors detection
* Downloading data to memory or file 
* HTTP POST for key-value data (urlencoded)
* HTTP POST for files (multipart)
* Setting request body to data / file (HTTP PUT, PROPFIND, ...)
* All kinds of HTTP authorization (Basic, Digest, ...)
* Trust-callback for self-signed SSL certificates


## Requirements 

* iOS 5+


## Installation 

1. Copy RDHTTP.h and RDHTTP.m from RDHTTP directory to your Xcode project. 
2. Add MobileCoreServices.framework



## Documentation 

RDHTTP documentation is written as appledoc comments in RDHTTP.h file.



## Usage Example

Simple HTTP GET:

```objective-c
RDHTTPRequest *request = [RDHTTPRequest getRequestWithURLString:@"http://osric.readdle.com/tests/ok.html"];
[request startWithCompletionHandler:^(RDHTTPResponse *response) {
    if (response.error)
        NSLog(@"error: %@", response.error) 
    else
		NSLog(@"response text: %@", response.responseString);
}];
```

Form-data compatible HTTP POST:

```objective-c
RDHTTPRequest *request = [RDHTTPRequest postRequestWithURLString:@"http://osric.readdle.com/tests/post-values.php"];

[[request formPost] setPostValue:@"value" forKey:@"fieldName"];
[[request formPost] setPostValue:@"anotherValue" forKey:@"anotherField"];

[request startWithCompletionHandler:^(RDHTTPResponse *response) {
    if (response.error)
        NSLog(@"error: %@", response.error) 
    else
		NSLog(@"response text: %@", response.responseString);
        
}];
```

Saving file: 

```objective-c
RDHTTPRequest *request = [RDHTTPRequest getRequestWithURLString:@"http://www.ubuntu.com/start-download?distro=desktop&bits=32&release=latest"];

request.shouldSaveResponseToFile = YES;

[request setDownloadProgressHandler:^(float progress) {
    NSString *progressString = [NSString stringWithFormat:@"%f", progress];
    label.text = progressString;
    NSLog(@"%@", progressString);
}];
    
RDHTTPOperation *operation = [request startWithCompletionHandler:^(RDHTTPResponse *response) {

    if (response.error) {
        NSLog(@"error: %@", response.error);
        return;
    }
        
    NSURL *dest = [[[NSFileManager defaultManager] URLsForDirectory:NSDocumentDirectory
                                                          inDomains:NSUserDomainMask] objectAtIndex:0];
    
    dest = [dest URLByAppendingPathComponent:@"latest-ubuntu.iso"];
    
    [response moveResponseFileToURL:dest
        withIntermediateDirectories:NO 
                              error:nil];
    
    NSLog(@"saved file to latest-ubuntu.iso");
    
}];
```

