---
layout: post
title:  Understanding Latency Performance of Wi-Fi and LTE
date:   2018-01-27 21:43:55 -0500
---
_A project report I finished in late 2015 for **Tufts COMP112 Networks & Protocols**.  Using data collected from a single mobile device, I compared the latency performance of two prevalent wireless technologies: Wi-Fi and LTE. I also investigated the causes of the performance difference._

_Posted with minor changes in wording._

_ _ _

### Prologue

Mobile communication is pervasive and now connecting billions of people. Low communication latency is essential to provide a satisfying experience for many services such as chatting, navigating, online gaming, and video streaming. There are various cellular technologies, such as GSM, CDMA, HSPA, and LTE, are deployed all over the world. At home or workplace, Wireless LANs are pervasive. Among these technologies, LTE and IEEE 802.11 wireless LAN (also known as Wi-Fi) get the most attention because they offer high throughput and somehow low latency. Based on data collected from a mobile device, this essay makes a comparative analysis of the latency performance of those two technologies.

### Brief Introduction to Wi-Fi and LTE

#### Wi-Fi

The Wi-Fi Alliance defines Wi-Fi as any “wireless local area network” (WLAN) product based on the Institute of Electrical and Electronics Engineers’ (IEEE) 802.11 standards.” Wi-Fi is one of the most important access network technologies on the Internet today. It has a base station that is connected to a larger wired network (e.g., the Internet). Here we investigate both corporate network and home network. In a corporate network, there are multiple base stations to support a large number of clients. And usually, there is a firewall between the internet and the corporate intranet. All data transferred over the wireless probably is encrypted, providing added security. In a home network, there is probably only one base station and the security is of less concern. the ISP provides wired networking through cable. Using a Wi-Fi router, consumers can easily turn a wired network to a wireless network.

#### LTE

LTE is the Long Term Evolution of the Universal Mobile Telecommunications System (UMTS). The purpose of LTE is offering high speeds and low latencies over long distances. And the high-level network architecture of LTE is comprised of following three main components:

1.  The User Equipment (UE): e.g. the mobile device
2.  The Evolved UMTS Terrestrial Radio Access Network (E-UTRAN): The E-UTRAN handles the radio communications between the mobile and the evolved packet core(EPC). The evolved base stations are called eNodeB or eNB. Each eNB is a base station that controls the mobiles in one or more cells. The base station that is communicating with a mobile is known as its serving eNB.
3.  The Evolved Packet Core (EPC): The EPC is a simplified all-IP core network that unifies the separate circuit-switched cellular voice network and the packet-switched cellular data network. And both voice and data will be carried in IP datagrams.

![Components of a LTE network](assets/img/latency_report_LTE_structure.jpg) **Figure 1:** Components of a LTE network [^fn1]

### Settings of Measurement

#### Device

All measurements were carried out from a Moto Nexus 6 smartphone with Android 6.0 Marshmallow. A very interesting part of the phone is that it is not using traditional carriers, such as T-mobile or AT&T. Instead, it’s supported by Google’s Project Fi[^fn2]. Here Google plays a role as Mobile Virtual Network Operator(MVNO). It works by giving LTE service on two mobile networks, T-Mobile and Sprint, which the phone can intelligently switch between. Also, it uses Wi-Fi making calls and sending texts whenever available.

#### Network Environments

The data was collected from four different network environments:

-   tufts-secure, representing corporate network
-   Comcast wifi, representing home network
-   t-mobile LTE
-   sprint LTE

### Data and Analysis

#### Part I: Round-trip Time (RTT)

In the experiment, 50 top US websites had been pinged[^fn3] from the device. Ping command uses the ICMP protocol which has been created to check IP connectivity and get information about other hosts in an IP network. However, the ICMP requests could be blocked by the servers. Although 100% packet loss doesn’t mean the connection is bad, for the integrity of the data, only the websites that experienced zero packet loss were considered as valid samples. There were 28 such websites.

**Table 1**: rates of packet loss under four networks

| rate of packet loss | tufts-secure Wi-Fi | Comcast Wi-Fi | t-mobile LTE | sprint LTE |
| --- | --- | --- | --- | --- |
| 0% | 28 | 31 | 29 | 32 |
| 100% | 21 | 19 | 20 | 18 |
| Other | 1 | 0 | 1 | 0 |

There are two dimensions worth of checking: average RTT and covariance of RTT. The former dimension gives us a general idea on the performance, while the later tells us how stable the performance is.

![avarage RTT](/assets/img/latency_report_ave_RTT.png) **Figure 2**: average RTT in four networks

From figure 2, it’s obvious that the latencies of Wi-Fi are much less than the latencies of LTE. For example, 50% of the RTTs in Wi-Fi environment is around 20 ms or less, but in the case of LTE, the threshold is about 120 ms. There is a 100-ms gap.

![cv of RTT](/assets/img/latency_report_cv_RTT.png) **Figure 3**: covariance of RTT in four networks

