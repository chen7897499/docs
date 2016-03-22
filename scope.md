##关于scope要注意的地方

请不要这样
```ruby
scope :visible, where(:visible => true)
```

推荐这样写
```ruby
scope :visible, -> { where(:visible => true) }
```

而且是可以连起来用的
```
class Shirt < ActiveRecord::Base
  scope :red, -> { where(color: 'red') }
  scope :dry_clean_only, -> { joins(:washing_instructions).where('washing_instructions.dry_clean_only = ?', true) }
end
```

把它包在lambda或者proc里面, 要不然rails 4会报这个错误
```
Argument Error: The scope body needs to be callable
```

scope其实就是一个语法糖而已,定义了一个类方法
```
class Shirt < ActiveRecord::Base
  def self.red
    where(color: 'red')
  end
end
```

经测试rails 3.2可用

详细请看http://api.rubyonrails.org/classes/ActiveRecord/Scoping/Named/ClassMethods.html#method-i-scope
