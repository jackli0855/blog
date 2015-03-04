---
layout: post
title: "[译]Ruby on rails 实战圣经：Part 2: 深度剖析环境设定与Bundler"
date: 2015-03-04 10:00
comments: false
---


目录结构

这一节让我们走访一个 Rails 的目录结构：
app/

app 目录是你主要工作的地方，不同子目录存储了 Models 、 Controllers 、 Views 、 Helpers 和 Assets 等档案。
app/controllers

Controller 的类别档案存放在这里
app/models

Model 的类别档案存放在这里
app/views

View 的样本 (template) 档案，依照不同 Controllers 分子目录存储。
app/helpers

Helper 一些在 Views 中可以使用的小方法，用来产生较复杂的 HTML 。默认的 Helper 档案命名是对应 Controller 的，不过并不强制，定义在任一个 Helper 档案中的方法，都可以在任何 Views 中使用。
app/assets

Assets 静态档案存放在这里，包括有 JavaScript 、 Stylesheets 样式表单和 Images 图档。详细的用法会在 Assets 一章中介绍。
config/

虽然 Rails 的原则是惯例优于设定，不过还是有一些需要设定的地方。这个目录下存放了例如数据库配置文件 database.yml 、路由设定 routes.rb 、应用程序配置文件 application.rb 和不同执行环境的配置文件在 config/environments 目录下。
db/

数据库 Schema( 纲要 ) 和定义档 migrations
doc/

你可以将你的文件放在这里
lib/

如果你有一些共享的类别或模块档案，可以放在这里，然后用 require 加载。例如一个放在 lib/foobar.rb 的类别或模块档案，可以在要使用的 .rb 档案中这样加载：

require "foobar"

如果放在子目录 lib/foo/bar.rb 的话：

require "foo/bar"

lib/tasks

Rake 任务档案存放在这里，我们会在 Rails 锦囊妙计 一章介绍 Rake 。
log/

不同执行环境的 log 档案会分别记录在这里
public/

这个目录对 Web 服务器来说，就是文件根目录 (document root) ，也就是唯一可以在网络上读取到的目录。
script/

Rails 的脚本档案
test/

单元测试、功能测试及整合测试的档案
tmp/

用来存放暂时用途的档案
vendor/

第三方函式库或 Plugin 套件会放在这里
其他根目录下的档案

    config.ru 用来启动应用程序的 Rack 配置文 件
    Gemfile 设定你的 Rails 应用程序会使用哪些 Gems
    README.rdoc 你的应用程序使用手册。你可以用来告诉其他人你的应用程序是做什么用的，如何使用等等 。
    Rakefile 用来加载可以被命令行执行的 Rake 任 务

多重环境

Rails 应用程序默认提供了三种不同的执行模式：

    developmentenvironment 开发模式，用在你的开发的时 候
    test environment 测试模式，用在执行测试程序 时
    production environment 正式上线模式，用在实际的上线运作环 境

不同环境的差异在于有不同的设定，除了数据库设定 database.yml 里分开设定之外，个别的环境设定放在 config/environments/development.rb 、 config/environments/test.rb 和 config/environments/production.rb ，它们可以有不同的 Log 层级、 Session 设定、 Email 设定等等。除了默认的这三种模式，我们也可以自定模式，只需要建立对应的档案即可，例如 config/environments/staging.rb 。我们会在下一节详述这些档案里面的设定。

staging 可以用来表示准上线模式，用来做正式上线前的 QA 测试用途 。

因为程序本身是不是写死是哪一种执行模式，那么要怎么区分呢 ? 根据不同情况有不同方法，包括：

根据环境变量 RAILS_ENV 或 RACK_ENV 来决定使用哪一种模式，例如使用 rake 时：

RAILS_ENV=production rake db:migrate

下一节会介绍的 rails 指令根据参数决定 :

rails console production

rails server -e production

最后，应用程序服务器则看服务器配置文件，例如 Passenger 里会设定 RackEnv 参数，布署一章会详细介绍。
Rails 指令

