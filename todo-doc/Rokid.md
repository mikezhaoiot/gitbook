
[Rokid文档](https://developer.rokid.com/docs/)

##### 下载RokidOS源码

./repo init -u ssh://mikezhao@openai.rokid.com:29418/amlogic_a113_audio/manifest -m rokidbase.xml --repo-url=ssh://mikezhao@openai.rokid.com:29418/tools/repo --no-repo-verify

##### 下载YodaOS源码
- https://openai-corp.rokid.com
- 登录https://openai-corp.rokid.com/#/dashboard/self，添加电脑的公钥
- 下载若琪官方提供的repo ,可参考[RokidOS下载源代码](https://developer.rokid.com/docs/rokidos-linux-docs/source/downloading_building/downloading_codes.html)

```
curl https://scm-deps-library.rokid-inc.com/linux/buildroot_dl_aml/tools/repo > ~/bin/repo
chmod a+x ~/bin/repo
```

- 参考[YodaOS Universal 如何配置系统和应用](https://yodaos-project.github.io/yoda-book/yodaos-source/system/compile-run.html),下载源代码

```
repo init -u ssh://{yourname}@openai-corp.rokid.com:29418/kamino_universal_cust/open-platform/manifest -m yodaos-7.27.0-alpha-20181030.xml -repo-url=ssh://{yourname}@openai-corp.rokid.com:29418/tools/repo --no-repo-verify

针对自己的
repo init -u ssh://mikezhao@openai-corp.rokid.com:29418/kamino_universal_cust/open-platform/manifest -m yodaos-7.27.0-alpha-20181030.xml --repo-url=ssh://mikezhao@openai-corp.rokid.com:29418/tools/repo --no-repo-verify
```


##### 下载S905D 源码

- [Amlogic/S905D 参考文档](https://developer.rokid.com/docs/rokidos-linux-docs/reference/dev_board/amlogic/usermanual_s905d.html)
- 下载若琪官方提供的repo
```git
./repo init -u ssh://your-account@openai.rokid.com:29418/amlogic_a1xx_linux/manifest -m rokidbase.xml --repo-url=ssh://your-account@openai.rokid.com:29418/tools/repo --no-repo-verify
```
##### 相关参考

[开发套件 905D android代码编译烧写教程](https://forum.rokid.com/topic/991#46)