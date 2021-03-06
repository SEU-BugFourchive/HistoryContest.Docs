# yzh

## 6.22

1. 成功运行C#+vue.js模板->需要解释模板结构
2. 下载mediawiki,尝试搭建wiki文档


## 7.17-7.18

* 后端：编写一个WebApi, 提供Http接口给前端使用

  后端采用MVC模式，其中：

  * Models负责将数据库的裸数据封装成一个具体的逻辑实体，并用该逻辑实体进行操作
  * Controllers负责接收/返回http请求，以及与数据库通讯等层与层之间的事物
  * View负责生成页面，用cshtml作模板，最后被解析成一个正常的html并返回

* 依赖注入模式

  ​	通过将类放入一个容器中，用容器来管理对象的实例化与生命周期，从而达到解耦的效果

  ​	参见：https://www.zhihu.com/question/32108444

* C# 委托：对应C/C++的函数指针概念，最常用的例子是作为回调函数使用

  ​	声明一个委托实际上是创建了一个继承自System.Delegate的类，这个委托实例化时，其构造函数接受一个函数指针（函数名/委托）

  如：

  ```
  public delegate void printString(string s);
  ...
  printString ps1 = new printString(WriteToScreen);
  printString ps2 = new printString(WriteToFile);
  ```

* C# 属性：待后日补充

* 想用跟实验室学网络协议时用到的Wireshark来抓包分析，但是由于目前是在localhost上测试，ws只能抓取通过网卡的流量，所以暂时不可行……

* web api 生成文档？


* Visual Studio插件推荐：

  https://blogs.msdn.microsoft.com/webdev/2017/03/21/five-visual-studio-2017-extensions-for-web-developers/

  （另附：VS Code前端类插件推荐：https://zhuanlan.zhihu.com/p/27905838

* Entity Framework:数据访问层设计技术

* （！）C# MVC插件可以自动将C#对象序列化重整为JSON，因此可以直接用作Http请求的返回值。

  * C# MVC也可以在参数中加入[FromBody]属性，使得该参数从Http请求中获取构造对象，赋值给参数



## 7.19

* Web API文档自动生成器：Swagger

  可以通过hostname/swagger来查看，网页源数据在hostname/swagger/{version}/swagger.json里

  可以用///注释来为API添加说明

* 研究了一下迷之复杂的Azure...：

  * 按(Ctrl+)F5在本地(localhost)运行网站程序，而我们可以通过项目右键->发布(Publish)将应用按照Release方式Build，放到本地的一个文件夹，或是直接部署到Azure上。

  * 部署到Azure时，需要一个Azure订阅，一个资源组（用来对云资源分类），一个Plan(指定了机房地区、总配置，可以容纳多个应用在这个配置下运行)

  * 部署完毕后，Azure会自动为我们分配一个网址，代表对应的网络资源，一般是<app名字>.azurewebsites.net/， 我们以后便可基于这个网址来测试服务。

  * 本地对项目更新以后，再次点发布即可更新到Azure上

  * 后续将看一下自动部署的方法。

    ​

## 7.20-7.21

* Azure 自动部署(git)步骤:
  1. 在Azure Portal找到自己已经部署好的App, 找到设置栏
  2. 选择设置->部署凭据，设置好用来验证的账户与密码
  3. 选择设置->部署选项->设置源
  4. 此时，有两种选择：
     * git: 会提供一个远程库的URL，将本地与这个远程库关联后，配合账户密码即可上传
     * github:关联一个github页面，适合进行团队开发
  5. 选择好后，对于VS，可在Team Explorer中用按键进行简单操作，也可用git shell直接操作（不知道VS Code是怎么样的）
* GitHub教育用户申请……


* Bundle:资源压缩

* 使用.UseWebRoot()手动更改wwwroot路径

* 信息量太大……但又感觉说不清楚看了什么，等待后面消化。

  ​


## 7.22

