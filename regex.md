##关于在rails当中写正则表达式要注意的地方

最好这样写
```ruby
/\Aabcde\z/
```

不要这样子写
```ruby
/^abcde$/
```
因为这样子写不安全, rails4会报安全错误
具体请看http://guides.rubyonrails.org/security.html#regular-expressions
