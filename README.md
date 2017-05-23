# 内容目录  

>## 前言  
>## 认识markdown
>## Markdown的优势  
>## Markdown语法

___ 

# 正文  
## 1.前言  
Markdown是一种轻量级的「标记语言」，它的优点很多，目前也被越来越多的写作爱好者，撰稿者广泛，程序员使用。  
看到这里不要被[标记语言]吓到了，其实Markdown的语法非常简单，甚至你只需花上半个小时就能掌握其语法，相对于  
较为复杂的html语法来说，里面没有那么多的标记符号，常用的也就10多个，一旦你花点时间掌握后，就可以终身受益。  
和我一起开启学习Markdown之旅吧！
## 2.认识markdown  
前面说过Markdown是一种用来写作的轻量级「标记语言」，它用简洁的语法代替排版，而不像一般我们用的字处理软件  
Word或Pages有大量的排版、字体设置。它使我们专心于码字，用「标记」语法，来代替常见的排版格式。例如此文从  
内容到格式甚至插图，键盘就可以通通搞定了。目前来看，支持Markdown语法的编辑器有很多，包括很多网站（例如[简书](https://www.jianshu.com)）  
也支持了Markdown的文字录入。Markdown从写作到完成，导出格式随心所欲，你可以导出HTML格式的文件用来网站发布，  
也可以十分方便的导出PDF格式，这种格式写出的简历更能得到HR的好感。甚至可以利用[CloudApp](http://www.getcloudapp.com/)这种云服务工具直接上传  
至网页用来分享你的文章，全球最大的轻博客平台[Tumblr](http://te1ee.tumblr.com/)也支持Mou这类Markdown工具的直接上传，所以作为一名合格的程序员,Markdown更是你的基本功。  

## 3.Markdown的优势  
- 简洁高效，易读易写，学习成本极。
- 轻松的导出 HTML、PDF 和本身的 .md 文件。  
- 专注你的文字内容而不是排版样式，安心写作。
- 纯文本内容，兼容所有的文本编辑器与字处理软件，可以随意的复制黏贴，而且格式基本上不改变。  

## 4.Markdown语法  

### 4.1. 引用  
由于下面的内容大多数涉及到这个样式，所以先介绍引用部分。  

**语法：**
  
 `> 引文内容`  
  `>>二重引文内容`  
  `>>>三重引文内容`  
 ..............
  
**效果：**
>引文内容    
>>二重引文内容  
>>>三重引文内容    
 .....

### 4.2. 标题  

**样式一：**  
>一级标题  
    `=======`  
>二级标题  
    `--------`  

**效果：**
>#一级标题  
>##二级标题  
 
**样式二：**
> #一级标题  
> ##二级标题  
> ###三级标题  
> ####四级标题  
> #####五级标题  
> ######六级标题   

**效果：**
> # 一级标题  
> ## 二级标题  
> ### 三级标题  
> #### 四级标题  
> ##### 五级标题  
> ###### 六级标题   

### 4.3. 段落  
段落由普通文字组成，不需要添加任何标记。如果需要区分行与行，让文字断句更加美观，则需要在断句的地方回车  
且在行与行之间多键入一个空行。如果感觉行间距大的话，可以采取在需要断句的地方键入两个空格，也能让文字换行。  
这里就不演示了，自己动手尝试。  

### 4.4. 强调  
>** 粗体 ** 或__粗体__  
>*斜体 *  .       
>***倾斜加粗 ***或者___倾斜加粗 ___  
>~~删除线 ~~  

**效果：**
> **粗体** 或 __粗体__  
> *斜体*  
> ***倾斜加粗***或者 ___倾斜加粗___  
> ~~删除线~~  

### 4.5. 列表  
通常用` * `、` + `或者` - `标记无序列表
#### 无序列表：    
**效果：**
> + 列表1  
> + 列表2  
> + 列表3  

##### 多级无序列表：  
在每个父极列表后增加两个空格，即：二级列表在一级列表的基础上键入两个空格，三级列表在二级列表基础上键入两个空格  
注：为了更好的体验效果，养成在回车前，先在回车的地方键入两个空格的习惯。  

**效果：**
> - 一级列表1   
>    - 二级列表1  
>      - 三级列表1  
>      - 三级列表2  
>    - 二级列表2
>    - 二级列表3
> + 列表2  
> + 列表3  

#####有序列表：
  在无序列表的基础上，把符号改成阿拉伯数字即可  
  
**效果：**
>1. 一级列表1    
>    1. 二级列表1    
>       1. 三级级列表1     
>       2. 三级级列表2      
>       3. 三级级列表3    
>    2. 二级列表2    
>    3. 二级列表3    
>2. 一级列表2  
>3. 一级列表3  
### 4.6. 水平分割线
`<hr/>`    horizontal rules  

水平分割线有三种表示方式:`*`号、减号`- `和下划线`_` 
>-   `***  `
>-   `---  `
>-   `_ _ _  `
 

**效果：**
    
---

### 链接
####  内嵌式链接：  
**语法：**    
**` 外部链接：[链接名称](url)`**    
>**`例如：[百度](https://www.baidu.com)`**  [百度](https://www.baidu.com)  

**` 内部链接(链接本文档的其他部分，一般用于目录索引)：[索引目录的名称](本文档的名称.md#索引目录的名称) `  **  

举例说明:假设我要给我的**Markdown的优势**这个目录添加一个索引，以至于我鼠标一点击这个目录，就能跳到这个目录下的内容区。    
**注意:**如果索引的名称中含有空格的话，需要用**-**来替代 。  
**`[Markdown的优势](README.md#Markdown的优势)` **
**效果:**  
>[Markdown的优势](README.md#Markdown的优势)  


#### 引用式链接    
这里直接举例子说明更简洁明了，所以也就不啰嗦了，直接上干货。
- 外部链接:[百度]  
- 内部链接(链接本仓库的其他文件，这里以我的仓库为例):[Markdown]  
- 内部链接(链接本文档的其他部分，一般作为索引目录):[Markdown的优势]    

**注释:使用引用式链接时，需要在文档的最下引用<!--下面是本文档中用到的引用链接-->**  
**` [百度]:https://www.baidu.com `**  
**`[Markdown]:Markdown.md`**
**`[Markdown的优势]:README.md#Markdown的优势`**









 




