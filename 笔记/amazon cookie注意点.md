请求首页      validation_token, ubid_main_url, cookie_dict
请求second    cookie_ubid
请求third     CSRF_TOKEN
请求four


请求首页      ubid_main_url
请求second    cookie_dict
请求搜索页    cookie_ubid, validation_token, a2z
请求accept   
请求third   CSRF_TOKEN
请求four    


每轮请求的header和impersonate一定要保持一致

验证码问题没办法解决 因为如果用某个ip解决了验证码并且获取到邮编cookie
那么这个ip和cookie是绑定的 如果下次用别的ip就会失效

### 各ip代理商对Amazon cookie友好度

| 站点ID           | url                        | 快代理(0.008) | 青果(0.003) | 巨量3分(0.009) | 豆芽（0.004） | 青果独享 |
| :------------- | :------------------------- | :--------: | :-------: | :---------: | :-------: | :--: |
| A13V1IB3VIYZZH | https://www.amazon.fr/     |    95%     |    55%    |   ==99%==   |           | 100  |
| A1AM78C64UM0Y8 | https://www.amazon.com.mx/ |  ==99%==   |  ==99%==  |     92%     |    0%     |  20  |
| A1F83G8C2ARO7P | https://www.amazon.co.uk/  |    20%     |    40%    |  ==100%==   |    99%    | 100  |
| A1PA6795UKMFR9 | https://www.amazon.de/     |  ==99%==   |    42%    |     20%     |    97%    | 100  |
| A1RKKUPIHCS9HS | https://www.amazon.es/     |  ==99%==   |  ==99%==  |     90%     |           | 100  |
| A2EUQ1WTGCTBG2 | https://www.amazon.ca/     |     0%     | ==100%==  |   ==99%==   |    65%    |  0   |
| A2NODRKZP88ZB9 | https://www.amazon.se/     |    90%     |    30%    |   ==95%==   |           | 100  |
| APJ6JRA9NG5V4  | https://www.amazon.it/     |     2%     |  ==99%==  |   ==99%==   |           | 100  |
| ATVPDKIKX0DER  | https://www.amazon.com/    |  ==99%==   |  ==99%==  |     88%     |           | 100  |
| A1C3SOZRARQ6R3 | https://www.amazon.pl/     |            |    99%    |             |           |      |
| A19VAU5U5O7RUS | https://www.amazon.sg/     |            |    99%    |             |           |      |
| A2Q3Y263D00KWC | https://www.amazon.com.br/ |            |           |             |           | 99%  |
| A39IBJ37TRP1C6 | https://www.amazon.com.au/ |            |    99%    |             |           |      |
| A2VIGQ35RCS4UG |                            |            |    90%    |     60%     |           |      |
|                |                            |            |           |             |           |      |
|                |                            |            |           |             |           |      |
|                |                            |            |           |             |           |      |
|                |                            |            |           |             |           |      |
|                |                            |            |           |             |           |      |
|                |                            |            |           |             |           |      |
cookie结论：
A1AM78C64UM0Y8 青果
A2EUQ1WTGCTBG2 青果
其余站点                  青果独享

### 各ip代理商对Amazon product友好度

| 站点ID           | url                        | 快代理(0.008) | 青果(0.003) | 巨量3分(0.009) | 豆芽（0.004） | 青果独享 |
| :------------- | :------------------------- | :--------: | :-------: | :---------: | :-------: | :--: |
| A13V1IB3VIYZZH | https://www.amazon.fr/     |    95%     |    55%    |   ==99%==   |           | 100  |
| A1AM78C64UM0Y8 | https://www.amazon.com.mx/ |  ==99%==   |  ==99%==  |     92%     |    0%     | 100  |
| A1F83G8C2ARO7P | https://www.amazon.co.uk/  |    20%     |    40%    |  ==100%==   |    99%    |  0   |
| A1PA6795UKMFR9 | https://www.amazon.de/     |    25%     |    42%    |   ==99%==   |    97%    | 100  |
| A1RKKUPIHCS9HS | https://www.amazon.es/     |  ==99%==   |  ==99%==  |     90%     |           | 100  |
| A2EUQ1WTGCTBG2 | https://www.amazon.ca/     |    90%     |    85%    |      0      |     0     |  80  |
| A2NODRKZP88ZB9 | https://www.amazon.se/     |    90%     |    30%    |   ==95%==   |           | 100  |
| APJ6JRA9NG5V4  | https://www.amazon.it/     |     2%     |  ==99%==  |   ==99%==   |           | 100  |
| ATVPDKIKX0DER  | https://www.amazon.com/    |  ==99%==   |  ==99%==  |     88%     |           | 100  |
product结论：
A1F83G8C2ARO7P       青果
A2EUQ1WTGCTBG2     青果
其余站点                      青果独享


search结论：
A13V1IB3VIYZZH        青果独享
APJ6JRA9NG5V4         青果独享
其余站点                     青果

## 0213测试情况

1、list情况
14:21:54        2450 
15:15:42        0
53分钟 48秒  3228秒
速度：0.758/s


2、product情况
2个任务 20线程 5323 3883


3、merchant
16:35:36 25468
17:31:39 17750
18:39:14 0
0.56.03   3363  s    7718     2.29/s  一个进程
1.07.35   4055  s    17750   4.37/s  两个进程
 


4、search
6个任务 10线程 3845

10个任务 20线程 2251-3413


### 0214测试cookie获取
快代理0.008：
9457
9227
获取cookie 387个 成本1.84元


巨量1分钟0.005：
10000
9187
获取cookie 93个 成本4.065元

巨量3分钟0.009：
5000
4935
获取cookie 283个 成本0.585元



开始时间： 14:07:19
结束时间： 14:34:55
480个

开始时间： 14:45:14
结束时间： 14:55:00
283个


预计一轮700个cookie需要25分钟
花费2.425元



每天定时更新amazon_crawl_object_info的任务到各个任务表中（clear_task_object.py）
每天定时更新cookie 700个（amazon_cookies_cdy_2.py）


澳大利亚

![[Pasted image 20250408101208.png]]

南非
{"locationType":"CITY","city":"Cape Town","cityName":"Cape Town","deviceType":"web","storeContext":"generic","pageType":"Gateway","actionSource":"glow"}


阿联酋
{"city":"Dubai","deviceType":"web","locationType":"CITY","storeContext":"generic","pageType":"Gateway","actionSource":"glow"}


沙特
{"locationType":"CITY","city":"Riyadh","cityName":"Riyadh","deviceType":"web","storeContext":"generic","pageType":"Gateway","actionSource":"glow"}