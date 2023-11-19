# 远程Git pull笔记

将自己的服务器远程接入Git进行代码版本管理不要太方便！简单记录几处可能的坑。

```php
    // 最终代码呈现 PHP 案例
    $directory = '/var/xxx/nebula'; // 仓库目录

    $ssh_key = '/xxxxx/id_rsa'; // ssh 私钥路径
    putenv("GIT_SSH_COMMAND=ssh -i $ssh_key -o StrictHostKeyChecking=no");

    // 执行拉取master命令 复杂的情况自行设计
    $command = "git -C $directory pull 2>&1";
    exec($command, $output, $return_var);
    if ($return_var !== 0) {
        echo "Error occurred:<br>";
    }
    echo implode('<br>', $output);
```

第一个坑是运行git的远程脚本可能权限不够，本案例中PHP服务的用户组是 `www-data` ，从 `nginx.conf` 能看到。所以要给执行者添加足够的权限：
```sh
# 查看php用户
ps aux | grep php
```

```sh
# 给仓库目录添加权限
sudo chown -R www-data:www-data /var/xxx/nebula
chmod -R g+rw /var/xxx/nebula

# 给仓库目录添加到git安全目录列表中
git config --global --add safe.directory /var/xxx/nebula
```

第二个坑就是私钥的权限还不能太高也不能太低：
```sh
chmod 600 /xxxxx/id_rsa
```


