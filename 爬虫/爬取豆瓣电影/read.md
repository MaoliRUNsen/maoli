### ʲô�����ݿ�
���ݿ⣬�����֮����Ϊ���ӻ����ļ��񡪡��洢�����ļ��Ĵ������û����Զ��ļ��е����ݽ�����������ѯ�����¡�ɾ���Ȳ�����






### Python DB-APIʹ�����̣�
- ���� API ģ�顣
- ��ȡ�����ݿ�����ӡ�
- ִ��SQL���ʹ洢���̡�
- �ر����ݿ����ӡ�


###### ���������ݿ�Ӧ����mysql ��mongodb��redis
������Ȱ�װ��mysql��mongodb��redis




### MySQL��Python�Ľ���
��ʹ�� PyMySQL ֮ǰ��������Ҫȷ�� PyMySQL �Ѱ�װ

`$ pip3 install PyMySQL` ����cmd pip ��װ��
�������ǿ�ʼ��ѧϰһ����ô���Ӳ���mysql��

����������Ҫ����pymysql��ģ�飬`import pymysql`��Ȼ�����`pymysql.connect()`�������ݿ⡣����`connect`���ص��α�`connection.cursor()`��ִ�в�ѯ��䡣������������Ҫ����`cursor.execute()`��ִ��sql��䣬`connection.commit()`��������`connection.close()`�����ر����ݿ����ӡ�

�������£�
```
import pymysql
# �����ݿ�����
db = pymysql.connect("localhost","testuser","test123","TESTDB" )
# ʹ�� cursor() ��������һ���α���� cursor
cursor = db.cursor()
# ʹ�� execute()  ����ִ�� SQL ��ѯ 
cursor.execute("SELECT VERSION()")
# ʹ�� fetchone() ������ȡ��������.
data = cursor.fetchone()
print ("Database version : %s " % data)
# �ر����ݿ�����
db.close()
```
���м���д��
```python
import pymysql
db_config = {
    'user':'root',
    'password':'qwe123',
    'db':'spiders', #���ݿ�����
    'charset':'utf8'
    'host' : 'localhost' #�����ip
}
db = pymysql.connect(**db_config)
cur = db.cursor()
#����
cur.execute('select * from teacher_student')
# ����
sql1 = 'insert into items(id,name,age) VALUES(%s,%s,%s)'
cur.execute(sql1,('1','ë��',18))
db.commit() #Ҫ�ύ
#��
sql2 = 'update items set age = %s where name = %s'
cursor.execute(sql2,(8888,'ë��'))
db.commit()
db.close()
```

###  mongodb��Python�Ľ���

ȷ����װ��pymongo

```
import pymongo
collection = pymongo.MongoClient()
db = collection['my_mongo'] 
my_col = db['student']
result = my_col.insert_one({'name':'ë��'},{'age':18})
```

![���������ͼƬ����](https://img-blog.csdnimg.cn/20190318234334138.jpg)

### redis��Python�Ľ���
redis�Ŀⲻ��pyredis����redis
```
import redis
conn = redis.StrictRedis() #����һ��StrictRedis()
result = conn.get('name')
print(result)
```
![���������ͼƬ����](https://img-blog.csdnimg.cn/2019031823434410.jpg)


### ʵս��ȡ

��ȡ���󣨶����Ӱtop250����ץȡ����mongodb���ݿ⣩

> https://movie.douban.com/top250?start=25&filter=


![��ȡ������](https://img-blog.csdnimg.cn/20190720231815190.png)




- ��װ���ݿ�



```
# ��װmonongodb
import pymongo
from pymongo.collection import Collection
class Handle_Mongo(object):
    def __init__(self):
        # �������ip��192.168.96.128
        mongo_client = pymongo.MongoClient(host="192.168.96.128",port=27017)
        # ���ݿ������
        self.db_data = mongo_client['douban']
    def handle_save_data(self,item):
        # ���ϵ�����
        task_collection = Collection(self.db_data,'douban_data')
        # ��������
        task_collection.insert(item)
douban_mongo = Handle_Mongo()

```

�������ʹ�ö��̵߳ķ���

```
import re
from concurrent.futures import ThreadPoolExecutor
import requests
from lxml import etree
from handle_mongo import douban_mongo

```
����url����start��������ڱ仯�����Բ��ϵĵ���
```
def handle_page_url(self):
        #ͨ������ҳ��URL���Ե�֪
        #ͨ��range����ҳ�����,��0��ʼ,��249����,����Ϊ25
        for i in range(0,250,25):
            url = "https://movie.douban.com/top250?start=%s&filter="%i
            self.page_url.append(url)
```

���ϵ���xpath������
```
    def handle_page_detail(self,url):
        # print(url)
        #���������ַ�
        sub_search = re.compile(r"[\s\r\t]")
        response = self.handle_request(url=url)
        html = etree.HTML(response)
        #������ǰҳ���ж��ٸ���Ӱ��Ϣ
        item_list = html.xpath("//ol[@class='grid_view']//li")
        print(item_list)
        for item in item_list:
            info = {}
            #��Ӱ����,�������ַ��滻Ϊ��
            info['movie_name'] = sub_search.sub('',''.join(item.xpath(".//div[@class='hd']/a//span/text()")))
            info['actors_information'] = sub_search.sub('',''.join(item.xpath(".//div[@class='bd']/p/text()")))
            info['score'] = sub_search.sub('',''.join(item.xpath(".//div[@class='bd']/div[@class='star']/span[2]/text()")))
            info['evaluate'] = sub_search.sub('',''.join(item.xpath(".//div[@class='bd']/div[@class='star']/span[4]/text()")))
            info['describe'] = sub_search.sub('',''.join(item.xpath(".//p[@class='quote']/span/text()")))
            info['from_url'] = url
            #�������
            # print(info)
            douban_mongo.handle_save_data(info)
```

�����߳�����
```
#��������
def run(self):
    self.handle_page_url()
    #�����̳߳�
    t = ThreadPoolExecutor()
    for i in self.page_url:
        t.submit(self.handle_page_detail,i)
    t.shutdown()
    print(self.page_url)
```


![Ч��ͼ](https://img-blog.csdnimg.cn/20190720232642102.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDUxMDYxNQ==,size_16,color_FFFFFF,t_70)


�ڹ��ںŻظ���[ ����] ��ñ���ȫ����



