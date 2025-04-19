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
