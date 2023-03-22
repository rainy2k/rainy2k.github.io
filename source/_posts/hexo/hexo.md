---
title: Hello World
tags:
categories:
- develop
- hexo
---

## Create a new hexo blog
```bash
hexo init xxx
cd xxx
npm install hexo-cli -g
npm install
npm install hexo-deployer-git
```

## Create a new post

``` bash
$ hexo new "My New Post"
```

## quick start

``` bash
$ hexo clean && hexo g && hexo d && hexo s
```

## 图片保存到相对路径

### 安装hexo-asset-image
执行`npm install https://github.com/CodeFalling/hexo-asset-image --save`

图片保存在./{title}/中
`config.yml`中设置`post_asset_folder: true`

## 修改段间距

[link](https://github.com/theme-next/hexo-theme-next/issues/1703)

## github actions自动部署

### 生成部署密钥
```bash
ssh-keygen -f github-deploy-key -C "your email"
```
### gitignore
在gitignore上把生成的密钥ignore下

`github-deploy-key`放到`setting -> secrets -> New repository secret`，命名为`HEXO_DEPLOY_PRI`

`github-deploy-key.pub`放到`Settings -> Deploy keys -> Add deploy key`，命名为`HEXO_DEPLOY_PUB`

`.github/workflows/pages.yml`:

```yaml
# Action 的名字
name: Hexo Auto Deploy

on:
  # 触发条件1：main 分支收到 push 后执行任务。
  push:
    branches:
      - hexo

# 这里放环境变量,需要替换成你自己的
env:
  # Hexo 编译后使用此 git 用户部署到 github 仓库
  GIT_USER: xxx
  # Hexo 编译后使用此 git 邮箱部署到 github 仓库
  GIT_EMAIL: xxx
  # Hexo 编译后要部署的 github 仓库
  GIT_DEPLOY_REPO: xxx/xxx.github.io
  # Hexo 编译后要部署到的分支
  GIT_DEPLOY_BRANCH: main
  # 注意替换为你的 GitHub 源仓库地址
  GIT_SOURCE_REPO: git@github.com:xxx/xxx.github.io.git

jobs:
  build:
    name: Build on node ${{ matrix.node_version }} and ${{ matrix.os }}

    runs-on: ubuntu-latest
    if: github.event.repository.owner.id == github.event.sender.id
    strategy:
      matrix:
        os: [ubuntu-18.04]
        node_version: [18.15.0]

    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Checkout deploy repo
        uses: actions/checkout@v3
        with:
          repository: ${{ env.GIT_DEPLOY_REPO }}
          ref: ${{ env.GIT_DEPLOY_BRANCH }}
          path: .deploy_git

      - name: Use Node.js '18.15.0'
        uses: actions/setup-node@v3
        with:
          node-version: '18.15.0'

      - name: Cache NPM dependencies
        uses: actions/cache@v2
        with:
          # npm cache files are stored in `~/.npm` on Linux/macOS
          path: ~/.npm
          # path: node_modules
          key: ${{ runner.OS }}-npm-cache
          restore-keys: |
            ${{ runner.OS }}-npm-cache

      # - name: Configuration environment
      #   env:
      #     HEXO_DEPLOY_PRI: ${{secrets.HEXO_DEPLOY_PRI}}
      #   run: |
      #     sudo timedatectl set-timezone "Asia/Shanghai"
      #     mkdir -p ~/.ssh/
      #     echo "$HEXO_DEPLOY_PRI" > ~/.ssh/id_rsa
      #     chmod 600 ~/.ssh/id_rsa
      #     ssh-keyscan -t rsa github.com >> ~/.ssh/known_hosts
      #     git config --global user.name $GIT_USER
      #     git config --global user.email $GIT_EMAIL

      # - name: Install Pandoc
      #   run: |
      #     curl -s -L https://github.com/jgm/pandoc/releases/download/2.9.2/pandoc-2.9.2-linux-amd64.tar.gz | tar xvzf - -C $RUNNER_TOOL_CACHE/

      - name: Install dependencies
        run: |
          sudo apt-get install pandoc
          npm install hexo-cli -g
          npm install
          npm uninstall hexo-renderer-marked
          npm install hexo-deployer-git https://github.com/CodeFalling/hexo-asset-image --save
          npm install hexo-generator-archive hexo-generator-category hexo-generator-feed hexo-generator-index hexo-generator-tag --save
          npm install hexo-renderer-ejs hexo-renderer-pandoc hexo-renderer-stylus hexo-server --save 
          node -v
  

      - name: clean & build
        run: |
          npm run clean && npm run build

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          deploy_key: ${{ secrets.HEXO_DEPLOY_PRI  }}
          user_name: xxx
          user_email: xxx
          # 获取提交文章源码时的commit message，作为发布gh-pages分支的信息
          commit_message: ${{ github.event.head_commit.message }}
          full_commit_message: ${{ github.event.head_commit.message }}
          github_token: ${{ secrets.GITHUB_TOKEN }}
          # GITHUB_TOKEN不是个人访问令牌，GitHub Actions 运行器会自动创建一个GITHUB_TOKEN密钥以在您的工作流程中进行身份验证。因此，您无需任何配置即可立即开始部署
          publish_dir: ./public
          allow_empty_commit: true # 允许空提交

      # Use the output from the `deploy` step(use for test action)
      - name: Get the output
        run: |
          echo "${{ steps.deploy.outputs.notify }}"
```
### 相关问题

[Error: Action failed with "The process '/usr/bin/git' failed with exit code 128"](https://github.com/peaceiris/actions-gh-pages/issues/497)

[修复GitHub Action 存储上限错误 工作流命令和版本的弃用警告](https://baijiahao.baidu.com/s?id=1754074661687942534&wfr=spider&for=pc)

[Mathjax : pandoc exited with code null (pandoc-2.9.2.1 already installed in my laptop)](https://github.com/theme-next/hexo-theme-next/issues/1454)

### link
[Github Actions部署](https://blog.csdn.net/wbsu2004/article/details/122661116)

[Github Actions部署](https://cloud.tencent.com/developer/article/1968497)

[Using pandoc with GitHub Actions](https://github.com/pandoc/pandoc-action-example#simple-usage)

[Hexo显示Latex公式最新解决方案](https://blog.csdn.net/qq_52466006/article/details/126924064)

['pandoc exited with code null' 解决方案](https://finisky.github.io/pandoc-exited-with-code-null-solution/)

[Hexo生成pandoc报错解决方法](https://umr.ink/2020/09/19/Hexo%E7%94%9F%E6%88%90pandoc%E6%8A%A5%E9%94%99%E8%A7%A3%E5%86%B3%E6%96%B9%E6%B3%95/)

[Hexo之问题集合](https://pxxyyz.com/posts/hexo-problem/)

## Link
More info: 

[官网](https://hexo.io/)

[备份](https://www.jianshu.com/p/baab04284923)

http://home.ustc.edu.cn/~sdyzzy/posts/6670fe39.html

https://www.cnblogs.com/eidolonw/p/13066869.html