* 项目结构分离过程（开发时）：
  * 在Program.cs中将ContentRoot设为了"当前目录（Program.cs所在目录)"的上一级目录:

    ```
    .UseContentRoot(Directory.GetParent(Directory.GetCurrentDirectory()).FullName)
    ```

  * 在Startup.cs中设定WebpackDevMiddleWare的ProjectPath为ContentRoot中的`/client`(前端)目录：

    ```
    ProjectPath = env.ContentRootPath + "/client"
    ```

  * 在webpack.config.js中将所有./wwwroot目录设置为../wwwroot

  * index.cshtml中的main.js目录不用改，因为可以用~定位到设定好的wwwroot 

  * 由于webpack的build是通过`application`项目的csproj发布后运行的，所以需要修改.csproj文件来：

    1. 配置正确的运行位置:

       ```
       <Exec Command="webpack_build.bat" WorkingDirectory="../client/" />
       ```

       这里webpack_build.bat是将几个发布命令整合在一起的批处理。前端也可以用这个文件来将前端文件直接正式build。（不过平时开发还是靠middleware自动生成就好了~）

    2. 将生成的文件拷贝到正确的publish目录下：

       ```
             <DistFiles Include="..\wwwroot\**" />
             <ResolvedFileToPublish Include="@(DistFiles->'%(FullPath)')" Exclude="@(ResolvedFileToPublish)">
               <RelativePath>wwwroot/%(RecursiveDir)%(Filename)%(Extension)</RelativePath>
               <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
             </ResolvedFileToPublish>
       ```

  * 此时，缺少View目录的指定。View目录的修改通过继承`Microsoft.AspNetCore.Mvc.Razor.IViewLocationExpander`实现。[详细见此](https://stackoverflow.com/questions/38810978/adding-a-search-location-in-asp-net-cores-viewengineexpander)：

    ```
    		public void PopulateValues(ViewLocationExpanderContext context)
    		{
    			context.Values["customviewlocation"] = nameof(MyViewEngine);
    		}

    		public IEnumerable<string> ExpandViewLocations(
    			 ViewLocationExpanderContext context, IEnumerable<string> viewLocations)
    		{
    			return new[]
    			{
    			 "~/application/Views/{1}/{0}.cshtml",
    			 "~/application/Views/Shared/{0}.cshtml"
    		};
    		}
    ```

    然后在services中通过mvc函数修改：

    ```
    			services.AddMvc().AddRazorOptions(options=>
    			{
    				options.ViewLocationExpanders.Clear();
    				options.ViewLocationExpanders.Add(new MyViewEngine());
    			});
    ```

* 因为发布后的文件夹结构与开发时有所不同，因此需要在代码中作区分，即[条件编译](https://stackoverflow.com/questions/3788605/if-debug-vs-conditionaldebug)：

  ```
  #if DEBUG
                .UseContentRoot(Directory.GetParent(Directory.GetCurrentDirectory()).FullName)
  #else
                .UseContentRoot(Directory.GetCurrentDirectory())
  #endif
  ```

  ```
  #if DEBUG
  			services.AddMvc().AddRazorOptions(options=>
  			{
  				options.ViewLocationExpanders.Clear();
  				options.ViewLocationExpanders.Add(new MyViewEngine());
  			});
  #else 
  			services.AddMvc();
  #endif
  ```

  由于这是一个函数中的一小部分方法改变，因此不能用`[System.Diagnostics.Conditional("DEBUG")]`属性来做。

  ​

## 7.23-7.24

* 附加到进程调试：Ctrl+Shift+R
* 解决了部署后程序不能正确运行，返回5xx错误的问题：[一次错误的解决记录](../../index.html#!pages/docs/一次错误的解决记录.md)
* Swagger 开启xml文档格式
* 为了能让run_server.bat运行后，应用启动成功时加载浏览器，在主程序Main函数里加了参数，如果开发环境为Debug且加了参数，则会自动打开默认浏览器

## 8.7-8.8

* 由于ContentRoot改变，所需要改变的地方还有一个：appsettings.json的位置。

  在Startup函数里更改：

  ```
          public Startup(IHostingEnvironment env)
          {
              var builder = new ConfigurationBuilder()
                  .SetBasePath(env.ContentRootPath)
                  .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                  .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
                  .AddEnvironmentVariables();
              Configuration = builder.Build();
          }
  ```

  由于Startup里应该都是后端任务，因此直接更改BasePath要更为妥当。

  **但是，还需要区分DEBUG与RELEASE时行为：**

  ```
  #if DEBUG
  				.SetBasePath(Path.Combine(env.ContentRootPath, "HistoryContest.Server"))
  #else
                  .SetBasePath(env.ContentRootPath)
  #endif
  ```

* 数据库结构设计：

  | 表    | 字段                                       | get属性设计  |
  | ---- | ---------------------------------------- | -------- |
  | 学生   | 学号（主键）、名字、一卡通号、对应辅导员ID（外键）、[剩余时间?]、题目随机池ID、状态二进制串 | 获取题号数组   |
  | 辅导员  | ID、名字、院系                                 | 获取一条概况记录 |
  | 题目   | ID、题目、选项、答案、[分值?]                        | 规范格式     |

* 建立MVC框架的Model层：

  * Entities：代表与**数据库**进行交互的Model
  * ViewModel：代表与**前端**进行交互的Model

* 建立数据库层：

  数据库抽象工具：`Entity Framework`，它充当了数据库与MVC程序的中间层。

  在本地开发时，将使用一个本地数据库文件，它被设置在用户文件夹%USERPROFILE%里（即C:\Users\UserName\ )

* 使用Migrations：

  ​待补充

* 查看数据库：

  1. 离线：Visual Studio->视图->Sql Server对象资源管理器
  2. 在线：在后面将加入的自动生成的页面进行直接操作。



* **取题方案设计**：使用从随机池抽题的*伪随机模式*。

  1. 在数据库中专门建立一个`题目池`的表，里面每条记录保存*20道选择题+10道填空题*的题号，作为“随机”生成给答题者的试卷的种子。

  2. 服务端程序启动时，会调用一个函数，从数据库`题目表`中随机roll出20道选择题，10道填空题作为一条记录，并生成一定规模（比如500份，保证一个机房内的学生的试卷重复率足够小）的这种记录保存在`题目池`表中。

     由于这一步是部署服务器时做的，所以不会影响考试时性能。

  3. 学生登录答题后，会随机Roll出一个题目池的ID，按照该ID寻找到30道题的题号，从而取出卷子，并在数据库学生表中添加一个保存该ID的字段，用于持久化保存结果。

     <u>这种做法的好处与注意点</u>：

     1. 将随机出30题的过程前移，提高取卷效率。我们甚至可以对每个随机种子，按顺序将对应题目加载到内存，提高读取速度；
     2. 每一位用户与其所做题目绑定，加上每题正确错误情况记录，使得未来任意时刻重新查询分数变得很方便；
     3. 为了达到`每次重做获得的题目都不一样`的目的，每个学生随机出来的题目种子应该先放在内存中，重做时新随机一个。出分后，再随得分情况一起持久记录在数据库中，方便将来重新查询。

* API设计构思：

  1. 提供一个Students/DetailByDepartment/departmentID的API，用来获取指定院系编号的所有新生（可以根据辅导员的年级，选取该年级的学生）
  2. 学生列表json：学生（需要过滤）、排布顺序？搜索、排序是否需要给前端做，以实时返回搜索结果、无需刷新而排序？
  3. 提供一个获取按院系简单统计分数段的API，这样列出东西：平均分、最高分、>=90、>=75...

* 辅导员页面设计构思：

  * 查看本院系情况：顶部列出辅导员信息，下面按照API设计构思第1条提供内容。（注意指明未交卷的人），最后按照API设计构思第2条列出简单概况。
  * 查看各院系概况：按照API设计构思2，遍历各院系，列出数据表格。
  * ……

  ​


## 8.9

* 解决恼人的Migrations路径问题：

  我们项目的Content Root在Main函数中被设置，并且在DEBUG和RELEASE版下各不相同。然而，由于Main函数是用来运行服务器的，而一些特殊的程序（如Migrations）并不需要运行服务器，因此其跳过了Main函数，而是从dll中直接读取Startup.cs工作，这就造成了Content Root的紊乱。

  这个问题暂时没有找到较好的解决方法……最后采取了这么一个措施：在Main函数所在的Program类里添加了一个静态bool变量FromMain，用来表示程序是否执行了Main函数：

  ```
  	class Program
  	{
  		public static bool FromMain { get; set; } = false;
          public static void Main(string[] args)
          {
   			// other codes
              FromMain = true;
              host.Run();
          }
      }
  ```

  根据这个变量来重新规定Content Root:

  ```
              if(!Program.FromMain && env.IsDevelopment())
              {
                  env.ContentRootPath = Path.Combine(env.ContentRootPath, "..");
                  env.WebRootPath = Path.Combine(env.ContentRootPath, "wwwroot");
              }
  ```

  问题暂时得以解决了。

* 数据库层次设计：

  模型简单如下图所示

  ![RepositoryPattern](../uploads/images/progress_yzh/RepositoryPattern.png)

  即：

  > -- 被控制器正式使用
  >
  > ↑  Unit of work（工作单元）
  >
  > ↑  各种Repositories组合
  >
  > ↑  Entity Framework + Model.Entity
  >
  > ↑  数据库

  其中：

  * Model.Entity：自己写的与数据库一张表对应的类，类的成员与表字段一一对应。

  * Entity Framework：使用Entity构造了一个模拟的数据库环境，有着每个Entity构成的数组。

  * Repository：仓库，是对Entities数组的一层封装。主要是在里面添加各种自定义方法，用于封装各种业务逻辑。

    如：对于Student仓库，可以添加“求平均分”，“求最高分”，“按院系求平均分”等各种方法。

  * Unit of work：工作单元，各种Repository的集合，是更上层的业务逻辑代码所使用的最终成品。如有必要，可以直接取出Entity Framework的数据库环境使用。

* 为UnitOfWork类添加了依赖注入，使用Scoped模式（不能用单例，因为是线程不安全的）：

  ```
  services.AddScoped<UnitOfWork>();
  ```

* 数据模型（Entity）设计说明：

* BUG FIX：Webpack不会自动更新的问题

  其原因是用run_server.bat运行程序时，程序运行环境是Production，而非Development造成的。这连带着也出现了一些目录错误问题。

  解决方法：在bat中加入一个临时环境变量设置：`set ASPNETCORE_ENVIRONMENT=Development`

* 最新Commit内容概述：

  1. `ADDED:`数据库->表设计见Devlog

  2. `ADDED:`后端DAL（数据访问层）->以数据仓库与工作单元封装

  3. `ADDED:`后端MVC - Models，包含数据库模型与前端交互模型

  4. `ADDED:`后端控制器API：仅仅只是写好一些接口

  5. `MODIFIED:`restore_packages.bat重命名为build.bat，除了重新获取包依赖，它现在还会完成生成工作。

  6. `MODIFIED:`Client/run_server.bat 使用dotnet ef命令重新生成，这样可以保证数据库是最新的。

  7. `MODIFIED:`readme.md/index.md/devlog

  8. `REMOVED:`package-lock.json

  9. `REMOVED:`移去wwwroot/dist文件夹（because having tasks that create the project's static files in wwwroot -- by .gitignore)它现在由在本地运行一次build.bat或webpack_build.bat来生成。

  10. `FIX BUG:`Client/webpack.config.vendor.js的路径配置有误（致使webpack生成时将一部分wwwroot/dist/文件生成在Client文件夹下了）

  11. `FIX BUG:`用run_server.bat运行服务器时，环境没有设为Development。在bat中设置了临时环境变量解决问题。现在Webpack MiddleWare可以正常使用了。

  12. `FIX BUG:`修正后端工作路径的若干BUG。




# 8.15

* Controller.HttpContext属性：

  Encapsulates all HTTP-specific information about an individual HTTP request.

  即针对一个单独的Http请求而模拟出的环境，可以从中获取到这次请求对应的Session等内容。

* 添加Session:

  Session保存在一个字典（哈希表）中，由session id检索。session id将会保存在每个用户本地的cookie上。

* 初步添加Authentication功能：

  采用Cookie验证模式

  添加了登录/注销功能

  注销功能只返回状态码（成功200）。最好在前端判断一下状态码再跳转页面

* 添加Authorization功能，采取Role-based模式：每个登录的人会被分配一个身份，每个控制器只允许指定身份的人访问。

* 添加时间相关API

* 待添加管理员页面：查看log、wiki、api文档、直接查看并修改原始数据库、解析doc

## 8.17

* 后端从.Net Core 1.1 迁移至 2.0：

  安装好SDK与Runtime后，需要将global.json的sdk版本修改至2.0：

  `"sdk": {"version": "2.0.0-preview3-006912"}`

  这是解决方案依赖的东西。

* 修改包依赖：

  2.0版本，可以使用一个MetaPackage解决问题：

  `<PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />`


## 8.19-8.20
改动太多了……只列出几个重要的：

* Web API发布：
```
1. 发布Web APIs；
2. 针对API重新设计了数据库与DAL层，并完善了相关Model的设计；
3. 项目迁移到.Net Core 2.0，留下了一些预处理命令来兼容1.1版；
4. 在Server/Data/Migrations文件夹下添加了一个bat，用以及时更新数据库。请使用cmd传入一个字符串作为新Migration的名字。
```
* 区分DEBUG/RELEASE与Development/Production环境：

  主要思路为：D/P用来区分网站运行与本地运行，D/R用来区分性能模式

## 8.23

* 附加到进程调试(Azure)：

  1. Visual Studio -> 服务器资源管理器 -> 连接上Azure账户
  2. 选择对应的应用服务，右键->附加调试器

  * 但是，此时很有可能面对的情况是：

    `断点不会命中，因为没有为文档加载任何符号。`

    对于这个问题，要注意以下几点：

    1. **本地的符号文件(pdb)是否有足够的调试信息**。在项目属性->生成->输出->高级->输出->调试信息中，将选项由原来的"por的的table"改为"full"。生成后可以看到，pdb文件的大小由原来的20k变成了200k，拥有足够的调试信息。

    2. **本地与远程的版本是否一致。**这个可以通过调试前将代码commit后push到github上重新部署，或是通过VS发布到Azure解决。

    3. 远程网站上运行的版本是否为DEBUG。如果不是DEBUG，调试时在模块上会发现“是否优化”选项被设为了“是”，会影响到调试。这个可以通过VS的发布功能，将设置设为DEBUG，发布到Azure上来解决。

  * 查看符号文件加载情况：

       进入远程调试后，在VS中调试->窗口->模块，便可看到各模块的符号加载情况。可能可以看到我们自己的dll的“用户代码”被标为"否"，优化标为"是"。这可能就是上面提到的问题的根源。如果上面的方法都不奏效，还可使用下面的一个大招：

       `工具->选项->调试->常规->去掉"仅我的代码"勾选`

       这将加载所有用到的dll符号。

       还有一个可能有所帮助的选项：

       `工具->选项->调试->符号->符号文件(.pdb)的位置：Microsoft 符号服务器`

# 8.24

* 添加数据Seed，为了保证可移植，采用从json文件导入的方法。

* 修复若干数据库相关bug

* 完善了问题种子的生成

* 学生和辅导员ID处理方式变更：

  在客户端，学生学号和辅导员工号统一采用字符串表示。GET POST方法需要传入参数时，也采用字符串。

  这样，"03A16301"这样的学号也可以比较自然地处理。

  在服务端，这两者采用十六进制数表示，无需在意数据库中的十进制表示值。服务端与客户端之前的平滑转换已经完成。

* 添加了一个StatusCode响应页

* 为首页添加了登录状态脚本，现在可以通过全局user变量获取用户登录状态与信息了，如：

  `user = { isLoggedIn: true, id: "09016319", role: "Student" };`

* 为登录API提供了登录状态检验。若已经登录，则返回失败标记与用户信息。

## 8.25

* 目标：负载均衡、站到站VPN、压力测试
* 缓存

## 8.30

* 设计缓存服务：

  Redis的Hash数据结构抽象成一个RedisDictionary，提供相关服务

* 更新缓存层：

  问题答案、学生信息、一些经常会被读取的ViewModel，都已可以通过缓存获得

* 定义分数概况的更新机制：

  * 全校概况：每10分钟从缓存中过期被销毁，再次调用时重新在缓存生成一个并返回。比较是否更新的办法是：查看UpdateTime。
  * 院系概况：学生提交分数计算后更新。比较是否更新的办法是：比较NotTested的人数。

## 9.1

* 添加获取全部院系API

* 添加注册功能：

  注册时，会对传过来的信息进行验证。具体验证方法为：程序通过post请求与学校vpn3建立vpn连接，然后通过学号查询到学生课表，再将返回的课表html中必要的信息爬下来进行验证。

  可以对各种不同的登录情况正确处理，尽量保证能正常进行vpn连接。

  （爬的是17-18-1的课表，大一的同学在这个学期没有课，因此不涉及其他任何隐私信息）

## 9.3-9.5

* 保证每个院都有学生信息，并且都能正确加载到缓存；

* 现在，所有前后端交互操作都将在缓存上进行，而每隔10分钟，将会将缓存中的数据同步到数据库（学生得分信息、问题种子等）

* 考虑院系概况更新的并行问题。当一个学生交卷并开始更新对应院系概况时，将会暂时阻塞其他想要获取院系概况的线程继续执行。

* 缓存增加：Http响应缓存

* 性能：编译型语言的固有优势

* 程序安全：
  * 对所有从API来的外部输入进行检查。通过两种方法进行：
    1. 将前后端发出和接收的json内容绑定到一个Model类上，通过.Net Core的Model Binding进行初始化。这个过程同时也会对参数合法性进行基本检验，并反馈在ModelState.IsValid属性上；
    2. 通过业务逻辑对传进来的内容进行额外检验，如学号是否符合格式等。

  * 跨站点伪造请求攻击：CSRF/XSRF

    登录验证成功后，后端会返回两个token，一个作为以后发送的cookie，另一个则被前端用来作为发送请求时的Header，从而防止CSRF。

  * SQL注入：通过以下几点来防范：

    * 用来查询的数据是以C#的强类型变量表示的，因此隔绝了除字符串变量外的不合法输入
    * 对于字符串变量，在控制器接收参数后进行合法性检查。
    * 所有对数据库的查询工作都被封装在Entity Framework中（而不是使用原始sql查询语句），而Entity Framework包含了对参数的parameterlize与escape工作。

  * ​