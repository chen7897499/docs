##ActiveRecord::ConnectionAdapters::Column找不到primary方法

rails 4.1还是有的,4.2这个方法就被抛弃了
具体见这个commit[https://github.com/rails/rails/commit/05dd3df35db8b2d39ed177f4ccfe112bdfe7726d]

解决办法就是
```ruby
module Fwk::CustomId
  def self.included(base)
    base.class_eval do
      before_create :setup_custom_id

      private
      def setup_custom_id
        id_column = self.class.columns.detect{|i| i.primary}
        if id_column&&id_column.type.eql?(:string)&&id_column.limit>21
          self.id = Fwk::IdGenerator.instance.generate(self.class.table_name)
        end
      end
    end
  end
end
```

变成
```ruby
module Fwk::CustomId
  def self.included(base)
    base.class_eval do
      before_create :setup_custom_id

      private
      def setup_custom_id
        binding.pry
        id_column = self.class.columns.detect{|i| i.name == self.class.primary_key }
        if id_column&&id_column.type.eql?(:string)&&id_column.limit>21
          self.id = Fwk::IdGenerator.instance.generate(self.class.table_name)
        end
      end
    end
  end
end

```
