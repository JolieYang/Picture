# NLKeyboard手册
####介绍:
-  将任意UIView模拟成文本输入框，包括UILabel,自定义键盘

###特性




###一.集成框架
1. 

###二.用法

    


	
    

###三.API文档
####NLInputConnection.h 

扩展UIView,将UIView模拟成文本输入框。

	// 将任意控件模拟成带苹果原生键盘的文本输入框
	-(void)setInputViewWithTextField:(UITextField *)textField;
	// 将任意控件模拟成带自定义键盘的文本输入框
	- (void)setInputViewWithKeyboard:(id<NLKeyboard>)keyboard secureTextEntry:(BOOL)secureTextEntry delegate:(id<UITextFieldDelegate>)delegate;


####NLKeyboard.h
抽象键盘实现类NLAbstracgtKeyboard,自定义的键盘需继承该类

	// 关联自定义键盘xib布局文件中的按键集合
	@property (nonatomic, strong) IBOutletCollection(UIView) NSArray *randomItems;

###四.系统要求
该项目最低支持iOSx.0和Xcode7.0

###五.许可证


