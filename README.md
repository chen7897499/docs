##rails升级笔记

###关于如何去掉rails插件的弃用警告

1.  对于风格比较简单的插件,可以在lib目录下建一个与插件同名的文件夹, 把原插件lib目录下的文件都丢到这个里面去,然后把插件目录下的init.rb重命名为插件名.rb,移动到/config/initializers/这个目录,当然还要在这个文件里require一下那些lib目录下的文件,具体详见http://matt.coneybeare.me/how-to-convert-simple-rails-23-style-plugins/
2. 对于一些带erb的插件,因为移动到了lib目录下,所以需要把这些erb的新目录添加到view_path,不然的话会报类似于这种错误:
	```
	Missing partial multilingual/edit with {:locale=>[:zh, :en], :formats=>[:html], :handlers=>[:erb, :builder, :coffee]}. Searched in:
	  * "/home/vagrant/data/hisuite/modules/180_cmi_cmi/app/views"
	  * "/home/vagrant/data/hisuite/modules/130_thing_gtd/app/views"
	  * "/home/vagrant/data/hisuite/modules/80_bootstrap_bsp/app/views"
	  * "/home/vagrant/data/hisuite/modules/70_service_slm/app/views"
	  * "/home/vagrant/data/hisuite/modules/60_config_com/app/views"
	  * "/home/vagrant/data/hisuite/modules/50_change_chm/reports/views"
	  * "/home/vagrant/data/hisuite/modules/50_change_chm/app/views"
	  * "/home/vagrant/data/hisuite/modules/40_survey_csi/reports/views"
	  * "/home/vagrant/data/hisuite/modules/40_survey_csi/app/views"
	  * "/home/vagrant/data/hisuite/modules/30_knowledge_skm/reports/views"
	  * "/home/vagrant/data/hisuite/modules/30_knowledge_skm/app/views"
	  * "/home/vagrant/data/hisuite/modules/20_incident_icm/reports/views"
	  * "/home/vagrant/data/hisuite/modules/20_incident_icm/app/views"
	  * "/home/vagrant/data/hisuite/modules/10_ironmine_irm/reports/views"
	  * "/home/vagrant/data/hisuite/modules/10_ironmine_irm/app/views"
	  * "/home/vagrant/data/hisuite/app/views"
	  * "/home/vagrant/data/hisuite/vendor/plugins/acts_as_task/app/views"
	  * "/home/vagrant/.rvm/gems/ruby-2.2.3/gems/twitter-bootstrap-rails-3.2.2/app/views"
	  ```
	  目前的解决方案是在applicationcontroller中建一个before_filter,自动添加到view_path
	```ruby
	before_filter :append_view_paths
	
	def append_view_paths
	    append_view_path Rails.root + "./lib/acts_as_multilingual/app/views"
	    append_view_path Rails.root + "./lib/acts_as_task/app/views"
	end
	```
3. 第三种情况就是这个插件不仅是插件还是gem,自带了gemspec,比如themes_for_rails这个插件,参考这里https://bibwild.wordpress.com/2012/04/25/converting-a-more-complex-rails-2-3-style-plugin-to-non-deprecated-under-rails-3-2/, 处理的办法是:   

	```
	git mv ./vendor/plugins/themes_for_rails ./themes_for_rails
	```   
	
	然后在Gemfile当中   
	
	```
	gem "themes_for_rails", :path => './themes_for_rails'
	```


###关于一些语法的变更

1.activesupport concern的变更
	以前这样
	```ruby
	require 'active_support/concern'
	
	module Foo
	  extend ActiveSupport::Concern
	
	  module ClassMethods
	  	def bar
	  	  puts "class methods"
	  	end
	  end
	
	  module InstanceMethods
		  def baz
		  	puts "instance methods"
		  end
		end
	end
	
	class Test
		include Foo
	end
	
	Test.new.baz
	
	Test.bar
	```
	
	现在这样
	```ruby
	require 'active_support/concern'
	
	module Foo
	  extend ActiveSupport::Concern
	
	  module ClassMethods
	  	def bar
	  	  puts "class methods"
	  	end
	  end
	
	  def baz
	  	puts "instance methods"
	  end
	end
	
	class Test
		include Foo
	end
	
	Test.new.baz
	
	Test.bar
	```

###一些其他的警告信息

1. hz2py这个gem会报这种错误
	```
	/home/vagrant/.rvm/gems/ruby-2.2.3/bundler/gems/hz2py-801e632c02a9/lib/elvuel/traditional_and_simplified.rb:5: warning: duplicated key at line 5 ignored: "壟"
	/home/vagrant/.rvm/gems/ruby-2.2.3/bundler/gems/hz2py-801e632c02a9/lib/elvuel/traditional_and_simplified.rb:5: warning: duplicated key at line 5 ignored: "墊"
	/home/vagrant/.rvm/gems/ruby-2.2.3/bundler/gems/hz2py-801e632c02a9/lib/elvuel/traditional_and_simplified.rb:5: warning: duplicated key at line 5 ignored: "謔"
	/home/vagrant/.rvm/gems/ruby-2.2.3/bundler/gems/hz2py-801e632c02a9/lib/elvuel/traditional_and_simplified.rb:5: warning: duplicated key at line 5 ignored: "僥"
	/home/vagrant/.rvm/gems/ruby-2.2.3/bundler/gems/hz2py-801e632c02a9/lib/elvuel/traditional_and_simplified.rb:5: warning: duplicated key at line 5 ignored: "濫"
	/home/vagrant/.rvm/gems/ruby-2.2.3/bundler/gems/hz2py-801e632c02a9/lib/elvuel/traditional_and_simplified.rb:5: warning: duplicated key at line 5 ignored: "寧"
	/home/vagrant/.rvm/gems/ruby-2.2.3/bundler/gems/hz2py-801e632c02a9/lib/elvuel/traditional_and_simplified.rb:5: warning: duplicated key at line 5 ignored: "滾"
	/home/vagrant/.rvm/gems/ruby-2.2.3/bundler/gems/hz2py-801e632c02a9/lib/elvuel/traditional_and_simplified.rb:7: warning: duplicated key at line 7 ignored: "冲"
	``` 
	这个错误出现的原因是hash当中出现了重复的key,解决方法是fork一下这个gem,然后在Gemfile当中这样指定
	```
	gem 'hz2py', '1.0.0', :git => 'https://github.com/chen7897499/hz2py.git'
	```

2. 关于 warning: circular argument reference - now这种警告, 参考这里https://github.com/riseshia/neroscape/issues/9, 貌似升级rails版本就没事了



