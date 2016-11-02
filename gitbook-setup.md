# Gitbook Setup

Environment: CentOS 6.5, NginX, NodeJs, PM2

These days I'm thinking of building my own Gitbook Server on my server. But it seems that the original Gitbook needs lots of configuration on your server to be running the Gitbook in the background. You cannot just follow some simple instructions on its official website, but you need lots of trying.

This time I want to use NginX to redirect the requests to some specific port I want to run. Also since I also use PM2 to run my personal website, I want to follow the original setup rules for all my other websites. So I first set up a configuration file in the NginX's directory `/etc/nginx/conf.d`.

```
$ cd /etc/nginx/conf.d/
```

Create a new file called `gitbook.conf`. You might want to specify \`sudo\` before vim. Otherwise you will not be able to save the file.

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

Be sure that you change the \`YOUR\_GITBOOK\_SERVER\_URL\` to the url you want to set to. Here since I have wild-card DNS setup of my url http:\/\/cubes.studio\/, then I can input any sub-domain of this cubes.studio domain. In my case I will put http:\/\/wiki.liby.cubes.studio\/. Note that if you have https for your domain, you can redirect the normal port 80 to 443. Also change \`YOUR\_PORT\` to any port you want. For example 4000. And this will be the port you are going to specify when setting up your Gitbook directory. You must make sure that no other application is using this port. 

The configuration file will start working as long as you restart the NginX service. Also, you might want to add \`sudo\` as well.

```
$ service nginx restart
```

Now, every request of your url will be redirect to the local port on your server now. But there's no application listening to it. So lets start building our Gitbook Server! Before everything started, we must first install gitbook client on your server! Add \`sudo\` if you see any error about permission. 

```
$ npm install -g gitbook-cli
```

Here, npm is the Node Package Manager that is required for NodeJs applications. If you don't have one, please go to [https:\/\/nodejs.org\/](https://nodejs.org/) and see the instructions to install NodeJs and NPM on your Linux server. 

And after you successfully installed globally gitbook client on your server, we need to set up the Gitbook Directory. Go to any directory you want to set the server, and initiate the Gitbook:

```
$ cd YOUR_DIRECTORY
$ gitbook init
```

Then you will see some README files, and book.json and other files we will be looking at afterwards. Meantime lets still focus how to run the gitbook server. Now, if you run the command below, you can visit your Gitbook site on any browser if you enter the url you have written in the NginX configuration file. 

```
$ gitbook --port YOUR_PORT serve
```

But you might notice that this cannot be run in the background. It will always be terminated as you logout your Linux account. So right now we need another program to run this in the background. And the program we will be using is PM2, a process manager for NodeJs. You can see more detailed information in its website: [http:\/\/pm2.keymetrics.io](http://pm2.keymetrics.io). I have used it for my own websites and it works really well. So lets install PM2! Rememeber to add sudo if you see permission denied errors as well. 

```
$ npm install pm2 -g
```

This will allow you to run any node programs in the background by using this command:

```
$ pm2 start app.js
```

But here we don't have `app.js` . What we have is the gitbook commands. But how do we specify options in pm2? Here's the solution:

```
$ pm2 start gitbook -- -p YOUR_PORT serve
```

PM2 will consider all the arguments after `--` as the command line arguments for the program that will be running in PM2. So here we specified the port we mentioned before, and also serve, telling PM2 to serve the Gitbook service. Now you will see a form containing a `gitbook` process. It will also show the status, restart time, memory usage and other information. Normally there will be no restarts if there's no error occurs. If everything goes fine, you can logout from your server now and browse your Gitbook at any time. 

