Here is my thinkings during the development of project in ESTUN.
- WPF Skills

>1、当新建一个WPF类输出库类型项目，默认是只有.cs文件而没有XAML界面的，如果手动在其中添加WPF控件类（也就是.xaml和.xaml.cs两个文件），须要在项目引用中添加对System.XAML的引用。

>2、如果在跨工程调用图片资源显示，需要将图片资源的属性中的“生成操作”修改为“Resources”，而不能为默认的“内容”。否则报异常System.Xaml.XamlParseException。

>3、Grid.Background的内容设置ImageBrush
<Grid.Background>
  <ImageBrush Source = "/ProjectName;Component/Resources/Images/Servo.png" 
Opacity = "0.3"   Stretch = "None"/>
</Grid.Background>

>4、如果遇到Xaml.cs中窗口类的构造函数调用InitializeComponent失败，一般原因是XAML文件中对本窗体的类名定义x:Class与.cs文件中类名及所在的命名空间不对应。

>5、在跨工程调用的窗体中使用的字符串资源所在的Property文件夹下的Resources.resx文件的属性中的文件生成器属性需要由默认的ResXFileCodeGenerator 修改为PublicResXFileCodeGenerator 。否则会报XamlParseException，这种异常的原因很多，像第二条也是，但是具体原因需要看异常详细说明中的InnerException，本错误提示StatIcResources无法解析为枚举、...；查了StackOverflow，并看了自己的资源生成文件Resources.Designers.cs中File等字符串资源属性都是Internal，而不是public。所以要避免该错误，需要将生成器指定为生成Pulic属性的生成器PublicResXFileCodeGenerator 。

>6、为工程添加资源文件夹时，e.g.应首先通过解决方案管理树为Resources文件夹下Images文件夹添加一个名为Menu文件夹；如果这时在工程目录中已存在Menu文件夹已存在，就换个名字如NewFold之类的；然后为这个新建的NewFold添加资源图片；最后去工程目录下修改这个名字为Menu。

>7、Shell中指定主窗口为其他工程中的XAML窗口（窗口类为MainWindow），需要在Shell.xaml中加上元素<views:MainWindow>。且如果想设置窗口背景，可以为Window.icon属性设置ImageSource，如下所示：
 xmlns:views="clr-namespace:ESviewTest.Business.MainWindow.Views;
assembly=ESviewTest.Business.MainWindow"
    Title="ESViewTest" 
    WindowState="Maximized">
    <views:MainWindow />
    <Window.Icon>
        <ImageSource >/Resources/ESView.ico</ImageSource>
    </Window.Icon>

>8、WPF应用程序框架搭建步骤理解认识小结
  - 1. 创建主应用程序的主窗体shell.xaml
  - 2. 在主窗体中跨工程引用窗体ESviewTest.Business.MainWindow，主要考虑跨工程窗体嵌套。
  - 3. 系统分层设计（）
  - 4. Business中每个功能模块（独立界面工程）都需要考虑MVVM（Model-View-ModelView）的设计模式。
Model: This can be really simple, the goal here is for the ViewModel not to have to do any of the business logic.
ViewModel: This should essentially delegate everything to the Model except for exposing data for the View.
View: This should just bind to the ViewModel and make stuff look pretty.

>9、字符串资源都会被生成public Static 只读属性，所以在XAML中绑定字符资源需要x:Static标记扩展。标记扩展详见《深入浅出WPF》

>10、只读属性通过{get;}设置，而只读字段使用readonly修饰符限定，其中readonly字段只能在声明时或者同一构造函数中赋值，其他任何地方不允许赋值。
且只读/写是属于读/写范畴，与访问权限（pubic/private/protected）不可混淆。

>11、wpf XAML design-time expression  http://blog.csdn.net/yapingxin/article/details/23942923
xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
d:DesignHeight="336"
d:DesignWidth="300"
d:DataContext="{d:DesignInstance viewModels:Presenter}"

>12、键盘Enter快捷键
<UserControl.InputBindings>
          <KeyBinding Key="Enter" Command="{Binding ConvertTextCommand}"/>
       </UserControl.InputBindings>

>13、无法启动远程调试器  
目标平台修改为x86即可。

>14、WPF工程模板提供了Properties分支，用于添加资源，范围包含图标、图片、静态公有字符串和配置信息。一般为了方便管理，图片资源一般直接放到工程下新建的Resources/Images文件夹下。

>15、windows规定，一个程序就是一个进程，一个GUI工程需要有一个窗体作为主窗体。App.xaml文件的作用就是声明了程序的进程会是谁，同时指定了程序的主窗体是谁。App.xaml.cs是App.xaml的后台代码。DLL不算进程。

