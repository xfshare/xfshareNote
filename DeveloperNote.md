<ul id="tree" class="ztree"></ul>
<article class='markdown-body'>

# Foundation
### NSLog Define
	#ifdef DEBUG
	#define NSLog(FORMAT, ...) fprintf(stderr,"%s:%d\t%s\n",[[[NSString stringWithUTF8String:__FILE__] lastPathComponent] UTF8String], __LINE__, [[NSString stringWithFormat:FORMAT, ##__VA_ARGS__] UTF8String]);
	#else
	#define NSLog(FORMAT, ...) nil
	#endif

### 相机权限
	
	// 相机 授权 说是7.0添加的,但是只在8.0上看到相机,7.0没有看到
    if ([[UIDevice currentDevice] systemVersion].floatValue >= 7.0) {
         if(([AVCaptureDevice authorizationStatusForMediaType:AVMediaTypeVideo] != AVAuthorizationStatusAuthorized)&& ([AVCaptureDevice authorizationStatusForMediaType:AVMediaTypeVideo] != AVAuthorizationStatusNotDetermined))		{
			//没有授权,添加提示信息
        }
    }
    
### 相册权限访问

	-(void)getLibraryLimiteAndDoSomething{
    
    ALAuthorizationStatus author = [ALAssetsLibrary authorizationStatus];
    switch (author) {
        case ALAuthorizationStatusNotDetermined://还没决定是否授权
        {
            [self applicationAccessLibiray];
        }
            break;
        case ALAuthorizationStatusRestricted://不授权访问
        case ALAuthorizationStatusDenied://已经明确否认让此应用授权可
        {
            [self performSelector:@selector(showAlert) withObject:nil afterDelay:1.0];

        }
            break;
        case ALAuthorizationStatusAuthorized://允许访问
        {
            [self libraryAction];

        }
            break;
        default:
            break;
    }
    
	}


	
	//第一次，会弹出询问信息
	
	-(void)applicationAccessLibiray{
    
    //拒绝访问，一般会走这个block
    ALAssetsLibraryAccessFailureBlock failureblock = ^(NSError *myerror) {//虽然说是失败了，一般点Dont't Allow会走这个
        
        ALAuthorizationStatus author = [ALAssetsLibrary authorizationStatus];
        if (author != ALAuthorizationStatusAuthorized) {
            //doNothing
        }else{
            NSLog(@"访问相册权限的逻辑出错了");
            //发到崩溃日志那
            
        }
    };
    
    
    //同意访问了，一般会走这个block
    ALAssetsLibraryGroupsEnumerationResultsBlock
    libraryGroupsEnumeration = ^(ALAssetsGroup* group, BOOL* stop){
        //同意访问了，继续进入下一个vc
        
    };
    
    //第一次访问，系统会自动调用权限的alertView
    ALAssetsLibrary* library = [[ALAssetsLibrary alloc] init];
    [library enumerateGroupsWithTypes:ALAssetsGroupAll
                           usingBlock:libraryGroupsEnumeration
                         failureBlock:failureblock];
	}
	
### EXTERN 
	Example
	.h
	extern NSString * const AFNetworkingReachabilityDidChangeNotification;
	.m
	NSString * const AFNetworkingReachabilityDidChangeNotification = @"com.alamofire.networking.reachability.change";
	
### NSRunLoop
	//线程阻塞
	[[NSRunLoop currentRunLoop] runMode:NSDefaultRunLoopMode beforeDate:[NSDate distantFuture]];


### Emoji Keyboard

	[[UITextInputMode currentInputMode].primaryLanguage isEqualToString:@"emoji"]
	
### Auto Layout

	[tableView registerNib:[UINib nibWithNibName:@"CustomTableViewCell" bundle:nil] forCellReuseIdentifier:@"CustomTableViewCell"];

	//动态获取 Row Height
	CustomTableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"CustomTableViewCell"];
	//涉及到可变高度区域的赋值操作{}
    CGFloat height = [cell.contentView systemLayoutSizeFittingSize:UILayoutFittingCompressedSize].height;
    
