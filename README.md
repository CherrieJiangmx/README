# README
文本聚类--投诉
====
项目地址
----
190服务器  `/home/mengxiao/Online_chat_classification`<br>

代码运行环境
----
python2.7 (最好用anaconda2)<br>
数据预处理和聚类的代码必须在服务器上跑，因为一些数据包，本地没安装。还有部分编码格式的问题。<br>
数据分析的代码，比如：从结果文件中统计top商户根据在各个类别下的数量。可以在本地跑，但是要先从服务器下载结果文件。

数据描述
---
>`Data`: 存放所有的数据；<br>
>>`tmp_complain`存放中间结果，例如：客户说的第一句话的`txt`格式文件，抽取的否定短语，费老抽取的第一句话摘要等；<br>
>>`cluster_complain`存放用于聚类的输入，和产生的聚类结果的输出，费老生成的摘要信息（摘要信息.txt），以及各种统计结果。<br>
>>`org` 里面存放原始的excel数据，`6.1-30`表示6月1号到30号的所有数据，`6.1-30-product`表示6月1号到30号的所有数据，里面有商户产品和区域信息<br>
>>`org_json`里面存放由原始excel表格生成的json格式的文件，包含:`产品`，`序号`，`商户`，`对话内容`，`区域`，`客服分类`。便于后续处理。<br>
>>`combined`里面放的是放的是之前用于分类器的输入文件，聚类可以不用管。<br>
>> `predict` 里面放的是之前分类生成的预测结果文件，聚类可以不用管。<br>
>>其他文件都不用管，临时放在这里的。<br>

>`dict`：存放所有的字典：<br>
>> `customer_word.txt` 表示所有的客户说的话里面包含的一元词； eg: 中国 <br>
>>`customer_word_bigram.txt`表示所有的客户说的话里面包含的二元词；eg: 中国 | 建设 <br>
>> `customer_word_trigram.txt`表示所有的客户说的话里面包含的三元词；eg: 中国 | 建设 | 银行 <br>
>> `customer_char.txt` 表示所有的客户说的话里面包含的一元字符； eg: 中 <br>
>>`customer_char_bigram.txt`表示所有的客户说的话里面包含的二元词；eg: 中 | 国 <br>
>>`customer_char_trigram.txt` 三元词。<br>
>>`customer_word_idf.txt` 表示所有的客户说的话里面包含的一元词以及对应的idf值；<br>
>>其他的同理上面。<br>

>`embed`: 存放所有的词向量：<br>
>> `emb_ch`字符的词向量；<br>
>> `emb_wd` 词的词向量；<br>
>> `emb_wd` 用Google的工具在所有对话数据上跑的词向量；<br>
>> `training_data` 用所有6.1-30对话数据整理的，跑词向量的输入文件。<br>

>`feature`和`modle`不用管，中间结果。<br>
>`result` 分类和聚类的结果文件：<br>
>> 聚类：`predict.txt` 存放聚类结果,一行一个聚类的类别，结果和输入数据对应一致。<br>
>> 分类：`predict.txt` 存放分类结果，一行一个分类的类标。<br>
        `hill_predict.txt`特征选择的结果。<br>
>`src`: 源代码。<br>
 
用于聚类的输入数据格式
----
JSON格式的文件。  `config.py`中`TRAIN_DATA` 为输入文件的路经 `/cluster_complain/6_1-30/train_customer_first_6_1-30.json` 
>{
    "customer": {
      "char": [
        "67", 
        "参", 
        "加", 
        "支", 
        "付", 
        "后", 
        "发", 
        "现", 
        "没", 
        "有", 
        "优", 
        "惠"
      ], <br>
      "postag": [
        "m", 
        "v", 
        "v", 
        "nd", 
        "v", 
        "d", 
        "v"
      ], <br>
      "dependencies": [
        "ADV(参加,67)", 
        "ATT(后,参加)", 
        "VOB(参加,支付)", 
        "ADV(发现,后)", 
        "HED(ROOT,发现)", 
        "VOB(发现,没有)", 
        "VOB(没有,优惠)"
      ], <br>
      "word": [
        "67", 
        "参加", 
        "支付", 
        "后", 
        "发现", 
        "没有", 
        "优惠"
      ]
    }, <br>
    "index": "6.11-1867", <br>
    "label": 1<br>
  }<br>

代码描述
---
>`src`: `source` 目录。<br>
>> `config.py`存放所有的文件路径。本地和服务器的根目录已经配好，如果需要添加新的路径，只需调用`config.XXX`就可以，在服务器和本地都能跑。<br>
>>`util.py` 存放所有的小工具，包含：去掉标点，url，停词，根据dict和词的list得到稀疏表示的特征等。<br>
>>文件夹外的，除上述两个文件外的，不用管，包含：分类的一些代码，类的定义，一些测试代码。<br>
>>`data_preprocessor`:数据预处理。<br>
>>>1.`read_json_from_excel.py`:从`org`文件夹中对应日期文件夹中的excel原文件，生成汇总的`json`格式的文件，`6_1-30.json`。包含的字段上述有介绍。后续所有预处理的输入都是这个`json`格式的文件，抽取除了我们需要的字段。<br>
>>>2.`get_customer_first_and_json.py`:得到客服说的第一句话，位置：`tmp_complain/投诉_访客问题_6_1-30.txt`。<br>
>>>3.获得否定形式的短语，从而生成聚类器的json格式的输入文件。有两种方法：<br>
>>>>3.1.`get_negative_and_json.py`: 根据客服说的第一句话，得到否定短语：`tmp_complain/投诉_访客问题否定短语_6_1-30.txt` 和对应的json格式的文件，`cluster_complain/6_1-30/否定短语/train_customer_first_negative_6_1-30.json`。<br>
>>>>3.2.`transform_first_sentence_to_phrase_json.py`:根据客服说的第一句话，给费老生成第一句话的摘要，然后得到摘要的json文件。`cluster_complain/6_1-30/train_customer_first_6_1-30.json`。<br>
>>>4.
