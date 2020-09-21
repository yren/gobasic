## brew update 慢

homebrew 默认源是 github, 在国内很慢。 
brew update -v 查看详情。
可以修改 aliyun 的镜像提高速度。

* 替换 brew.git

```
cd "$(brew --repo)"
git remote set-url origin https://mirrors.aliyun.com/homebrew/brew.git
```

* 替换 homebrew-core.git

```
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.aliyun.com/homebrew/homebrew-core.git
```

* export HOMEBREW_BOTTLE_DOMAIN

```
// 替换为你的 shell
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.aliyun.com/homebrew/homebrew-bottles' >> ~/.bash_profile
source ~/.bash_profile
```

* 替换 homebrew-cask.git: cask 表示 GUI 应用的源, 阿里云没有提供 cask 源, 使用 USTC 源

```
cd "$(brew --repo)"/Library/Taps/homebrew/homebrew-cask
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-cask.git
```

brew update -v 现在应该顺滑了

