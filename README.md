# My-Sphinx-Note
Gyc' sphinx git-note.
这里是基于Sphinx编写的博客页面源码。执行命令：
> git clone https://github.com/gch8295322/My-Sphinx-Note.git
> 
> ./make clean
> 
> ./make html

在 **./build/html** 中找到 **index.html** 即为博客主页，主页托管在Read the Docs上。地址：https://gycs-blog.readthedocs.io/en/latest/
更新好博客后，采用如下命令更新到git上，Read the Docs就会自己更新（或去主页手动编译更新）：
>
> git add .
>
> git commit -m "latest_commit"
>
> git push -u origin master
