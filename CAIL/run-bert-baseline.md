## CAIL信息抽取的baseline运行总结

### 数据生成

联合关系抽取模型需要很多不同类型的数据文件，而大部分需要data相关函数文件进行生成，不是一般直接一个train、test的data文件直接训练的那种，需要先运行数据处理生成函数

### 模型运行

这次的baseline使用的模型名字是错误的，需要改下名字（bert-base-chinese），不要完全相信和使用原来的模型名称

### 环境配置

首先需要安装必要的相关库，直接在vscode中直接安装即可，需要注意的是transformers文档中的版本是4.8.2，我试了很多遍，最后选择3.0.0版本才成功。

### gpu使用

注意cuda和pytorch版本匹配，另外还有30系列、A40、A4000、A5000、A100等安培架构的卡需要cuda 11.x。所以请选镜像torch1.9.0及以上，tf2.5.0及以上。

### 训练

报显存不够问题：

1. 模型太大导致显存不够
2. batch_size太大，先设为1再进行向上调试，不过太小会导致训练速度太慢

### 调试

尽量使用命令行进行调试，因为运行py文件需要参数，不然会报找不到文件等错误

注意train、test以及评分文件的区别

### 提交文件

需要自己写预处理文件

注意json文件内容的格式，比如关系中的两个实体不要搞反了，不然结果评价都是

### Linux下运行python程序

需要cd到当前文件夹下，否则会找不到文件（代码中涉及到相对路径打开文件）

或者在新建一个jupyter notebook，使用%run xxx.py

### 总结

运行baseline遇到的基本都是环境问题

### convert to roberta 出错

问题出在哪？（数据 or 函数）

代码定位（具体哪行）

gpu不好判断的话，使用cpu判断

不要无脑乱改，否则出现新bug更头疼；最好搞个备份！！！



花了一天多的时间找bug，简单写个报告：

报错信息可能很复杂，网上解决办法不下十种，比如transformers版本问题、embedding层输出和标签不匹配等等，都不能解决问题

**其实很简单，改改名字就行**

1. 去https://huggingface.co/下载chinese-roberta-wwm-ext预训练模型（[hfl/chinese-roberta-wwm-ext · Hugging Face](https://huggingface.co/hfl/chinese-roberta-wwm-ext)），具体文件如下

2. 导入main文件同级文件下下

3. 把data_for_joint（数据预处理文件夹）下的bert改为roberta，这里注意只需要改一处(data_handle.ipynb)，因为中文roberta模型依旧使用BertTokenizer

4. 模型和训练文件需要改三个文件：

   ① config.ini只需要导入本地roberta模型即可，只需要改一处，如图

   ② joint_model.py而是只需要改一处（BertConfig->RobertaConfig），当然需要查找下面调用的BertConfig改掉，

   **注意 BertTokenizer,BertModel, BertPreTrainedModel不要改，之前就是改了BertModel导致出现上面错误**

   ③ main.py同②，只需要改BertConfig