### 本地化
	//新建文件名
	Localizable.strings
	"KEY" = "CONTENT";    
	读取
	NSLocalizedString(key, comment)
	添加本地化语言,Localizable.string右侧,勾选
	
	//不同地区,对于数字的分隔 如 1,000,000  
	NSNumberFormatter *numberFormatter = [[NSNumberFormatter alloc] init]; 
	[numberFormatter setNumberStyle:NSNumberFormatterDecimalStyle]; 
	NSString *numberString = [numberFormatter stringFromNumber:@(1000000)];
	
    
### 获取位置信息
	
	#import <CoreLocation/CoreLocation.h>
	#import <MapKit/MapKit.h>
    
    Delegate
    	CLLocationManagerDelegate
	
	//获取经纬度
	@property (strong, nonatomic) CLLocationManager *locationManager;
	
	//创建CLLocationManager对象
    self.locationManager = [[CLLocationManager alloc] init];
    //设置代理
    self.locationManager.delegate = self;

    [self.locationManager startUpdatingLocation];

    
//delegate 方法	

    - (void)locationManager:(CLLocationManager *)manager
	didUpdateToLocation:(CLLocation *)newLocation
		   fromLocation:(CLLocation *)oldLocation
	{
    
    //将经度显示到label上
    self.longitude.text = [NSString stringWithFormat:@"%lf", newLocation.coordinate.longitude];
    //将纬度现实到label上
    self.latitude.text = [NSString stringWithFormat:@"%lf", newLocation.coordinate.latitude];
    
    // 获取当前所在的城市名
    CLGeocoder *geocoder = [[CLGeocoder alloc] init];
    //根据经纬度反向地理编译出地址信息
    [geocoder reverseGeocodeLocation:newLocation completionHandler:^(NSArray *array, NSError *error)
     {
         if (array.count > 0)
         {
             CLPlacemark *placemark = [array objectAtIndex:0];
             
             NSDictionary *dict = placemark.addressDictionary;
             
             NSLog(@"%@",placemark.addressDictionary);
             //将获得的所有信息显示到label上
             self.location.text = placemark.name;
             //获取城市
             NSString *city = placemark.locality;
             if (!city) {
                 //四大直辖市的城市信息无法通过locality获得，只能通过获取省份的方法来获得（如果city为空，则可知为直辖市）
                 city = placemark.administrativeArea;
             }
             NSLog(@"city = %@", city);
             
         }
         else if (error == nil && [array count] == 0)
         {
             NSLog(@"No results were returned.");
         }
         else if (error != nil)
         {
             NSLog(@"An error occurred = %@", error);
         }
     }];
    
    
    //系统会一直更新数据，直到选择停止更新，因为我们只需要获得一次经纬度即可，所以获取之后就停止更新
    [manager stopUpdatingLocation];
	}

    
### Frame && Bounds
	
	frame:
		origin相对于父视图的位置
	bounds:
		origin影响子视图的位置
	
	bounds.size && frame.size 取小的 size 显示
    
### __bridge
	
	__bridge:只做类型转换,不修改内存管理权
	__bridge_retained:类型转换,同时将内存管理权从 ARC 中删除,需要使用CFRelease释放内存
	__bridge_transfer:CF 类型转换成 Object 类型,将内存管理权交给 ARC
    
    
    //设置context
	void dispatch_set_context ( dispatch_object_t object, void *context );
	//获取context
	void * dispatch_get_context ( dispatch_object_t object );
	
	context是一个“void类型指针”，C语言，void型指针可以指向任意类型，就是说，context在这里可以是任意类型的指针
	
### 字符

判断占几个字符

	- (NSInteger)getToInt:(NSString*)strtemp
	{
    	NSStringEncoding enc = CFStringConvertEncodingToNSStringEncoding(kCFStringEncodingGB_18030_2000);

    	NSData* da = [strtemp dataUsingEncoding:enc];
    	return [da length];
	}
	
### 类名
	//获取类名
	NSString *className = [NSString stringWithUTF8String:object_getClassName(obj)];	

	
    

# UIKit    
### UICollectionViewLayout
    
    -(void)prepareLayout
    - (NSArray *)layoutAttributesForElementsInRect:(CGRect)rect
    -(CGSize)collectionViewContentSize
    
### UIView
	