>16、每个Xaml文件可以有且最多只能有一个默认名称控件，且默认名称控件应该选择其中元素被最频繁使用的名称空间充当默认名称控件，在xaml根元素起始标签中定义方式xmlns = "http://schemas.microsoft.com/winfx/2006/xaml/presentation"，在此名称空间下，Windows、Grid等可以不用加上名称空间前缀。
其他关于WPF XAML 名称空间映射 详见MSDN以下链接：
 https://msdn.microsoft.com/zh-cn/library/ms747086.aspx
WPF模板为xaml文件提供默认引用的两个名称空间，格外重要。格式上看上去像个网址，实际上只是XAML解析器的一个硬性编码，只要见到这些固定的字符串，就会把一系列必要的程序集和程序集中包含的.NET名称空间引用进来。
"http://schemas.microsoft.com/winfx/2006/xaml名称空间主要对应一些与XAML语法和编译相关的CLR名称空间。使用这些名称空间中的类型时，需要加上前缀x:。x名称空间详解参见第20条。

>17、Xaml文件中使用x:class定义了类名与Xaml.cs后台定义的类名相同，在XAML解析器将.xaml文件解析后生成的类和后台定义的同名类不会冲突，因为后台代码声明时使用partial关键字。
正是这种partial机制，使得可以把类的逻辑代码留在.cs文件中，把与界面UI相关的代码分离，实现UI与逻辑分离（code-behind代码后置）。
XAML中事件性Attribute充当XAML与C#之间沟通的纽带。

>18、属性元素
它是一个标签（子级标签）：是父级标签内容的一个元素，位于父级标签的内容处。
它对应于父级标签的一个属性。
写法格式上如下：
<ClassName>
<ClassName.属性名>
<!--以对象形式为属性赋值-->
</ClassName.属性名>
</ClassName>
有很多例子，如Rectangle.Fill作为属性元素形式如下：
<Rectangle>
<Rectangle.Fill>
<!--以对象形式为属性赋值-->
<SolidBrush Color = "Blue">
</Rectangle.Fill>
</Rectangle>
又例如ESView主窗体Windows定义一个图标Icon：

可以通过为WIndow添加一个属性元素WIndow.Icon实现，也可以直接在Window标签指定属性Icon，这里注意由于XAML语法限制，直接属性Icon是一个字符串，经过XAML解析后字符串对应的资源被解析成ImageSource类型的实例对象。
一般来说系统模板自带的类型，解析器可以识别并进行正确的类型转换。但是自定义的类型往往需要以属性元素形式设置正确对应类型的value。当然也可以通过绑定到外部对应类型的属性。

>19、标记扩展{}中属性值不再加引号。编译器会将花括号里的内容解析成对应的对象。对象的数据类型就是紧邻左花括号的字符串，例如绑定的话就是binding对象
Text = "{Binding ElementName = Slide, Path = Value, Mode = OneWay}"。
等价于后台代码 :
 Binding binding = new Binding() {Source = slider, Mode = BindingMode.OneWay };
BindingOperations.SetBinding(this.textBoxName,TextBox.TextProperty, binding);
只有MarkupExtension类的派生类才能使用标记扩展语法来创建对象。

>20、x名称空间
x:Class：与对应后台代码中类的名称一致，且后台要用partial关键字
x:ClassModifier：用来设置类的访问权限
x:Name：所有涉及到引用变量的都统一使用x:Name
x:FieldModifier：用来设置引用变量的访问权限

>21、RelayCommand事件处理机制