我们已经陆续使用过一些指令了，让我们看看全部的指令吧：
generate 可缩写为 g

产生各种不同类型的档案，例如

rails generate model person

rails g controller people

console 可缩写为 c

开启一个 Rails 控制台

rails console

rails c

默认的环境是 developement ，如果需要指定环境，请多输入环境名称即可，例如：

rails c production

Rails 也有提供沙盒模式 (Sandbox) ，任何数据库的修改都会在离开时回复 ( 原理是数据库 Transaction ) ：

rails c --sandbox

在控制台中输入 exit 就会离开。
server 可缩写为 s

开启一个 Rails 服务器

rails s

默认是使用 Port 3000 和 development 环境，如果需要指定：

rails s -p 4000 -e production

new

建立一个新 Rails 项目

rails new my_app

将会建立一个叫做 MyApp 的 Rails 项目在 ./my_app 目录下。加上 --database 参数可以改变配置文件的默认值，例如：

rails new my_app --database=mysql

其他说明可以输入 rails 看到全部的指令。
其他指令

    dbconsole 开起一个数据库控制台 ( 可简写为 rails db) ，让你直接输入 SQL 指令 。
    destroy 删除 “generate” 所产生的档 案
    benchmarker 产生 benchmark 信息，我们会在 性能 一章介绍如何使 用
    profiler 产生 profiler 信息，我们会在 性能 一章介绍如何使 用
    runner 在 Rails 环境中执行一段程序，例如 rails runner “puts Person.count”

Rails 启动与应用程序设定

不同的 Rails 版本产生的配置文件可能会略有差异，这些配置文件也没有列出所有 Rails 设定，只有列出比较常用的 。

启动整个 Rails 程序 ( 包括 rails server, rails runner,rails console 等 ) 时，会执行 application.rb 的应用程序设定，让我们来看看这个档案一些比较重要的部分吧。如果你对这个档案有修改，无论在什么模式下，都必须重新启动 Rails 设定才会生效。

# Custom directories with classes and modules you want to be autoloadable.

# config.autoload_paths += %W(#{config.root}/extras)

任何放在 app/models 、 app/controllers 目录下的类别档案， Rails 都会根据类别的命名惯例来自动加载。如果你有其他类别档案需要加载，请在这个设定加入。例如通常我们会加入

