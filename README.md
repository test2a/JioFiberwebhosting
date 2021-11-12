# JioFiber Webhosting
Monorepo to collect data on how to host web services primarily on JioFiber Home Connection | Should work in any CGNAT Based Internet

So you want to host some web services because you have some SBC like Raspberry Pi lying around or want to convert your old Laptop / PC into a server but don't know where to start?

Well in this repo, we will attempt to assimilate the various free software (as opposed to mere 'open source') you can install in the least amount of friction available and to host them yourself.

We will assume you want to use your CGNAT based internet connection because otherwise if you already use a vpn, this guide is just worthless.

Anyways, we will be using Jio Fiber to start this because this is what I have at home and what I can test readily. If your ISP is different, please contribute changes to the software so that their users can also join in.

## Some other very working services

If you are trying to get your normal test website or something like that on the web for a brief amount of time, default on https://localhost.run. You can get your website running on just 2-3 steps and you will get a subdomain. All of this while maintaining privacy. If you want another collaborator or friend to access your local internet, ZeroTier is a great place to start. Do note that these services have a ton of latency.

## Hosting on JioFiber

JioFiber gives you a public facing IPv4 address which is behind a CGNAT, meaning you do NOT get an ipv4 address and all attempts to connect to the public IPv4 address of Jio will fail because it is just one/few for all users. To that end, there is nothing you can do about it, unless you go enterprise connection where the high end connections do give you option to have a private IPv4. (The lowest tier where you get an static IP is 5k INR and 1GBitPS)

Now, we wouldn't be here if this was the end of the story, so instead of IPv4, Jio also offers you IPv6 adresses, which basically cannot be exhausted and therefore is public facing.

Jio to their credit, locks down pretty hard on any incoming traffic which is blocked by default. This is **GOOD** because 99.99% of users should not have to need this and if it were on by default, it would have been a security nightmare.

Lets start, assume you have a raspberry pi connected to the network via LAN, you can do `ifconfig` or `ip a` to find your local IPv4 address. you will also need your IPv6 address. The Raspberry Pi gives you this output the last part is important if you are using wifi,
```
wlan0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.29.242  netmask 255.255.255.0  broadcast 192.168.29.255
        inet6 2401:201:2913:k027:d140:be1q:3d2bb5:ecds  prefixlen 64  scopeid 0x0<global>
        inet6 fes0::c7ee:d38a:6w19:3e9  prefixlen 64  scopeid 0x20<link>
        ether b8:27:eb:0x:23:9f  txqueuelen 1000  (Ethernet)
        RX packets 175901  bytes 115485441 (110.1 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 109502  bytes 88104704 (84.0 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

The important bits are `inet` and `inet6`

Keep a note of these two.

What you will need to do is login to http://192.168.29.1 with the default credentials "admin/Jiocentrum". It will ask you to change the password, you need to do it anyways.

Once that is done, go to Security on the sidebar. then Firewall.

There open "Port Forwarding".

If you want to host a website on this Pi for example whose IPv4 address you found out was `192.168.29.242` you need to write in the box as follows


![Screenshot_20210527_134833](https://user-images.githubusercontent.com/39449563/119804846-5c090e00-befe-11eb-80e5-5d7736983a82.png)

Once that is done, you need to save it. Then, to have your website/service accessible from outside, you need to go to "IPv6 Firewall Rules". there you will need your IPv6 or inet6 address you found above. 

![Screenshot_20210527_135056](https://user-images.githubusercontent.com/39449563/119804872-62978580-befe-11eb-9763-f01cc21c228b.png)


Once that is done, follow the following guide which we followed to set up duckdns https://www.wundertech.net/how-to-setup-duckdns-on-a-raspberry-pi/


At this point, if you just need a simple web server, you could use apache2 or lamp server and use the duckdns url to access it. It "should" work.


Now, the problem with JioFiber is that they keep rotating the IPv6 addresses due to Dynamic IP perks (this is also a privacy feature) thus, your duckdns will work but the "IPv6 Firewall Rules" you set up earlier will have changed, otherwise duckdns will publish your new ipv6 address but the new address will not be allowed by JioFiber and you will have to manually configure it again.

Enter Python. I used something called Selenium and Bash to find my current IPv6 address and paste that to a text file. I used a second check file to check if IPv6 address has changed since last update but the idea is, if the address has changed, Selenium will use ChromeDriver to log into the Jio admin portal, open this IPv6 firewall rules page and update the old address with new one. Cool.

Note. you will need a domain name or at least a subdomain to get access to many services because they demand https and letsencrypt. Assuming you already have a subdomain handy, simply follow this guide https://pimylifeup.com/raspberry-pi-ssl-lets-encrypt/ to set up lets encrypt

If you have a domain and want to redirect a subdomain to the duckdns url, simply go to your domain registrar page and find "set custom dns records" and add a record with `type=cname records`