From figure 3, it’s also obvious that Wi-Fi has less relative variability even in static testing. 90% of all coefficient of variation with WiFI are less than 0.2, but only 40% of the coefficient falls in that rage. In general, Wi-Fi is preferred to LTE due to less latency and smaller relative variability. The 100-ms gap of Wi-Fi and LTE reflects the different architecture of these two technologies. Internet routing latency domains Wi-Fi, but there are more latencies involved in the case of LET:

-   Control-plane latency: it is a fixed, one-time latency incurred for RRC negotiation and state transitions, the latency is usually less than 100ms for idle to active, and less than 50 for dormant to active.
-   User-plane latency: it is also a fixed cost for every application packet transferred between the device and the radio tower, and it is less than 5 ms.
-   Core network latency: it’s the latency for transporting the packet from the radio tower to the packet gateway, and the value is between 30 t0 100 ms in practice.

Also, when considering RTT, we should double some of the above latencies. Thus, it is not surprising to see a 100-ms gap between LTE and Wi-Fi.

#### Part II: Routing

Above analysis gives us a general idea of the performance of those two techniques. This part will check some details by using `traceroute` to investigate the routing path. Facebook and Reddit were selected for this case analysis because they share some interesting figures:

-   top U.S. website[^fn4], high frequency of interaction with users and high demand on low latency
-   traceroute-friendly: many hosts block traceroute request, which makes it difficult to get a full path.
-   Facebook owns network infrastructure while Reddit does not.

##### Facebook

![paths from users to hosts](/assets/img/path_to_facebook.png)
**Figure 4**: The path to Facebook

In general, all paths converge to Facebook's own network “tfbnw”. For tufts-secure, the packets were sent from tufts corporate intranet to `level 3` and then to `tfbnw`. For Comcast, all traffic up to `tfbnw` are actually in the domain of Comcast. For LTE, in the case of t-mobile, it took 48 ms for the packets to get into the internet, and t-mobile then sent packets to another ISP, while sprint core network sent the packets to its own network service.

##### Reddit

![paths from users to hosts](/assets/img/path_to_reddit.png)
**Figure 5**: The path to Reddit

Reddit doesn’t operate network infrastructure, so the packets accessed the server via different paths. Similar to Facebook, there were only few ISPs involved in the routing. For tufts-secure, they were `level 3` and `telia`. For Comcast, they were just Comcast and NTT. And in the t-mobile case, `level 3` and `telia` again. For sprint, packets went through sprint core network and service network and finally gtt. It’s clear that core network latency well explains the difference between Wi-Fi and LTE. If we deduct the core network latency, the gap would be much much narrow.

#### Part III: Mobility

Another interesting topic is the networking handoff problem. The problem arises when a mobile host moves beyond the range of one base station and into the range of another, it will change its point of attachment into the larger network (i.e., change the base station with which it is associated). Mobility in the same subnet of Wi-Fi network is of less problem. For example, when a mobile device in a building moves from AP1 to AP 2, it would detect a weakening single from AP1 and stars to scan for a stronger signal. The device receives beacon frames from AP2, then it disassociates with AP1 and associates with AP2. Both APs have same SSID, e.g. tufts-secure, so the IP address is same as before and the ongoing TCP sessions could be maintained. The situation will be more complex in the case of LTE. The switching between LTE and Wi-Fi is beyond the scope of the essay. Below is a simple test to show the problem. The test was carried out by simply pinging reddit.com continuously while walking around campus. The network used started with tufts-secure, then t-mobile, then tufts-secure, so on so forth. The worst latencies happened when the network switched in between.

![Volatility of RTT](/assets/img/latency_report_mobility_annotated.png) Figure 6: Volatility of RTT

### Conclusion

Some preliminary results:

-   The latency of Wi-Fi network is much lower than the latency of LTE network. A typical gap is 100 ms.
-   The variability of latency in Wi-Fi network is also less than the variability in LTE network.The high latency of LTE is mainly caused by Core network latency, or the latency for transporting the packet from the radio tower to the packet gateway.
-   For top websites, the paths from users to hosts only involves 2 or 3 ISPs. Some LTE carriers try to keep as much traffic as possible in its own network, e.g. sprint. Some corporate users build their own network, e.g. Facebook.
-   Switching between different types of networks can make latency extremely high.

One of the limitations of the investigation is that the Quality of service (QoS) of networks varies from time to time, therefore more observations are needed to consolidate the conclusion.

_ _ _

#### References
[^fn1]: Source: [http://www.telekomakademi.com/](http://www.telekomakademi.com/)
[^fn2]:[Google Project Fi](https://fi.google.com)
[^fn3]: [Alexa - Top Sites in United States.](http://www.alexa.com/topsites/countries/US)
[^fn4]:Facebook ranked no. 2 and Reddit ranked no. 9 when the experiment was conducted.
