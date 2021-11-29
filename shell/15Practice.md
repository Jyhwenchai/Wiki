# Practice

## 统计文件行数

```sh
wc -l filename
```

## 打印文件前5行/最后5行

```sh
head -5 filename
tail -5 filename
```

## 输出数字0到500中7的倍数(0 6 14 ...)

```sh
#!/bin/bash
for value in {0..500}; do
    [[ "((value%7))" -eq 0 ]] && echo "${value}"
done
exit 0

# 或

for value in {0..500..7}; do
    echo "${value}"
done

```

## 输出第5行的内容

```sh
#!/bin/bash

sed -n '5p' filename 
```

## 打印空行的行号

```sh
#!/bin/bash
grep -n '^\s*$' filename
awk '/^\s*$/{print NR}' filename 
sed -n '/^\s*$/=' filename 
```