>22、属性（数据源）绑定
通过类派生INotifyPropertyChanged接口，并在属性Set中激发事件。如下：
class Student : INotifyPropertyChanged {
public event PropertyChangedEventHandler()
public string Name{
 get { return _name; }
 set {
      _name= value;
//激发事件
if(PropertyChanged != null)
PropertyChanged(this,new PropertyChangedEventArgs("Name"));
}
   }
}
目前第三方插件为数据源提供了更为方便的事件通知机制，有RaisePropertyChangedEvent(string)；也有Reshaper|Options|Code Annotations提供了(详参https://blog.jetbrains.com/dotnet/2012/07/24/inotifypropertychanged-support-in-resharper-7/)
OnPropertyChanged(string)+Annotations(定义了特性[NotifyPropertyChangedInvocator])	
Resharper使用属性改变通知机制：
将属性所在的类派生自INotifyPropertyChanged，这时ReSharper会自动提示要求实现OnPropertyChanged，按照提示自动添加以下代码：
 [NotifyPropertyChangedInvocator]
 protected virtual void OnPropertyChanged([CallerMemberName] string propertyName = null)
 {
          PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
 }
然后在属性的Set中添加OnPropertyChangeds();就OK了。

>23、数据库Sqlite技术在C# WPF上的应用

>24、NModbus技术

>NModbus is a 

>25、绘图技术之OxyPlot.dll及进度条Toolkit

>26、异常处理以及日志NLog

>27、参数表DataGrid界面技术

>28、定时器技术
using System.Timers.Timer;
var timer  =  new Timer(){Enable = True;}
timer.Elapsed += new ElapsedEventHandler(EventHandler_timer);
private void EventHandler_timer(object sender, ElapsedEventArgs args)
{      }
29、枚举随机数
StateMachine is an enum type value as supposed:
StateMachine[] enuArr = Enum.GetValues(typeof(StateMachine)) as StateMachine[];
Random ran = new Random();
StateMachine sm = StateMachine.Error;
if(enuArr != null){
sm  = enuArr[ran.Next(0,enuArr.Length)]; }
30、泛型

31、事件处理机制和委托
Application类将对象发送过来的消息从应用程序消息队列中提取出来后，分发到相应的窗体，并转换成事件。NET框架定义了一个特殊的类型(Delegate委托)，该类型提供函数指针的功能。这样，委托就等效于一个类型安全的函数指针或一个回调函数。C#中通过Delegate委托机制将事件与响应函数的函数地址关联起来，并形成一种函数指针列表，当消息到来的时候，即可通过这些函数指针列表逐一调用这些响应函数。我们通过以下方法自定义一个事件触发，来验证我们以上的猜测。我们通过手动添加代码来实现自定义的代理事件（同VC++中的自定义消息） 
      ○ 声明事件委托。此处int para仅是方便实验，代表所需要的参数列表，但要注意参数列表需要和第3步的参数列表相统一。 public delegate void MyEventHandler(int para);
      ○  声明事件，event 关键字用于在发行者类中声明事件，委托MyEventHandler作为事件的类型。 public event MyEventHandler MyEvent;
      ○ 添加事件的处理程序（响应事件的方法）。public void OnMyEvent(int para) { MessageBox.Show(""事件触发，参数为:""+para);}
      ○  将指定的事件处理程序邦定到要处理的事件上（订阅事件）,注意，此语句需要写在程序执行语句中，如Form_Lord函数内。 this.MyEvent += new MyEventHandler(OnMyEvent);
      ○  触发事件（调用事件的触发方法）。MyEvent(3); 
      ○  通过事件委托的回调，执行我们需要的事件处理程序。弹出消息框“事件触发，参数为:3”。
 以上实验可以得出结论，C#中的消息通过Application转换成事件以后，通过以上6个步骤完成了事件与处理程序之间的对应关系，在用户触发事件以后，相应的时间处理程序得到准确执行。也可通过以上方法，增加用户自定义事件。
32、异步编程、任务、线程和同步

33、文件操作（XML文件序列化/反序列化）

34、内存管理

35、反射机制
（1）更改特性的值
 关键点：typeof、GetProperty、GetCustomAttributes、GetType、GetField、FildInfo
 【例】
 using System;
 using System.Reflection;

namespace DynamicEditAttribute
{
    class Program
    {
        static void Main(string[] args)
        {
            var attributes = typeof(MyCard).GetProperty("IdCard").GetCustomAttributes(false); //获取MyCard类IdCard属性的自定义特性
            foreach (var attribute in attributes)
            {
                if (attribute is MessageAttribute)
                {
                    var memberInfo = attribute.GetType().GetField("_message", BindingFlags.NonPublic | BindingFlags.Instance);//查找特性的指定字段

                    if (memberInfo != null)
                    {
                        Console.WriteLine(memberInfo.GetValue(attribute)); 
                        memberInfo.SetValue(attribute, "New Message...."); //为特性设置新值
                        Console.WriteLine(memberInfo.GetValue(attribute));
                    }
                }
            }
            Console.ReadLine();
        }
    }


    public class MyCard
    {
        [Message("Old Message")]
        public IdCard IdCard { get; set; }
    }

    public class IdCard
    {

    }

    [AttributeUsage(AttributeTargets.Property)]
    public class MessageAttribute : Attribute
    {
        private string _message;
        public MessageAttribute(string message)
        {
            _message = message;
        }
    }
}


36、NetFramework网络通信

37、Visual Studio单元测试.
