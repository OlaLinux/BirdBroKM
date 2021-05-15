# Linux常用指令集

## 使用`pushd` & `popd` 將當前目錄路徑記錄起來，並再次造訪

當前目錄push到stack內

```
pushd . 

# 顯示目前之後可能會被造訪的目錄
~/linux/crontab ~/linux/crontab
```

前往之前記錄起來的目錄

```
popd
```