## ARL(Asset Reconnaissance Lighthouse)资产侦察灯塔系统
ARL资产侦察灯塔系统备份项目，**已跑通**

### 简介
旨在快速侦察与目标关联的互联网资产，构建基础资产信息库。
协助甲方安全团队或者渗透测试人员有效侦察和检索资产，发现存在的薄弱点和攻击面。

ARL删库后，备份项目使用到ARL-NPoC、arl_files等项目，无法跑通，大多数人使用docker运行ARL，docker镜像同样被删除，无法拉取镜像，需要修改安装脚本调试环境
### 修改内容
用新不用旧，更新为centos8版本运行，修改软件源使用cloudflare代理，nmap使用最新版本，nuclei待更新

### 系统要求

建议采用**Docker内源码安装**或**Docker内源码安装**方式运行。系统配置建议：CPU:4线程 内存:8G 带宽:10M。  
由于自动资产发现过程中会有大量的的发包，建议采用云服务器可以带来更好的体验。
### Docker 安装（快速）
```bash
docker run --privileged -it -d -p 5003:5003 --name=arl adysec/arl /usr/sbin/init
```
### Docker 内源码安装（最新版）

```bash
curl https://github.com/adysec/ARL/raw/master/tools/smartdns.conf > /etc/smartdns/smartdns.conf
docker run -d --name smartdns --restart=always -p 53:53/udp -v /etc/smartdns:/etc/smartdns pymumu/smartdns:latest
docker run --privileged -it -d -p 5003:5003 --name=arl centos /usr/sbin/init
docker exec -it arl bash
#docker内运行
curl https://raw.githubusercontent.com/adysec/ARL/master/misc/setup-arl.sh >install.sh
bash install.sh
exit
```

docker内执行后直接exit退出即可

Ubuntu 下可以直接执行 `apt-get install docker.io docker-compose -y` 安装相关依赖

### 源码安装

原版ARL仅适配centos 7，我更新至仅支持centos8（centos:latest）
如果在其他目录可以创建软连接，且安装了四个服务分别为`arl-web`, `arl-worker`, `arl-worker-github`, `arl-scheduler`

```
wget https://raw.githubusercontent.com/adysec/ARL/master/misc/setup-arl.sh
chmod +x setup-arl.sh
./setup-arl.sh
```

### 查看服务状态

```
systemctl status arl-web
systemctl status arl-worker
systemctl status arl-worker-github
systemctl status arl-scheduler
```
### 特性

1. 域名资产发现和整理
2. IP/IP 段资产整理
3. 端口扫描和服务识别
4. WEB 站点指纹识别
5. 资产分组管理和搜索
6. 任务策略配置
7. 计划任务和周期任务
8. Github 关键字监控
9. 域名/IP 资产监控
10. 站点变化监控
11. 文件泄漏等风险检测
12. nuclei PoC 调用
13. [WebInfoHunter](https://tophanttechnology.github.io/ARL-doc/function_desc/web_info_hunter/) 调用和监控

### 截图

1. 登录页面     
   默认端口5003 (https), 默认用户名密码admin/arlpass  
   ![登录页面](./image/login.png)
2. 任务页面
   ![任务页面](./image/task.png)
3. 子域名页面
   ![子域名页面](./image/domain.png)
4. 站点页面
   ![站点页面](./image/site.png)
5. 资产监控页面
   ![资产监控页面](./image/monitor.png)
   详细说明可以参考：[资产分组和监控功能使用说明](https://github.com/TophantTechnology/ARL/wiki/%E8%B5%84%E4%BA%A7%E5%88%86%E7%BB%84%E5%92%8C%E7%9B%91%E6%8E%A7%E5%8A%9F%E8%83%BD%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E)
6. 策略页面
   ![策略配置页面](./image/policy.png)
7. 筛选站点进行任务下发
   ![筛选站点进行任务下发](./image/scan.png)
   详细说明可以参考： [2.3-新添加功能详细说明](https://github.com/TophantTechnology/ARL/wiki/ARL-2.3-%E6%96%B0%E6%B7%BB%E5%8A%A0%E5%8A%9F%E8%83%BD%E8%AF%A6%E7%BB%86%E8%AF%B4%E6%98%8E)
8. 计划任务
   ![计划任务](./image/task_scheduler.png)
   详细说明可以参考： [2.4.1-新添加功能详细说明](https://github.com/TophantTechnology/ARL/wiki/ARL-2.4.1-%E6%96%B0%E6%B7%BB%E5%8A%A0%E5%8A%9F%E8%83%BD%E8%AF%A6%E7%BB%86%E8%AF%B4%E6%98%8E)
9. GitHub 监控任务
   ![GitHub 监控任务](./image/github_monitor.png)

### 任务选项说明

| 编号 | 选项            | 说明                                                         |
| ---- | --------------- | ------------------------------------------------------------ |
| 1    | 任务名称        | 任务名称                                                     |
| 2    | 任务目标        | 任务目标，支持IP，IP段和域名。可一次性下发多个目标           |
| 3    | 域名爆破类型    | 对域名爆破字典大小, 大字典：常用2万字典大小。测试：少数几个字典，常用于测试功能是否正常 |
| 4    | 端口扫描类型    | ALL：全部端口，TOP1000：常用top 1000端口，TOP100：常用top 100端口，测试：少数几个端口 |
| 5    | 域名爆破        | 是否开启域名爆破                                             |
| 6    | DNS字典智能生成 | 根据已有的域名生成字典进行爆破                               |
| 7    | 域名查询插件    | 已支持的数据源为13个，`alienvault`, `certspotter`,`crtsh`,`fofa`,`hunter` 等 |
| 8    | ARL 历史查询    | 对arl历史任务结果进行查询用于本次任务                        |
| 9    | 端口扫描        | 是否开启端口扫描，不开启站点会默认探测80,443                 |
| 10   | 服务识别        | 是否进行服务识别，有可能会被防火墙拦截导致结果为空           |
| 11   | 操作系统识别    | 是否进行操作系统识别，有可能会被防火墙拦截导致结果为空       |
| 12   | SSL 证书获取    | 对端口进行SSL 证书获取                                       |
| 13   | 跳过CDN         | 对判定为CDN的IP, 将不会扫描端口，并认为80，443是端口是开放的 |
| 14   | 站点识别        | 对站点进行指纹识别                                           |
| 15   | 搜索引擎调用    | 利用搜索引擎搜索下发的目标爬取对应的URL和子域名              |
| 16   | 站点爬虫        | 利用静态爬虫对站点进行爬取对应的URL                          |
| 17   | 站点截图        | 对站点首页进行截图                                           |
| 18   | 文件泄露        | 对站点进行文件泄露检测，会被WAF拦截                          |
| 19   | Host 碰撞       | 对vhost配置不当进行检测                                      |
| 20   | nuclei 调用     | 调用nuclei 默认PoC 对站点进行检测 ，会被WAF拦截，请谨慎使用该功能 |
| 21   | WIH 调用        | 调用 WebInfoHunter 工具在JS中收集域名,AK/SK等信息            |
| 22   | WIH 监控任务    | 对资产分组中的站点周期性 调用 WebInfoHunter 工具在JS中域名等信息进行监控 |

### FAQ

请访问如下链接[FAQ](https://tophanttechnology.github.io/ARL-doc/faq/)  

