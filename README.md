bookfetcher
===========
<p>项目主页：bookfetcher.sinaapp.com <a href="http://bookfetcher.sinaapp.com">点击进入</a></p>
<p>DEMO：<a href="http://bookfetcher.sinaapp.com/Demo/index">点击进入</a></p>
<p>BookFetcher是一个实现各大主流网上书店图书数据抓取的类库，能根据搜索关键字返回各大网上书店的搜索列表数据，或根据图书的ISBN码精确抓取图书的详细数据。</p>
<p>基于BookFetcher我们对外开放了公共API，您只需要访问我们的Open API 便可快速获得图书数据，数据在初次采集时会保存在我们的服务器，避免重复采集。</p>
<p>目前支持的目标站点有：</p>
<ul>
  <li>卓越亚马逊</li>
  <li>博库网</li>
  <li>当当网</li>
  <li>京东图书</li>
</ul>
<hr>
<h2 >有什么用？</h2>
<ol>
  <li>获取任意一本书的详细信息，海量图书数据采集</li>
  <li>快速开发一个图书比价应用 <a href="http://bookfetcher.sinaapp.com/Demo/index">[DEMO]</a></li>
  <li><del>轻松搞定某高校CS专业“B/S体系软件设计”课程设计</del></li>
</ol>
 <h2 >如何使用？</h2>
             <p>BookFetcher类主要实现了以下几个方法：</p>
            <dl class="dl-horizontal">
              <dt>search：</dt>
              <dd>传入关键字获得搜索列表</dd>
              <dt>detailFetch：</dt>
              <dd>传入id获得图书的详细信息</dd>
              <dt>quickFetch：</dt>
              <dd>传入ISBN码获得图书的详细信息</dd>
              <dt>setProxy：</dt>
              <dd>设置采集代理</dd>
              <dt>getUrl：</dt>
              <dd>传入id获取图书详情页URL</dd>
              <dt>picSizeSwitch：</dt>
              <dd>传入图片URL，切换图片的不同尺寸</dd>
              <dt>getError：</dt>
              <dd>返回最近一条错误信息</dd>
            </dl>
            <p>详细内容请查看<a href="http://bookfetcher.sinaapp.com/Index/document">[API手册]</a></p>
            <h3>1.使用BookFetcher类库</h3>
            <p>
              BookFetcher目前只有PHP版本，您只需<a href="http://bookfetcher.sinaapp.com/Index/download">[下载]</a>并导入BookFetcher类库，实例化相应BookFetcher类，然后调用API即可。
            </p>
            <p>代码包(for php)目录结构如下：</p>
            <pre class="prettyprint">BookFetcher/
├── BookFetcher.class.php // BookFetcher基类
├── Snoopy.class.php // 基于socket的php数据采集类，感谢原作者
├── Amazon.class.php // 亚马逊数据采集类
├── Bookuu.class.php // 博库网数据采集类
├── DangDang.class.php // 当当网数据采集类
└── Jd.class.php // 京东图书数据采集类
</pre>
           <p>关键字搜索：</p>
           <pre class="prettyprint linenums">//实例化亚马逊数据采集类，并搜索关键字“计算机网络”，将返回的结果输出
require_once("./BookFetcher/Amazon.class.php");
$bf = new Amazon;
$list = $bf->search("计算机网络");
var_dump($list);</pre>
           <p>图书详细信息获取：</p>
           <pre class="prettyprint linenums">//实例化当当网数据采集类，首先搜索“小时代”，然后抓取搜索列表中第一条记录的详细信息
require_once("./BookFetcher/Dangdang.class.php");
$bf = new Dangdang;
$list = $bf->search("小时代");
if($list){
  $id = $list['list'][0]['id'];
  $detail = $bf->detailFetch($id);
  if($detail)
    var_dump($detail);
  else
    echo $bf->getError();
}else
  echo $bf->getError();</pre>
  <p>通过ISBN快速获取图书详细信息</p>
           <pre class="prettyprint linenums">//实例化京东图书数据采集类，获取ISBN码为9787302274629的图书的详细信息
require_once("./BookFetcher/Jd.class.php");
$bf = new Jd;
$detail = $bf->quikFetch("9787302274629");
if($detail)
  var_dump($detail);
else
  echo $bf->getError();</pre>
  <h3>2.使用BookFetcher Open API</h3>
            <p>
              <blockquote>BookFetcher追求的不仅是简化数据抓取流程，还要加快抓取速度，提高抓取效率。因此我们提供了公共API，您只需通过HTTP协议访问我们的API就能获取数据，采集工作将在我们的服务器完成，这能降低您本地服务器的带宽和CPU压力，不仅如此，每一条数据我们只抓取一次，数据会缓存在数据库，在接收到相同的数据抓取请求时（search请求除外），将直接从数据库获取数据并返回，避免无谓的重复采集。</blockquote>
            </p>
            <h4>1.在类库上开启BookFetcher Open API:（推荐）</h4>
            <p>BookFetcher类库已经封装了Open API，默认不开启，您只需在实例化BookFetcher类的时候传入bool true给构造函数即可开启，开启了Open API后您依旧按上述方法调用类的方法即可，不同的是BookFetcher将不在本地完成采集工作，而是通过访问BookFetcher Open API获取数据。</p>
           <pre class="prettyprint linenums">//使用BookFetcher Open API获取数据
require_once("./BookFetcher/Jd.class.php");
$bf = new Jd(true);//传入bool true开启BookFetcher Open API
$detail = $bf->quikFetch("9787302274629");//和原来一样调用类的方法即可
if($detail)
  var_dump($detail);
else
  echo $bf->getError();</pre>
          <h4>2.直接访问BookFetcher Open API:</h4>
          <p>BookFetcher目前只有php版本，对于使用其它编程语言的应用，在我们开发出其相应版本之前，可以通过访问BookFetcher Open API抓取数据。Open API 的地址和参数请移步至 <a href="http://bookfetcher.sinaapp.com/Api">[Open API]</a></p>
          <hr>
          <h2 >为什么只做 book?</h2>
            <p>每一本书都有一个唯一的ISBN码，这使得图书数据的采集变得可行和有意义。</p>