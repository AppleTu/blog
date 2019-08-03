# ��Pythonͽ��ߣһ����Ʊ�ز���

ͨ����Python��ɹ�Ʊ�ز��ܵĴ��
## ʲô�ǻز���?
�����Ǵ�ͳ��Ʊ���׻����������ף��޷������һ��������������Ҫ�����Լ��Ľ��ײ����Ƿ���У�����򵥵ķ�ʽ����������ʷ���ݼ��齻�ײ��ԣ����ز��ܾ����ṩ������һ��ƽ̨�ý��ײ�������ʷ�����в��Ͻ��ף������������ս����ͨ���鿴����Ĳ������棬�껯���棬���ز�������������ײ��ԵĿ����ԡ�

�����ַ�����

> ����Ŀ������һ�������Ƶ���Ŀ, ���ڲ��ϵ����ơ�

## �ز���
�ز���Ӧ�����ٰ�����������, �ز���, ������.
�ز����ṩ���ֹ��Ӻ��������ڷ����Լ��Ľ����߼�������������ģ���г��Ľ���ƽ̨��������ṩ���룬�����ķ�����

## ����ܹ�
���Լ��Ļز���Ϊ������Ҫ�������������ļ�

```
backtest/
  backtest.py
  broker.py
```

backtest.py��Ҫ�ṩBackTest����������ṩ�ز��ܣ���¶���¹��Ӻ���.

```
    def initialize(self):
        """�ڻز⿪ʼǰ�ĳ�ʼ��"""
        pass

    def before_on_tick(self, tick):
        pass

    def after_on_tick(self, tick):
        pass

    def before_trade(self, order):
        """�ڽ���֮ǰ����ô˺���

        �����ڴ˷����ʽ�������չ���Ĵ���
        �������True�������ף������������
        """
        return True

    def on_order_ok(self, order):
        """������ִ�гɹ������"""
        pass

    def on_order_timeout(self, order):
        """��������ʱ�����"""
        pass

    def finish(self):
        """�ڻز���������"""
        pass
    
    @abstractmethod
    def on_tick(self, bar):
        """
        �ز�ʵ������ʵ�ֵķ���������д�Լ��Ľ����߼�
        """
        pass
```
�������ƽ̨�Ļز��ܻ��߿�Դ���Ӧ�ö���Щ���Ӻ�����İ����ֻ�����ֲ�һ�����ѣ������������һ�µģ�����on_tick.

֮������on_tick������on_bar, ����Ϊ��ϣ�������߼���һ��һ��ʱ���Ĳ��뽻�ף������ʱ����ҿ��Ի�ȡ���е�ǰʱ������й�Ʊ�Լ�֮ǰ�Ĺ�Ʊ���ݣ������ж��Ƿ��ף�������һ��ʱ����һ��һ����Ʊ���뽻���߼���

��broker.py��Ҫ�ṩbuy,sell�����������ڽ��ס�

