[Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) 与 Go 1.8 和更高版本兼容。 对于使用 [Azure Stack 配置文件](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-version-profiles)的环境，最低需要 Go 版本 1.9。
如果系统上没有可用的 Go，请遵循 [Go 安装说明](https://golang.org/doc/install)。

可以通过 `go get` 获取 Azure SDK for Go 及其依赖项。

```bash
go get -u -d github.com/Azure/azure-sdk-for-go/...
```

> [!WARNING]
> 请务必在 URL 中将 `Azure` 大写。 否则，在使用该 SDK 时，可能导致大小写相关的导入问题。 此外，还需要在导入语句中将 `Azure` 大写。

