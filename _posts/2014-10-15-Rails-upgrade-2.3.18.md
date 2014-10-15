---
layout: post
title: "Rails upgrade to 2.3.18, fix some warning messages"
date: 2014-10-15 15:17 -0700
comments: false
---

1 DEPRECATION WARNING: Kernel#returning has been deprecated in favor of Object#tap
============

acts_as_paranoid发生的warning

fix before

```
def has_many_without_deleted(association_id, options = {}, &extension)
  with_deleted = options.delete :with_deleted
  **returning has_many_with_deleted(association_id, options, &extension)** do
    if options[:through] && !with_deleted
    reflection = reflect_on_association(association_id)
    collection_reader_method(reflection, Caboose::Acts::HasManyThroughWithoutDeletedAssociation)
    collection_accessor_methods(reflection, Caboose::Acts::HasManyThroughWithoutDeletedAssociation, false)
    end
  end
end
```
  
fix after

```
def has_many_without_deleted(association_id, options = {}, &extension)
  with_deleted = options.delete :with_deleted
  **has_many_with_deleted(association_id, options, &extension).tap** do
    if options[:through] && !with_deleted
    reflection = reflect_on_association(association_id)
    collection_reader_method(reflection, Caboose::Acts::HasManyThroughWithoutDeletedAssociation)
    collection_accessor_methods(reflection, Caboose::Acts::HasManyThroughWithoutDeletedAssociation, false)
    end
  end
end
```

2 DEPRECATION WARNING: Giving :session_key to SessionStore is deprecated, please use :key instead. (called from new at /home/mencio/.gem/ruby/1.8/gems/actionpack-2.3.8/lib/action_controller/middleware_stack.rb:72)
=======

```
config.action_controller.session = {
  :secret_key => 'aaaaa' ,
  :secret => 'tajneprzezpoufne'
}
```


3 DEPRECATION WARNING: ActionView::SafeBuffer is deprecated! Use ActiveSupport::SafeBuffer instead. (called from local_constants at /home/mencio/.gem/ruby/1.8/gems/activesupport-2.3.8/lib/active_support/core_ext/module/introspection.rb:74)
============

代码发生地方是在 lib/fckeditor.rb:

```
include ActionView
module ActionView::Helpers::AssetTagHelper
  alias_method :rails_javascript_include_tag, :javascript_include_tag
 
  #  <%= javascript_include_tag :defaults, :fckeditor %>
  def javascript_include_tag(*sources)
    main_sources, application_source = [], []
    if sources.include?(:fckeditor)
      sources.delete(:fckeditor)
      sources.push('fckeditor/fckeditor')
    end
    unless sources.empty?
      main_sources = rails_javascript_include_tag(*sources).split("\n")
      application_source = main_sources.pop if main_sources.last.include?('application.js')
    end
    [main_sources.join("\n"), application_source].join("\n")
  end
end
```

需要把上面的代码都要注释掉
然后 页面的调用 fckeditor的方法也要修改

```
javascript_include_tag "fckeditor/fckeditor"
```

4 DEPRECATION WARNING: Giving :session_http_only to SessionStore is deprecated, please use :httponly instead. 
============

错误发生的地方是：
config/initializers/session_store.rb
代码如下：

```
ActionController::Base.session = {
:key         => '_src_session',
:session_http_only => true,
:secret      => '0f118033ca9bae289e520c98bbb1f68ef100bec29c7caf25baa7d9e7236876448a1d5c8d7ee384114125b2ae047fefb0d33729ae7476cc3b6b59b5f795d6908a'
}
```

修改为如下的代码：

```
ActionController::Base.session = {
:key         => '_src_session',
:httponly => true,
:secret      => '0f118033ca9bae289e520c98bbb1f68ef100bec29c7caf25baa7d9e7236876448a1d5c8d7ee384114125b2ae047fefb0d33729ae7476cc3b6b59b5f795d6908a'
}
```