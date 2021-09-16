# 常见的几种代理设置与取消

------------------------------------------

## git代理设置与取消

```shell
git config --global https.proxy http://127.0.0.1:1080
git config --global https.proxy https://127.0.0.1:1080

git config --global --unset http.proxy
git config --global --unset https.proxy
```



## npm代理设置与取消

```shell
npm config set proxy http://127.0.0.1:1080
npm config set https-proxy http://127.0.0.1:1080

npm config delete proxy
npm config delete https-proxy
```

