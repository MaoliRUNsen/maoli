![Ч��ͼ](https://img-blog.csdnimg.cn/20190720212927924.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDUxMDYxNQ==,size_16,color_FFFFFF,t_70)
> ������е���Ȥ�����Լ���CSDN �Ĳ��ͣ�����������ͼ�������Լ�����д��ʲô

˵�𣬾Ϳ��ɣ������ǲ�����ҳ

> https://blog.csdn.net/weixin_44510615


### ����
����ϵͳ��Windows

Python�汾��3.7.2

### ģ��
�����漰����Python������ģ�飬����������ִ�ģ��jieba��������ģ��wordcloud����ͼģ��matplotlib������������ͼƬ��ģ��cv2�����ʵ�ģ��requests��������ģ��bs4
��Щģ�����ͨ��pip��ʽ���а�װ


### ʵ��˼·
�Ȼ��ÿ�����µ�ǰ�ԣ��ü������洢���������Դﵽȥ�أ���ƴ�ӳ��ַ�������ͨ��jiebaģ����ַ������зִʴ���Ȼ��Դ����Ĳ���ʹ��wordcloud������ģ��������Ӧ�Ĵ���ͼƬ���ɡ�


![���������ͼƬ����](https://img-blog.csdnimg.cn/20190720212410254.png)


**���ж��Ƿ���ʳɹ�**
```
titles = set()
def html(url):
    if url:
        r = requests.get(url,headers={'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/53.0.2785.143 Safari/537.36'})
        if r.status_code == 200:
            # print('���ʳɹ�')
            return r.text
    else:
        print('����ʧ��')
        return None
```




**ͨ��BeautifulSoup��������Ӧ���ַ���**
```
def get_text(html):
    soup = BeautifulSoup(html, "lxml")
    links = soup.find_all('a', href=re.compile(r'/weixin_44510615/article/details'))
    for i in links:
    	# ͨ�����ϴﵽȥ�صĲ���
        titles.add(i.get_text())
```

**������ͼ��˼·��ֱ�ӿ����룬�������������ϸע��˵����**
```
def get_img_1():
    # ��ʹ��ͼƬ����
    strs = ''
    # ���Ϊ�վ�ֱ���˳�
    if titles.__len__() == 0:
        return
    for item in titles:
        strs = strs + item
    # ȥ���ո�ͻ���
    s = strs.replace('\n','').replace(' ','')
    # ���зִ�
    word_cut = jieba.cut(s)
    # �ѷִ��ÿո�������
    word_cut_join = " ".join(word_cut)
    print(word_cut_join)
    w = WordCloud(
        # �������� (��C���ǹ̶��ģ�
        font_path='C:/Windows/Fonts/simfang.ttf',
        # ���������ͼƬ�������ֵ
        width=1000, height=700,
        # ���������ͼƬ����ɫ
        background_color='white')
    # ���ɴ���
    w.generate(word_cut_join)
    # ����ͼƬ
    w.to_file('demo1.jpg')
    # չʾͼƬ
    plt.show()
```
![Ч��ͼ](https://img-blog.csdnimg.cn/20190720214233878.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDUxMDYxNQ==,size_16,color_FFFFFF,t_70)
### ʹ��ͼƬ��չʾ


```
def get_img_2():
    # ʹ���й���ͼ��ͼƬ��Ϊ����
    strs = ''
    if titles.__len__() == 0:
        return
    for item in titles:
        strs = strs +item
    s =  strs.replace('\n','').replace(' ','')
    # ���ñ���ͼƬ
    img_file = 'China.jpg'
    # ��������ͼƬ����cv2�е��룩
    mask_img = imread(img_file)
    # ���зִ�
    word_cut = jieba.cut(s)
    # �ѷִ��ÿո�������
    word_cut_join = " ".join(word_cut)
    # ���ô��Ʋ���
    wc = WordCloud(
        # ��������
        font_path='C:/Windows/Fonts/simfang.ttf',
        # �������ʻ���
        max_words=2000,
        # �������������С
        max_font_size=90,
        # ����ʹ�õı���ͼƬ�����������Ϊ��ʱ��width��height�ᱻ����
        mask=mask_img,
        # ���������ͼƬ����ɫ
        background_color='white')
    # ���ɴ���
    wc.generate(word_cut_join)
    # ��ʾͼƬ
    plt.imshow(wc)
    # ��ͼƬ���浽����
    plt.savefig('demo.jpg')
    plt.show()
```
![���������ͼƬ����](https://img-blog.csdnimg.cn/20190720214734122.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDUxMDYxNQ==,size_16,color_FFFFFF,t_70)

### �ܽ�


���Ƽ򵥣����������ֻ������ӵġ�ͨ�����ɴ���ͼ���������г���Ƶ�ʽϸߵġ��ؼ��ʡ������Ӿ�����չ�֣��������߿����������µ���ּ���ȷ����ָ�Ч��



�ڹ��ںŻظ� ��CSDN �� ����ȡ����ȫ�״���