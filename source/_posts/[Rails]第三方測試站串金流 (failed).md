---
title: '[Rails]第三方測試站串金流 (failed)'
date: 2018-04-27 06:33:00
categories:
 - [Rails]
tags:
 - Rails
 - 金流
---
這次是失敗的先敘述原因和結果：
1.[Pay2Go測試平台](https://cwww.pay2go.com/)雖然是可以正常開啟和使用但似乎不能用了？因為在[套件pay2go](https://github.com/CalvertYang/pay2go)他敘述了『智付寶第三方金流業務已轉移到智付通，請改用 [spgateway](https://github.com/CalvertYang/spgateway/blob/1aec1b57b06a5c95807c7907fcf8ce29f46e6f93/test/spgateway_test.rb)』。

2.Pay2Go和Spgateway官方API document和實作上幾乎是一樣的，Spgateway甚至還標明了版本更新日期是2018-01-03，但測試站要串的API需要給予『CheckValue』這個文件沒提到的參數，要怎麼算出來的我不知道，所以最終是一直卡在這關。

## 實作紀錄
---
這是[Pay2go測試平台](https://cwww.pay2go.com/)，這是[Spgateway測試平台](https://cwww.spgateway.com/)，兩個都可以直接簡單註冊資料不必填真實，創立商店後主要需拿到：
`商店編號 MerchantID`
`串些金鑰 HashKey`
`串些金鑰 HashIV`

可以直接把任何繳費服務都打開(預設應該也都是全部開啟)，再來就是去裝套件了。

套件上[pay2go](https://github.com/CalvertYang/pay2go)和 [spgateway](https://github.com/CalvertYang/spgateway/blob/1aec1b57b06a5c95807c7907fcf8ce29f46e6f93/test/spgateway_test.rb)兩個文件寫的幾乎一樣，這裡用pay2go做示範。

``` ruby Gemfile
gem "pay2go"
```

`$ bundle install`

設定整合模式
``` ruby config/environments/development.rb
Rails.application.configure do
	...(略)
	config.after_initialize do
		Pay2go.integration_mode = :development
	end
end
```

``` ruby config/environments/production.rb
Rails.application.configure do
	...(略)
	config.after_initialize do
		Pay2go.integration_mode = :production
	end
end
```

加入 pay2go 的 API key
`$ touch config/initializers/pay2go.rb`
``` ruby config/initializers/pay2go.rb
Pay2go.setup do |pay2go|
	if Rails.env.development?
		pay2go.merchant_id = "1234567"  # 放測試站的 key

		pay2go.hash_key    = "xxxxxxxx"
		pay2go.hash_iv     = "xxxxxxxx"
		pay2go.service_url = "https://cpayment.pay2go.com/MPG/mpg_gateway"
	else
		pay2go.merchant_id = "1234567"  # 放正式站的 key

		pay2go.hash_key    = "xxxxxxxx"
		pay2go.hash_iv     = "xxxxxxxx"
		pay2go.service_url = "https://payment.pay2go.com/MPG/mpg_gateway"
	end
end
```

此時先把測試站的MerchantID(商店編號), key 和 iv 貼上去

再來設定信用卡付款按鈕
`$ mkdir app/services`

``` ruby app/services/pay2go_service.rb
class Pay2goService
	def initialize(order)
		@order = order
		@timestamp = order.created_at.to_i
		@merchant_order_no = "#{order.id}s#{Time.now.strftime("%Y%m%d%H%M%S")}"
		@total_price = order.total
	end

	def check_value
		d = Digest::SHA256.hexdigest(url_params).upcase
	end

	def url_params
		"HashKey=#{Pay2go.hash_key}&Amt=#{@total_price}&MerchantID=#{Pay2go.merchant_id}&MerchantOrderNo=#{@merchant_order_no}&TimeStamp=#{@timestamp}&Version=1.1&HashIV=#{Pay2go.hash_iv}"
	end
end
```

`重開 rails server`

新增按鈕的partial
``` html _pay_with_credit_card.html.erb
<%= form_tag Pay2go.service_url, method: :post do  %>
	<%= hidden_field_tag "MerchantID",      Pay2go.merchant_id %>
	<%= hidden_field_tag "HashKey",         Pay2go.hash_key %>
	<%= hidden_field_tag "HashIV",          Pay2go.hash_iv %>
	<%= hidden_field_tag "LoginType",       "0" %>
	<%= hidden_field_tag "RespondType",     "JSON" %>
	<%= hidden_field_tag "Version",         "1.1" %>

	<%= hidden_field_tag "ItemDesc",        "order_sample" %>
	<%= hidden_field_tag "MerchantOrderNo", "#{order.id}s#{Time.now.strftime("%Y%m%d%H%M%S")}" %>
	<%= hidden_field_tag "TimeStamp",       order.created_at.to_i %>
	<%= hidden_field_tag "Amt",             order.total %>

	<%= hidden_field_tag "CREDIT",          "1" %>
	<%= hidden_field_tag "ReturnURL",       pay2go_cc_notify_order_url(order.token) %>
	<%= hidden_field_tag "CheckValue",      Pay2goService.new(order).check_value %>

	<%= submit_tag "使用信用卡付款", class: "btn btn-danger btn-lg btn-default" %>
<% end %>
```

然後在想要的頁面的地方render上去
``` html view.html.erb
<%= render "pay_with_credit_card", order: @order %>
```

加上 controller 的 callbacks
``` javascript some_controller.rb
...(略)
	protect_from_forgery except: :pay2go_cc_notify

...(略)

	def pay2go_cc_notify
		@order = Order.find_by_token(params[:id])

		if params["Status"] == "SUCCESS"
			@order.make_payment!

			if @order.is_paid?
				flash[:notice] = "信用卡付款成功"
				redirect_to account_orders_path
			else
				render text: "信用卡失敗"
			end
		else
			render text: "交易失敗"
		end
	end
```

修改routes
``` ruby config/routes.rb
resources :comes do
	post :pay2go_cc_notify
end
```

發現post出去的網址即使剛剛在`config/initializers/pay2go.rb`裡面設定`pay2go.service_url`測試網址，但他依然post出去的是舊的測試平台網址『https://capi.pay2go.com/MPG/mpg_gateway』。

我就直接把form_tag的action寫死：
``` html _pay_with_credit_card.html.erb
<%= form_tag action="https://cpayment.pay2go.com/MPG/mpg_gateway", method: :post do  %>
	…(略)
<% end %>
```
但測試結果是不行的。

網上很多舊的教學文章引導到這裡但是實際上post出去是失敗的，後來我開始去看官方文件，Pay2Go的說需要：
`MerchantID 商店代號`
`Version 版本請帶1.0`
`TradeInfo 交易資料 AES 加密`
`TradeSha 交易資料 SHA256 加密`

但我嘗試了許久終究會是TradeSha錯誤，網上也說很有可能是AES 加密先錯誤造成SHA256 加密錯誤，我也試了不下幾個方法，最後才看到文件上說要用Spgateway的套件去做才猜測是不是Pay2Go的套件和測試平台不能用了。

再來換成Spgateway的套件和他的測試平台去實作，那就去把action位置改為Spgateway的測試API吧！
``` html _pay_with_credit_card.html.erb
<%= form_tag action="https://ccore.spgateway.com/MPG/mpg_gateway", method: :post do  %>
	…(略)
<% end %>
```
再到[Spgateway測試平台](https://cwww.spgateway.com/)像Pay2Go一樣建立商店取得商店代號和key，把信用卡服務什麼的都打開，然後去修改config/initializers/pay2go.rb把商店代號和key填進去。

結果報的錯誤是`檢查碼錯誤...`，檢查碼到底是什麼官方文件上都沒有寫，後來一個一個刪除去找發現是`CheckValue`這個東西有問題，因為當把他從_pay_with_credit_card.html.erb移除後會報錯`檢查碼不可空白...`，CheckValue又在官方文件上都沒有提到...就這樣嘗試了幾回合後發現自己真的無法通靈就先告一段落了...

此次是我錯誤的結果紀錄，但我發現串正式機的話似乎不用驗證CheckValue，但要串正式機建立真實商店就不是這麼容易了...
那其他的串金流也沒有像這個這麽好串可以callback回來我的localhost，也許是錯過最好的時代了...(苦笑)