* layoutSubviews

		layoutSubviews触发时机
		1. init初始化不会触发layoutSubviews,但是initWithFrame会
		2. addSubview会触发layoutSubviews
		3. 设置view的Frame会触发layoutSubviews，当然前提是frame的值设置前后发生了变化
		4. 滚动一个UIScrollView会触发layoutSubviews
		5. 旋转Screen会触发父UIView上的layoutSubviews事件
		6. 改变一个UIView大小的时候也会触发父UIView上的layoutSubviews事件
		
* setNeedsLayout
		
		标记为需要重新刷新布局,异步调用 layoutIfNeeded刷新布局,不立即刷新,但是 layoutSubviews 一定会被调用
		
* layoutIfNeeded
		
		如果有刷新的标记,立即调用layoutSubviews进行布局(如果没有,不调用 layoutSubviews)
		
***如果想要立即刷新,先调用 setNeedsLayout,把标记设置为需要布局,然后马上调用 layoutIfNeeded,实现布局***

* setNeedsDisplay

		setNeedsDisplay调用drawRect
		

### UITextField

* TextField 禁止贴边对齐
	
		UIView *clearView = [[UIView alloc]initWithFrame:CGRectMake(0, 0, 5, 0)];
		textField.leftView = clearView;
		textField.leftViewMode = UITextFieldViewModeAlways;

### UIWebView

* 透明
	
		myWebView.opaque = NO;
	
* 文本颜色
	
		[webView stringByEvaluatingJavaScriptFromString:[NSString stringWithFormat:@"document.getElementsByTagName('body')[0].style.webkitTextFillColor= '%@‘“,@“#fff444"]];
	
* 文本大小
	
		[webVie stringByEvaluatingJavaScriptFromString:@"document.getElementsByTagName('body')[0].style.webkitTextSizeAdjust= '150%'”];	
	
### UIButton

* Title 左对齐

		button.contentHorizontalAlignment = UIControlContentHorizontalAlignmentLeft;
		button.titleEdgeInsets = UIEdgeInsetsMake(0, 10, 0, 0);
	
	
		
		

    
    
# Tips
### 企业级分发

* [Xcode 6.1 做ipa企业级分发(In-House模式)详细步骤](http://blog.csdn.net/pang040328/article/details/40924737)
	
	[企业证书 Plist 文件](./source/AppName.plist)
	
### Appstore 商品

	NSString *appleID = @"XXXXXX";
    NSString *str = [NSString stringWithFormat:@"itms-apps://itunes.apple.com/app/id%@",appleID];
    [[UIApplication sharedApplication] openURL:[NSURL URLWithString:str]]; 
### 隐藏键盘
	
* [TableView Tap 空白,隐藏键盘](http://blog.csdn.net/xiazailushang/article/details/10941471)
    
		UITapGestureRecognizer *tapGesture = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(resignAllFirstResponder)];

    	tapGesture.delegate = self;

		[upImageTableView addGestureRecognizer:tapGesture];


		- (BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer shouldReceiveTouch:(UITouch *)touch

		{

		// 输出点击的view的类名

	    NSLog(@"%@", NSStringFromClass([touch.view class]));

	    // 若为UITableViewCellContentView（即点击了tableViewCell），则不截获Touch事件

    	if ([NSStringFromClass([touch.view class]) isEqualToString:@"UITableViewCellContentView"]) {

        return NO;

    	}

    	return  YES;

		}

# WebSite
* [Apple developer](https://developer.apple.com)
* [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa)
* [蒲公英(第三方应用托管平台)](http://www.pgyer.com/)

	
    
    
#End    
</article>
<script type="text/javascript" src="http://i5ting.github.io/git-quick-start/preview/js/jquery-1.10.2.min.js"></script>
<script type="text/javascript" src="http://i5ting.github.io/git-quick-start/preview/js/jquery.ztree.all-3.5.min.js"></script>
<script type="text/javascript" src="http://i5ting.github.io/git-quick-start/preview/js/jquery.ztree_toc.js"></script>
<SCRIPT type="text/javascript" >
			<!--
			$(document).ready(function(){
				$('#tree').ztree_toc({
					is_auto_number:true,
					documment_selector:'.markdown-body',
					ztreeStyle: {
						width:'260px',
						overflow: 'auto',
						position: 'fixed',
						'z-index': 2147483647,
						border: '0px none',
						left: '0px',
						top: '0px'
					}
				});
			});
			//-->
			</SCRIPT>
