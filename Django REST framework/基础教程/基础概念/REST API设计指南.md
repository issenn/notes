# 起源

- 网络应用分为前端和后端,众多的智能设备需要适配,急需一种统一的机制方便不同的前端框架与后端通信.
- RESTFUL API由于其结构清晰,符合标准,易于理解,扩展方便,目前已为比较成熟的API设计理论.

# 协议

- 客户端和API通信协议,强推HTTPS

# 域名

- 强推独立域名,如https://api.xxoo.com/
- 如果API比较简单且不会在扩展,可放在主域名下https://ops.xxoo.com/api/

# 版本

- 应该将API的版本号加入URI,如https://api.xxoo.com/v3/
- 当然个人更建议将版本放在HTTP头信息中,因为不同版本可以看作是资源的不同表现形式,应该保证资源的唯一性,如GITHUB

# 路径

- 路径又称为"终点"(endpoint),表示API的具体网址,每个网址表示一种资源
- 所有网址不能有动词,只能有名词,名词应与数据表名对应,且一般为复数形式
```
https://api.xxoo.com/v1/zoos/, 列出所有动物园
https://api.xxoo.com/v1/animals/, 列出所有动物
https://api.xxoo.com/v1/employees/, 列出所有员工
```

# 动词

* 常用的
  - GET, 从服务器取资源
  - POST, 在服务器新建资源
  - PUT, 在服务器更新资源(客户端提供改变后的完整资源)
  - PATCH, 在服务器更新资源(客户端提供改变的属性)
  - DELETE, 从服务器删除资源
* 不常用
  - HEAD, 从服务器取资源元数据
  - OPTIONS, 从服务器取哪些属性是客户端可以改变的
```
GET    https://api.xxoo.com/v1/zoos/, 列出所有动物园
POST   https://api.xxoo.com/v1/zoos/, 新建一个动物园
GET    https://api.xxoo.com/v1/zoos/<id>/, 获取某个动物园
PUT    https://api.xxoo.com/v1/zoos/<id>/, 更新某个动物园(提供全部信息)
PATCH  https://api.xxoo.com/v1/zoos/<id>/, 更新某个动物园(提供部分信息)
DELETE https://api.xxoo.com/v1/zoos/<id>/, 删除某个动物园
GET    https://api.xxoo.com/v1/zoos/<id>/animals/, 获取某个动物园的所有动物
POST   https://api.xxoo.com/v1/zoos/<id>/animals/, 为某个动物园新建一个动物
GET    https://api.xxoo.com/v1/zoos/<id>/animals/<id>/, 获取某个动物园某个动物的信息
PUT    https://api.xxoo.com/v1/zoos/<id>/animals/<id>/, 更新某个动物园某个动物的信息(提供全部信息)
PATCH  https://api.xxoo.com/v1/zoos/<id>/animals/<id>/, 更新某个动物园某个动物的信息(提供部分信息)
DELETE https://api.xxoo.com/v1/zoos/<id>/animals/<id>/, 删除某个动物园某个动物
```

# 过滤信息

> 记录过多,一次性返回会对服务器造成压力,API应该提供参数支持,过滤返回的结果
- ?limit=10, 指定返回记录的数量
- ?offset=10, 指定返回记录的开始位置
- ?page=2&rows=20, 指定第几页,以及每页的记录数
- ?sort=create_time&order=desc, 指定排序属性和排序顺序
- ?animal_id=1, 指定筛选条件,设计上可以存在冗余,其实GET /v1/zoos/<id>/animals?animal_id=1等同于GET /v1/zoos/<id>/animals/<id>/

# 状态码

* 常用的
  - 200, 常用于GET方法,表示服务器成功返回用户请求的数据
  - 201, 常用于POST/PUT/PATCH方法,表示用户新建或修改数据成功
  - 202, 可用于所有方法,表示请求已进入后台排队(异步任务)
  - 204, 常用于DELETE方法,表示数据删除成功
  - 400, 常用于POST/PUT/PATCH,表示发生请求参数有误
  - 401, 可用于所有方法,表示未认证(令牌, 用户名/密码错误)
  - 403, 可用于所有方法,表示未授权(权限不允许)
  - 404, 可用于所有方法,表示目标资源不存在
  - 406, 常用于GET方法,表示服务端不存在请求的数据格式
  - 410, 常用于GET方法,表示请求的资源被永久删除,且不会在得到
  - 422, 常用于POST/PUT/PATCH,表示创建/更新资源时发生异常
  - 500, 可用于所有方法,表示服务端发生错误,请求是否成功未知
* 不常用
  - https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html

# 错误处理

> 如果状态为4xx,应该为用户返回错误信息,一般信息中将error作为键名,出错信息为键值
```
{
    "error": "..."
}
```

# 返回结果

> 针对不同操作,服务器应该返回响应规范的结果
```
GET    https://api.xxoo.com/v1/zoos/, 返回一个数组
POST   https://api.xxoo.com/v1/zoos/, 返回新生成对象
GET    https://api.xxoo.com/v1/zoos/<id>/, 返回查询对象
PUT    https://api.xxoo.com/v1/zoos/<id>/, 返回更新后对象(完整)
PATCH  https://api.xxoo.com/v1/zoos/<id>/, 返回更新后对象(完整)
DELETE https://api.xxoo.com/v1/zoos/<id>/, 返回空白文档
```

# Hypermedia API

> RESTFUL API最好支持Hypermedia API也就是响应中提供链接,连接其它的API方法,引导用户进入下一个endpoint.
- https://api.github.com/

# 文档

- 强烈推荐手动或自动生成一份漂亮的REST API文档.

# 其它

- URL中大小写敏感,不要出现大写字母.
- 使用-而不是_做URL路径中的字符串连接.
- API身份认证依框架而定,[OAuth2.0](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)比较流行.
- 使用Token令牌做身份认证和权限分级,而不是cookie.
- 服务器返回的数据格式应尽量使用JSON,避免使用XML.