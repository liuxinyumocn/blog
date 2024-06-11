# Node.js安装(Ubuntu)

官方的一种方式：

```sh
apt install curl
# installs nvm (Node Version Manager)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.nvm/nvm.sh
# download and install Node.js (you may need to restart the terminal)
nvm install 20
# verifies the right Node.js version is in the environment
node -v # should print `v20.14.0`
# verifies the right NPM version is in the environment
npm -v # should print `10.7.0`
```

但是这种方式安装的Node.js与npm没有放到 /usr/bin 下，你可以创建软连接，也可以用apt-get方式安装：

```sh
apt install sudo
sudo apt update
sudo apt install curl

curl -sL https://deb.nodesource.com/setup_16.x | sudo -E bash -

sudo apt update
sudo apt install nodejs

node -v
npm -v
```