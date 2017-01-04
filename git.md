#git中操作中文问题
```
git config --global core.quotepath false
```
core.quotepath设为false的话，就不会对0x80以上的字符进行quote，中文显示正常

# git命令简写

配置gitconfig

```
#vi ~/.gitconfig
[alias]
st = status
ci = commit
br = branch
co = checkout
ad = add
```

