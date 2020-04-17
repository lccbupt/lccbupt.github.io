### github 和 hexo 搭建简易博客

>参考网页方法：https://blog.csdn.net/gdutxiaoxu/article/details/53576018

    在安装好hexo就可以使用以下命令发布博客了
    
    hexo s                     --运行本地hexo博客
    hexo new post "blog name"  --生成新的文章
    hexo g                     --生成
    hexo d                     --部署
    
    ps:在部署到github之前最好能hexo clean 一下，清楚一些缓存，这样防止一些样式文件配置文件无法同步