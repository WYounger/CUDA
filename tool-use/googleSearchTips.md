1.完全匹配 `""`

```bash
"key"
```

2.排除 `-`

```bash
key -notKey #-没有空格
```

3.匹配 `*`

```bash
key*key
```

4.网址 `site`

```bash
key site:address #指定网址
key -site:address #排除网址
```

5.文件类型 `typefile`

```bash
key typefile:pdf/ppt/docx/doc/mobi
```

