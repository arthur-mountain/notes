Question:

git fetch 遇到 would clobber existing tag 報錯

Reason:

原因可能是刪了原有的一個 tag，然後又重新建立了一個同名的 tag。

Solution:

強制刷新 local tags

```shell
git fetch --tags -f
```
