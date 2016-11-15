#Node下载与配置
- Linux环境下载
```
wget https://nodejs.org/dist/v4.6.2/node-v4.6.2-linux-x64.tar.xz
```
- 路径配置(不建议)
```
ln -s /data/node/node-v4.4.4-linux-x64/bin/node /usr/local/bin/node
ln -s /data/node/node-v4.4.4-linux-x64/bin/npm /usr/local/bin/npm
```

- 设置环境变量

`vi /etc/profile`
```
export NODE_HOME=/data/node/node-v4.4.4-linux-x64
export PATH=$NODE_HOME/bin:$PATH
export NODE_PATH=$NODE_HOME/lib/node-modules
```
`source /etc/profile`

#cnpm
`http://npm.taobao.org`

- 安装
```
npm config set strict-ssl false
npm install -g cnpm --registry=https://registry.npm.taobao.org
```
- 设置代理
```
npm config set proxy= http://代理服务器ip:代理服务器端口
```
- 清除代理
```
npm config delete http-proxy
npm config delete https-proxy
```
