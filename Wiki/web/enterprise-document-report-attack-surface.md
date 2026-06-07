---
title: 企业文档、报表与导入导出链路攻击面
category: web
tags: [document, report, export, import, office, pdf, attachment, workflow, template-injection]
triggers: [report, reportserver, WebReport, export, import, upload, preview, attachment, office, officeserver, htmlofficeservlet, pdf, template, workflow, print, 报表, 导出, 导入, 预览, 附件, 打印, 协同, 表单, 流程]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/web/企业文档报表导入导出链路攻击面.md]
---

## Summary

针对文档平台、OA、报表中心、在线 Office 等企业系统的攻击面分析。优先按"业务链路"找接口。

## Key Points

- 只看主站不看业务子路径是常见误区 — 危险接口常在历史兼容路径里
- "附件功能"不等同于普通文件上传 — 文档系统有自己的存储/预览/转码链
- 报表导出、PDF 生成经常是异步任务，没有直接回显（blind 场景）
- 读不到源码也可能先读到模板、配置、凭据

## Details

### 攻击步骤

#### 1. 按业务链路枚举接口

优先枚举路径和动作：
- `upload` / `download` / `preview`
- `export` / `import` / `print`
- `ReportServer` / `WebReport`
- `office` / `officeserver` / `htmlofficeservlet`
- `attachment` / `template` / `workflow`

#### 2. 报表/资源读取链路 — 优先任意文件读取

```http
GET /WebReport/ReportServer?op=chart&cmd=get_geo_json&resourcepath=privilege.xml HTTP/1.1
```

高价值目标文件：`privilege.xml`、数据库配置、管理员凭据、报表模板目录。

#### 3. Office/附件上传链路 — 优先任意文件写入

在线 Office、历史兼容控件、附件中转接口往往直接把客户端提交的内容落盘。

#### 4. PDF/模板/HTML 生成链路 — 考虑注入

当系统支持生成 PDF、把用户备注渲染进 PDF、导出 HTML 转 PDF 时，考虑：
- PDF 注入
- 模板注入
- 服务端 SSRF
- 后台渲染器命令执行

#### 5. 工作流/服务接口 — "业务外壳下的技术入口"

很多协同/审批系统暴露 SOAP/XML/Workflow 服务接口，本质上是高危解析入口——如果 XML 交给 XStream/XMLDecoder/模板引擎处理，就落到更底层技术面。

### 典型成链方式

1. 枚举 `upload`/`ReportServer`/`office`/`workflow` 接口
2. 从文件读取拿配置，或从上传拿写入能力
3. 获取后台凭据、数据库配置、模板目录
4. 继续打后台、模板执行、文件上传、反序列化或命令执行

## Connections

- Related: [[ops-console-attack-surface]]
- Related: [[php-file-inclusion-filter]]
- Related: [[ssrf-gopher-techniques]]
