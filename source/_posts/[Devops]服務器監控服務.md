---
title: '[Devops]服務器監控服務'
date: 2018-04-29 10:30:00
categories:
 - [Devops]
tags:
 - Devops
---
## 網站 Uptime 監控
---
以下這些第三方服務，可以每隔幾分鐘檢查你指定的 URL 是否正常回應(不需要額外安裝Gem)，如果連不上可以透過 E-mail 通知你。免費的方案就夠用了，如果需要短信通知或增加檢查頻率，則需要付費。

https://uptimerobot.com/
https://www.statuscake.com/
https://tools.pingdom.com/
https://www.pagerduty.com

## Rails 異常監控
---
以下這些第三方服務，可以在 Rails 發生異常 (exception) 時，主動通知及紀錄下這個錯誤例外(exception)，好讓我可以進行追踪除錯。

https://rollbar.com
https://airbrake.io
https://sentry.io
https://www.honeybadger.io
這些服務都有提供 gem 可以安裝，按照註冊流程應該就可以完成。

## 效能監控
---
以下這些第三方服務，會紀錄監控網站程式的效能，例如網站的回應速度，協助你分析哪些部分需要做最佳化改善：

https://www.skylight.io/
https://newrelic.com/
https://appsignal.com/
這些服務都有提供 gem 可以安裝，按照註冊流程應該就可以完成。
