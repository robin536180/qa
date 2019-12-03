# 基于简单表格的问答系统
基于简单表格的问答系统

应用场景：

你有一些简单表格数据：
----------------------------------
人名    职业    年龄    性别    兴趣

张三    老师    45岁    男      看书

李四    学生    18岁    女      画画

王五    厨师    30岁    男      电影

赵六    司机    30岁    女      画画


------------------------------------

我们把第一列 看作实体列表，第一行，看作属性列表，表中内容 是值。

你想让机器能根据表格回答用户的问题： 张三兴趣是什么 ，查表得知--"看书"。

王五的性别是什么?

。。。。。。


在实际应用中，表格的行列字段可能变换，但我们希望程序能够自适应各种字段，支持百万以上条数据，都能读懂数据内容，解答关于各个表的自然语言问题。

有的问题是查询表中的值，有的问题是已知值查实体，或属性，或者值关联的实体的属性的另一个值......

这样只要更新表的数据，无需再编程。

如果你觉得系统支持的问题不够多，可以输入一条简单的问答示例，系统就能从你的例子里学习到你的回答逻辑和知识发现思路，完成触类旁通的字段问答。


------------------------------

安装方法：

文档和数据在dockerhub
docker版本Docker version 18.09.7，

安装docker后使用命令安装镜像：

docker pull zb1840/kbqa:v1
需要在你的系统新建一个空的目录 /opt/user来放入日志和系统生成的各种临时文件。

mkdir /opt/user;

用命令启动docker里的服务服务：
docker run -it -v /opt/user:/opt/user -p 29999:29999 -p 3004:3004   -d zb1840/kbqa /bin/bash &

running......
三分钟后 ,服务加载数据启动完成，并自动生成自测日志  /opt/user/firstrun.log. 
然后你可以登陆系统自带的ssh服务管理虚拟环境中的服务，
ssh -p 29999  root@127.0.0.1
密码是六个空格，登陆。

现在可以开始使用服务，测试脚本/mnt/sdb/shell2/demo2.sh 

############################################################
硬件需求：8G内存 30G磁盘空间

使用方法：
准备好数据文件，写入"测试.txt"
cd /mnt/sdb/shell2/

cat 测试.txt 

人名    职业    年龄    性别    兴趣

张三    老师    45岁    男      看书

李四    学生    18岁    女      画画

王五    厨师    30岁    男      电影

赵六    司机    30岁    女      画画

运行./demo2.sh 进行数据处理。大约几秒钟后。

输入问题 ：李四的性别是什么

访问3004的端口服务：

curl http://127.0.0.1:3004/api/xlsqa/test6/李四的性别是什么

输出：  李四 性别 女,


 curl http://127.0.0.1:3004/api/xlsqa/test6/谁的职业是老师
 
 输出：张三, 职业 老师
 


你可以更改 测试.txt 里的数据，csv格式，列之间用"\t" tab符号间隔。第一行是属性，第一列是实体，针对实体和属性问答。

默认支持相似语义句型的查询：
curl http://127.0.0.1:3004/api/xlsqa/test6/李四的兴趣是什么
李四 兴趣 画画,
curl http://127.0.0.1:3004/api/xlsqa/test6/李四的爱好是什么
李四 兴趣 画画

------------------------------
基于问答例子的学习：
针对测试.txt这个csv文件，的问答对场景的学习的配置文件路径是/mnt/sdb/shell2/testqa22.txt
修改/mnt/sdb/shell2/testqa22.txt 后，运行./demo2.pl使之生效。
在testqa22.txt里输入一行：

张三的个人爱好是什么 看书

运行./demo2.pl
原本不能回答的问题“ 李四的个人爱好是什么” 现在也能回答了。

当你在/mnt/sdb/shell2/testqa22.txt 增加一条配置：

谁是司机  赵六

运行demo2.pl

输入问题：

”谁是老师“

系统就可以回答"张三"了



这个例子里，你在配置文件里用 谁是XX 答案是YY  交给了系统如何解决谁是XX这样的问题。

再如：

调教：Q:老师几岁  A:答案45岁

再问系统，Q:厨师几岁， A:30岁。

其他更多复杂多跳问题正在开发测试中。


如果希望开启同义词泛化问题支持，需要在/opt/user/loaddic写入1 重启docker镜像服务即可。服务会载入向量词典帮助自动扩充多样化的问法。



本项目中使用北京大学邹磊老师团队开发GSTORE开源数据库。参考链接：
https://github.com/pkumod/gStore

语义模糊搜索插件（可选配置）使用NGT向量搜索引擎：
https://github.com/yahoojapan/NGT

作者张博QQ：75926462，微信搜索：zb1840，电话13424269685

