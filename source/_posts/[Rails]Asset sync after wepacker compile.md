---
title: '[Rails]Asset sync after wepacker compile'
date: 2019-12-29 21:06:24
categories:
 - [Rails, Gem]
tags:
 - Rails
 - Gem
 - AssetSync
---

當設定好Asset sync且自動把檔案sync到S3, 在官方的敘述是
> If AssetSync.config.run_on_precompile is true (default), then assets will be uploaded to S3 automatically after the assets:precompile rake task is invoked
> 意思就是沒有設定run_on_precompile是false, 就會跑sync rake上傳檔案到S3

再來這個rake是包在Gem裡面的, 且預設為`在asset:precompile之後執行`, 這樣會造成webpacker還沒compile好檔案就先執行了, 會造成沒有把webpacker的東西sync到S3

所以我們要客製化rake流程, 就直接在lib/tasks底下新增一個`asset_sync.rake`的檔案
```ruby asset_sync.rake
if defined?(AssetSync)
  Rake::Task['webpacker:compile'].enhance do
    Rake::Task["assets:sync"].invoke
  end
end
```

最後AssetSync預設只sync asset資料夾底下的東西, webpacker compile出來的東西預設在publick/packs, 所以要更新設定
```ruby config/initializers/asset_sync.rb
  config.add_local_file_paths do
    # Support webpacker assets
    public_root = Rails.root.join('public')
    Dir.chdir(public_root) do
      packs_dir = Webpacker.config.public_output_path.relative_path_from(public_root)
      Dir[File.join(packs_dir, '/**/**')]
    end
  end
```
