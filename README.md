# README
#文本聚类--投诉
=======
##项目地址：190服务器  `/home/mengxiao/Online_chat_classification`
输入：JSON格式的文件。  `/cluster_complain/6_1-30/train_customer_first_6_1-30.json` 对应于`config` 中的 `customer_first_json_file`
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
  
  
  
