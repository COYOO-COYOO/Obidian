`Github创建仓库`
![1.png](Obsidian管理/Obsidian%20Git配置/1.png)

 …or create a new repository on the command line
```
echo "# Obidian" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/COYOO-COYOO/Obidian.git
git push -u origin main
```

…or push an existing repository from the command line
```
git remote add origin https://github.com/COYOO-COYOO/Obidian.git
git branch -M main
git push -u origin main
```

```
Jamie@DESKTOP-S0RTM5I MINGW64 /e/Obsidian/COYOO
$ git init
Initialized empty Git repository in E:/Obsidian/COYOO/.git/

Jamie@DESKTOP-S0RTM5I MINGW64 /e/Obsidian/COYOO (master)
$ git config --global user.name COYOO-COYOO

Jamie@DESKTOP-S0RTM5I MINGW64 /e/Obsidian/COYOO (master)
$ git config --global user.email 1731540509@qq.com
```

按时发散
