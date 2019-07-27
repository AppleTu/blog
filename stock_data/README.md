# python��Ʊ�г�����̽��ָ��
## ǰ��
��Ȼͬ��˳֮��Ľ������Ӧ�õ������㹻���ˣ����������Լ����Ƶĳ嶯, ������Ȼ���᲻���ǰ�ߺúܶ࣬���ǰ����Լ����뷨�����ƻ��ǲ���ġ�

## Ŀ��
ͨ����ѵ����ݽӿڻ�ȡ���ݣ�ÿ���������±����ʷ��������, Ȼ��ͨ��Kibana�����ӻ������ݷ���.

��ʵ�Լ�ͨ��echarts֮��Ŀ��ӻ���������ӻ�Ҳ�Ǹ������ѡ�񣬲���ǰ�ڳɱ�̫�󡣻��о���pandas+matplotlib�Ѿ�����Ӧ���󲿷������ˣ����ǽ�����̫�������Խ���һ�����ӻ�Ӧ���Ǻ��б�Ҫ�ģ�����ѡ�����kibana, ���ľ�Ʒ��Grafana.

���Ŀ��Ӧ�û�һֱ��ȥ�ɣ���ҿ���ͨ���������ӻ�ȡ����

https://github.com/youerning/stock_playground

## ��������
Python3(�Ƽ�Anaconda��װ)

��װ���������

```
pip install -r requirement.txt
```

����eleasticsearch, kibana����(�Ƽ�ʹ��docker)

> Elasticsearch, Logstash, Kibana 7.2.0


## ����Դ
��ȡ���ݵķ�ʽ�кܶ��֣��շѻ�����ѣ���Ϊҵ�మ������Ȼѡ����ѵģ�����ѡ�� tushare.pro, ����ʵtushare����һ������, ���ȡ���ݵ�Ƶ����һ�������ƣ����ҽӿ�Ҳ������, ��Ҫ�ܶ���֡������Ҷ��������Ȥע�ᣬ��ͨ���ҵ��Ƽ�����ע����, �����ҿ��Ը���ҷ������������ݿ��ӻ�������,�Լ������������������ݷ��������

> https://tushare.pro/register?reg=277890

> ֵ��ע�����, tushare��ʵҲ���м���û���Ƶ���Ѱ汾��. ����pro�汾���ݸ�ȫ��Ϊ�˱������ά���ɱ�������ѡ��pro�汾��

