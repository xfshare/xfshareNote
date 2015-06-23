<ul id="tree" class="ztree"></ul>
<article class='markdown-body'>
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