config.autoload_paths += %W( #{config.root}/app/lib )

如果 app/models 下的档案太多，我们可以很简单地增加新的子目录来做分类，例如我们可以将档案直接搬到 app/models/foobar 子目录下 ( 程序内容无需修改 ) ，然后将这个目录加进 autoload_paths 即可，例如 config.autoload_paths +=%W( #{config.root}/app/lib #{config.root}/app/models/foobar) 。这里的 %W 是 Ruby 的数组简写用法 。

# Activate observers that should always be running.

# config.active_record.observers = :cacher, :garbage_collector, :forum_observer

设定 ActiveRecord 的观察者类别，我们会在 ActiveRecord 章节详述这个模式，简单来说，它会在 ActiveRecord 数据变动时执行外部回呼，例如清除快取。

# Set Time.zone default to the specified zone and make Active Record auto-convert to this zone.

# Run "rake -D time" for a list of tasks for finding time zone names. Default is UTC.

# config.time_zone = 'Central Time (US & Canada)'

设定默认的应用程序时区，默认是 UTC 。在 Rails 中，数据库里面储存的时间皆为 UTC 时间，而设定此时区会自动帮你处理转换动作。例如设定 Taipei 的话，从数据库读取出来时会自动加八小时，存进数据库时会自动减八小时。

# The default locale is :en and all translations from config/locales/*.rb,yml are auto loaded.

# config.i18n.load_path += Dir[Rails.root.join('my', 'locales', '*.{rb,yml}').to_s]

# config.i18n.default_locale = :de

设定默认的应用程序语系，默认是 :en 。我们会在 ”I18n 多国语系及时区 ” 一章介绍如何使用。

# Configure sensitive parameters which will be filtered from the log file.

config.filter_parameters += [:password]

设定 filter_paramsters 可以避免任何叫做 password 的参数值记录到 log 中，有效防止用户的原始密码外泄到 log 档案。

# Use SQL instead of Active Record's schema dumper when creating the database.

# This is necessary if your schema can't be completely dumped by the schema dumper,

# like if you have constraints or database-specific column types

# config.active_record.schema_format = :sql

每次跑测试的时候，为了节省建立数据库的时间，默认的 schema_format = :ruby 会使用 schema.rb 而不是跑 Migrations 。不过， schema.rb 没办法表达出特定数据库所专属的功能，像是外部键约束（ foreignkey constraints ）、触发（ triggers ）或是预存程序（ stored procedures ）。所以如果你的 Migration 中有自定的 SQL 陈述句，请在这里把 schema 的格式设定成 :sql 。

改用 :sql 的话， Rails 会倒出现有的 development 数据库，产生 #{Rails.env}_structure.sql 档案来作为测试数据库之用 。

# Enforce whitelist mode for mass assignment.

# This will create an empty whitelist of attributes available for mass-assignment for all models

# in your app. As such, your models will need to explicitly whitelist or blacklist accessible

# parameters by using an attr_accessible or attr_protected declaration.

config.active_record.whitelist_attributes = true

 

預設打開*Mass assignment*的白名單設定，詳見安全性一章。

其他初始配置文件 (initialzers)

如果将所有的设定都放到 application.rb 就太混乱了，所以非 Rails 核心的设定，我们会放在 config/initializers 目录下。这个目录下的所有 .rb 档案会在 Rails 启动时都会自动加载执行。默认产生的档案有五个：
backtrace silencers

可以让你选择性地移除例外追踪 (exceptionbacktrace) 讯息，例如有些套件可能会很吵，妨碍你除错。
inflections

Rails 的命名惯例十分倚赖英文的单复数，例如将单数的类别名称 Person 转成复数的表格名称 people 。 Inflector 就是负责将字符串转换成单复数的类别，虽然它内建了一些基本的转换规格，但是英文常常有例外的时候，你可以在这个档案中加上新的规格来做修正。如果你不太确定 Rails 转换的对不对，请进入 console 控制台试试看：

$ rails c

$ Loading development environment (Rails 3.2.8)

$ > "Business".singularize  => "Busines" # 轉單數

$ > "moose".pluralize => "mooses"  # 轉複數

很不幸地这两个例子 Rails 都没转对，这时候你就可以利用 inflections.rb 来修正。

Rails 核心不接受有关单复数转换的单字错误回报，毕竟它不是想做字典 。
mime_types

Rails 默认支持了如下常见的标准 MIME(Multipurpose Internet Mail Extensions) 格式， MIME 被用在 HTTP 通讯协议中的请求标头 Accept 和响应标头 Content-Type 中，来说明此文件的格式。例如 Accept:application/xml,application/xhtml+xml,text/html; 和 Content-Type:text/html; charset=UTF-8 。而 Rails 会在 Controller 的 respond_to 方法中辨识并响应所请求的格式样板，例如浏览器请求 application/xml 就会响应 xml 格式

type/subtype
	

respond_to symbol
	

别名 / 说明

text/html
	

:html, :xhtml
	

application/xhtml+xml

text/plain
	

:text, :txt

text/javascript
	

:js
	

application/javascript, application/x-javascript

text/css
	

:css

text/calendar
	

:ics
	

iCalendar 格式

text/csv
	

:csv

application/xml
	

:xml
	

text/xml, application/x-xml

application/rss+xml
	

:rss

application/atom+xml
	

:atom

application/x-yaml
	

:yaml
	

text/yaml

application/x-www-form-urlencoded
	

:url_encoded_form
	

默认的 HTML forms 格式

multipart/form-data
	

:multipart_form
	

HTML forms 格式 ( 包含二进制文件数据 )

application/json
	

:json
	

text/x-json application/jsonrequest

如果你需要客制，可以在这里注册。
secret_token

这个档案包括了随机数生成的一组 key 用来编码需要保护的 Cookie 讯息 ( 例如下述的 Cookie session) 。修改这组 key 会让已经存放在用户浏览器上的 Cookie Session 和 Signed Cookie 失效。你可以用来强制用户需要重新登入。
sesssion_store

Rails 默认使用了 Cookie 来储存 Session 讯息。它会用上述的 key 编码之后，直接存放在用户浏览器 Cookie 上。除了 Cookie Session ，我们也可以使用 ActiveRecord 储存在数据库中。我们会在 Controller 一章中详细介绍及比较。
环境配置文件

我们在上一节 ” 多重环境设定 ” 曾经介绍不同环境会有不同的配置文件，让我们来更深入看看有哪些设定值，以及这些值是如果影响 Development 、 Production 和 Test 环境的不同：
Development 模式

# In the development environment your application's code is reloaded on

# every request.  This slows down response time but is perfect for development

# since you don't have to restart the webserver when you make code changes.

config.cache_classes = false

使用 Rails 开发可以快速的原因之一，就是当你修改一个小东西，只要重新整理浏览器就可以马上看到修改后的结果。这个秘诀就在于 cache_classes = false 会让每一次的 HTTP 请求都重载类别档案。更仔细的说，当这个值是 false 的时候， Rails 会改用 Ruby 的 load 方法，每次执行都会重载一次。相反地，如果这个值是 true ，则会用 Ruby 的 require 方法，只会在第一次碰到的时候加载，之后碰到 require 相同的档案，就会自动忽略，也就是说如果你启动 Rails 后，档案有修改想看到结果，必须重新启动 Rails 才行，否则无法立即看到结果。

# Log error messages when you accidentally call methods on nil.

config.whiny_nils = true

当你对 nil 呼叫方法时，会出现 NoMethodError 。 whiny_nils= true 会提示你更多讯息来除错。这个值在 production 默认是 false 。

# Show full error reports and disable caching

config.consider_all_requests_local = true

Rails 只有在联机是来自本地端的时候，才会将发生错误时的 Callstack trace 信息给浏览器显示。这个设定将所有联机都当做本地端联机，好让开发模式时所有人联机都可以看到错误讯息。

config.action_controller.perform_caching = false

是否启用 Controller 层级的快取 ( 我们会在 Controller 一章介绍到有哪些快取方法 ) ，一般来说在开发模式不会启用，除非你要测试它。

# Don't care if the mailer can't send

config.action_mailer.raise_delivery_errors = false

如果寄信失败，是否要丢出例外。建议可以改成 true 。

建议可以在开发模式设定 config.action_mailer.perform_deliveries = false ，这样就不会真的寄信出去。我们会再 ActionMailer 一章详细介绍如何实现寄信功能 。

# Print deprecation notices to the Rails logger

config.active_support.deprecation = :log

随着 Rails 版本的升级，如果有方法会在之后的版本中移除， deprecation 会提示你如何因应。这里的 :log 表示会记录到 log 档案中。

# Only use best-standards-support built into browsers

config.action_dispatch.best_standards_support = :builtin

Rails 会在 HTTP Header 中加上 X-UA-Compatible 属性，这个属性可以用来告诉 IE 浏览器去支持最新网页标准，而不是兼容模式。在这里开发模式中这里设成 :builtin 的意思是 IE=edge ，而在 production 模式中默认是 true ，意思是 IE=edge,chrome=1 ，多启用了 Chrome Frame ，如果用户有装 ChromeFrame ，就可以让旧版 IE 浏览器使用 Chrome 的 WebKit 引擎来处理网页，让旧版 IE 也可以使用到现代网页技术。

http://code.google.com/chrome/chromeframe/

# Log the query plan for queries taking more than this (works

# with SQLite, MySQL, and PostgreSQL)

 config.active_record.auto_explain_threshold_in_seconds = 0.5

当 SQL 查询超过 0.5 秒时，自动做 SQLexplain 在 Log 里。

# Do not compress assets

config.assets.compress = false

 

# Expands the lines which load the assets

config.assets.debug = true

以上这两行 Asset Pipeline 的设定会在 Asset 一章中介绍。
Production 模式

# The production environment is meant for finished, "live" apps.

# Code is not reloaded between requests

config.cache_classes = true

cache_classes = true 表示在 production 中，类别档案加载进内存中就快取起来了，大大获得性能。不像在 development 环境中每一次 HTTP 请求就会重载一次。

# Full error reports are disabled and caching is turned on

 config.consider_all_requests_local       = false

config.action_controller.perform_caching = true

不同于 development ，如果在 production 环境出现例外错误，不会显示程序 call stack 讯息，而是回传 public/500.html 页面。

# Disable Rails's static asset server (Apache or nginx will already do this)

config.serve_static_assets = false

不像 development 和 test ，在这里我们会让 Rails 应用服务器关掉对静态档案的响应。在 production 环境中，静态档案应该由性能极佳的 Apache 或 Nginx 网页服务器直接提供档案。我们会在部署一章详细介绍服务器的架构。

# Disable Rails's static asset server (Apache or nginx will already do this)

config.serve_static_assets = false

 

# Compress JavaScripts and CSS

config.assets.compress = true

 

# Don't fallback to assets pipeline if a precompiled asset is missed

config.assets.compile = false

 

# Generate digests for assets URLs

config.assets.digest = true

 

# Defaults to nil and saved in location specified by config.assets.prefix

# config.assets.manifest = YOUR_PATH

 

# Precompile additional assets (application.js, application.css, and all non-JS/CSS are already added)

# config.assets.precompile += %w( search.js )

这几个设定会在 Assets 一章中介绍。

# Specifies the header that your server uses for sending files

# config.action_dispatch.x_sendfile_header = "X-Sendfile" # for apache

# config.action_dispatch.x_sendfile_header = 'X-Accel-Redirect' # for nginx

“X-Sendfile” 是网页服务器提供的功能，可以让下载文件的动作完全委派给网页服务器， Rails 送出 X-Sendfile 标头后就毋需再占住资源。

# Force all access to the app over SSL, use Strict-Transport-Security, and use secure cookies.

# config.force_ssl = true

是否限制全站必须 SSL 才能使用。

# See everything in the log (default is :info)

# config.log_level = :debug

我们在 RESTful 应用程序 一章最后介绍了 Logger 。这里可以设定 Logger 的层级。默认 production 是 :info ，其他则是 :debug

# Use a different logger for distributed setups

# config.logger = ActiveSupport::TaggedLogging.new(SyslogLogger.new)

可以更换掉 Rails 内建的 Logger ，例如换成使用 syslog 的 SyslogLogger 。

# Use a different cache store in production

# config.cache_store = :mem_cache_store

设定不同的快取储存库，默认是 :memory_store ，也就是每个 Rails process 各自用内存存放。业界最常用的则是 memcached 内存快取服务器。

# Enable serving of images, stylesheets, and javascripts from an asset server

# config.action_controller.asset_host = "http://assets.example.com"

默认的静态档案位置是目前主机的 public 目录，你可以透过修改 asset_host 变更位置。例如你的静态档案放在不同台机器或 CDN(Content delivery network) 上。

这就是为什么 Rails 在 View 中会使用 Helper 方法的原因之一，我们不会平铺直叙的写 ，而是使用 <%= image_tag(“rails.png”) %> 目的就在于透过程序来获得修改位置的弹性。其他还包括 stylesheets 、 javascripts 等静态档案都有 Helper 可以使用 。

# Disable delivery errors, bad email addresses will be ignored

# config.action_mailer.raise_delivery_errors = false

 

# Enable threaded mode

# config.threadsafe!

虽然 Rails 支持 thread-safe 模式，不过这里默认是关闭的。 Ruby 1.8 的 thread 由于不是操作系统层级的 thread ，并不会真的使用到多颗 CPU ， Ruby 1.9 虽然是，但是因为某些内部函数库不 thread-safe ，所以多 thread 实际上也是跑在同一颗 CPU 。因此，启用 threaded 模式获得的性能改善有限，但是设定上却麻烦的多，例如你无法使用 Rails 的自动加载类别功能。你也无法在 development 环境中打开。实务上我们会在服务器上执行多个 Rails process ，因此不需要也不建议打开 threaded 模式。

如果您是使用 JRuby ，受益于 Java VM 强大的 Thread 实现，那么就有值得打开 threaded 模式的理由 。

# Enable locale fallbacks for I18n (makes lookups for any locale fall back to

# the I18n.default_locale when a translation can not be found)

config.i18n.fallbacks = true

如果 I18n 翻译档找不到，则找用默认语系的文字。我们会在 I18n 一章详细介绍多国语系功能。

# Send deprecation notices to registered listeners

config.active_support.deprecation = :notify

将 deprecation 讯息传到 Notifications 频道，你可以用以下程序去订阅这个讯息：

ActiveSupport::Notifications.subscribe("deprecation.rails") do |message, callstack|

  # deprecation message

end

如果没有订阅的话，就什么事都不会发生。
Test 模式

# Show full error reports and disable caching

config.consider_all_requests_local       = true

config.action_controller.perform_caching = false

 

# Raise exceptions instead of rendering exception templates

config.action_dispatch.show_exceptions = false

不同于 development 或 production 碰到例外会捕捉例外后，给浏览器显示出 call stack trace 或 public/500.html 画面，在 test 模式就不处理，让例外直接爆出。

# Tell Action Mailer not to deliver emails to the real world.

# The :test delivery method accumulates sent emails in the

# ActionMailer::Base.deliveries array.

config.action_mailer.delivery_method = :test

测试模式下不会真的去寄送 email

# Print deprecation notices to the stderr

config.active_support.deprecation = :stderr

让 deprecation 讯息会直接显示到窗口之中。
数据库配置文件 database.yml

几乎每一个 Rails 应用程序都会与数据库互动。而数据库需要一个配置文件是 config/database.yml 。如果你打开这个档案，你会发现默认设定是 SQLite3 。这个档案包含三个不同的部分，对应到三个 Rails 默认环境。

一个 Mysql 的配置文件范例如下：

development:

  adapter: mysql

  encoding: utf8

  database: blog_development

  username: root

  password:

production:

  adapter: mysql

  encoding: utf8

  database: blog_production

  username: root

  password:

test:

  adapter: mysql

  encoding: utf8

  database: blog_test

  username: root

  password:

Bundler 与 Gemfile 配置文件

Bundler http://gembundler.com/ 是管理应用程序 Gem 相关性 (dependencies) 管理工具，它会根据 Gemfile 的设定自动下载及安装 Gem 套件，并且帮你解决不同套件之间的依存关系，更重要的是，它可以让不同开发者之间和布署时，所有依存套件的版本都能够一致。

在 Rails3 中 (Bundler 不只用在 Rails3 ，其他例如 Sinatra 或是 Rails2 也都可以使用 ) 要使用的 Gems ，都必须宣告在 Gemfile 配置文件中，没写在里面的话，就算手动 require 也找不到。这跟 Rails2 以前可以直接 require 任意 rubygems 不同，在使用 Bundler 的环境中，要 require 什么 rubygems 必须透过 Gemfile 管理。

Gemfile 的写法说明如下：

  # 第二個參數可以指定版本

  gem "rails", "3.2.8" 

 

  # 也可以不指定版本，這樣會安裝最新的穩定版本 (不包括 .pre 或 .rc 結尾的版本)

  gem 'mysql2'

  

  # 如果 require 的檔名不同，可以加上 :require

  gem 'yajl-ruby', :require => 'yajl'

 

  # 可以用 Git 當做來源(根目錄要有 .gemspec 檔案)，甚至可以指定 branch, tag 或 ref。

  gem 'authlogic', :git => 'git://github.com/odorcicd/authlogic.git',

                            :branch => 'rails3'

 

  # 也可以直接用電腦裡的其他目錄

  # gem "rails", :path => '/Users/ihower/github/rails'

 

  # Group 功能可以讓特定環境才會載入

  group :development, :test do

     gem "rspec", "~> 2.0"

     gem "rspec-rails", "~> 2.0"

  end

版号的指定方式除了指定特定版本，还可以指定大于等于 >= 某个版本。不过最建议的方式则是使用 ~> 的语法。 ”~> x.y.z” 的意思是版号 x,y 固定，但可以大于等于 z 。例如 “~> 1.3.5” 的意思是 1.3.5, 1.3.6, 1.3.9 可以安装，但是 1.4.0, 1.5.5, 2.0.1 就不行。这种写法的好处是，通常版号的命名有其惯例： x major 版号升级表示有 API 发生不向后的兼容性变动， y minor 版号升级表示有功能新增， z tiny 版号升级表示 bugs 修正。因此 “~> x.y.z” 可以让我们保有升级弹性，又不致于升级太多让程序发生不兼容错误 。
安装及更新 Gems

如果你修改了这个档案，请执行 bundle install ，这样 Bundler 就会检查并安装这些函式库，并产生一个 Gemfile.lock 档案。 Gemfile.lock 档案会详细列出所有使用到的套件版本，你应该把这个档案也 commit 送进版本控制系统，这样其他开发者及上线的版本就都会安装完全一样的版本了。

执行 bundle update gem_name 则会更新此 gem 的版本。 bundleupdate 则会检查所有的 gem 更新到最新版本。一般来说你只需要在每次 Gemfile 修改后，执行 bundle install 即可。如果有套件关连性 bundle install 无法解决，它会提示你执行 bundle update 。

什么时候该执行 bundle install 或 bundle update 呢 ? 一般来说，总是执行 bundle install 即可。这个指令只会做必要的更新到 Gemfile.lock ，执行速度较快，它不会帮你升级现有的 Gem 。而 bundleupdate 会重新产生整个 Gemfile.lock 档案，更新所有 Gem 到最新版本。但是，一次升级太多套件，可能会造成除错上的困难。因此会建议如果要升级，请执行 bundle update gem_name 一次升级一个套件。

怎么知道可以升级哪些 Gem 呢 ?

bundle outdated    

这个指令就会列出有新版本可以升级的 gems 。
打包 Gems

执行以下指令，会将所有用到的 Gems 打包进 vendor/cache 目录。如此执行 bundle install 时就不会联机到 http://rubygems.org 下载套件。

bundle package

什么时候需要用到这个功能呢 ? 例如你希望布署的时候避免外部联机，或是你有非公开的 gems 不会上传到 http://rubygems.org 网站上。

如果你有非 Rails 的 script 需要执行 ( 也就是放在 Gemfile 档案中的 Gem 所自行提供的执行档 ) ，使用 bundle exec 可以正确的加载 Bundler 的环境。例如 bundle exec rspec spec/
名称惯例

在 Rails 中有一些命名上的惯例：
类别命名与自动加载

档名使用小写、单数，用底线区隔。例如当 Rails 看到一个 OrderItem 的类别或模块 (Module) ，它会在 autoload_paths ( 我们在 config/application.rb 中有此项设定 ) 目录中自动去加载叫做 order_item.rb 的档案，也就是 require “order_item” 。

如果是有嵌套的类别或模块，例如 Admin::OrderItem ，则会多一层目录，它会自动加载 admin/order_item.rb 的档案，也就是 require“admin/order_item” 。

如果你没有设定 autoload_paths 加入 lib 目录，或是你的档案没有依照惯例命名，那么你会需要在程序中手动 require 它。基本上，只要依照命名惯例，你不太需要在程序中写 require 。

autoload_paths 目录是指 Rails 会自动根据命名惯例加载，而 Ruby 的 $LOAD_PATH 常数则是 require 时会寻找的目录。像 lib 这个目录 Rails 默认就只有加到 $LOAD_PATH 之中，所以你放在 lib 的档案是可以 require 到，但是因为默认没有加到 autoload_paths 之中，所以没有自动加载的机制 。
Model 命名

类别名称使用大写、单数，没有底线。而档名使用小写、单数，用底线。数据库表格名称用小写且为复数。例如：

    数据库表格 line_items
    档名 app/models/line_item.rb
    类别名称 LineItem

Controller 命名

假设有一个 store controller 的话：

    档名 app/controllers/store_controller.rb
    类别名称 StoreController

如果需要将 controllers 档案做分类，这时候可以使用 Mobules ，将档案放在子目录下，例如后台专用的 controllers ：

    档名 app/controllers/admin/store_controller.rb
    类别名称 Admin::StoreController

View 命名

例如一个叫做 People 的 controller ，其中的 index action ：

    档名 app/views/people/index.html.erb
    Helper 名称 module PeopleHelper
    档名 app/helpers/people_helper.rb

Rails 组件导览

Rails 包含许多个别的函式库组件：

    Action Pack
    Action Controller
    Action Dispatch
    Action View
    Action Mailer
    Active Model
    Active Record
    Active Resource
    Active Support
    Railties

Action Pack

Action Pack 是个包含 Action Controller 、 ActionView 和 Action Dispatch 的 gem 。也就是 “MVC” 中的 “VC” 部分。
Action Controller

Action Controller 是 Rails 应用程序中，管理 Controllers 的组件。 Action Controller 框架处理传给 Rails 的 HTTP 请求，萃取出参数，然后分派给所属的 Action 。 ActionController 还提供了 session 管理、样板演算显示 (template rendering) 和 redirect 功能。
Action View

Action View 负责 Rails 应用程序中的 Views 。它默认可以产生 HTML 或 XML 输出。 ActionView 负责样板的演算显示 (template rendering) ，包括嵌套 (nesting) 或局部 (partial) 样板，甚至也内建支持一些 Ajax 。
Action Dispatch

Action Dispatch 处理 HTTP 请求的路由 (routing) ，它把 HTTP 请求发派 (dispatch) 到它该去的地方，也许是你的应用程序或其他 Rack 程序。
Action Mailer

Action Mailer 是个建构 E-mail 功能的框架。你可以使用 Action Mailer 来接收来信，或是使用样板来寄出纯文本或复杂的 multipart 信件。
Active Model

Active Model 在 Action Pack gem 和 ORMgem ( 例如 Active Record) 之间定义了一组接口。 Active Model 允许 Rails 可以依你的需求把 Active Record 换成其他 ORM 框架。
Active Record

Active Record 是 Rails 应用程序中的 Models 基础。它不依存特定的数据库系统，提供了 CRUD 功能、先进的查询能力以及可以跟其他 Models 关联的本事。
Active Resource

Active Resource 提供了与其他业务对象和 RESTful 网络服务的链接框架。它实现了一种可以对应以 Web 为基础的 Resources 成为本地端支持 CRUD 的对象。

ActiveResource 可以很简单地实现 SOA 架构示范，但是作为实际用途上则显得薄弱。笔者会建议自行实现客户端程序，请参考 Service-Oriented Design and Implement with Rails3 投影片 。 Rails4 也将移除这个组件 。
Active Support

Active Support 是 Rails 里的工具函式库，它也扩充了一些 Ruby 标准函式库。除了被用在 Rails 核心程序中，你也可以在你的程序中使用。
Railties

Railties 是 Rails 的核心程序代码，用来把以上各种的框架函式库以及 Plugin 全部组合在一起。