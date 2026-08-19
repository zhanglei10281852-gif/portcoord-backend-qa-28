# Bug Reproduction

## 包的性质

当前 test_model_fix 保存的是被测模型修复后的结果源码，不是初始含 Bug 源码。要复现原始缺陷，必须检出下面固定的 parent SHA；不要在当前修复结果源码上期待重新出现修复前失败。生成系统使用的可信验证补丁和完整验证日志仅在本地留存，不提交到结果分支。

## 问题现象

分页查询翻到第二页时仍返回第一页数据，并且过滤后的总数与列表不一致。请修复分页查询的跨查询一致性。

## 含 Bug 版本

- 仓库：zhanglei10281852-gif/portcoord-backend-qa-28
- 仓库地址：https://github.com/zhanglei10281852-gif/portcoord-backend-qa-28.git
- parent SHA：99b8b21f6b54a66a6eff2cce2fa96ddd7b372f60

## 复现步骤

```bash
git clone -- https://github.com/zhanglei10281852-gif/portcoord-backend-qa-28.git bug-repro
cd bug-repro
git checkout --detach 99b8b21f6b54a66a6eff2cce2fa96ddd7b372f60
go test ./internal/store -run "^TestStore_ListWithPagination$" -count=1
```

## 双架构完整错误信息

### linux/amd64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/store -run "^TestStore_ListWithPagination$" -count=1
--- FAIL: TestStore_ListWithPagination (0.00s)
    store_test.go:174: page 3 items: expected 5, got 10
FAIL
FAIL	portcoord/internal/store	0.007s
FAIL

```

stderr：

```text
warning: internal/store/concurrent_test.go has type 100755, expected 100644
warning: internal/store/notfound_test.go has type 100755, expected 100644
warning: internal/store/store_test.go has type 100755, expected 100644
warning: internal/store/concurrent_test.go has type 100755, expected 100644
warning: internal/store/notfound_test.go has type 100755, expected 100644
warning: internal/store/store_test.go has type 100755, expected 100644

```

### linux/arm64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/store -run "^TestStore_ListWithPagination$" -count=1
--- FAIL: TestStore_ListWithPagination (0.25s)
    store_test.go:174: page 3 items: expected 5, got 10
FAIL
FAIL	portcoord/internal/store	0.454s
FAIL

```

stderr：

```text
warning: internal/store/concurrent_test.go has type 100755, expected 100644
warning: internal/store/notfound_test.go has type 100755, expected 100644
warning: internal/store/store_test.go has type 100755, expected 100644
warning: internal/store/concurrent_test.go has type 100755, expected 100644
warning: internal/store/notfound_test.go has type 100755, expected 100644
warning: internal/store/store_test.go has type 100755, expected 100644

```

## 通过条件

在题面触发条件下，公开行为必须恢复且原始异常不再出现；定向命令 go test ./internal/store -run ^TestStore_ListWithPagination$ -count=1、相关包测试、全量测试、race、vet 和 build 必须通过；不得删除或跳过测试，也不得绕过目标逻辑。
