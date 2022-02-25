- [Router](#router)
	- [无参数](#无参数)
	- [解析路径参数](#解析路径参数)
	- [获取Query参数](#获取query参数)
	- [获取POST参数](#获取post参数)
	- [Query和POST混合参数](#query和post混合参数)
	- [Map参数(字典参数)](#map参数字典参数)
	- [重定向(Redirect)](#重定向redirect)
	- [分组路由(Grouping Routes)](#分组路由grouping-routes)




# Router

路由方法有 GET, POST, PUT, PATCH, DELETE 和 OPTIONS，还有Any，可匹配以上任意类型的请求。

## 无参数

```go
// 无参数
r.GET("/", func(c *gin.Context) {
	c.String(http.StatusOK, "Who are you?")
})

/*
$ curl http://localhost:9999/
Who are you?
*/
```

## 解析路径参数

有时候我们需要动态的路由，如 /user/:name，通过调用不同的 url 来传入不同的 name。/user/:name/*role，* 代表可选。

```go
r.GET("/user/:name", func(c *gin.Context) {
	name := c.Param("name")
	c.String(http.StatusOK, "Hello %s", name)
})

/*
$ curl http://localhost:9999/user/abcd
Hello abcd
*/
```


## 获取Query参数

```go
// 匹配users?name=xxx&role=xxx，role可选
r.GET("/users", func(c *gin.Context) {
	name := c.Query("name")
	role := c.DefaultQuery("role", "teacher")
	c.String(http.StatusOK, "%s is a %s", name, role)
})

/*
$ curl "http://localhost:9999/users?name=Tom&role=student"
Tom is a student
*/
```

## 获取POST参数

```go
// POST
r.POST("/form", func(c *gin.Context) {
	username := c.PostForm("username")
	password := c.DefaultPostForm("password", "000000") // 可设置默认值

	c.JSON(http.StatusOK, gin.H{
		"username": username,
		"password": password,
	})
})

/*
$ curl http://localhost:9999/form  -X POST -d 'username=geektutu&password=1234'
{"password":"1234","username":"geektutu"}
*/
```

## Query和POST混合参数

```go
// GET 和 POST 混合
r.POST("/posts", func(c *gin.Context) {
	id := c.Query("id")
	page := c.DefaultQuery("page", "0")
	username := c.PostForm("username")
	password := c.DefaultPostForm("username", "000000") // 可设置默认值

	c.JSON(http.StatusOK, gin.H{
		"id":       id,
		"page":     page,
		"username": username,
		"password": password,
	})
})

/*
$ curl "http://localhost:9999/posts?id=9876&page=7"  -X POST -d 'username=geektutu&password=1234'
{"id":"9876","page":"7","password":"1234","username":"geektutu"}
*/
```

## Map参数(字典参数)

```go
r.POST("/post", func(c *gin.Context) {
	ids := c.QueryMap("ids")
	names := c.PostFormMap("names")

	c.JSON(http.StatusOK, gin.H{
		"ids":   ids,
		"names": names,
	})
})

/*
$ curl -g "http://localhost:9999/post?ids[Jack]=001&ids[Tom]=002" -X POST -d 'names[a]=Sam&names[b]=David'
{"ids":{"Jack":"001","Tom":"002"},"names":{"a":"Sam","b":"David"}}
*/
```

## 重定向(Redirect)

```go
r.GET("/redirect", func(c *gin.Context) {
    c.Redirect(http.StatusMovedPermanently, "/index")
})

r.GET("/goindex", func(c *gin.Context) {
	c.Request.URL.Path = "/"
	r.HandleContext(c)
})

/*
$ curl -i http://localhost:9999/redirect
HTTP/1.1 301 Moved Permanently
Content-Type: text/html; charset=utf-8
Location: /
Date: Thu, 08 Aug 2019 17:22:14 GMT
Content-Length: 36

<a href="/">Moved Permanently</a>.

$ curl "http://localhost:9999/goindex"
Who are you?
*/
```

## 分组路由(Grouping Routes)

如果有一组路由，前缀都是/api/v1开头，是否每个路由都需要加上/api/v1这个前缀呢？答案是不需要，分组路由可以解决这个问题。利用分组路由还可以更好地实现权限控制，例如将需要登录鉴权的路由放到同一分组中去，简化权限控制。

```go
// group routes 分组路由
defaultHandler := func(c *gin.Context) {
	c.JSON(http.StatusOK, gin.H{
		"path": c.FullPath(),
	})
}
// group: v1
v1 := r.Group("/v1")
{
	v1.GET("/posts", defaultHandler)
	v1.GET("/series", defaultHandler)
}
// group: v2
v2 := r.Group("/v2")
{
	v2.GET("/posts", defaultHandler)
	v2.GET("/series", defaultHandler)
}

/*
$ curl http://localhost:9999/v1/posts
{"path":"/v1/posts"}
$ curl http://localhost:9999/v2/posts
{"path":"/v2/posts"}
*/
```
