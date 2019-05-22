# Go-MTR

## 介绍

> 程序基于https://github.com/tonobo/mtr和https://github.com/liuxinglanyue/mtr进行了一定的bug修复和优化开发。支持单机多携程并发mtr探测，同时支持ipv4和ipv6。

## 案例
```
var targets = []string{"216.58.200.78", "52.74.223.119", "123.125.114.144"}

func main() {
	var wg sync.WaitGroup
	for {
		for _, val := range targets {
			wg.Add(1)
			go func(target string) {
				mm, err := mtr.Mtr(target, 30, 10, 5)
				if err != nil {
					fmt.Println(err)
				}
				fmt.Println(mm)
				wg.Done()
			}(val)
		}

		wg.Wait()

		time.Sleep(60 * time.Second)
	}
}

```

*注：针对mtr可设定的参数包括maxHops(最大跳数)、sntSize(发送数据包数量)、retries(失败重试次数)。亦可设定icmp探测的超时时间，可通过修改程序实现。*

## 原理
> mtr发送icmp数据报，先发送TTL为1的，到第一个路由器TTL减1，并返回一个超时的ICMP报文，这样就得到了第一个路由器的地址；  接下来发送TTL值为2的报文，得到第二个路由器的报文； 直到找到目的IP地址，mtr一次探测结束。然后根据发送数据包的数量，重新发起mtr探测，记录结果。

