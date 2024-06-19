---

layout: post

title: 如何在Jekyll博客中插入图片

date: 2024-06-19

categories: 计算机

tags: jekyll

description: 在markdown文档中插入图片

---
# 如何在markdown文档中插入图片


## 解决办法：
使用 Github 作为图床在博文中插入图片<br />
在博客根目录下创建一个储存图片的文件夹pictures（或任何名字）<br />
把需要插入的图片保存到pictures文件夹里并push到github远程仓库<br />
在github仓库中打开图片，复制url，如 https://github.com/Yuleii/Yuleii.github.io/blob/master/pictures/test.PNG<br />
将URL中blob替换为raw，即 https://github.com/Yuleii/Yuleii.github.io/raw/master/pictures/test.PNG<br />
使用markdown插入图片的语法即可显示图片<br />
```
<div align=center>
<img src="https://github.com/AKangle123/akangle123.github.io/raw/master/attachments/Clipboard_2024-06-19-17-53-21.png">
</div>
```


