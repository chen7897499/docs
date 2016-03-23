##请遵循restful架构

什么是restful我就懒得说了, 我就说说我在路由里面看到的一些不符合restful的设计误区

比如这个
```ruby
match '/fee_list/(index)' => 'fee_list#index', :via => :get
```

详细请看这篇文章吧http://www.ruanyifeng.com/blog/2011/09/restful, 我就摘抄一段
```
RESTful架构有一些典型的设计误区。
最常见的一种设计错误，就是URI包含动词。因为"资源"表示一种实体，所以应该是名词，URI不应该有动词，动词应该放在HTTP协议中。
举例来说，某个URI是/posts/show/1，其中show是动词，这个URI就设计错了，正确的写法应该是/posts/1，然后用GET方法表示show。
```

所以说你为什么要加这个index呢?
比如这样,这样才是正确的
```
posts GET    /posts(.:format)          posts#index
```

前面那个就会生成这种
```
GET          /fee_list(/index)(.:format)                                                           cmi/fee_list#index
```

还有就是前面的资源名字最好能够表示这个资源是单数还是复数的

比如有时候,你有一个资源是不需要id参数才获取的,举个例子就是,/profile总是显示当前登进来的用户的profile,你就只要用单数了
那就这样写
```
resource :profile
```

他就会生成六个路由
```
GET       /profile/new
POST      /profile
GET       /profile
GET       /profile/edit
PATCH/PUT /profile
DELETE    /profile
```

你想用复数的时候,那就这样写
```
resources :photos
```

就会生成七个路由, 可以用id参数来获取单个资源
```
GET       /photos
GET       /photos/new
POST      /photos
GET       /photos/:id
GET       /photos/:id/edit
PATCH/PUT /photos/:id
DELETE    /photos/:id
```

还有就是有些资源之间是有关系的,比如一张photo有很多comments
那你就可以用命名空间,这样写
```
resources :photos do
  resources :comments
end
```

生成的路由就是这样的
```
GET       /photos/:photo_id/comments
GET       /photos/:photo_id/comments/new
POST      /photos/:photo_id/comments
GET       /photos/:photo_id/comments/:id
GET       /photos/:photo_id/comments/:id/edit
PATCH/PUT /photos/:photo_id/comments/:id
DELETE    /photos/:photo_id/comments/:id
```
这样从url一眼就可以看出关系,不好吗?

还有就是能用resource,就不要写那么多match啊, 有意义么?
你想添加自定义的action,可以这样写的
```
resources :posts do
  collection do
    get 'search'
  end
end
```

生成
```
search_posts GET    /posts/search(.:format)   posts#search
       posts GET    /posts(.:format)          posts#index
             POST   /posts(.:format)          posts#create
    new_post GET    /posts/new(.:format)      posts#new
   edit_post GET    /posts/:id/edit(.:format) posts#edit
        post GET    /posts/:id(.:format)      posts#show
             PUT    /posts/:id(.:format)      posts#update
             DELETE /posts/:id(.:format)      posts#destroy

```

或者这样写
```
resources :posts do
  member do
    get 'search'
  end
end
```

生成
```
search_post GET    /posts/:id/search(.:format) posts#search
      posts GET    /posts(.:format)            posts#index
            POST   /posts(.:format)            posts#create
   new_post GET    /posts/new(.:format)        posts#new
  edit_post GET    /posts/:id/edit(.:format)   posts#edit
       post GET    /posts/:id(.:format)        posts#show
            PUT    /posts/:id(.:format)        posts#update
            DELETE /posts/:id(.:format)        posts#destroy
```


当然虽然前面说url不能有动词,但是在实际场景中还是有例外的,肯定是要允许额外的动词
比如前面这种
```
search_posts GET    /posts/search(.:format)   posts#search
```

当然还是有其他的方法来避免的 比如这样子
```
/users?q={query}
```