```
    def buy(self, code, price, shares, ttl=-1):
        """
        �޼��ύ���붩��

        ---------
        Parameters:
          code:str
                ��Ʊ����
          price:float or None
                ��߿�����ļ۸�, ���ΪNone���м�����
          shares:int
                �����Ʊ����
          ttl:int
                ����������ڵ����ʱ�䣬Ĭ��Ϊ-1��������ʱ

        ---------
        return:
          dict
             {
                "type": ��������, "buy",
                "code": ��Ʊ����,
                "date": �ύ����,
                "ttl": ���ʱ��, ��ttl����0ʱ��ʱ�����󲻻���ִ��
                "shares": Ŀ��ɷ�����,
                "price": Ŀ��۸�,
                "deal_lst": ���׳ɹ�����ʷ���ݣ���
                    [{"price": �ɽ��۸�,
                      "date": �ɽ�ʱ��,
                      "commission": ����������,
                      "shares": �ɽ��ݶ�
                    }]
                ""
            }
        """
        if price is None:
            stock_info = self.ctx.tick_data[code]
            price = stock_info[self.deal_price]
        order = {
            "type": "buy",
            "code": code,
            "date": self.ctx.now,
            "ttl": ttl,
            "shares": shares,
            "price": price,
            "deal_lst": []
        }
        self.submit(order)
        return order
        
    def sell(self, code, price, shares, ttl=-1):
        """
        �޼��ύ��������
        ---------
        Parameters:
          code:str
                ��Ʊ����
          price:float or None
                ��Ϳ������ļ۸�, ���ΪNone���м�����
          shares:int
                ������Ʊ����
          ttl:int
                ����������ڵ����ʱ�䣬Ĭ��Ϊ-1��������ʱ

        ---------
        return:
          dict
             {
                "type": ��������, "sell",
                "code": ��Ʊ����,
                "date": �ύ����,
                "ttl": ���ʱ��, ��ttl����0ʱ��ʱ�����󲻻���ִ��
                "shares": Ŀ��ɷ�����,
                "price": Ŀ��۸�,
                "deal_lst": ���׳ɹ�����ʷ���ݣ���
                    [{"open_price": ���ּ۸�,
                      "close_price": �ɽ��۸�,
                      "close_date": �ɽ�ʱ��,
                      "open_date": �ֲ�ʱ��,
                      "commission": ����������,
                      "shares": �ɽ��ݶ�,
                      "profit": ��������}]
                ""
            }
        """
        if code not in self.position:
            return

        if price is None:
            stock_info = self.ctx.tick_data[code]
            price = stock_info[self.deal_price]

        order = {
            "type": "sell",
            "code": code,
            "date": self.ctx.now,
            "ttl": ttl,
            "shares": shares,
            "price": price,
            "deal_lst": []
        }
        self.submit(order)
        return order
```
�����Һ���������̫���࣬�����̫���༰�������������Լ��������ˣ����Զ��ڶ����ѡ���Ǿ����ܵ�ʹ�ó��õ����ݽṹ����list, dict.
������һ��dict����һ��������

�������Щ������֤��һ���ز��ܵĻ��������߼������ز�����л���Ҫһ�����������ϵ�������Щ����������ĵ��������¡�

```
class Scheduler(object):
    """
    �����ز�����еĵ�������, ͨ��һ����ʱ��̶�(tick)�������ز��߼�

    ���б����ȵĶ��󶼻��һ������ctx��Context����,���ڹ��������ز�����е����йؼ�����,
    ���ñ�������:
        ctx.feed: {code1: pd.DataFrame, code2: pd.DataFrame}����
        ctx.now: ѭ������ʱ��
        ctx.tick_data: ѭ������ʱ��������б��۵Ĺ�Ʊ����
        ctx.trade_cal: ��������
        ctx.broker: Broker����
        ctx.bt/ctx.backtest: Backtest����

    ���÷���:
        ctx.get_hist

    """

    def __init__(self):
        """"""
        self.ctx = Context()
        self._pre_hook_lst = []
        self._post_hook_lst = []
        self._runner_lst = []
    def run(self):
        # runnerָ���ڿɵ��õ�initialize, finish, run(tick)�Ķ���
        runner_lst = list(chain(self._pre_hook_lst, self._runner_lst, self._post_hook_lst))
        # ѭ����ʼǰΪbroker, backtest, hook��ʵ����ctx���󼰵�����initialize����
        for runner in runner_lst:
            runner.ctx = self.ctx
            runner.initialize()

        # ������������
        if "trade_cal" not in self.ctx:
            df = list(self.ctx.feed.values())[0]
            self.ctx["trade_cal"] = df.index

        # ͨ����������������ʱ�����ε���runner
        # ���ȵ�������pre-hook��run����
        # Ȼ�����broker,backtest��run����
        # ������post-hook��run����
        for tick in self.ctx.trade_cal:
            self.ctx.set_currnet_time(tick)
            for runner in runner_lst:
                runner.run(tick)


        # ѭ���������������runner�����finish����
        for runner in runner_lst:
            runner.finish()
```

��Backtest��ʵ������ʱ��ͻ��Զ�����һ������������Ȼ��ͨ��Backtestʵ����start�����������������������������������ʷ���ݵ�һ��һ��ʱ�����������Backtest, Brokerʵ�������á�

