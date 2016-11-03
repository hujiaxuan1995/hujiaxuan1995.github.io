title: hexo-tinny 主题添加多说
date: 2015-05-24 21:08:42
tags: hexo
categories: hexo
---
为了配置多说评论，也是把网上的教程搜了个遍。因为tinny的主题和其他的主题不太一样，所以很多网上的东西是不适用的。（*不懂nodejs啊*）
###首先
你得先去多说注册帐号[多说](http://duoshuo.com/),注册后记住你的short_name，short_name就是xxxxxxxx.duoshuo.com中的前半段。注册完成后，会跳转到获取代码，但是在tinny中是不需要重新粘贴代码的，tinny中这一段代码在Tinny\layout\_partial\post\comment.ejs中。
###然后

    <% if (theme.duoshuo.enable && page.comments){ %>
        <!-- 多说评论框 start -->
        <div class="ds-thread" data-thread-key="<%= page.path %>" data-title="<%= page.title %>" data-url="http://hujiaxuan.me/<%= page.permalink %>"></div>
    <!-- 多说评论框 end -->
    <!-- 多说公共JS代码 start (一个网页只需插入一次) -->
    <script type="text/javascript">
    var duoshuoQuery = {short_name:"vensli"};
        (function() {
            var ds = document.createElement('script');
            ds.type = 'text/javascript';ds.async = true;
            ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
            ds.charset = 'UTF-8';
            (document.getElementsByTagName('head')[0] 
             || document.getElementsByTagName('body')[0]).appendChild(ds);
        })();
        </script>
    <!-- 多说公共JS代码 end -->
    <% } %>

把原来的data-thread-key，data-title，data-url中的汉字改成对应的变量（有的主题是post.comments）page.path,page.title,page.permalink。
###别忘了
在主题中的_config.yml中修改

    duoshuo: 
      enable: true  ## duoshuo.com
      short_name: vensli ## duoshuo short name.

enable为true，short_name为注册时的short_name。
###OK，收工