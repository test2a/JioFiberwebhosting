# JioFiberwebhosting
Repo to collect data on how to host web services primarily on Jio fiber home connection
So you want to host some web services because you have some SBC like Raspberry pi lying around or want to convert your old laptop/Pc into a server but don't know where to start?
Well In this repo, i will attempt to assimilate the various free software (as opposed to mere 'open source') you can install in the least amount of friction available and to host them yourself.
I will assume you want to use your home fiber internet connection because otherwise if you already use a vpn, this guide is too basic for you.

Anyways, I will be using Jio Fiber to start this because this is what i have at home and what i can test readily. If your ISP is different, please contribute changes to the software so that their users can also join in.

## Hosting on Jio Fiber

Jio Fiber gives you a public facing Ipv4 address which is behind a CGNAT, meaning long story short you do NOT get an ipv4 address and all attempts to conect to the public ipv4 address of jio will fail because it is just one/few for all users. To that end, there is nothing you can do about it, unless you go enterprise connection where the high end connections do give you option to have a private ipv4.

Now, we wouldn't be here if this was the end of the story, so instead of ipv4, jio offers you ipv6 adresses, which the world has a lot of and as such, all ipv6 addresses are public.

Jio to their credit, locks down pretty hard on any incoming traffic which is blocked by default. THIS IS GOOD because 99.99% of users should not have to need this and if it were on by default, it would have been a security nightmare.

Lets start, assume you have a raspberry pi connected to the network via LAN, you can do ```` ifconfig ```` to find your ipv4 address, which is local. you will also need your ipv6 address. The Raspberry pi gives you this output the last part is important if you are using wifi,
````
wlan0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.29.242  netmask 255.255.255.0  broadcast 192.168.29.255
        inet6 2401:201:2913:k027:d140:be1q:3d2bb5:ecds  prefixlen 64  scopeid 0x0<global>
        inet6 fes0::c7ee:d38a:6w19:3e9  prefixlen 64  scopeid 0x20<link>
        ether b8:27:eb:0x:23:9f  txqueuelen 1000  (Ethernet)
        RX packets 175901  bytes 115485441 (110.1 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 109502  bytes 88104704 (84.0 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
````

the important bits are ```` inet ```` and ````inet6````

keep a note of these two.

what you will need to do is login to http://192.168.29.1 with the default credentials "admin/Jiocentrum". It will ask you to change the password, you need to do it anyways.

Once that is done, go to security on the sidebar. then firewall.

There open "port forwarding".

If you want to host a website on this Pi for example whose ipv4 address you found out was ````192.168.29.242```` you need to write in the box as follows


![Screenshot_20210527_134833](https://user-images.githubusercontent.com/39449563/119804846-5c090e00-befe-11eb-80e5-5d7736983a82.png)

once that is done, you need to save it. Then, to have your website/service accessible from outside, you need to go to "ipv6 firewall rules". there you will need your ipv6 or inet6 address you found above. 
![Screenshot_20210527_135056](https://user-images.githubusercontent.com/39449563/119804872-62978580-befe-11eb-9763-f01cc21c228b.png)


Once that is done, this was the hard part. Now follow the following guide which i followed to set up duckdns https://www.wundertech.net/how-to-setup-duckdns-on-a-raspberry-pi/


At this point, if you just need a simple web server, you could use apache2 or lamp server and use the duckdns url to access it. It "should" work.


Now, the problem with Jio fiber is that they keep rotating the IPV6 addresses somehow. Why? i am not sure but they do and as such, your duckdns will work but the ipv6 firewall rules" you set up earlier will have changed, otherwise duckdns will publish your new ipv6 address but the new address will not be allowed by Jio fiber and you will have to manually configure it again.

Enter python. I used something called selenium and bash to find my current ipv6 address and paste that to a text file. I used a second check file to check if ipv6 address has changed since last update but the idea is, if the address has changed, selenium will log into the jio admin portal, open this ipv6 firewall rules page and update the old address with new one. Cool.

Note. you will need a domain name or at least a subdomain to get access to many services because they demand https and letsencrypt. Assuming you already have a subdomain handy, simply follow this guide https://pimylifeup.com/raspberry-pi-ssl-lets-encrypt/ to set up lets encrypt

if you have a domain and want to redirect a subdomain to the duckdns url, simply go to your domain registrar page and find "set custom dns records" and add a record with ``` type=cname records ````
