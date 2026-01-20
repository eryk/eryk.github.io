---
title: leve1、level2 解释
date: 2017-08-24 16:36:21
categories: ["金融"]
tags: ["level2", "order book", "level1"]
---

# Level 1 Market Data 

Basic market data is known as level 1 market data, and includes the following information:

* **Bid price**: The highest price that a trader is willing to buy an asset at.
* **Bid size**: The number of shares, forex lots or contracts that are available at the bid price.
* **Ask price**: The lowest price that a trader is willing to sell an asset at.
* **Ask size**: The number of shares, forex lots or contracts that are available at the ask price.
* **Last price**: The price at which the most recent trade was completed.
* **Last size**: The number of shares, forex lots or contracts that were traded in the most recent trade.

Level 1 market data provides all of the information needed to trade using most trading systems. If you are trading a price action or indicator based strategy, then Level 1 market data should satisfy your informational needs. 

Scalpers, and traders who trade based on changes in how other traders are bidding and offering (offer and ask are used interchangeably), will need Level 2 market data, which provides multiple levels of bids and offers.

# Level 2 Market Data

Additional market data is known as level 2 market data, the order book, or the depth of market, and includes the following additional information:

* **Highest bid prices**: The highest 5 to 15 prices (depending upon the market) where traders are willing to buy an asset. This means you not only see the current bid, but also all the bids currently below it. In actively traded stocks, there will typically be bids every $0.01 below the current bid, and in actively traded futures, there will typically be a bid each tick below the current bid. If there is a gap between the current bid and next bid, that typically means the stock or contract may experience a larger bid/ask spread and less volume.

* **Bid sizes**: The number of shares, forex lots or contracts that are available at each of the bid prices.
* **Lowest ask prices**: The lowest 5 to 15 prices (depending upon the market) where traders are willing to sell an asset. This means you not only see the current ask, but also all the asks above the current ask. In actively traded stocks, there will typically be asks every $0.01 above the current ask, and in actively traded futures, there will typically be an ask each tick above the current ask. If there is a gap between the current ask and next ask, that typically means the stock or contract may experience a larger bid/ask spread and less volume.
* **Ask sizes**: The number of shares, forex lots or contracts that are available at each of the ask prices.

Level 2 market data provides the additional information that is needed to trade based on changes that occur in the bids and offers. Level 2 market data is also known as the order book, because it shows the orders that are currently pending for the market. It is also known as the depth of market, or market depth, because it shows the number of contracts (or shares or lots) that are available at each of the bid and ask prices.

![](images/15035650306126.jpg)


# Order Book

> An order book is the list of orders (manual or electronic) that a trading venue (in particular stock exchanges) uses to record the interest of buyers and sellers in a particular financial instrument. A matching engine uses the book to determine which orders can be fulfilled i.e. what trades can be made.   --wikipedia

# Level2行情

### 买卖行情

买入委托和卖出委托前10档的委托价和委托量,投资者可以看得更远,哪个价位有阻力？哪个价位有支撑？哪个价位有大笔挂单,一目了然。

### 总买总卖

当前全部买入(卖出)委托的总量和加权均价,据此投资者可以判断盘中的支撑位(委买均价)、阻力位(委卖均价)、支撑力度(委买总量)、阻力大小(委卖总量),还可以根据这些数据的动态变化分析多空双方力量的变化,寻找行情的转折点

### 成交明细

在Level-2之前,沪深交易所提供的都是行情快照,大家看到的分笔成交其实是两次快照期间累计的成交量和最后一笔的价格,而逐笔成交则是真实的每笔成交价和成交量的明细数据。逐笔成交极大地提高了行情的透明度。

### 买卖队列

买一或卖一的前50笔委托单明细,根据委托单的大小或委托单是否有规律,可以判断委托是机构、大户、或散户所为。


# 逐笔数据与分笔数据的根本区别

国内的Tick数据指的是 按固定时间间隔取个快照记录下来

1. 逐笔成交一般显示的数据格式为在几分几秒以多少价格分几笔成交了多少手。在这里我们要注意的是成交手数有时候是带小数点的，这是因为股票买进的股数最少是100股，委托的股数也应是100的整数倍，卖出却没有限制，因此成交的手数会有小数点。另外一点就是如果在成交价格和手数前面没有显示，则一半是默认的1笔。 
2. 分时成交一般显示的数据格式为在几分几秒以多少价格成交了多少手。这里需要注意的是成交手数永远是整数，不会出现小数点数字。其中现手累计数就是总手数。总手数也叫做成交量。有些软件在现量后面标注蓝色S和红色B，前者代表卖，后者代表买。目前市面上出现了LEVEL-2行情数据，比较具有代表性的是大智慧，在那里把分笔成交是叫分时成交，实际上就是我们在普通分析软件上F1看到的“分笔成交明细”，但是他和LEVEL-2行情数据提供的逐笔成交明细是不一样的。 
3. 分笔数据由于是合成混合数据，它是以最后1笔的买卖方向来表示该时间内（3秒或者6秒）的买卖方向，所以误差很大。（见下图说明）
4. 一个孤独的数字是缺乏意义的，但是一些连续的数字则是充满想像的。一般来说，成交笔数越少，金额越大，表示成交比较强势，反之是弱势。尤其是成交手数比较大而集中的时候，表示有大资金活跃迹象，该股出现价格异动的概率就大，应该引起投资者的注意。而如果半天也没人买或者都是一些小单子在交易，则至少短期不大可能成为好股。 
5. 交易数据三要素----成交量、成交价格和成交笔数。不陌生的是前面两个，笔数就是交易批次。在成交量一定的前提下，笔数少说明交易力度强，反之就弱。笔数的变动与数量方向一致，交易为常态，反之就是非常态。

![](images/15035663072399.gif)
 

# 引用

https://www.thebalance.com/order-book-level-2-market-data-and-depth-of-market-1031118

https://baike.baidu.com/item/Level-2

https://www.zhihu.com/question/26950456

http://www.cnblogs.com/chuncn/archive/2009/03/13/1410144.html