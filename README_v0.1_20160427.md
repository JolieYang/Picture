# NLKeyboard手册
####介绍:

>**将任意UIView模拟成文本输入框，并可为模拟文本输入框设置苹果原生键盘或自定义键盘**


####特性
- 兼容原生输入框与原生键盘
- 任意UIView都可模拟成文本输入框，原生输入框/模拟输入框与原生键盘/自定义键盘可随机组合



###一.集成框架
####1.导入frameworks
添加NLKeyboard.framework
####2.导入自定义键盘
目前框架中的自定义键盘

- 快钱键盘
- ...

###二.用法
####1.引入头文件
	
	#import <NLKeyboard/NLInputConnection.h>
	
	// 快钱文本输入框
	#import "NLKeyboardNumberPad.h" // 快钱自定义键盘
	#import "QBSecurityLimitTextField.h" // 快钱密码输入框
	
####2.调用接口
#####关联输入框

	// 关联输入框,可以是UITextField, UILabel,UIView。其中QBSecurityLimitTextField关联的控件是一个IView
	IBOutlet UITextField *tf;
	IBOutlet UILabel *lb;
	IBOutlet NLSimpleSecurityLimitTextField *limitTf;
	IBOutlet QBSecurityLimitTextField *qbTf;// 快钱密码输入框,关联的控件是一个UIView
	
##### 系统原生输入框，设置自定义键盘

	// 原生输入框+快钱密码非随机键盘
	// (自定义键盘默认为随机键盘,键盘不随机则需设置randomItems属性为nil)
	id<NLKeyboard> keyboard = [NLKeyboardNumberPad standardKeyboard];
	[(NLKeyboardNumberPad *)keyboard setRandomItems:nil];
    [tf setInputViewWithKeyboard:keyboard];
    
![示例键盘截图](https://raw.githubusercontent.com/JolieYang/Picture/master/origin+self.png)
	
##### 文本模拟输入框，设置原生密码键盘

	// 文本输入框+原生键盘
	UITextField *systemKeyboardTf = [[UITextField alloc] initWithFrame: lb.frame];
	systemKeyboardTf.delegate = self;
	systemKeyboardTf.keyboardType = UIKeyboardTypeDefault;
	[lb setInputViewWithTextField: systemKeyboardTf];
![示例键盘截图](https://raw.githubusercontent.com/JolieYang/Picture/master/self+origin.png)
	
##### 文本模拟输入框，设置自定义键盘
	
	// 文本输入框+自定义键盘
	[lb setInputViewWithKeyboard:[NLKeyboardNumberPad standardkeyboard] secureTextEntry:YES delegate:self];
	
![示例键盘截图](https://raw.githubusercontent.com/JolieYang/Picture/master/self+self.png)
	
##### 组合控件模拟输入框，设置原生键盘
	// 快钱输入框+自定义键盘
	// 控件继承NLSimpleSecurityLimitTextField，该类已实现文本输入在labels的显示处理
	UITextField *sysKeyboardTf = [[UITextField alloc] initWithFrame:limitTf.frame];
    sysKeyboardTf.delegate = self;
    sysKeyboardTf.secureTextEntry = YES;
    sysKeyboardTf.keyboardType = UIKeyboardTypeDefault;
    [limitTf setInputViewWithTextField:sysKeyboardTf];
    
![快钱键盘截图](https://raw.githubusercontent.com/JolieYang/Picture/master/group.png)

##### 快钱模拟输入框与键盘
	
	// 1. UIView控件继承QBSecurityLimitTextField,已内部实现加载快钱键盘，故无需多余代码
	// 2. QBSecurityLimitTextField集成NLSimpleSecurityLimitTextField,UIView中的六个方框(即UILabel)与NLSimpleSecurityLimitTextField的labels数组(IBOutletCollection)关联，故无需多余代码

![快钱键盘截图](https://raw.githubusercontent.com/JolieYang/Picture/master/QB.png)
    

###三.核心API
####NLInputConnection.h 

扩展UIView,将UIView模拟成文本输入框。

	// 将任意控件模拟成带苹果原生键盘的文本输入框
	- (void)setInputViewWithTextField:(UITextField *)textField;
	// 将任意控件模拟成带自定义键盘的文本输入框
	- (void)setInputViewWithKeyboard:(id<NLKeyboard>)keyboard secureTextEntry:(BOOL)secureTextEntry delegate:(id<UITextFieldDelegate>)delegate;

NLInputConnection协议中
	
	/**
 	 *  自定义键盘文本变化时，如何更新显示
 	 *
 	 *  @param keyboard  自定义键盘
 	 *  @param textField 内部输入框
 	 *  @param text      当前的完整文本
 	 */
	- (void)keyboard:(id<NLKeyboard>)keyboard textField:(UITextField *)textField doUpdateText:(NSString *)text;

####NLKeyboard.h
抽象键盘实现类NLAbstracgtKeyboard,自定义的键盘需继承该类

	// 关联自定义键盘xib布局文件中的按键集合
	@property (nonatomic, strong) IBOutletCollection(UIView) NSArray *randomItems;

###四.系统要求
该项目最低支持iOS?.0和Xcode?.0 //TODO

###五.许可证


