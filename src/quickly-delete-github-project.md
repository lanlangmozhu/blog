# 使用指令快速批量删除 github 的项目

## 前情提要
    最近辞职在找工作，要好好经营一下我的github，打开我的 github 一看，头大了一截。他喵的我的 github 库有 70 多个，直接 fork 的有 50 多个。你说 fork 后又不做二次开发，留在这不瞎扯吗？必须删了，最多给他留个 star。直接删又麻烦，要点这点哪，有啥其他办法呢？（大帅摸头中～～）


## github 删除项目

> 1. 选中项目进入项目详情

![](https://raw.githubusercontent.com/lanlangmozhu/picb/main/img/20250514211034740.png)
 >    2. 选择设置 滑到最底

![](https://raw.githubusercontent.com/lanlangmozhu/picb/main/img/20250514211135515.png)
![](https://raw.githubusercontent.com/lanlangmozhu/picb/main/img/20250514211225904.png)
>    3. 选择删除这个库

![](https://raw.githubusercontent.com/lanlangmozhu/picb/main/img/20250514211248358.png)
>    4. 弹弹窗确认真的删除这个库
![](https://raw.githubusercontent.com/lanlangmozhu/picb/main/img/20250514211308043.png)
>     5. 阅读删除条例并确认删除
![](https://raw.githubusercontent.com/lanlangmozhu/picb/main/img/20250514211323740.png)
 >    6. 在输入框中填入用户名/库名 并确认
![](https://raw.githubusercontent.com/lanlangmozhu/picb/main/img/20250514211358251.png)

>    总结： 这个删除流程真的跟老太婆的抹脚布一样---又臭又长

## 这时候杀手锏可以出手了---> 命令行脚本
下面是使用 Github API 批量删除仓库示例方法（以命令行脚本为例）

### 1. 获取Github Token
首先，你需要一个有删除仓库权限的 [Personal Access Token](https://github.com/settings/tokens)。建议勾选 `delete_repo` 权限。

### 2. 使用 curl 命令删除单个仓库

```sh
curl -X DELETE -H "Authorization: token YOUR_TOKEN" \
  https://api.github.com/repos/你的用户名/仓库名
```
*注意 命令行直接执行需要在前面加上 `bash -c`
例如 
```sh
bash -c 'curl -X DELETE -u 你的用户名:YOUR_TOKEN "https://api.github.com/repos/你的用户名/仓库名"'
```
### 3. 批量删除脚本（以 bash 为例）
```sh
#!/bin/bash

GITHUB_USER="你的用户名"
TOKEN="你的token"


# 需要删除的仓库名（用空格分隔）
repos="repo1 repo2 repo3"

for repo in $repos; do
  echo "Deleting $repo ..."
  curl -X DELETE -u $USER:$TOKEN "https://api.github.com/repos/$USER/$repo"
done
```
使用方法：
将上面脚本保存为 `delete_repos.sh`。
给脚本执行权限：`chmod +x delete_repos.sh`
执行脚本：`./delete_repos.sh`

### 4. 其他脚本命令
```sh
# 获取所有 fork 仓库名
repos=$(curl -s -u $USER:$TOKEN "https://api.github.com/users/$USER/repos?per_page=100" | jq -r '.[] | select(.fork==true) | .name')
```
```sh
# 获取所有 私有 仓库名 
curl -s -u $USER:$TOKEN "https://api.github.com/user/repos?per_page=100&visibility=all" | jq -r ".[] | select(.private==true) | .name"
```

### 5. 注意事项

- 请再三确认是否删除该库
- 删除操作不可恢复，请谨慎操作。
- token 建议只赋予最小权限，操作完毕后及时删除。
- 如果是组织仓库，API 路径中的用户名要换成组织名。


### 6. 结尾
好了大功告成，开始继续 github 之旅