# GitHub 新文件上传操作指南

本文适用于当前仓库：

- 本地目录：`D:\Github\-`
- 远端仓库：`https://github.com/yzyyohh/-.git`
- 默认分支：`main`

## 一、需要记住的基本流程

将新文件上传到 GitHub，通常需要依次完成四步：

1. 将文件放入本地仓库目录。
2. 使用 `git add` 将文件加入暂存区。
3. 使用 `git commit` 创建本地提交。
4. 使用 `git push` 将本地提交推送到 GitHub。

`git add` 和 `git commit` 只处理本地仓库；只有执行 `git push` 后，文件才会出现在 GitHub 网站上。

## 二、日常上传操作

打开 PowerShell，然后进入仓库：

```powershell
cd 'D:\Github\-'
```

### 1. 查看有哪些新文件或修改

```powershell
git status
```

常见状态含义：

- `Untracked files`：Git 尚未跟踪的新文件。
- `Changes not staged for commit`：文件已修改，但尚未暂存。
- `Changes to be committed`：文件已经暂存，可以提交。
- `nothing to commit, working tree clean`：当前没有需要提交的变化。

### 2. 将指定文件加入暂存区

建议明确写出文件名：

```powershell
git add -- '文件名.md'
```

同时添加多个文件：

```powershell
git add -- '文件一.md' '文件二.md'
```

如果确认要暂存当前仓库中的所有新增、修改和删除，可以使用：

```powershell
git add -A
```

使用 `git add -A` 前应先运行 `git status`，避免把临时文件、密钥或无关内容一起提交。

### 3. 检查即将提交的内容

```powershell
git status
git diff --cached --stat
git diff --cached --check
```

其中：

- `git diff --cached --stat` 显示即将提交的文件和修改量。
- `git diff --cached --check` 检查常见的空白字符问题。

如果误暂存了某个文件，可取消暂存，但保留文件内容：

```powershell
git restore --staged -- '文件名.md'
```

### 4. 创建本地提交

```powershell
git commit -m "docs: add new documentation"
```

提交消息应简短说明本次变化，例如：

```powershell
git commit -m "docs: add GitHub upload guide"
git commit -m "docs: update research prompt"
git commit -m "fix: correct configuration example"
```

### 5. 推送到 GitHub

第一次推送当前 `main` 分支时：

```powershell
git push -u origin main
```

成功设置上游分支后，以后通常只需：

```powershell
git push
```

如果 GitHub 要求登录，请按终端或浏览器提示完成账户授权。不要把 GitHub 密码、访问令牌或私钥写入仓库文件。

### 6. 验证上传结果

```powershell
git status
git log -1 --oneline
```

正常情况下，`git status` 应显示工作区干净，并提示本地分支与 `origin/main` 同步。随后可打开远端仓库页面确认文件已经出现。

## 三、完整操作示例

假设新增了 `学习笔记.md`：

```powershell
cd 'D:\Github\-'
git status
git add -- '学习笔记.md'
git diff --cached --stat
git diff --cached --check
git commit -m "docs: add study notes"
git push
git status
```

## 四、在 Codex 中操作时的权限说明

Codex 的受管环境可能允许修改普通项目文件，但限制直接写入 `.git`。这是安全隔离策略，因为 `.git` 中保存着提交历史、分支、钩子、远端配置等仓库元数据。

因此，让 Codex 执行以下命令时，界面可能要求你额外批准：

```text
git add
git commit
git pull
git push
git branch
```

遇到批准提示时，请先确认命令、文件范围和远端仓库正确，再允许执行。若不希望授权，也可以复制本文中的命令，在你自己的 PowerShell 中执行。

出现下面的错误时：

```text
Unable to create '.git/index.lock': Permission denied
```

如果错误只发生在 Codex 中，通常是沙箱权限限制，不是 Windows 文件夹 ACL 损坏。应批准对应的 Git 操作或改在本机终端运行，不要直接修改 `.git` 的 ACL。

## 五、常见问题

### 1. `nothing to commit`

说明没有已暂存的变化。先运行：

```powershell
git status
```

如果文件是新建或已修改的，再执行 `git add`。

### 2. 文件没有出现在 GitHub 上

可能只完成了 `git commit`，尚未执行 `git push`。检查：

```powershell
git status
git log -1 --oneline
git push
```

### 3. 文件被 `.gitignore` 忽略

检查忽略来源：

```powershell
git check-ignore -v -- '文件名'
```

确认该文件确实应该进入仓库后，再调整对应的忽略规则。不要强行提交密码、令牌、`.env` 或其他敏感文件。

### 4. 推送提示远端包含本地没有的提交

先同步远端提交：

```powershell
git pull --rebase origin main
git push
```

如果发生冲突，应先解决冲突、检查文件内容，然后继续 rebase 并推送。不要在不理解后果的情况下使用强制推送。

### 5. 推送时认证失败

确认以下事项：

- 当前登录的 GitHub 账户有该仓库的写权限。
- `git remote -v` 显示的是正确仓库。
- 浏览器或 Git 凭据管理器中的授权仍然有效。

查看远端地址：

```powershell
git remote -v
```

### 6. 出现全局 ignore 权限警告

当前 Codex 沙箱可能显示：

```text
unable to access 'C:\Users\Lalawu/.config/git/ignore': Permission denied
```

只要 Git 命令最终退出成功，这条警告通常不会阻止提交或推送。若在你自己的 PowerShell 中也出现，再检查该路径的读取权限和全局 Git 配置。

## 六、提交前安全检查

每次上传前建议确认：

- 文件中没有密码、访问令牌、私钥或个人敏感信息。
- `git status` 中没有无关文件。
- `git diff --cached` 中的内容符合预期。
- 提交消息能说明本次修改。
- 大型二进制文件是否应该使用 Git LFS，而不是直接提交。

最稳妥的日常命令顺序是：

```powershell
git status
git add -- '需要上传的文件'
git diff --cached
git commit -m "说明本次修改"
git push
git status
```
