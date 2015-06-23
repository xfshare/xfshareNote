#Objc-xfshare


* [线程阻塞](#线程阻塞)
* [Table](#Table)
* [第三方软件](#thirdParty)


## <a name = "线程阻塞">线程阻塞</a>
		
	while (1)
	{
		[[NSRunLoop currentRunLoop] runMode:NSDefaultRunLoopMode beforeDate:[NSDate distantFuture]];       
	}
	
## <a name = "Table">Table</a>

		UITapGestureRecognizer *tapGesture = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(resignAllFirstResponder)];
	    tapGesture.delegate = self;
	    [upImageTableView addGestureRecognizer:tapGesture];
	    
		- (BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer shouldReceiveTouch:(UITouch *)touch
		{
			if ([NSStringFromClass([touch.view class]) isEqualToString:@"UITableViewCellContentView"]) {
       			 return NO;
    		}
    		return  YES;
		}
		
		UITableViewStyleGrouped
		1.默认存在footer,高度大约在20pix
		如果 height 返回0,会使用默认高度
		2.group类型,底端会自动存在灰条,要不想看到他,把它多余的灰条,放到屏幕外边
		
## <a name = "">获取相对屏幕位置</a>
	
	UIWindow * window=[[[UIApplication sharedApplication] delegate] window];
    CGRect rect=[bView convertRect: bView.bounds toView:window];

## <a name = "thirdParty">第三方软件</a>

1. Facebook
	
	
	Facebook头像
	
	* You can render the current profile photo for any object by adding the suffix /picture to the object URL. For example, this will render your public profile photo
	
		<img src="https://graph.facebook.com/100002637692572/picture"/> 
	
			<img src="https://graph.facebook.com/100002637692572/picture"/> 

	* You can specify the picture size you want with the type argument, which should be one of square (50x50), small (50 pixels wide, variable height), normal (100 pixels wide, variable height), and large (about 200 pixels wide, variable height)

		<img src="https://graph.facebook.com/100002637692572/picture?type=large">

			<img src="https://graph.facebook.com/100002637692572/picture?type=large">

	* If you need a picture to be returned over a secure connection, you can set the return_ssl_resources argument to 1

		<img src="https://graph.facebook.com/100002637692572/picture?return_ssl_resources=1">
	
			<img src="https://graph.facebook.com/100002637692572/picture?return_ssl_resources=1">


