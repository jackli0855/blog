---
layout: post
title: "rails rescue_from"
date: 2015-03-04 13:00
comments: false
---

rescue_from

rescue_from可以在Controller中宣告救回例外，例如：

class ApplicationController < ActionController::Base
 
    rescue_from ActiveRecord::RecordNotFound, :with => :show_not_found
    rescue_from ActiveRecord::RecordInvalid, :with => :show_error
 
    protected
 
    def show_not_found
        # render something
    end
 
    def show_error
        # render something
    end
 
end
HTTP Basic Authenticate

Rails内建支持HTTP Basic Authenticate，可以很简单实现出认证功能：

class PostsController < ApplicationController
    before_filter :authenticate
 
    protected
 
    def authenticate
     authenticate_or_request_with_http_basic do |username, password|
       username == "foo" && password == "bar"
     end
    end
end
或是这样写：

class PostsController < ApplicationController
    http_basic_authenticate_with :name => "foo", :password => "bar"
end