��ʵ������������ѵ����ݻ�ȡ��ʽ�ģ���ҿ����Լ�����
1. [pytdx](https://github.com/rainx/pytdx)
2. [fooltrader](https://github.com/foolcage/fooltrader)
3. [QUANTAXIS](https://github.com/QUANTAXIS/QUANTAXIS)

## ��ȡ����
�����Լ���token
```
import tushare as ts
ts.set_token("<your_token>")
pro = ts.pro_api("<your_token>")
```

����Token�Ļ�ȡ���Բο�һ������
> https://tushare.pro/document/1?doc_id=39

�����ֶ���ȡ����
```
ͨ������ȡ��ʷĳһ���ȫ����ʷ
df = pro.daily(trade_date='20190725')

df.head()
ts_code	trade_date	open	high	low	close	pre_close	change	pct_chg	vol	amount	value
0	000032.SZ	20190725	9.49	9.60	9.47	9.56	9.49	0.07	0.7376	12658.35	12075.625	8906.981000
1	000060.SZ	20190725	4.39	4.40	4.35	4.36	4.39	-0.03	-0.6834	129331.65	56462.292	-38586.330353
2	000078.SZ	20190725	3.37	3.38	3.35	3.38	3.37	0.01	0.2967	76681.00	25795.633	7653.564311
3	000090.SZ	20190725	5.66	5.66	5.56	5.61	5.64	-0.03	-0.5319	105582.72	59215.389	-31496.665409
4	000166.SZ	20190725	4.97	4.98	4.93	4.96	4.97	-0.01	-0.2012	268122.48	132793.120	-26717.975744

��ȡĳһֻ��Ʊ��������������
data = ts.pro_bar(ts_code="601668.SH", adj='qfq', start_date="20120101")
data.head()
ts_code	trade_date	open	high	low	close	pre_close	change	pct_chg	vol	amount
0	601668.SH	20190726	6.01	6.06	5.98	6.03	6.04	-0.01	-0.17	696833.16	419634.547
1	601668.SH	20190725	6.05	6.07	6.02	6.04	6.04	0.00	0.00	543074.55	327829.380
2	601668.SH	20190724	6.09	6.11	6.02	6.04	6.05	-0.01	-0.17	788228.12	477542.609
3	601668.SH	20190723	5.93	6.07	5.92	6.05	5.94	0.11	1.85	1077243.46	650250.021
4	601668.SH	20190722	6.02	6.03	5.92	5.94	6.00	-0.06	-1.00	811369.73	485732.343
```

���ݵĻ�ȡ��Ȼ����Ҫ�Զ����ģ��������ڽӿڵ����ƣ�������Ҫ�����������⡣
1. ��Ʊ�б�
2. �ж��Ƿ񳬳��ӿ����ƣ�����ǣ�����ͣһ��ʱ��

�ؼ����벿��

```
def save_data(code, start_date, fp):
    print("���ع�Ʊ(%s)�������ݵ� %s" % (code, fp))

    try:
        data = ts.pro_bar(ts_code=code, adj='qfq', start_date=start_date)
        # ���������ô������Ʒ���None
        if data is None:
            time.sleep(10)
            return
        pass_set.add(code)
    except Exception:
        time.sleep(10)
        print("��Ʊ: %s ����ʧ��" % code)
        return

    if len(data) == 0:
        pass_set.add(code)
        return

    try:
        data.trade_date = pd.to_datetime(data.trade_date)
        data = data.sort_values("trade_date")
        if path.exists(fp):
            data.to_csv(fp, mode="a", header=False, index=False)
        else:
            data.to_csv(fp, index=False)
    except Exception:
        print("��Ʊ:%s ����ʧ��" % code)
```

��ҿ��Բο���GitHub�ֿ��save_data.py, ͨ����������Ϳ����Զ�����������

```
python save_data.py
```

�����������õ���ʼʱ����2012-01-01,����Ҫ�Ŀ����и��ģ�ֵ��ע�������Ҫ��ͬ��Ŀ¼����һ��config.json, ��������

```
{
    "token": "<your_token>"
}
```
> �����Լ���token

## ����elasticsearch, kibana
����ʹ�õ���docker�������á�

```
# ��ȡ����
docker pull sebp/elk:720

# ����docker����
docker run -p 5601:5601 -p 9200:9200 -p 5044:5044 -v /home/elasticsearch/:/var/lib/elasticsearch -itd  sebp/elk:720
```

## ת������
�������ϴ���elasticsearch�����Ա����ݷ���

����settings.py

```
# ��ip:port�ĳ��Լ�elasticsearch��ַ����192.168.56.102:9200
config["es_host"] = ["ip:port"]
```

���д���

```
# �ϴ���Ʊ����
python cmd.py dump

# �ϴ���ָ֤������
python cmd.py dump_index
```

## ���ӻ�
����kibana����Ҫһ����ʱ��ģ�����kibana�����Ǵ�������ö�֧�ֵ��뵼�������Դ�ҿ���ͨ���Ҳֿ��export.ndjson�ļ�ֱ�ӵ���

![import](img/import.png)


## Ч��չʾ
![market_status](img/market_status.png)

![stock_selector](img/stock_selector.png)

�������ڽӿ����ޣ���ȡ�Ĺ�Ʊ�������ޣ����Ե��ҵĻ��ָ����ˣ��һ��������dashboard, �Լ�visualization.


## ���
ϣ����������Լ��Ĵ��޵��д����ϵͳϵ������, Ȼ��ͨ����ʱ��ص�����֮·.

> ������ԲƸ�����, ��Ըʱ��ص�����^_^

