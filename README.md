# NLKeyboard手册
####介绍:

>**为文本输入框设置框架内自定义键盘，也可定制自定义键盘使用**


####特性
- 可定制自定义键盘
- 任意UIView都可模拟成文本输入框，并可为模拟文本输入框设置苹果原生键盘或自定义键盘
- 原生输入框/模拟输入框与原生键盘/自定义键盘可随机组合



###一.集成框架
####1.导入frameworks
添加NLKeyboard.framework
####2.导入自定义键盘
目前框架中的自定义键盘

- 快钱键盘
- ...

###二.用法
	
####1自定义键盘

#####1.1设置自定义键盘

UITextField设置自定义键盘

	#import "NLKeyboardNumberPad.h" // 快钱自定义键盘
	
	IBOutlet UITextField *tf;
	
	// 设置快钱自定义键盘,键盘默认为随机。
	id<NLKeyboard> keyboard = [NLKeyboardNumberPad standardKeyboard];
	//[(NLKeyboardNumberPad *)keyboard setRandomItems:nil];// 键盘不随机则需设置randomItems属性为nil
	[tf setInputViewWithKeyboard:keyboard];
![示例键盘截图](https://raw.githubusercontent.com/JolieYang/Picture/master/origin+self.png)

#####1.2定制自定义键盘
除了框架提供的键盘，可能会需要定制除了框架以外的键盘。以下以NLKeyboardCustom这个类为示例:

1. 新建一个继承NLAbstractKeyboard(抽象键盘实现类)的类NLKeyboardCustom
2. 新建一个xib文件为NLKeyboardCustom,将xib中的View关联NLAbstractKeyboard
3. 在xib中绘制自定义键盘
4. 自定义键盘事件绑定：按键按顺序关联到NLKeyboard.h文件中NLAbstractKeyboard抽象类中的randomItems数组；隐藏键盘按键跟onDone事件绑定；清除按键跟onClear事件绑定。
*<p style="color:#D8D8D8">注意事项:编译器如果无法识别NLKeyboard中的-(IBAction)事件，则可通过子类(即NLKeyboardCustom)继承NLKeyboard协议中的IBAction事件。</p>*
5. 在NLKeyboardCustome中提供静态方法加载该控件：

		+(id) standardKeyboard {
	  		return [[[NSBundle mainBundle] loadNibNamed:NSStringFromClass(self) owner:self options:nil] firstObject];
		}
	 
######使用定制键盘
	
	#import <NLKeyboardCustom.h>
 	
 	IBOutlet UITextField *tf;
 	
 	id<NLKeyboard> keyboard = [NLKeyboardCustom standardKeyboard];
 	[tf setInputViewWithKeyboard: keyboard];

####2.模拟文本输入框
任意UIView都可模拟成文本输入框，可以是UIView,UILabel等，并且都可设置自定义键盘。
任意UIView都可设置自定义的键盘，包括UIView,UILabel,UITextField等.
#####2.1 UILabel模拟成文本输入框
#####示例1:设置自定义键盘
	IBOutlet UILabel *lb;
	[lb setInputViewWithKeyboard:[NLKeyboardNumberPad standardKeyboard] secureTextEntry:YES delegate:self];
![示例键盘截图](https://raw.githubusercontent.com/JolieYang/Picture/master/self+self.png)
#####示例2:设置系统键盘
	IBOutlet UILabel *lb;
	
	UITextField *systemKeyboardTf = [[UITextField alloc] initWithFrame: lb.frame];
	systemKeyboardTf.delegate = self;
	systemKeyboardTf.keyboardType = UIKeyboardTypeDefault;
	[lb setInputViewWithTextField: systemKeyboardTf];
![示例键盘截图](https://raw.githubusercontent.com/JolieYang/Picture/master/self+origin.png)

	
#####2.2 快钱风格模拟输入框
#####示例1: 设置系统键盘
	IBOutlet NLSimpleSecurityLimitTextField *limitTf;
	
	UITextField *sysKeyboardTf = [[UITextField alloc] initWithFrame:limitTf.frame];
    sysKeyboardTf.delegate = self;
    sysKeyboardTf.secureTextEntry = YES;
    sysKeyboardTf.keyboardType = UIKeyboardTypeDefault;
    [limitTf setInputViewWithTextField:sysKeyboardTf];// 控件继承NLSimpleSecurityLimitTextField，该类已实现文本输入在labels的显示处理
![示例键盘截图](https://raw.githubusercontent.com/JolieYang/Picture/master/group.png)
#####示例2: 设置快钱键盘
	IBOutlet QBSecurityLimitTextField *qbTf;// 快钱密码输入框,关联的控件是一个UIView，继承QBSecurityLimitTextField
	// 1. UIView控件继承QBSecurityLimitTextField,已内部实现加载快钱键盘，故无需多余代码
	// 2. QBSecurityLimitTextField继承NLSimpleSecurityLimitTextField,UIView中的六个方框(即UILabel)与NLSimpleSecurityLimitTextField的labels数组(IBOutletCollection)关联，故无需多余代码

![示例键盘截图](https://raw.githubusercontent.com/JolieYang/Picture/master/QB.png)

#####2.3 定制模拟文本输入框
1. 首先，#import <NLKeyboard/NLInputConnection.h>
2. 遵循NLInputConnection和UITextField协议
3. 实现NLInputConnection协议中的方法

		#pragma mark - NLInputConnection implement
		// 自定义键盘文本变化时，如何更新显示
		-(void)keyboard:(id<NLKeyboard>)keyboard textField:(UITextField*)textField doUpdateText:(NSString*)text;// 其中text是当前的完整文本，显示在相应的控件中
		
4. 实现UITextField协议中的方法
		
		#pragma mark - UITextFieldDelegate implement
		- (BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string;// 通过该方法限制输入字符位数

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


