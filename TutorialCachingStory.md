# 学习Memcached的一次冒险

两位中学时期的好朋友，李雷和韩梅梅，开始了他们的创业之旅。他们一起开始做网站。是那种经典的Web服务器外加数据库结构的网站。来自互联网的每个的用户通过对Web服务器请求他们所需要的页面，Web服务器就找数据库要构建这些页面所需要的数据。

李雷负责编程，韩梅梅负责维护管理Web服务器和数据库。

一天，韩梅梅发现他们的数据库生病了! 由于负荷过重而上吐下泻！韩梅梅发现它的平均负载有20! 李雷就问韩梅梅: “啊，那我们咋办哟!” 韩梅梅说: “我听说memcached这种技术不错，它在livejournal中发挥了很大作用呢!” “好吧，那我们也试试看吧!” 李雷回答到。

韩梅梅看了看她的6台Web服务器，分析了一阵，然后她决定用其中的3台来运行这个memcached服务器。她把每台Web服务器都额外添加了1G的内存 – 也就是说，现在他们有3个memcached实例, 每个能存1G的数据。

搞定之后，李雷和韩梅梅静坐在一旁，看着他们刚架设好的memcached 服务器。

然后呢？ “它一点动静都没有啊，这些memcached没有和其他任何程序通信啊，也没有任何有用的数据, 更惨的是，现在数据库的负载到了25了!”

无畏的李雷拿起了memcached客户端使用手册,对着韩梅梅深情配置好的memcached服务器说，“有什么好怕的!”他说. “我想到一个点子了” 他把那3台memcached服务器的ip地址和端口添加到一个php 数组中:

    $MEMCACHE_SERVERS = array(
    "10.1.1.1", //web1
    "10.1.1.2", //web2
    "10.1.1.3", //web3 );

然后他创建了一个叫'$memcache'的对象

    $memcache = new Memcache();
    foreach($MEMCACHE_SERVERS as $server){     
        $memcache->addServer ( $server );
    }

李雷继续想啊想,想啊想, 突然灵机一动: “对了! 我记得有个页面经常执行`SELECT * FROM hugetable WHERE timestamp > lastweek ORDER BY timestamp ASC LIMIT 50000;` 这个查询要花费5秒的时间呢! 我来把他放到memcache里面吧”, 结果他就把这些查询放进'$memcache'对象中, 他的代码询问:

Select语句所得的结果在我的memcache里面吗? 如果不在, 就执行查询并把结果放到memcache里:

    $huge_data_for_frong_page = $memcache->get("huge_data_for_frong_page");
    if ($huge_data_for_frong_page === false) {
        $huge_data_for_frong_page = array();
        $sql = "SELECT * FROM hugetable WHERE timestamp > lastweek
                                ORDER BY timestamp ASC LIMIT 50000";     
        $res = mysql_query($sql, $mysql_connection);
        while($rec = mysql_fetch_assoc($res)) {
             $huge_data_for_frong_page[] = $rec;
        }     
    // cache for 10 minutes
    $memcache->set("huge_data_for_frong_page", $huge_data_for_frong_page, 600); }
    // use $huge_data_for_frong_page how you please

李雷提交了他的代码. 韩梅梅胆战心惊~ 呼~~ 数据库负载下降到10了! 网站速度良好!

不过，韩梅梅就纳闷了，怎么一回事啊? 我用cacti看到所有的数据请求都是去了其中一个memcached服务器,可是我明明建立了3个啊? 韩梅梅很快学会了用ASCII协议telnet一个个登录她设立的memcached 服务器进行查询:

喂喂, huge\_data\_for\_front\_page, 你在哪里?<br />
第一个memcached 没反应<br />
第二个memcached也没反应<br />
第三个memcached, 吐出来一大堆的Q$%@#$&%*($^@#^#$WY%@^N@$%<br />
数据! 只有第三台memcached缓存了李雷的数据!

搞不懂, 韩梅梅只好在邮件列表里面去询问, 她得到的回答惊人的一致, “这是因为memcached是一个分布式的缓存系统!” 但是这又是什么意思呢? 韩梅梅还是不是很懂,她走到李雷面前,让他缓存更多的数据 “让我们静观其变, 我们一定能搞懂它的!”

“好吧, 我手头上还有一个很慢的查询语句, 而且每秒还需要执行100次, 或许我们可以把它也缓存起来” 李雷二话不说就去编程了, 就像之前那样, 数据库的负载下降到8了!

李雷继续把越来越多的数据缓存起来,他还使用了在FAQ学到的新技术! 与此同时, 数据库的负载继续下降: 7,5,3,2,1 !

“很好”韩梅梅说, “让我们继续” 她继续观察系统的图表, 三台memcached都运行起来了! 他们都在处理请求!
韩梅梅继续在三台memcached上查询李雷的输入数据,但每次其结果都只能在一台memcached里找到. 为什么要这样呢?她不解,奇怪了,把数据都在三台memcached上都存着难道不是更好么? "等一下"韩梅梅想,我给每台memcached机器1G的内存, 也就是说这样他们一共可以缓存3G的数据库数据,而不只是1G, 太棒了!

"mmm...,不过好像还是有问题, 其中一台memcached的机器已经过时了, 需要更新升级, 这样的话我就必须把这台机器给关了,这样一来我心爱的memcached会咋样啊~?" "那...让我试试看!" 韩梅梅二话不说,关掉了其中一台机器,然后她小心翼翼地观察系统曲线变化... "糟了,数据库的负载又直线上升了! 1...现在是2了,不过还算可以接受吧.
其余的2台memcached都还工作正常。不算太糟糕,只是多了一些无法被缓存的数据。" 韩梅梅试验完后, 把原先的机器重启, 几分钟后, 数据库的负载又恢复到1了。

"缓存又自动恢复了! 我懂了，如果其中有部分机器下线了，只是说会有部分应该缓存的数据不存在而已，没什么大不了的，偶稀饭!"

就这样，李雷和韩梅梅继续架设网站，继续缓存. 当他们遇到了什么问题了,他们就在邮件列表,或者FAQ来寻求答案.他们继续监视着数据库负载的图表，从此永远快乐地生活在一起。


Author: Dormando via IRC. Edited by Brian Moon for fun. Further fun editing by Emufarmers.
<br />
This story has been illustrated by the online comic [TOBlender.com](http://toblender.com/tag/memcached/).
<br />
[Chinese translation](http://newwaylw.blogspot.com/2011/11/blog-post_16.html) by Wei Liu.