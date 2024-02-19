# 类Ext2文件系统
基于Ext2的思想和算法，设计一个类Ext2的文件系统，实现Ext2文件系统的一个功能子集。并且用现有操作系统上的文件来代替硬盘进行硬件模拟。

## 代码主要思路

### 一些概念和理解

- **文件系统存储结构**
<img width="345" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/92826acc-3e90-4a80-a582-4a9d70906c1f">
 
- **文件访问权限**
<img width="101" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/a62b9fe0-916a-43e3-8f97-f0867e5d45be">

- **多级索引：直接索引6块，一级索引512/2=256块，二级索引256x256=65536块**
<img width="319" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/90af9320-6c32-49d4-87b5-dc9457ed2a7c">

- **三种时间：**
  - `CreateTime`：目录或文件创建时的时间
  - `LastAccessTime`：上一次目录列出所有项，文件读出的时间
  - `ModifyTime`：目录创建新文件或目录、删除子目录或文件，文件写入的时间

- **命令层操作准备：**
  ```c
  Void Fun() {
      fp=fopen(PATH"rb+");//文件打开权限不同函数可能不同
      ext2_inode cur_inode;
      reload_inode_entry(currentdir_inode&cur_inode);//获取当前目录的索引结点
      ...
      fclose(fp);
  }


## 功能测试结果

### 1.初始化及输入密码登陆
<img width="286" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/66f4fe37-cdc2-4a7b-8182-5cb41c481b93">

初始密码为666，若输入错误有一次重新输入密码的机会，两次输入都错误则退出文件系统

### 2.下面是通过help命令显示出的该文件系统可以实现的功能以及相应的命令
<img width="415" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/a344eaee-e9a4-4b72-bddb-5834f6a691bc">

### 3.ls：列出当前目录下所有项
<img width="415" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/920e73e5-4ddf-4b9c-a6ac-015c9ea2c84b">

- 当前目录为根目录，且初始化后没有创建文件或目录，因此只有两个目录项。
- 目录的size表示目录下所有目录项的大小之和，文件的size表示文件内容的大小所占的字节数。
- 文件或目录新创建时默认权限都为r_w_x，可以使用命令进行修改。由于此处并没有不同类型的用户，因此imode只设置了一组值。

### 4.mkdir+dir_name：在当前目录下创建一个目录
<img width="416" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/ba10bbf9-abcf-4ba5-b3cb-d464892f9e98">

成功创建目录dir1，并且可以看到根目录的modifytime修改为了dir1的createtime。

### 5.mkf+file_name：在当前目录下新建一个文件

- 成功创建文件file1，并且再次ls可以看到lastaccesstime被修改为上一次使用ls命令访问根目录的时间
- 可以看出，在同一目录下不允许存在同名文件或同名目录，也不允许文件和目录同名。
下面进行一些文件和目录的创建以便进行后面的测试。

root/

├── dir1/ 

│ ├── dir11/ 

│ └── file11

├── file1 

├── file2 

├── dir2/

│ ├── dir21/ 

│ └── file_a

└── dir3 

创建好后访问根目录如下：

<img width="416" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/cf93d77f-1038-4ccb-81ae-c0ac96a9eeb7">

### 6.cd+dir_name/path：改变当前目录路径
进入当前目录下的子目录dir1：

<img width="416" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/b38c3646-8fdd-4fa4-82cd-1e71dc4bbc4e">

返回上级目录，再使用相对路径进入dir1下的目录dir11：

<img width="167" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/81ab9100-d5a5-4099-ae0c-d81710a26d32">

在任意目录下使用绝对路径可进入另一路径下的目录：

<img width="415" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/ff4887b7-7344-435c-a609-c4312136483d">

使用cd  /命令可直接返回根目录：

<img width="415" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/b43d8c73-afcf-4fda-a35d-b55fe5f0f519">

### 7.chmod+name+rwx：修改当前目录下名为name的目录或文件的访问权限，rwx为包含想要设置权限字符的字符串
<img width="240" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/11a807f7-97b5-4c1d-819e-5cd6523af7ed">

- **测试x权限：**
<img width="342" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/6f6673fc-bf39-495d-a51c-e362c5b3cf1b">

- **测试w权限：**
<img width="416" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/78076079-e9bf-4b45-8355-987aa427c5e5">

- **测试r权限：**
<img width="335" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/7050b89a-896d-43b4-bddc-e751d3790e62">

### 8.rmdir+dir_name：删除空目录，若目录不为空则不能删除
<img width="276" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/facbd5ae-4a4c-477e-a93e-44e43102012a">

- dir1不为空所以不能删除
- 当前目录下不存在dir所以不能删除
- dir3为空目录被成功删除
### 9.rmdir+-r+dir_name：删除目录及目录下所有目录和文件
<img width="415" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/f0a640ef-0f66-47d2-8bf8-5dcb83f2a012">

### 10.open+file_name：打开文件，close+file_name：关闭，read+file_name：读出write+file_name+>/>>+...：>为写入时覆盖原文件，>>为写入时追加内容到文件末尾
(1)测试打开文件和读文件

<img width="167" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/0218c044-359b-4157-9df0-454e03ca1821">

- 文件未打开时不能进行读写操作
- 成功打开file1，未写入时读取文件显示文件为空
  
(2)测试两种写入文件的方式

<img width="194" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/2fcf6c65-ffae-47a7-8a94-59ce88da3897">
<img width="416" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/a9e16399-cff3-4e3b-b2c5-4613c129ebc3">
 
(3)测试写入过程中数据块的分配及文件的访问时间和修改时间

<img width="168" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/fa8aa608-7588-4844-942f-bbeca79e13cb">

<img width="415" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/f2b33660-b386-4c3d-aa26-47f39cb56440">

- 第一次输入103个a覆盖文件原有内容，再追加103x4个字符，总共515个字符，一个数据块存不下，需要在写入时分配数据块，可以从文件大小及读出结果看出成功分配了数据块。
- 从ls的结果可以看出，file1的modifytime在写入文件时被修改，lastaccesstime在读文件时被修改
  
(4)测试文件的关闭

<img width="192" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/c19ac4ba-7c86-4688-8192-3161c1833105">

(5)测试文件的访问权限

<img width="416" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/05cfe467-42e3-4ce7-9628-5164ce97995a">

### 11.password：修改密码
<img width="213" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/a062fcff-36e3-4eed-a971-9a101cbcb570">

- 修改访问权限时需要输入密码，这里使用这个功能来验证密码是否成功被修改
### 12.login：登录   logoff：登出
<img width="157" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/491502e9-92c9-41cc-84fb-c9ece7297669">

- 未登出时不能重复登录
- 登出后重新登录文件系统内容保持不变
### 13.若输入不存在的命令，系统会给出提示
<img width="254" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/c9e56960-af66-4081-858c-0e31dff64376">

### 14.退出文件系统
<img width="415" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/eab5b956-2343-40a2-8c2a-81a380fc83d0">

### 15.format:格式化
<img width="171" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/fd09d271-e754-4f09-ad2a-b4d5f8f67e7b">

### 16.测试多级索引
<img width="415" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/650c513c-8c31-4df2-8773-9f5f48018a21">

<img width="415" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/0b314a74-8b12-4fad-8d30-7221b4dcbbee">

<img width="416" alt="image" src="https://github.com/xuchangtolearn/File-System--Ext2--XJTUOS-/assets/147292722/ae86b923-cbb4-4b14-a428-5cf2edefcfe9">

输入两次2500个字符，共5000个字符，需要10个数据块来存储（如果没有索引文件是无法分配这么多块的）。从运行结果可以看出，文件成功分配了5000字节空间并能读出5000个a（太长了，没有放全），之后成功删除文件并在删除时关闭了文件。

## 遇到的问题和困难

### 1.用来模拟磁盘的文件始终为空
原因：使用fopen打开文件时未正确设置权限，每次打开时都使用wb+清空了文件

解决方法：通过查阅资料设置了正确的权限
(https://blog.csdn.net/qq_37037348/article/details/130563950)

### 2.输出的同一个结点的三个时间始终相同
原因：localtime函数实现的时候采用的是一块固定buffer，因此如果多次调用此函数，结果值会是最后一次的结果值

解决方法：在localtime函数调用后直接将buffer内容拷贝出来
(https://blog.csdn.net/cleanfield/article/details/6224804)
### 3.每次需要从磁盘中读出一整个块，如何从读出的数据块缓冲区中分离出需要的目录项结构体
解决方法：使用memcpy函数可以在不同类型的变量之间进行拷贝
(https://blog.csdn.net/GoodLinGL/article/details/114602721?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522170124371616800222899886%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=170124371616800222899886&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-114602721-null-null.142^v96^pc_search_result_base3&utm_term=memcpy&spm=1018.2226.3001.4187)
