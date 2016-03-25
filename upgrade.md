##rails 4 升级笔记

1. scope的报错: Argument Error: The scope body needs to be callable,
解决方案就是把scope body包在lambda里面,比如:
  ```ruby
  scope :visible, where(:visible => true)
  ```
变成
  ```ruby
  scope :visible, -> {
    where(:visible => true)
  }
  ```

  还可以传参的
  ```ruby
  scope :available, -> (attribute) { where("'model'.attribute NOT LIKE ?", "%**") }
  ```

2. 正则的报错:The provided regular expression is using multiline anchors (^ or $), which may present a security risk. Did you mean to use \A and \z, or forgot to add the :multiline => true option?
解决方案是:
  ```ruby
  /^abcde$/
  ```
  要换成
  ```ruby
  /\Aabcde\z/
  ```

3. model里面set_table_name要换成self.table_name = 了,而且后面的symbol也要换成字符串了,
举个例子就是:
  ```ruby
  class Irm::Person < ActiveRecord::Base
    set_table_name :irm_people
  end
  ```
  要换成
  ```ruby
  class Irm::Person < ActiveRecord::Base
    self.table_name = "irm_people"
  end
  ```

4. 关于路由的报错,请看这篇文档https://github.com/chen7897499/docs/blob/master/routes.md

5. 添加
  ```ruby
  Rails.application.config.assets.precompile += %w( Y.png )
  ```
  到config/initializers/assets.rb


6. Rails::Paths::Path在rails3和rails4当中有很大的不同,具体请看
  http://api.rubyonrails.org/v3.1.2/classes/Rails/Paths/Path.html
  http://api.rubyonrails.org/classes/Rails/Paths/Path.html
  以前是这样的
  ```
  Rails::Paths::Path < Array
  ```
  现在是这样的
  ```
  Rails::Paths::Path < Object
  ```
  所以那些array提供的方法都不能用了,具体修改在config/application.rb
  
7. 以前的
  ```ruby
  require "active_record/railtie"
  require "action_controller/railtie"
  require "action_mailer/railtie"
  require "active_resource/railtie"
  require "sprockets/railtie"
  ```
  换成
  ```ruby
  require "rails/all"
  ```
  
8. 以前的
  ```ruby
  if defined?(Bundler)
    # If you precompile assets before deploying to production, use this line
    Bundler.require(*Rails.groups(:assets => %w(macdev development test)))
    # If you want your assets lazily compiled in production, use this line
    # Bundler.require(:default, :assets, Rails.env)
  end
  ```
  换成
  ```ruby
  Bundler.require(*Rails.groups)
  ```

9. application_helper.rb当中重写的content_for方法在rails4当中需要四个参数了,所以要修改一下,调用super的时候需要四个参数

10. 以前
    ```ruby
    has_many :application_tabs,:order=>:seq_num,:dependent => :destroy
    ```
    要改成
    ```ruby
    has_many :application_tabs, -> { order "seq_num" } , dependent: :destroy
    ```

11. gemfile里面
    ```ruby
    group :assets do
    end
    ```
    完全可以去掉了,在rails 4根本没必要

12. 在gemfile当中要加入两个新gem
    ```
    gem 'activerecord-session_store'
    gem 'delayed_job_active_record'
    ```

13. eager_load rails报的错, 按他的改就是了
    ```
      config.eager_load is set to nil. Please update your config/environments/*.rb files accordingly:
  
    * development - set it to false
    * test - set it to false (unless you use a tool that preloads your test environment)
    * production - set it to true
    ```
    
14. modules/01_framework_fwk/lib/fwk/form_helper.rb当中,rails4当中apply_form_for_options!需要三个参数了,不然会报错,
    以前http://apidock.com/rails/v3.2.3/ActionView/Helpers/FormHelper/apply_form_for_options%21,
    现在http://apidock.com/rails/ActionView/Helpers/FormHelper/apply_form_for_options!


15. rails4当中没有instance_method_names这个方法了,改成这样instance_methods.map(&:to_s)

16. https://github.com/rails/rails/blob/4-2-stable/activerecord/lib/active_record/relation/delegation.rb
    https://github.com/rails/rails/blob/3-2-stable/activerecord/lib/active_record/relation/delegation.rb
  





