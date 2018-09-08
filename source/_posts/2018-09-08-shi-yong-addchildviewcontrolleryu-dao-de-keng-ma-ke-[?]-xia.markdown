---
layout: post
title: "使用addChildViewController遇到的坑 马克一下"
date: 2018-09-08 20:53:14 +0800
comments: true
categories: 
---

在开发过程中可能遇到各种需求，在这次开发中碰到一个界面中有几个选项卡，来切换不同的界面，每个界面的布局大不相同，如下界面：

<!--more-->

{% img /images/F54FEDF7-9CC1-4DF1-B37A-44AE0BF9D15B.png %}


{% img /images/QQ20180908-210941.png %}

这时有多种方法实现，为了减少界面的耦合度，这边用到 addChildViewController 来实现，直接上代码

	
	#import "ShopManagerVC.h"
	#import "ShopMaterialVC.h" //全部材料
	#import "ShopNavVC.h"      //帮我导航
	#import "ShopInfoVC.h"     //门店资料


	@interface ShopManagerVC ()
	@property (nonatomic,strong) NSMutableArray *btnsArr;
	@property (nonatomic,strong) UIViewController *currentVC;
	@property (nonatomic,strong) UIButton *currentBtn;
	@property (nonatomic,strong) ShopMaterialVC *firstVC;
	@property (nonatomic,strong) ShopNavVC      *secondVC;
	@property (nonatomic,strong) ShopInfoVC     *thirdVC;

	@end

	@implementation ShopManagerVC

	- (void)viewDidLoad {
	    [super viewDidLoad];
	    // Do any additional setup after loading the view.
	    
	    [self addBottomView];
	    [self addChildVCs];
	}
	- (void)addChildVCs{
	    _firstVC = [[ShopMaterialVC alloc]init];
	    [self addChildViewController:_firstVC];
	    [_firstVC didMoveToParentViewController:self];
	    [_firstVC.view setFrame:CGRectMake(0, 0, UI_SCREEN_WIDTH, UI_SCREEN_HEIGHT-UI_Tabbar_Height)];
	    _currentVC = _firstVC;
	    [self.view addSubview:_firstVC.view];
	    
	    _secondVC = [[ShopNavVC alloc]init];
	    _secondVC.navHeight = UI_Nav_Sta_Height;
	    [_secondVC.view setFrame:CGRectMake(0, 0, UI_SCREEN_WIDTH, UI_SCREEN_HEIGHT-UI_Tabbar_Height)];
	    
	    _thirdVC = [[ShopInfoVC alloc]init];
	    _thirdVC.navHeight = UI_Nav_Sta_Height;
	    _thirdVC.view.frame = CGRectMake(0, 0, UI_SCREEN_WIDTH, UI_SCREEN_HEIGHT-UI_Tabbar_Height);
	    
	}
	- (void)addBottomView{
	    UIView *bottomView = [UIView new];
	    [self.view addSubview:bottomView];
	    bottomView.backgroundColor = [UIColor whiteColor];
	    [bottomView mas_makeConstraints:^(MASConstraintMaker *make) {
	        make.left.right.bottom.equalTo(self.view);
	        make.height.mas_equalTo(UI_Tabbar_Height);
	    }];
	    NSArray *titleArr = @[@"全部材料",@"帮我导航",@"商家资料"];
	    UIButton *leftView;
	    for (NSInteger i = 0; i < 3; i++) {
	        UIButton *btn = [UIButton buttonWithType:UIButtonTypeCustom];
	        [btn setTitle:titleArr[i] forState:UIControlStateNormal];
	        [btn setTitleColor:LABELMAINCOLOR2 forState:UIControlStateNormal];
	        [btn setTitleColor:MAINCOLOR forState:UIControlStateSelected];
	        btn.titleLabel.font = JQFontSize(15);
	        [btn addTarget:self action:@selector(click:) forControlEvents:UIControlEventTouchUpInside];
	        btn.tag = 500+i;
	        [bottomView addSubview:btn];
	        [self.btnsArr addObject:btn];
	        [btn mas_makeConstraints:^(MASConstraintMaker *make) {
	            if (i == 0) {
	                make.left.equalTo(bottomView);
	            }
	            else{
	                make.left.equalTo(leftView.mas_right);
	            }
	            make.top.equalTo(bottomView);
	            make.height.mas_equalTo(UI_BottomBtnHeight);
	            make.width.mas_equalTo(UI_SCREEN_WIDTH/3.0);
	        }];
	        if (i == 0) {
	            _currentBtn = btn;
	            _currentBtn.selected = YES;
	            UIView *line = [UIView new];
	            line.backgroundColor = LABELMAINCOLOR3;
	            [bottomView addSubview:line];
	            [line mas_makeConstraints:^(MASConstraintMaker *make) {
	                make.left.equalTo(btn.mas_right);
	                make.width.equalTo(@1);
	                make.height.equalTo(@20);
	                make.centerY.equalTo(btn);
	            }];
	        }
	        else if (i == 1){
	            UIView *line = [UIView new];
	            line.backgroundColor = LABELMAINCOLOR3;
	            [bottomView addSubview:line];
	            [line mas_makeConstraints:^(MASConstraintMaker *make) {
	                make.left.equalTo(btn.mas_right);
	                make.width.equalTo(@1);
	                make.height.equalTo(@20);
	                make.centerY.equalTo(btn);
	            }];
	        }
	        leftView = btn;
	    }
	    
	}
	- (void)click:(UIButton *)btn{
	    if (btn.tag == _currentBtn.tag) {
	        return;
	    }
	    for (UIButton *tempBtn in self.btnsArr) {
	        tempBtn.selected = NO;
	    }
	    btn.selected = YES;
	    _currentBtn = btn;
	    switch (btn.tag-500) {
	        case 0:
	            [self changeControllerFromOldController:_currentVC toNewController:_firstVC];
	            break;
	        case 1:
	            [self changeControllerFromOldController:_currentVC toNewController:_secondVC];
	            break;
	        case 2:
	            [self changeControllerFromOldController:_currentVC toNewController:_thirdVC];
	            break;
	        default:
	            break;
	    }
	    
	}
	
	- (void)changeControllerFromOldController:(UIViewController *)oldController toNewController:(UIViewController *)newController
	{
	    [self addChildViewController:newController];
	    /**
	     *  切换ViewController
	     */
	    [self transitionFromViewController:oldController toViewController:newController duration:0.3 options:UIViewAnimationOptionCurveEaseIn animations:^{
	        
	        //做一些动画
	        
	    } completion:^(BOOL finished) {
	        
	        if (finished) {
	            //移除oldController，但在removeFromParentViewController：方法前不会调用willMoveToParentViewController:nil 方法，所以需要显示调用
	            [newController didMoveToParentViewController:self];
	            [oldController willMoveToParentViewController:nil];
	            [oldController removeFromParentViewController];
	            self->_currentVC = newController;
	            
	        }else
	        {
	            self->_currentVC = oldController;
	        }
	        
	    }];
	}
	
	
	#pragma mark - get & set
	- (NSMutableArray *)btnsArr{
	    if (!_btnsArr) {
	        _btnsArr = [NSMutableArray new];
	    }
	    return _btnsArr;
	}




#### <a name="fenced-code-block">iPhone X 出现的坑 </a>

{% img /images/QQ20180908-211629.png %}

上图可以看到除了最先加到super View上的控制器获取到导航栏+状态栏的高度是正确的88，后面的状态栏+导航栏的高度为44，这具体原因还不知道

###解决办法
一个取巧的办法：

	 _secondVC = [[ShopNavVC alloc]init];
	    _secondVC.navHeight = UI_Nav_Sta_Height;
	    [_secondVC.view setFrame:CGRectMake(0, 0, UI_SCREEN_WIDTH, UI_SCREEN_HEIGHT-UI_Tabbar_Height)];
	    
	    _thirdVC = [[ShopInfoVC alloc]init];
	    _thirdVC.navHeight = UI_Nav_Sta_Height;
	    _thirdVC.view.frame = CGRectMake(0, 0, UI_SCREEN_WIDTH, UI_SCREEN_HEIGHT-UI_Tabbar_Height);
	    
在初始化控制器时候给把正确的导航栏+状态栏的高度传进去，且位置一定要在设置VC.view.frame 之前

