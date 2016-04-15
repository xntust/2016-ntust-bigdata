[第一次作業] Data Collection and Persistence
===
[Assignment 1] Data Collection and Persistence


### 作業要求
>*提醒：作業繳交時間 4/15 23:59:59 不接受遲交喔*
>
>作業簡介: 讓同學瞭解資料分析的第一個階段，如何永久化保存資料(Persistence)，保存資料的格式包含：CSV與JSON格式。
>
>詳細需求與繳交方式: 
>
>1. 請參考上課所給予的範例程式，對於Twitter或Github的資料進行蒐集，所蒐集到的資料的純文字檔需大於1G(JSON或CSV任一格式達到1G即可)。
>
>2. 請以Markdown(http://markdown.tw) 說明收集到的Data Format(JSON的欄位架構、CSV的Fields)、Data Sources (從哪個API來的？內容主題為何？下哪個查詢字串？)，並將說明文件存成readme.md。
>
>3. 收集完後的資料一律壓縮成zip檔，連同收集程式上傳到自己的github上，另外也將readme.md上傳至github(看到的樣子如：https://github.com/apache/spark) 建議資料夾配置為/crawler與/dataset，dataset中有兩個資料夾，分別為/csv與/json。
>
>4. 作業繳交請直接傳送github網址直接線上繳交。(只要繳交網址)

### 1. 建立作業環境(Ubuntu LINUX 64-bit)
#### ELK
+ [JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
+ [Elasticsearch](https://www.elastic.co/downloads/elasticsearch) (plugin/head)
	+ $ ./bin/plugin install mobz/elasticsearch-head 
+ [Logstash](https://www.elastic.co/downloads/logstash)
+ [Kibana](https://www.elastic.co/downloads/kibana)* (視覺化工具)
*參考課程教學投影片*

#### Other Tools
+ [Twitter Application Management](https://apps.twitter.com/) 取得相關Key
+ Git
+ [npm](https://github.com/npm/npm)
+ [Elasticdump](https://github.com/taskrabbit/elasticsearch-dump/)

### 2. 主要配置檔案
#### /logstash/bin/twitter.config
```shell=
input{
	twitter {
	    consumer_key => "My Consumer Key (API Key)"
	    consumer_secret => "My Consumer Secret (API Secret)"
	    oauth_token => "My Access Token"
	    oauth_token_secret => "My Access Token Secret"
	    keywords => ["a"]
	    full_tweet => true
	}
}
output{
	elasticsearch{
	    index => "twitter"
	}
}

```
### 3. 開始接收資料(指令)
```shell
$ ./elasticsearch/bin/elasticsearch
$ ./logstash/bin/logstash agent -f twitter.config
```
![](http://i.imgur.com/HdVD16y.png)

### 4. 匯出json檔
```shell
$ elasticdump \
--input=http://localhost:9200/twitter \
-- output=garbage-data.json \
--type=data
```
[garbage-data.json]()(檔案過大，多次傳送失敗)

### 後記
由於追求抓資料的速度，設定關鍵字為“a”，過於粗糙，
以至於"garbage-data.json"該Dataset可能沒有分析價值。
下次要有目的性地設定關鍵字，再重新抓過。


--------------

#### 參考資料
+ Wiki - GitHub 設定指引 http://wiki.csie.ncku.edu.tw/github
+ elasticsearch + fluentd + kibana4(EFK) 安裝詳細流程 in ubuntu14.04 « chi - 下班隨筆
http://chi15036-blog.logdown.com/posts/297025-elasticsearch-fluentd-kibana4-installation-details-processes-in-ubuntu1404
+ 匯出Elasticsearch的資料 « chi - 下班隨筆
http://chi15036-blog.logdown.com/posts/301342-dump-elk-log-to-the-r-statistical-diagram
+ PyES - Python Elastic Search — PyES Documentation 0.99.7dev (unstable) documentation
https://pyes.readthedocs.org/en/latest/index.html
+ mark19891107/elasticsearch-tutorial: Examples of pyes ( elasticsearch python library ) and documents
https://github.com/mark19891107/elasticsearch-tutorial
+ GitHub - taskrabbit/elasticsearch-dump: Import and export tools for elasticsearch
https://github.com/taskrabbit/elasticsearch-dump
+ JSON Editor Online - view, edit and format JSON online
http://www.jsoneditoronline.org/
