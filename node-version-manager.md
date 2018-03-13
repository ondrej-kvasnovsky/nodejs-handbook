# Node Version Manager

Install `nvm`. 

```
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash
```

Find out what version of node we are using. 

```
$ nvm current
v8.9.4
```

List all available node versions.

```
$ nvm ls
         v8.3.0
         v8.4.0
->       v8.9.4
         system
```

Update to latest version of a major version. 

```
$ nvm install 8
Downloading and installing node v8.10.0...
Downloading https://nodejs.org/dist/v8.10.0/node-v8.10.0-darwin-x64.tar.xz...
######################################################################## 100.0%
Computing checksum with shasum -a 256
Checksums matched!
Now using node v8.10.0 (npm v5.6.0)
```



