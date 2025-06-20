# 使用Acme.sh实现网站SSL证书的自动安装

## 1.安装

```sh
curl https://get.acme.sh | sh -s email=my@example.com
```

## 2.设置默认证书发行商

```sh
sudo ~/.acme.sh/acme.sh --set-default-ca --server letsencrypt
```

## 3.将 nginx.conf 路径配置好

一般ca路径生成都在 
```
ssl_certificate "/root/.acme.sh/你的域名_ecc/你的域名.cer";
ssl_certificate_key "//root/.acme.sh/你的域名_ecc/你的域名.key";
```

## 4.生成并自动重启Nginx

```sh
sudo ~/.acme.sh/acme.sh --issue -d 你的域名 --webroot 你的网站根目录路径 --force --reloadcmd "sudo systemctl reload nginx"
```

## 5.自动续期
通过检查续期任务确认任务存在：
```sh
sudo crontab -l | grep acme

# 输入应该类似：
0 0 * * * "/root/.acme.sh"/acme.sh --cron --home "/root/.acme.sh" > /dev/null
```

可以手动验证强制续期逻辑：
```sh
sudo ~/.acme.sh/acme.sh --renew -d 你的域名 --force
```

模拟续期测试：
```sh
# 手动触发续期检查（仅测试，不实际续期）
sudo ~/.acme.sh/acme.sh --cron --home ~/.acme.sh --test
```