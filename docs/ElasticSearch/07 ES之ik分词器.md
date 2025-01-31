# ik分词器

## 一、下载安装

- 下载网址：https://github.com/medcl/elasticsearch-analysis-ik/releases

<img src="/static/img/ES专题/ik_1.png" style="zoom:80%;" /> 

- 在 readme.md 文件中，下拉选择历史版本连接。

<img src="/static/img/ES专题/ik_2.png" style="zoom: 80%;" /> 

- 由于 ik 与 elasticsearch 存在兼容问题。所以在下载 ik时要选择和 elasticsearch 版本一致的，单击 elasticsearch-analysis-ik-6.5.4.zip 包，自动进入下载到本地。

<img src="/static/img/ES专题/ik_3.png" style="zoom:80%;" /> 

- 首先打开 ES 的安装目录的 plugins 目录，并将 elasticsearch-analysis-ik-6.5.4.zip 包解压到该目录内

<img src="/static/img/ES专题/ik_4.png" style="zoom:80%;" /> 

- 将 elascticsearch 和 kibana 服务重启

## 二、ik 目录简介

我们简要的介绍一下ik分词配置文件：

- IKAnalyzer.cfg.xml，用来配置自定义的词库
- main.dic，ik原生内置的中文词库，大约有27万多条，只要是这些单词，都会被分在一起。
- surname.dic，中国的姓氏。
- suffix.dic，特殊（后缀）名词，例如 乡、江、所、省 等等。
- preposition.dic，中文介词，例如 不、也、了、仍 等等。
- stopword.dic，英文停用词库，例如 a、an、and、the 等。
- quantifier.dic，单位名词，如 厘米、件、倍、像素 等。

## 三、ik 的使用

### **3.1、ik_max_word**

参数会将文档做最细粒度的拆分，以穷尽尽可能的组合

```python
GET _analyze
{
  "analyzer": "ik_max_word",
  "text": "今天是个好日子"
}
```

分词结果

```python
{
  "tokens" : [
    {
      "token" : "今天是",
      "start_offset" : 0,
      "end_offset" : 3,
      "type" : "CN_WORD",
      "position" : 0
    },
    {
      "token" : "今天",
      "start_offset" : 0,
      "end_offset" : 2,
      "type" : "CN_WORD",
      "position" : 1
    },
    {
      "token" : "是",
      "start_offset" : 2,
      "end_offset" : 3,
      "type" : "CN_CHAR",
      "position" : 2
    },
    {
      "token" : "个",
      "start_offset" : 3,
      "end_offset" : 4,
      "type" : "CN_CHAR",
      "position" : 3
    },
    {
      "token" : "好日子",
      "start_offset" : 4,
      "end_offset" : 7,
      "type" : "CN_WORD",
      "position" : 4
    },
    {
      "token" : "日子",
      "start_offset" : 5,
      "end_offset" : 7,
      "type" : "CN_WORD",
      "position" : 5
    }
  ]
}
```

### **3.2、ik_smart**

参数将文档作最粗粒度的拆分

```python
# 新建一个索引
GET _analyze
{
  "analyzer": "ik_smart",
  "text": "今天是个好日子"
}
```

分词结果

```python
{
  "tokens" : [
    {
      "token" : "今天是",
      "start_offset" : 0,
      "end_offset" : 3,
      "type" : "CN_WORD",
      "position" : 0
    },
    {
      "token" : "个",
      "start_offset" : 3,
      "end_offset" : 4,
      "type" : "CN_CHAR",
      "position" : 1
    },
    {
      "token" : "好日子",
      "start_offset" : 4,
      "end_offset" : 7,
      "type" : "CN_WORD",
      "position" : 2
    }
  ]
}
```





