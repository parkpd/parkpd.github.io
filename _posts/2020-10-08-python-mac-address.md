---
title: python 으로 이더넷 mac 주소 얻는 코드
date: 2020-10-08 13:00:00+0900
categories: Python
tags: [Python, Mac, Network, Nic]
---
https://pypi.org/project/get-mac/
{% highlight python %}
import getmac
print(getmac.get_mac_address())
{% endhighlight %}

https://pypi.org/project/netifaces/
{% highlight python %}
import netifaces
for nic in netifaces.interfaces():
    iface = netifaces.ifaddresses(nic)[netifaces.AF_LINK]
    if len(iface[0]['addr']) > 0:
        print("mac address: ", iface[0]['addr'])
{% endhighlight %}  
[Waves](https://www.waves.com/) 라는 가상악기가 MAC address 를 비교해 인증하고 있는데, Hyper-V 가 가상 이더넷 어뎁터를 설치했더니 인증에 실패하고 있다. Waves 기술지원팀과 논의해서 이 문제를 해결하려고 Python 코드를 간단히 짜 봤다.