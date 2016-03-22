##rails 3 和 4 当中match语法的变更

在rails3.2当中
```ruby
match "/users/:id" => "users#show"
```
这样子会匹配所有的http动词,但是千万不要这么要,这个不是最佳实践,请一定要指定后面的http动词,
而且在rails4当中这个全部匹配已经废弃了,而且会报警告

类似于这样的
```
/home/vagrant/.rvm/gems/ruby-2.2.3/gems/actionpack-4.2.5/lib/action_dispatch/routing/mapper.rb:238:in `add_request_method': You should not use the `match` method in your router without specifying an HTTP method. (ArgumentError)
If you want to expose your action to both GET and POST, add `via: [:get, :post]` option.
If you want to expose your action to GET, use `get` in the router:
  Instead of: match "controller#action"
  Do: get "controller#action"
```

请这样子写
```ruby
match "/users/:id" => "users#show", via: :get
```
还可以多个http动词写在一起
```
match "/users" => "users#index", via: [:get, :post]
```

还可以这样,这个是rails3.2和4都兼容的
```
get "/users" => "users#index"
post "/users" => "users#index"
```
