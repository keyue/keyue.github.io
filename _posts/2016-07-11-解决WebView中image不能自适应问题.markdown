---
layout: post
title: 解决WebView中image不能自适应问题
date: 2016-07-11 13:12:04.000000000 +09:00
---

#### 写在前面
> 电商组的iOS和Web前端的同事，还在为上周没有解决的图片在WebView不能自适应宽度问题烦恼，然后向我发来SOS的信号。


> 这里记录一下，方便以后遇到时查阅。

![image error](http://o9lammkmn.bkt.clouddn.com/20160711001.png)

> 我们先用chrome打开网页地址

![image error](http://o9lammkmn.bkt.clouddn.com/20160711002.png)

> 查看网页源代码

![image error](http://o9lammkmn.bkt.clouddn.com/20160711004.png)
	
> 可以看出前端应该是没有对自适应代码，应该加上以下代码
> 
	```<meta name="viewport" content="initial-scale=1.0, minimum-scale=0.1, maximum-scale=2.0, user-scalable=yes\"> 
	```
1. 核心代码

	```
	@property (weak, nonatomic) IBOutlet UIWebView *webView;
	@property (nonatomic, assign) BOOL isLoadingFinished;
	@property (nonatomic, strong) NSString *htmlBody;
	
	- (void)viewDidLoad {
    	[super viewDidLoad];
    	// Do any additional setup after loading the view, typically from a nib.
    	[self.webView setScalesPageToFit:YES];
    	[self loadRemoteUrl];
    
    	_isLoadingFinished = NO;
    	[self.webView setHidden:YES];
	}
	
	- (void)loadRemoteUrl
	{
    	NSURLRequest *req = [[NSURLRequest alloc] initWithURL:[NSURL URLWithString:@"http://192.168.4.65/apppage/describe?product_id=208"]];
    	[_webView loadRequest:req];
	}
	
	//#pragma mark - UIWebViewDelegate
	- (void)webViewDidFinishLoad:(UIWebView *)webView
	{
    	if(_isLoadingFinished)
    	{
       		[self.webView setHidden:NO];
        	return;
    	}
    	_htmlBody = [self.webView stringByEvaluatingJavaScriptFromString:@"document.documentElement.innerHTML"];
    	NSString *bodyWidth= [webView stringByEvaluatingJavaScriptFromString:@"document.body.scrollWidth"];
    	int widthOfBody = [bodyWidth intValue];
    	NSString *html = [self htmlAdjustWithPageWidth:widthOfBody
                                              html:_htmlBody
                                           webView:webView];
    	_isLoadingFinished = YES;
    	[self.webView loadHTMLString:html baseURL:nil];
	}

	- (NSString *)htmlAdjustWithPageWidth:(CGFloat )pageWidth
                                 html:(NSString *)html
                              webView:(UIWebView *)webView
	{
    	NSMutableString *str = [NSMutableString stringWithString:html];
    	CGFloat initialScale = webView.frame.size.width/pageWidth;
    	NSString *stringForReplace = [NSString stringWithFormat:@"<meta name=\"viewport\" content=\" initial-scale=%f, minimum-	scale=0.1, maximum-scale=2.0, user-scalable=yes\"></head>",initialScale];
    
    	NSRange range =  NSMakeRange(0, str.length);
    	[str replaceOccurrencesOfString:@"</head>" withString:stringForReplace options:NSLiteralSearch range:range];
    	return str;
	}
	```
	
#### 最后的效果

![image error](http://o9lammkmn.bkt.clouddn.com/20160711003.png)



