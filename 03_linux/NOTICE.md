制作引导u盘





安装中文输入法

ibus-rime 默认为繁体中文模式，需要自己到目录`~/.config/ibus/rime`中新建一个`default.custom.yaml`文件进行简体中文配置，如下

```yaml
# default.custom.yaml
patch:
  "schema_list":
    - schema: luna_pinyin_simp
    - schema: luna_pinyin

```

增加词库

使用[oh-my-rime](https://github.com/Mintimate/oh-my-rime.git "github仓库地址")

```bash
mv rime-ice ~/.config/ibus/rime
```









