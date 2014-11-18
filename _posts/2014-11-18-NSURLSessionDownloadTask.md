---
layout: post
title: "NSURLSessionDownloadTask save custome file path"
date: 2014-11-18 18:54
comments: false
---

1) サンプルソースコード
============

{% highlight c linenos %}
- (void)downloadFile: (NSURL *)url filePath: (NSString *)filePath process:(void(^)(bool success)) process
{
    if (![FileUtils fileExists:filePath]) {
        [FileUtils mkdirIfNotEist:filePath];
    }
    
    NSURLSessionConfiguration *configuration = [NSURLSessionConfiguration defaultSessionConfiguration];
    AFURLSessionManager *manager = [[AFURLSessionManager alloc] initWithSessionConfiguration:configuration];
    NSURLRequest *request = [NSURLRequest requestWithURL:url];
    
    NSURLSessionDownloadTask *downloadTask = [manager downloadTaskWithRequest:request progress:nil destination:^NSURL *(NSURL *targetPath, NSURLResponse *response) {
        NSURL *documentsDirectoryURL = [NSURL fileURLWithPath:filePath isDirectory:YES];
        return [documentsDirectoryURL URLByAppendingPathComponent:[response suggestedFilename]];
    } completionHandler:^(NSURLResponse *response, NSURL *filePathTmp, NSError *error) {
        process(YES);
    }];
    
    [downloadTask resume];
}
{% endhighlight %}

**documentsDirectoryURL**はcustom file pathです。
