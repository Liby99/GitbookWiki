# Gitbook Math

The Gitbook actually don't have math support in its basic version. You will have to install a plugin for it in order to show Math content in your Gitbook pages. So here is what we going to do about this. First, go to the directory where you store all your file in:

```
$ cd DIRECTORY
```

And open `book.json` with vim or any other text editor you want

```
$ vim book.json
```

Append these code to the end of the json object: 

```
{
    "plugins": [
        "mathjax"
    ]
}
```

So if you already have `plugins` array in the json, you only need to append `mathjax` to the `plugins` array. This will tell the Gitbook to include the `mathjax` plugin when building websites. So save and exit your file. But we haven't done yet, since Gitbook have not downloaded the plugin yet. So you need to

```
$ gitbook install
```

to install the plugins. It will basically read the `plugins` field in `book.json`, and try to download the plugin from the npm community and install it in your server environment. Actually I have encountered a problem here. The system says that I can't include contexify. And now I know that I have to download this manually. \(Remember to include `sudo` here if you ever encountered an error\)

```
$ npm install contexify -g
```

Also, I have to reinstall gcc \(I have totally no idea why\) to successfully compile the contexify things. If you ever encountered this error, go to Stack Overflow and see how people there reinstall latest version of gcc. Then, rebuild the Gitbook using

```
$ gitbook build
```

then all your files are good to go with gorgeous math LaTex syntax & style supported. 