Ϊ�˴���ͬʵ��֮������ݷ��ʸ��룬����ͨ��һ����һ��Context����󶨵�Backtest, Brokerʵ���ϣ�ͨ��self.ctx���ʹ�������ݣ������������Ҫ����feed���󣬼���ʷ���ݣ�һ�����ݽṹ���µ��ֵ����
```
{code1: pd.DataFrame, code2: pd.DataFrame}
```
�����Context����Ҳ����Broker, Backtest��ʵ��, ��Ϳ���ʹ�����ݷ��ʽӿ�ͳһ�����ǿ��ܵ������ݷ��ʻ��ң����Ҫ�������ߵ�ʹ���ˣ�������һ���ô����Ǽ�����һ�Ѵ�������ͨ����ӷ���ȥ���������Ķ���ķ������治���鷳����Щ�ˡ�

�󶨼�Context�����������:

```
class Context(UserDict):
    def __getattr__(self, key):
        # �õ��������ͨ�����������������ýԿ�
        return self[key]

    def set_currnet_time(self, tick):
        self["now"] = tick

        tick_data = {}

        # ��ȡ��ǰ�����б��۵Ĺ�Ʊ����
        for code, hist in self["feed"].items():
            df = hist[hist.index == tick]
            if len(df) == 1:
                tick_data[code] = df.iloc[-1]

        self["tick_data"] = tick_data

    def get_hist(self, code=None):
        """�����ָ��code, ��ȡ��������ǰʱ������й�Ʊ����ʷ����"""
        if code is None:
            hist = {}
            for code, hist in self["feed"].items():
                hist[code] = hist[hist.index <= self.now]
        elif code in self.feed:
            return {code: self.feed[code]}

        return hist
        
class Scheduler(object):
    """
    �����ز�����еĵ�������, ͨ��һ����ʱ��̶�(tick)�������ز��߼�

    ���б����ȵĶ��󶼻��һ������ctx��Context����,���ڹ��������ز�����е����йؼ�����,
    ���ñ�������:
        ctx.feed: {code1: pd.DataFrame, code2: pd.DataFrame}����
        ctx.now: ѭ������ʱ��
        ctx.tick_data: ѭ������ʱ��������б��۵Ĺ�Ʊ����
        ctx.trade_cal: ��������
        ctx.broker: Broker����
        ctx.bt/ctx.backtest: Backtest����

    ���÷���:
        ctx.get_hist

    """

    def __init__(self):
        """"""
        self.ctx = Context()
        self._pre_hook_lst = []
        self._post_hook_lst = []
        self._runner_lst = []

    def add_feed(self, feed):
        self.ctx["feed"] = feed

    def add_hook(self, hook, typ="post"):
        if typ == "post" and hook not in self._post_hook_lst:
            self._post_hook_lst.append(hook)
        elif typ == "pre" and hook not in self._pre_hook_lst:
            self._pre_hook_lst.append(hook)

    def add_broker(self, broker):
        self.ctx["broker"] = broker

    def add_backtest(self, backtest):
        self.ctx["backtest"] = backtest
        # ��д
        self.ctx["bt"] = backtest

    def add_runner(self, runner):
        if runner in self._runner_lst:
            return
        self._runner_lst.append(runner)
```

Ϊ��ʹ��������ܿ���չ���ز����п���г�����һ��Hook�࣬������������ÿ�λز��ܵ���ǰ���ߵ��ú󱻵��ã������Ϳ��Լ���һЩ�����߼�������ͳ���ʲ��仯�ȡ�

���ﴴ����һ��Stat��Hook��������ͳ���ʲ��仯��


```
class Stat(Base):
    def __init__(self):
        self._date_hist = []
        self._cash_hist = []
        self._stk_val_hist = []
        self._ast_val_hist = []
        self._returns_hist = []

    def run(self, tick):
        self._date_hist.append(tick)
        self._cash_hist.append(self.ctx.broker.cash)
        self._stk_val_hist.append(self.ctx.broker.stock_value)
        self._ast_val_hist.append(self.ctx.broker.assets_value)

    @property
    def data(self):
        df = pd.DataFrame({"cash": self._cash_hist,
                           "stock_value": self._stk_val_hist,
                           "assets_value": self._ast_val_hist}, index=self._date_hist)
        df.index.name = "date"
        return df
```
��ͨ����Щͳ�Ƶ����ݾͿ��Լ������س��껯�ʵȡ�


