配置

```shell
git config --global user.name '3rdtsuki'
git config --global user.email 'address@gmail.com'
ssh-keygen -C 'address@gmail.com' -t rsa
```

复制id_rsa.pub到github



commit

```sh
git init

git remote add origin https://github.com/3rdtsuki/Linux.git
git add .
git commit -m 20230226
git push -u origin master
```

