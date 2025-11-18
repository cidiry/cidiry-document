

		   https=TLS/SSL + http
客户端                                                              服务端
		   支持算法+信息=>md5                  第一次  TSL/SSL（通过/不通过）
								    第二次 http(数据)			

1、绕过TSL校验的思路
	-修改指纹信息=>md5
    -模拟浏览器的指纹

2、查看指纹
	- 网站：https://tls.browserleaks.com/json
	- 工具：wireshark

ja3_hash: cc1fed34a3c382f668581a208d4ecf1b
ja3_text: (算法代号 tsl版本 扩展代号 。。。)
771,- tsl版本
4865-4866-4867-49195-49199-49196-49200-52393-52392-49171-49172-156-157-47-53, - 算法代号
23-45-11-43-17513-35-51-27-13-5-18-10-0-65037-65281-16, - 扩展代号
25497-29-23-24, - extension
0 - ...

3、如何绕过
	- requests -> urllib3 -> openssl 注意版本 ssl.py 只能改加密算法
	- curl-cffi
		>>>curl 伪造发送请求 => 二次开发（常见浏览器指纹） => 发送请求
	- Go语言
		- cycletls 模块
