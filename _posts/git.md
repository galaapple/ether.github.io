#git 小知识
git diff *文件名* 将工作区和暂存区文件比较

git diff HEAD *文件名* 将工作区与最新版本比较
git diff commit-id *文件名* 将工作区与指定commit-id相比较

git diff --cached *文件名* 将暂存区与版本库文件比较
git diff --cached commit-id *文件名* 将暂存区与指定commit-id相比较


--name-status
Show only names and status of changed files.
See the description of the --diff-filter option on what the
status letters mean.

--diff-filter
Select only files that are Added ***(A)***, Copied (C), Deleted (D), Modified ***(M)***, Renamed (R), have their type (i.e. regular file, symlink, submodule, …​) changed (T), are Unmerged (U), are Unknown (X), or have had their pairing Broken (B).
