# fine-grained PAT 的权限假象

> 摘录自 GitHub 官方文档《REST API permissions for fine-grained PATs》+ 2026-10 实战核验

## 现象

用 fine-grained PAT（只勾了 Contents: Read and write）调 GitHub REST API：

- `GET /repos/{owner}/{repo}` 返回的 `permissions.push = true`
- 但真正去推文件（`POST /repos/.../git/blobs`）却得到 **403 Resource not accessible by personal access token**

## 结论

**`GET /repos` 返回的 `permissions` 是「你在这个仓库的角色」，不是「token 的实际授权」。**
仓库管理员角色的 token 如果没勾对应 permission，照样 403。

## 判别法

- 角色字段（`permissions.push`）≠ token 权限，**别拿它做预检**
- 直接发一次最小写请求（比如建一个 blob）探测真实权限
- 需要改 topics、PATCH 仓库、管 hooks 时，fine-grained PAT 要补 **Administration: Read and write**——Contents 权限覆盖不到这些端点

## 短评

官方文档把这个写得很散（分散在 REST 文档每一页的脚注里），属于「读完文档仍然会踩」的坑。
踩过一次之后，预检一律用最小写探针，再也没被假象骗过。
