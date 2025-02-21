---
title: Git常用命令速查
date: 2024-02-21
categories:
  - 工具
  - 版本控制
tags:
  - Git
  - 版本控制
  - 开发工具
---

## Git 常用命令速查表

本文整理了 Git 最常用的命令和使用场景，按照日常工作流程分类整理。

### 1. 基础配置
#### 1.1 用户信息配置
```bash
# 配置用户名和邮箱
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# 查看配置信息
git config --list
```

#### 1.2 SSH 配置
```bash
# 生成 SSH 密钥
ssh-keygen -t rsa -b 4096 -C "your.email@example.com"

# 查看公钥内容
cat ~/.ssh/id_rsa.pub
```

### 2. 仓库操作
#### 2.1 仓库初始化
```bash
# 初始化新仓库
git init

# 克隆远程仓库
git clone repository_url
git clone repository_url custom_folder_name  # 指定本地文件夹名
```

#### 2.2 远程仓库管理
```bash
# 查看远程仓库
git remote -v

# 添加远程仓库
git remote add origin repository_url

# 修改远程仓库地址
git remote set-url origin new_repository_url
```

### 3. 文件操作
#### 3.1 文件暂存
```bash
# 添加指定文件到暂存区
git add file_name
git add folder_name  # 添加文件夹
git add .  # 添加所有更改

# 查看文件状态
git status
git status -s  # 简短格式
```

#### 3.2 文件提交
```bash
# 提交更改
git commit -m "提交说明"
git commit -am "提交说明"  # 合并 add 和 commit

# 修改最后一次提交
git commit --amend
```

#### 3.3 文件撤销
```bash
# 撤销工作区修改
git checkout -- file_name

# 撤销暂存区修改
git reset HEAD file_name

# 撤销提交
git revert commit_id
```

### 4. 分支管理
#### 4.1 分支操作
```bash
# 查看分支
git branch  # 本地分支
git branch -r  # 远程分支
git branch -a  # 所有分支

# 创建分支
git branch branch_name
git checkout -b branch_name  # 创建并切换

# 切换分支
git checkout branch_name
```

#### 4.2 分支合并
```bash
# 合并分支
git merge branch_name
git merge --no-ff branch_name  # 保留分支记录

# 解决冲突
git status  # 查看冲突文件
git add .  # 标记冲突解决
git commit -m "解决冲突"
```

### 5. 历史记录
#### 5.1 查看历史
```bash
# 查看提交历史
git log
git log --oneline  # 简短格式
git log --graph  # 图形化显示

# 查看文件修改
git diff
git diff file_name
```

#### 5.2 版本回退
```bash
# 回退到指定版本
git reset --hard commit_id
git reset --hard HEAD^  # 回退到上一版本

# 查看操作历史
git reflog
```

### 6. 标签管理
```bash
# 创建标签
git tag tag_name
git tag -a v1.0 -m "版本说明"

# 查看标签
git tag
git show tag_name

# 删除标签
git tag -d tag_name
```

### 7. 实用技巧
#### 7.1 临时保存
```bash
# 保存工作现场
git stash
git stash save "备注信息"

# 恢复工作现场
git stash pop  # 恢复并删除
git stash apply  # 仅恢复
```

#### 7.2 忽略文件
```bash
# 创建 .gitignore 文件
touch .gitignore

# 常见忽略规则
*.log
node_modules/
.DS_Store
```

更多 Git 使用技巧和进阶命令将在后续文章中详细介绍。欢迎持续关注！
