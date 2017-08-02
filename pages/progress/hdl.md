<h2 id="711-720"><i class="icon-file"></i> 7/11-7/20</h2>

<p>构建登陆页login.html，答题页面index.html</p>

<blockquote>
  <ul>
  <li>添加向左翻页 向右翻页标志 <br>
  css雪碧图（利用学校素材） <br>
  background-position: a% b%; ＝》 background-position: x y; <br>
          其中：containerWidth为容器宽，containerHeight为容器高，bgWidth为雪碧图宽，bgHeight为雪碧图宽， <br>
      则存在（公式1）： <br>
      　　x = (containerWidth - bgWidth) * a% <br>
      　　y = (containerHeight - bgHeight) * b% <br>
  background-position 详解 <br>
  <a href="http://www.cnblogs.com/hity-tt/p/6306449.html">http://www.cnblogs.com/hity-tt/p/6306449.html</a></li>
  <li>鼠标停留在按键上 按键发生变化 <br>
  参考文档： <br>
  JQuery实现当鼠标停留在某区域3秒后自动执行 <br>
  <a href="http://www.jb51.net/article/54939.htm">http://www.jb51.net/article/54939.htm</a></li>
  </ul>
</blockquote>

<p>bug(I) jquery中文显示乱码</p>

<blockquote>
  <ul>
  <li>参考文档 <br>
  调用外部javascript的alert出现中文乱码 <br>
  <a href="http://www.2cto.com/kf/201702/601379.html">http://www.2cto.com/kf/201702/601379.html</a> <br>
  据说jquery对中文支持不太友好，将script中的gb-2312改成utf-8即可</li>
  </ul>
</blockquote>

<h2 id="723"><i class="icon-file"></i> 7/23</h2>

<p>转webpack</p>

<blockquote>
  <ul>
  <li>安装npm <br>
  打开git命令行，在github中clone下来npm的源码包</li>
  <li>git下载地址： <br>
  git clone –recursive git://github.com/isaacs/npm.git <br>
  npm其实是Node.js的包管理工具（package manager） <br>
  webpack 根据模块的依赖关系进行静态分析，这些文件(模块)会被包含到 bundle.js 文件中。Webpack 会给每个模块分配一个唯一的 id 并通过这个 id 索引和访问模块。 在页面启动时，会先执行 runoob1.js 中的代码，其它模块会在运行 require 的时候再执行。 <br>
  Webpack 本身只能处理 JavaScript 模块，如果要处理其他类型的文件，就需要使用 loader 进行转换。 <br>
  所以如果我们需要在应用中添加 css 文件，就需要使用到 css-loader 和 style-loader，他们做两件不同的事情，css-loader 会遍历 CSS 文件，然后找到 url() 表达式然后处理他们，style-loader 会把原来的 CSS 代码插入页面中的一个 style 标签中。</li>
  </ul>
</blockquote>

<p>BUG – UNSOLVED</p>

<blockquote>
  <ul>
  <li>webpack runoob1.js bundle.js –module-bind ‘css=style-loader!css-loader’ <br>
  不知道为啥 在运行的时候绑定不成功</li>
  </ul>
</blockquote>

<h2 id="724"><i class="icon-file"></i> 7/24</h2>

<p>BUG – 打包字体出现问题</p>

<blockquote>
  <ul>
  <li>加载字体 <br>
  { <br>
        // 专供iconfont方案使用的，后面会带一串时间戳，需要特别匹配到 <br>
        test: /.(woff|woff2|svg|eot|ttf)\??.*$/, <br>
        loader: ‘file?name=./static/fonts/[name].[ext]’, <br>
      },</li>
  </ul>
</blockquote>



<h2 id="725"><i class="icon-file"></i> 7/25</h2>

<p>BUG – 除了正常运行模式，ECMAscript 5添加了第二种运行模式：”严格模式”（strict mode）。顾名思义，这种模式使得Javascript在更严格的条件下运行</p>

<blockquote>
  <ul>
  <li>消除Javascript语法的一些不合理、不严谨之处，减少一些怪异行为;</li>
  <li>消除代码运行的一些不安全之处，保证代码运行的安全；</li>
  <li>提高编译器效率，增加运行速度；</li>
  <li>为未来新版本的Javascript做好铺垫。</li>
  </ul>
</blockquote>



<h2 id="728"><i class="icon-file"></i> 7/28</h2>

<p>将传统html+css+js形式网页改为vue框架 <br>
网站script初步设计结构如下</p>

<blockquote>
  <p>| Scripts <br>
  |–components <br>
  |——app (login界面) <br>
  |———app.ts <br>
  |———app.vue.html <br>
  |——href (答题界面，直接引用了之前的html+css传统格式页面) <br>
  |———index.html <br>
  |———index.css <br>
  |———index.js <br>
  |——home（登陆后主页） <br>
  |——counter（进入答题页面的跳转页） <br>
  |——fetchdata（答题结果页） <br>
  |——navmenu（导航栏）</p>
</blockquote>

<p>BUG – 提供模板无法提供readme里注释的”no need to run the webpack build tool”的功能，需要手动webpack –watch并刷新才能预览页面。</p>

<blockquote>
  <ul>
  <li>经前后端共同核查发现startup.cs文件中这一段代码错位： <br>
  if(env.IsDevelopment()){ <br>
  app.UseWebpackDevMiddleware(); <br>
  } <br>
  应插入在UseStaticFiles之前。</li>
  <li>更改startup.cs后，该问题得到解决，可以愉快地跳过webpack步骤啦~</li>
  </ul>
</blockquote>

<p>BUG – 添加背景图片(暂未解决)</p>

<blockquote>
  <ul>
  <li>在Vue项目开发中我们经常要向页面中添加背景图片，可是当我们在样式中添加了背景图片后，编译打包后，配置到服务器上时，由于路径解析的问题，图片不能够正确显示</li>
  <li>参考：《 Vue项目中设置背景图片 》 <br>
  <a href="http://blog.csdn.net/woyidingshijingcheng/article/details/72903800">http://blog.csdn.net/woyidingshijingcheng/article/details/72903800</a></li>
  </ul>
</blockquote>