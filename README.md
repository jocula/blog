# blog
# blog

git命令学习：
  // rm -rf .git
  git init
  git remote add origin git@github.com:jocula/blog.git
  git checkout -b main
  git add .
  git commit -m "Add the blog articles."
  // git remote -v
  git pull origin main
  git push -u origin main

  每次修改文件后：
  git add README.md
  // git log
  git commit -m "Add the git commands reference."
  git push -u origin main

github action自动部署：
ssh-keygen -t ed25519 -C "github-actions-hexo" -f hexo_deploy_key -N ""

会生成：

hexo_deploy_key：私钥
hexo_deploy_key.pub：公钥
然后配置：

打开目标仓库 jocula/jocula.github.io
进入 Settings → Deploy keys → Add deploy key
将 .pub 文件内容粘贴进去
勾选 Allow write access
保存
再在源码仓库 jocula/blog 中：

进入 Settings → Secrets and variables → Actions
新建 Secret：HEXO_DEPLOY_KEY
将私钥 hexo_deploy_key 的完整内容粘贴进去
内容包括：
-----BEGIN OPENSSH PRIVATE KEY-----
...
-----END OPENSSH PRIVATE KEY-----

不要将私钥提交到仓库，也不要把私钥发送到聊天中。公钥和私钥生成后可删除工作区中的这两个文件。
