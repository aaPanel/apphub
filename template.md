
# 应用模板说明

每个app的模板目录大致如下
```
.
├── app.json                #app的信息
├── icon.png                #app的图标 100x100像素 png格式
├── latest                  #latest版本文件夹（在app.json中定义了版本就要有该文件夹）
│   ├── docker-compose.yml
│   └── .env
└── 3.42.0                  #3.42.0版本文件夹（在app.json中定义了版本就要有该文件夹）
    ├── docker-compose.yml
    └── .env
```
## app.json

```
{
    "appid": -1,                                                             #使用-1
    "appname": "alist",                                                      #app名称
    "apptitle": "Alist",                                                     #展示的标题
    "apptype": "Storage",                                                    #应用类型
    "appTypeCN": "存储/网盘",                                                 #应用类型名称和上面对应
    "appversion": [                                                          #版本支持
        {
            "m_version": "latest",
            "s_version": []                                                 
        },
        {
            "m_version": "3",                                               #大版本号
            "s_version": [                                                  #小版本号 渲染时会拼接 3.42.0
                "42.0"
            ]
        }
    ],
    "appdesc": "一个支持多存储的文件列表程序，使用Gin和Solidjs",              #应用的简介
    "appstatus": 1,                                                        #可用状态 改为0则不显示
    "home": "",                                                            #主页 可放github 或官网
    "help": "https://alist.nn.ci",                                         #使用的说明
    "updateat": 1752027587,                                                #更新时间戳 秒
    "depend": null,                                                        #暂不使用 null即可
    "field": [                                                             #创建时的输入框配置
        {
            "attr": "domain",                                              #每个应用必填项 直接copy即可
            "name": "域名",                                                #若填写了 会为其创建网站
            "type": "textarea",
            "default": "",
            "suffix": "浏览器访问的域名,非必填",
            "unit": ""
        },
        {
            "attr": "allow_access",                                        #每个应用必填项 直接copy即可
            "name": "允许外部访问",                                         #用于控制env中host_ip为 0.0.0.0 或 127.0.0.1
            "type": "checkbox",
            "default": true,
            "suffix": "允许直接通过主机IP+端口访问",
            "unit": ""
        },
        {
            "attr": "alist_web_port",                                     #自定义的变量 输入的值会被替换到env中
            "name": "web管理端口",                                         #展示的名称
            "type": "number",                                             #变量的类型  number,string
            "default": 15244,
            "suffix": "alist的web管理端口",
            "unit": ""
        },
        {
            "attr": "s3_server_port",                                    #自定义的变量 输入的值会被替换到env中
            "name": "s3服务端口",                                         #展示的名称
            "type": "number",   
            "default": 5426,
            "suffix": "s3服务的端口",
            "unit": ""
        },
        {
            "attr": "cpus",                                              #每个应用必填项 直接copy即可
            "name": "cpu核心数限制",
            "type": "number",
            "default": 0,
            "suffix": "0为不限制,最大可用核心数为: ",
            "unit": ""
        },
        {
            "attr": "memory_limit",                                     #每个应用必填项 直接copy即可
            "name": "内存限制",
            "type": "number",
            "default": 0,
            "suffix": "0为不限制,最大可用内存为: ",
            "unit": ""
        }
    ],
    "env": [
        {
            "key": "alist_web_port",                                    #自定义的变量 需要和field中的对应 在.env文件中为大写字段
            "type": "port",                                             #变量类型 number,port,string   port在创建时会进行端口占用检测
            "default": null,
            "desc": "web管理端口"
        },
        {
            "key": "s3_server_port",                                    #自定义的变量 需要和field中的对应 在.env文件中为大写字段
            "type": "port",
            "default": null,
            "desc": "alist服务端口"
        },
        {
            "key": "app_path",                                          #必填项 会被替换到.env中 用于挂载
            "type": "path",
            "default": null,
            "desc": "应用数据目录"
        },
        {
            "key": "host_ip",                                           #必填项 和allow_access对应 在映射端口时可以控制是否支持外部访问
            "type": "string",
            "default": null,
            "desc": "主机IP"
        },
        {
            "key": "cpus",                                              #必填项
            "type": "number",
            "default": null,
            "desc": "CPU核心数限制"
        },
        {
            "key": "memory_limit",                                      #必填项
            "type": "number",
            "default": null,
            "desc": "内存大小限制"
        }
    ],
    "volumes": {
        "data": {                                                       #需要挂载的外部目录 对于已有目录挂载到容器内需要填
            "type": "path",                                             # path,file
            "desc": "数据目录"
        },
        "mnt": {                                                        
            "type": "path",
            "desc": "配置文件"
        }
    }
}
```

## .env
.env中有四项为必填项HOST_IP、CPUS、MEMORY_LIMIT、APP_PATH 在app.json中定义的env的key会通过大写来转换替换到.env中
```
ALIST_WEB_PORT=
S3_SERVER_PORT=
HOST_IP=            #必填
CPUS=               #必填
MEMORY_LIMIT=       #必填
APP_PATH=           #必填
```


## compose.yml
不推荐使用container_name 可能会导致应用状态识别异常，可以直接修改services的名称
对于应用的网络，如果services过多5个以上 建议使用compose的初始化自建网络
```
services:
  alist:
    image: xhofe/alist:v3.42.0
    deploy:                                     
      resources:
        limits:
          cpus: ${CPUS}                          #CPU限制
          memory: ${MEMORY_LIMIT}                #内存限制
    environment:
      - PUID=0
      - PGID=0
      - UMASK=022
    ports:
      - ${HOST_IP}:${ALIST_WEB_PORT}:5244       #端口的填写形式一般为${HOST_IP}:${ALIST_WEB_PORT} 这样可以控制外部访问
      - ${HOST_IP}:${S3_SERVER_PORT}:5426
    restart: always
    volumes:
      - ${APP_PATH}/data:/opt/alist/data        #挂载卷如果没有特殊 请使用${APP_PATH}/  ${APP_PATH}为compose的同级目录
      - ${APP_PATH}/mnt:/mnt/data
    labels:                                 
      createdBy: "bt_apps"                     #必填项
    networks:                                  #根据实际需要填写 也可以不填 使用compose自建的网络
      - baota_net

networks:                                      
  baota_net:
    external: true
```