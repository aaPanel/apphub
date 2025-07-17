# AppHub 示例仓库

本仓库用于演示宝塔面板 Docker 外部应用的目录标准，方便开发者参考和自定义自己的应用仓库。

**本仓库不接受 PR，也不维护其中的应用。**


## 目录结构说明
```
apphub/                               #必须为apphub
  └─ alist/                           #应用目录 应用名称
       ├─ app.json                    #应用配置文件
       ├─ icon.png                    #应用图标
       ├─ 3.42.0/                     #版本目录
       │    └─ docker-compose.yml
       └─ latest/
            └─ docker-compose.yml
```

## 使用方法
1. 请 fork 本仓库到你自己的账号下。
2. 按需修改或添加应用目录和配置文件。
3. 具体模板格式请阅读：[模板格式说明](template.md)
4. 通过10.0以上版本的宝塔面板导入使用

## 贡献说明
本仓库仅用于演示和模板参考，不接受任何 PR 及 后续仓库应用的维护，如有建议可以提Issues或加QQ群反馈：662047798。

## 同步地址
为方便国内用户，本仓库会同步至以下仓库：

**https://cnb.cool/btpanel/apphub**

**https://gitee.com/btpanel/apphub**