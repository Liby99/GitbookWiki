# Gitbook Setup

Environment: CentOS 6.5, NginX, PM2

These days I'm thinking of building my own Gitbook Server on my server. But it seems that the original Gitbook needs lots of configuration on your server to be running the Gitbook in the background. You cannot just follow some simple instructions on its official website, but you need lots of trying. 

This time I want to use NginX to redirect the requests to some specific port I want to run. Also since I also use PM2 to run my personal website, I want to follow the original setup rules for all my other websites. So I first set up a configuration file in the NginX's directory \`\/etc\/nginx\/conf.d\`.

```
$ cd /etc/nginx/conf.d/
```

 Create a new file called \`gitbook.conf\`. You might want to specify \`sudo\` before vim. Otherwise you will not be able to save the file. 

```
$ vim gitbook.conf
```

And paste these into the file:

```
server {
    listen 80;
    listen [::]:80;
    servername YOUR_GITBOOK_SERVER_URL;
    location / {
        proxy_pass http://127.0.0.1:YOUR_PORT;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade
    }
}
```

Be sure that you change the \`YOUR\_GITBOOK\_SERVER\_URL\` to the url you want to set to. Here since I have wild-card DNS setup of my url http:\/\/cubes.studio\/, then I can input any sub-domain of this cubes.studio domain. In my case I will put http:\/\/wiki.liby.cubes.studio\/. Note that if you have https for your domain, you can redirect the normal port 80 to 443. Also change \`YOUR\_PORT\` to any port you want. For example 4000. And this will be the port you are going to specify when setting up your Gitbook directory.

The configuration file will start working as long as you restart the NginX service. Also, you might want to add \`sudo\` as well.

```
$ service nginx restart
```