```
    def get_dropdown(self):
        high_val = -1
        low_val = None
        high_index = 0
        low_index = 0
        dropdown_lst = []
        dropdown_index_lst = []

        for idx, val in enumerate(self._ast_val_hist):
            if val >= high_val:
                if high_val == low_val or high_index >= low_index:
                    high_val = low_val = val
                    high_index = low_index = idx
                    continue

                dropdown = (high_val - low_val) / high_val
                dropdown_lst.append(dropdown)
                dropdown_index_lst.append((high_index, low_index))

                high_val = low_val = val
                high_index = low_index = idx

            if low_val is None:
                low_val = val
                low_index = idx

            if val < low_val:
                low_val = val
                low_index = idx

        if low_index > high_index:
            dropdown = (high_val - low_val) / high_val
            dropdown_lst.append(dropdown)
            dropdown_index_lst.append((high_index, low_index))

        return dropdown_lst, dropdown_index_lst

    @property
    def max_dropdown(self):
        """���س���"""
        dropdown_lst, dropdown_index_lst = self.get_dropdown()
        if len(dropdown_lst) > 0:
            return max(dropdown_lst)
        else:
            return 0

    @property
    def annual_return(self):
        """
        �껯������

        y = (v/c)^(D/T) - 1

        v: ���ռ�ֵ
        c: ��ʼ��ֵ
        D: ��ЧͶ��ʱ��(365)
        ע: ��ȻͶ�ʹ�Ʊֻ��250�죬���ǳ��й�Ʊ��ķǽ�����Ҳû�취Ͷ�ʵ������ط�������������ȡ365

        �ο�: https://wiki.mbalib.com/zh-tw/%E5%B9%B4%E5%8C%96%E6%94%B6%E7%9B%8A%E7%8E%87
        """
        D = 365
        c = self._ast_val_hist[0]
        v = self._ast_val_hist[-1]
        days = (self._date_hist[-1] - self._date_hist[0]).days

        ret = (v / c) ** (D / days) - 1
        return ret
```

����һ��������Ҫ�Ļز����γ��ˡ�

## ������ʷ����
�ڻز������Ҳ�û�м��ɸ��ֻ�ȡ���ݵķ�������Ϊ�Ⲣ���ǻز��ܱ��뼯�ɵĲ��֣��涨���ݽṹ�Ϳ����ˣ����ݵĻ�ȡͨ���鿴����ƪ��

## �زⱨ��
�زⱨ����Ҳ�����˻ز���֮�⣬����д��һ��Plottter�Ķ������ڻ���һЩ�ز�ָ��ȡ��������:

![report](img/report.png)

## �ز�ʾ��
������һ���ز�ʾ����


```
import json
from backtest import BackTest
from reporter import Plotter


class MyBackTest(BackTest):
    def initialize(self):
        self.info("initialize")

    def finish(self):
        self.info("finish")

    def on_tick(self, tick):
        tick_data = self.ctx["tick_data"]

        for code, hist in tick_data.items():
            if hist["ma10"] > 1.05 * hist["ma20"]:
                self.ctx.broker.buy(code, hist.close, 500, ttl=5)

            if hist["ma10"] < hist["ma20"] and code in self.ctx.broker.position:
                self.ctx.broker.sell(code, hist.close, 200, ttl=1)

if __name__ == '__main__':
    from utils import load_hist
    feed = {}

    for code, hist in load_hist("000002.SZ"):
        # hist = hist.iloc[:100]
        hist["ma10"] = hist.close.rolling(10).mean()
        hist["ma20"] = hist.close.rolling(20).mean()
        feed[code] = hist

    mytest = MyBackTest(feed)
    mytest.start()
    order_lst = mytest.ctx.broker.order_hist_lst
    with open("report/order_hist.json", "w") as wf:
        json.dump(order_lst, wf, indent=4, default=str)
    stats = mytest.stat
    stats.data.to_csv("report/stat.csv")
    print("�������棺 {:.3f}%".format(stats.total_returns * 100))
    print("���س���: {:.3f}% ".format(stats.max_dropdown * 100))
    print("�껯����: {:.3f}% ".format(stats.annual_return * 100))
    print("���ձ���: {:.3f} ".format(stats.sharpe))

    plotter = Plotter(feed, stats, order_lst)
    plotter.report("report/report.png")

```

## ��Ŀ��ַ
https://github.com/youerning/stock_playground

