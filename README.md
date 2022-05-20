<div id="top"></div>

<br />
<div align="center">

  <h3 align="center">NGINX (pronounced "engine x")</h3>


</div>

 ![Samba](https://adrianmejia.com/images/samba-filesharing-with-windows-ubuntu-mac-large.jpg)

<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ul>
    <li>
        <a href="#OVERVIEW">Overview</a>
    </li>
    <li>
      <a href="#Installation">Installation</a>
    </li>
  </ul>
</details>



<!-- ABOUT THE PROJECT 


[![Product Name Screen Shot][product-screenshot]](https://example.com)-->
## OVERVIEW
nginx (pronounced "engine x") is a free, open-source, high-performance HTTP server. nginx is known for its stability, rich feature set, simple configuration, and low resource consumption. For these reasons, it is a great alternative to the more commonly used Apache webserver.

Following the steps below will show you how to install nginx and test its functionality. Be sure to follow our more advanced how-to on configuring virtual hosts afterwards, which will give you the ability to serve multiple websites from your (ve) Server.


## INSTALLATION
* Log into your (ve) Server via SSH as the root user.
```sh
ssh root@hostname
```

* Use apt-get to update your (ve) Server.
```sh
root@karmic:~# apt-get update
```
* Install nginx.
```sh
root@karmic:~# apt-get install nginx
```

* By default, nginx will not start automatically, so you need to use the following command. Other valid options are "stop" and "restart".
```sh
root@karmic:~# sudo /etc/init.d/nginx start
Starting nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
configuration file /etc/nginx/nginx.conf test is successful
nginx.
```

* Test nginx by pointing your web browser at your domain name or IP address. You should see the default nginx page as shown in Figure 1 below:

![installation done](https://mediatemple.zendesk.com/hc/article_attachments/202351684/Nginx_1.png)

Now that you have successfully installed nginx, it's time to see what more you can do.

## SETTING UP WEBSITES/CONFIGURING VIRTUAL HOSTS

After installing nginx, you'll probably want to add some websites. nginx makes it very simple to create virtual hosts, allowing you to host multiple websites on a single (ve) Server. We will use Ubuntu for this article. Directions will vary slightly with other operating systems. These instructions are for Ubuntu 9.10 specifically, but should work for other versions of Ubuntu as well.

* Change to the `/var/www directory`. This is the default path for websites in nginx. A recursive listing of this directory will show you the basic structure:
```sh
root@karmic# cd /var/www/
root@karmic:/var/www# ls -R
nginx-default:
50x.html  index.html
```

* You can see two html pages: index.html is the homepage and 50x.html is used for error pages.
Let's add a couple of websites, ve-server1.com and ve-server2.com, with a very basic directory structure for content and logs:
```sh
root@karmic:/var/www# mkdir -p /var/www/ve-server{1,2}.com/{public,logs}
```

* Let's create a simple homepage for each domain that we can test with later:

```sh
root@karmic:/var/www#echo -e 
```
```html
<html>\n
    <head>\n
        <title>Welcome to nginx!</title>\n
    </head>\n
        <body bgcolor="white" text="black">\n<center>
            <h1>ve-server1.com is working!</h1>
            </center>\n
        </body>\n
</html>' > 
```sh
/var/www/ve-server1.com/public/index.html
root@karmic:/var/www#echo -e
```
```html
'<html>\n
    <head>\n
        <title>Welcome to nginx!</title>\n
    </head>\n
    <body bgcolor="white" text="black">\n<center>
        <h1>ve-server2.com is working!</h1>
        </center>\n
    </body>\n
</html>'
```
```sh
  > /var/www/ve-server2.com/public/index.html
```

* Now that we have our site structure created we need to tell nginx about them. To do that, we need to create a configuration file for each domain. The following is an example file for ve-server1.com. You will need to create the same file for ve-server2.com and make the respective changes in each file:

```sh
root@karmic:/var/www# nano /etc/nginx/sites-available/ve-server1.com
File: /etc/nginx/sites-available/ve-server1.com
```
```sh
server {

listen   80;
server_name  www.ve-server1.com;
rewrite ^/(.*) http://ve-server1.com/$1 permanent;
}

server {

listen   80;
server_name ve-server1.com;

access_log /var/www/ve-server1.com/logs/access.log;
error_log /var/www/ve-server1.com/logs/error.log;

location / {

root   /var/www/ve-server1.com/public/;
index  index.html;
}
}
```
In this file you are telling nginx where your website files are and where to log information that you can later use for statistics and error reporting, etc.


* Now that you have created these two files, we need to tell nginx that these sites are ready for primetime. If you look in the /etc/nginx directory you will notice there is a "sites-available" directory AND and a "sites-enabled" directory. nginx will only serve content from sites that have been "enabled". To do this you need to create symbolic links for the files we just created. Run the following command to do so:
```sh
root@karmic:/var/www# ln -s /etc/nginx/sites-available/ve-server1.com /etc/nginx/sites-enabled/ve-server1.com
root@karmic:/var/www# ln -s /etc/nginx/sites-available/ve-server2.com /etc/nginx/sites-enabled/ve-server2.com
```

* Now restart nginx and make sure we don't have any errors:

```sh
root@karmic:/etc/nginx/sites-available# /etc/init.d/nginx restart
Restarting nginx: nginx.
```

* Provided you had no errors you should now be able to navigate to your two new sites and see "success".

![image](https://mediatemple.zendesk.com/hc/article_attachments/202383020/Nginx_2.png)

![image2](https://mediatemple.zendesk.com/hc/article_attachments/202351714/Nginx_3.png)

* Also make sure your logs are working properly by taking a look at the access log for each domain:

```sh
root@karmic:~# cat /var/www/ve-server1.com/logs/access.log
Sample output:
127.0.0.1 - - [05/Feb/2010:13:47:46 -0800] "GET / HTTP/1.1" 200 15 "-" "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.6; en-US; rv:1.9.2) Gecko/20100115 Firefox/3.6"
127.0.0.1 - - [05/Feb/2010:13:47:46 -0800] "GET /favicon.ico HTTP/1.1" 404 148 "-" "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.6; en-US; rv:1.9.2) Gecko/20100115 Firefox/3.6"
127.0.0.1 - - [05/Feb/2010:13:47:49 -0800] "GET /favicon.ico HTTP/1.1" 404 148 "-" "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.6; en-US; rv:1.9.2) Gecko/20100115 Firefox/3.6"
127.0.0.1 - - [05/Feb/2010:14:06:33 -0800] "GET / HTTP/1.1" 200 155 "-" "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.6; en-US; rv:1.9.2) Gecko/20100115 Firefox/3.6"
127.0.0.1 - - [05/Feb/2010:14:17:39 -0800] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.6; en-US; rv:1.9.2) Gecko/20100115 Firefox/3.6"
127.0.0.1 - - [08/Feb/2010:08:46:23 -0800] "GET / HTTP/1.1" 200 155 "-" "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_2; en-us) AppleWebKit/531.21.8 (KHTML, like Gecko) Version/4.0.4 Safari/531.21.10"
127.0.0.1 - - [08/Feb/2010:08:46:23 -0800] "GET /favicon.ico HTTP/1.1" 404 148 "http://ve-server1.com/" "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10_6_2; en-us) AppleWebKit/531.21.8 (KHTML, like Gecko) Version/4.0.4 Safari/531.21.10"
```

You have now completed the steps necessary to host multiple websites on your (ve) Server. Obviously we have just scraped the surface; there is much more you can do with nginx.
<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[contributors-shield]: https://img.shields.io/github/contributors/othneildrew/Best-README-Template.svg?style=for-the-badge
[contributors-url]: https://github.com/othneildrew/Best-README-Template/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/othneildrew/Best-README-Template.svg?style=for-the-badge
[forks-url]: https://github.com/othneildrew/Best-README-Template/network/members
[stars-shield]: https://img.shields.io/github/stars/othneildrew/Best-README-Template.svg?style=for-the-badge
[stars-url]: https://github.com/othneildrew/Best-README-Template/stargazers
[issues-shield]: https://img.shields.io/github/issues/othneildrew/Best-README-Template.svg?style=for-the-badge
[issues-url]: https://github.com/othneildrew/Best-README-Template/issues
[license-shield]: https://img.shields.io/github/license/othneildrew/Best-README-Template.svg?style=for-the-badge
[license-url]: https://github.com/othneildrew/Best-README-Template/blob/master/LICENSE.txt
[linkedin-shield]: https://img.shields.io/badge/-LinkedIn-black.svg?style=for-the-badge&logo=linkedin&colorB=555
[linkedin-url]: https://linkedin.com/in/othneildrew
[product-screenshot]: images/screenshot.png
