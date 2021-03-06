# node.js模块系统知识分享

#####分享人：小麦

######ppt制作：reveal.js

---

## 👉🏻node.js模块系统

- node.js使用基于模块系统的文件。每个文件就是它自己的模块。
  - 模块导出：`module.exports`变量
  - 模块导入：require函数。

--

- 例子
  - 在一个目录下创建**myData.js**文件以及一个函数，为了让文件导出这个函数，我们将其分配给module.exports。
    - ![屏幕快照 2019-05-11 下午9.24.12.png](https://upload-images.jianshu.io/upload_images/12192297-11e8260f32c357c6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  - 在同级目录创建myValue.js文件使用上面的函数，使用`require`函数导入myData，并将返回的值存储在局部变量中。
    - ![屏幕快照 2019-05-11 下午9.24.21.png](https://upload-images.jianshu.io/upload_images/12192297-25035c8fd3f68a6c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

--

#### 导出多个变量的方法

1. 创建一个新的对象字面量并将其分配给module.exports
   - ![屏幕快照 2019-05-11 下午9.24.48.png](https://upload-images.jianshu.io/upload_images/12192297-2958cb7931c9cb03.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2. 做为`module.exports`的属性，一个一个导入
   - ![屏幕快照 2019-05-11 下午9.24.57.png](https://upload-images.jianshu.io/upload_images/12192297-05480bc4673c9424.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
3. 直接用exports别名，其实 exports = module.exports
   - ![屏幕快照 2019-05-11 下午9.25.05.png](https://upload-images.jianshu.io/upload_images/12192297-e3ce9b0dbb7d0afb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

--

###### 上面三种方法是等效的

![image.png](https://upload-images.jianshu.io/upload_images/12192297-844eed00bab7e09f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

--

### 模块的最佳实践

1. **使用相对路径**：在使用基于文件的模块时，你需要使用相对路径(也就是用require('./myData')而不是require('myData'))。
2. 导出整个文件夹：当你有很多模块都需要导入其他文件的时候，使用一个文件作为中间文件。
   1. 例如
      1. ![屏幕快照 2019-05-11 下午9.25.51.png](https://upload-images.jianshu.io/upload_images/12192297-66b4755863c98c8d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
   2. 那么我们可以创建一个index.js文件，导入上面所有文件
      1. ![屏幕快照 2019-05-11 下午9.25.57.png](https://upload-images.jianshu.io/upload_images/12192297-e1f9bf4df6a49a86.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
   3. 而当你需要使用这些文件的时候，你就只需要导入index文件。
      1. ![屏幕快照 2019-05-11 下午9.26.03.png](https://upload-images.jianshu.io/upload_images/12192297-66cac415b5868dfe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---

### nodejs require模块

###### Node.js require函数是将模块导入到当前文件的主要方式。在Node.js中有三种的模块:核心模块，文件模块和外部node_modules。

- 当我们使用相对路径(如require(./filename))调用require是，node.js会在新的作用域中运行目标js文件，并返回分配给该文件中module.exports的最终值。

--

### require函数的一些知识点

- 阻塞：require函数阻止进一步的代码执行，直到模块被加载完毕

- 缓存：第一次对特定文件进行require调用后，将缓存module.exports。

  - ![屏幕快照 2019-05-11 下午9.23.06.png](https://upload-images.jianshu.io/upload_images/12192297-90ff75da1eded630.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

    ![屏幕快照 2019-05-11 下午9.23.27.png](https://upload-images.jianshu.io/upload_images/12192297-88ae75bb0de9cf0d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

--

###### 核心模块

1. 核心模块在requiere()中具有最高优先级。在模块命名冲突的情况下，加载核心模块
   - 例如：Node包含一个名为http的核心模块。调用require('http')将始终加载核心http模块。

--

###### 解析模块位置

- 要找到某个模块的位置可以使用require.resolve()函数。它可以返回模块的路径
  - 如果是核心模块，则返回模块的名称
  - 如果是文件模块，则返回模块的文件名。
  - 如果找不到的话，即会抛出错误

---

### nodejs File（文件）模块

###### 文件模块是从文件系统加载的模块，可以使用绝对路径，相对路径或node_modules目录来引用文件模块。

- 绝对路径：一斜杠(/)开头的模块名称被视为绝对路径，例如require("/some/path/foo")；
- 相对路径：以一个或两个点(.或..)开头的模块路径被称为相对路径。它们被认为是与调用require()有关的文件，如(require("./foo"))。 
- 如果模块的路径不对应于核心模块，绝对路径或相对路径，node就会在node_modules文件夹中进行搜索，首先是从脚本的父目录开始，并附加/node_modules。找不到的话，node会沿目录树向上移动一级。追加/node_modules，然后再搜索。
- 重复上面模式，直至找到模块或目录结构的根目录。如果找不到就会抛出错误。

---

### nodejs path模块

- __dirname:表示当前执行脚本所在的目录 
- __filename:表示当前正在执行的脚本的文件名。它将输出文件所在位置的绝对路径。
- path.join()**：**用于连接路径。该方法的主要用途在于，会正确使用当前系统的路径分隔符，Unix系统是”/”，Windows系统是”\”。
- 例如
- ![image.png](https://upload-images.jianshu.io/upload_images/12192297-09e1b4693e559507.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- ![image.png](https://upload-images.jianshu.io/upload_images/12192297-9856355b1db3e61d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)