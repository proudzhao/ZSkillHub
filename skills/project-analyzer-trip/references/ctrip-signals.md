# 携程项目指纹与扫描线索

技术栈与架构阶段按需查阅。有则写入文档，无则省略对应行/节。

## 依赖 / BOM

| 信号（pom/gradle） | 含义 |
|--------------------|------|
| `framework-bom` / `com.ctrip.framework` BOM | Framework BOM 基线版本 |
| Super POM / 公司 parent | 组织级父 POM |
| `dal-client` / `com.ctrip.platform.dal` | DAL |
| `qconfig-client` / `qconfig` | QConfig |
| `qmq` | QMQ |
| `baiji` / `soa` 相关 | Baiji SOA |
| `cdubbo` | CDubbo |
| `credis` | CRedis |
| `cat-client` / clog / triplog | 日志与 CAT |
| `qschedule` | QSchedule |
| `hickwall` | Hickwall 指标 |
| `cmongo` | CMongo |

## 配置文件

| 路径 / 文件 | 说明 |
|-------------|------|
| `**/META-INF/app.properties` | 必查；`app.id`、可选 `jdkVersion` |
| `**/Dal.config` | D 大写；`*_dalcluster` |
| `datasource.xml` 等 | DAL/数据源 |
| QConfig 注解 | `@QConfig` `@QMapConfig` `@QTable` |
| SOA/Baiji 契约与 client 模块 | serviceCode、生成代码 |

WAR 注意：`WEB-INF/classes/META-INF/app.properties`。

## 代码线索

| 目标 | 搜索 |
|------|------|
| HTTP 入口 | `@RestController` `@Controller` `@RequestMapping` |
| CDubbo | `@DubboService` `@DubboReference` `@Reference` |
| QMQ | `@QmqConsumer`、Producer API |
| 定时 | QSchedule / `@Scheduled` |
| 外部调用 | `*Client` `*Proxy` `*Invoker` gateway |

## RPC 梳理优先级

1. 代码中的 Client / Service 声明  
2. MOM 契约（有命中时）  
3. BAT 上下游（采样，关系参考）  

开源 Dubbo/Kafka 与公司 CDubbo/QMQ 并存时，以实际依赖为准分别标注。

## 二方库额外关注

- `groupId:artifactId:version` 与 packaging  
- 对外公开 API 包（非 internal）  
- README / 模块说明中的接入方式  
- 一般无 `app.id`；不要为此报错或强行 enrichment  
