##activerecord 的一些保留字

如果一些字段或者方法跟activerecord的重名了,就会报这个错
```
record_changed? is defined by Active Record. Check to make sure that you don't have an attribute or method with the same name.
```

我在升级中遇到的问题更加奇葩,下面具体说明一下

我尝试全局搜索了一下record_changed?这个方法,可是根本找不到,后来我就想这个是不是acticerecord动态生成的方法,果然,具体请看这里
http://api.rubyonrails.org/classes/ActiveModel/Dirty.html
原来activerecord会对每个attribute生成一个changed?方法,举个例子就是name字段就会生成name_changed?方法,record就会生成record_changed?方法,
但是activerecord已经有这个方法了,所以会冲突
https://github.com/rails/rails/blob/4-2-stable/activerecord/lib/active_record/autosave_association.rb#L422
