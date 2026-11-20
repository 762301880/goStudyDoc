## 关键核心

> **Laravel 是"精装全屋"，Gin 只是"一组路由 + 一个 HTTP 内核"**。ORM、模板、配置、队列、迁移这些 Laravel 自带的东西，Gin 一概不管，需要你自己挑库拼起来。所以学 Gin 不是"换一套语法"，而是"把一个全家桶拆成零件，亲手组装"。先看概念对照：

| Laravel (PHP)                           | Gin (Go)                                  |
| --------------------------------------- | ----------------------------------------- |
| Route::get() · routes/web.php           | r.GET () /r.POST ()・注册路由             |
| Controller@method・类里的方法           | func (c *gin.Context)・一个普通函数       |
| Request $request・只读请求              | *gin.Context・请求 + 响应 + 传值          |
| $request->validate ()・规则数组         | ShouldBindJSON ()・struct 的 binding 标签 |
| response ()->json ()・return 出去       | c.JSON (200, gin.H {})・直接写出 + return |
| Middleware・$next ()・类 + Kernel 注册  | r.Use(HandlerFunc) · c.Next() / c.Abort() |
| Route::prefix ()->group ()・路由分组    | r.Group ("/api/v1")・组上再 Use ()        |
| Eloquent / DB・框架自带                 | GORM /database/sql・自己装，Gin 不管      |
| 容器自动注入 / Facade・隐式魔法         | 显式传参 / 结构体字段・全部看得见         |
| artisan serve · composer.json · PHP-FPM | go run main.go・go.mod・编译成单二进制    |

## 例 1：最小可跑的例子

Laravel 里你要碰两个文件（路由 + 控制器）：

```php
// routes/api.php
Route::get('/hello/{name}', [HelloController::class, 'show']);
// app/Http/Controllers/HelloController.php
public function show(string $name) {
    return response()->json(['msg' => 'Hi ' . $name]);
}
```

Gin 里一个 `main.go` 就够了：

```go
package main
import (
    "net/http"
    "github.com/gin-gonic/gin"
)
func main() {
    r := gin.Default()                       // ≈ new Application + 默认全局中间件(日志/Recovery)
    r.GET("/hello/:name", func(c *gin.Context) {
        name := c.Param("name")              // ≈ 路由参数 {name}
        c.JSON(http.StatusOK, gin.H{"msg": "Hi " + name}) // gin.H 就是 PHP 关联数组
    })
    r.Run(":8080")                          // ≈ php artisan serve
}
```

三个要点：**没有 Controller 类，处理器就是一个普通函数**；`gin.H` 是 `map[string]any` 的别名，等价于你随手写的 PHP 数组；`c.JSON` 是**直接把响应写出去**，函数没有返回值。整个请求链路和 Laravel 是能对上的：

![image-20261105120252447](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20261105120252447.png)



## 例 2：接收参数 + 校验（最常见的落差）

Laravel 用规则数组，失败自动抛 422；Gin 把规则写在**结构体标签**上，错误必须自己处理：

```php
public function store(Request $request) {
    $data = $request->validate([
        'name' => 'required|string|max:50',
        'age'  => 'required|integer|min:1',
    ]);
    return response()->json($data, 201);
}
```

```go
type CreateUserReq struct {
    Name string `json:"name" binding:"required,max=50"`
    Age  int    `json:"age"  binding:"required,min=1"`
}
func CreateUser(c *gin.Context) {
    var req CreateUserReq
    if err := c.ShouldBindJSON(&req); err != nil {   // ≈ validate()，但返回 error
        c.JSON(400, gin.H{"error": err.Error()})
        return                                       // 不 return 就会继续往下执行！
    }
    c.JSON(http.StatusCreated, req)
}

```

取参数的对应关系：`query('page') → c.DefaultQuery("page","1")`、`input('name') → c.PostForm("name")`、`header('X-Token') → c.GetHeader("X-Token")`、`route('id') → c.Param("id")`、`file('avatar') → c.FormFile()` + `c.SaveUploadedFile()`。

## 例 3：中间件 + 路由分组（最容易踩坑的地方）

```php
// Laravel：不调用 $next 就等于拦截
public function handle($request, Closure $next) {
    if (!$request->header('X-Token')) return response()->json(['error'=>'unauth'], 401);
    $request->attributes->set('uid', 1);
    return $next($request);
}
// routes/api.php
Route::prefix('v1')->middleware('auth.token')->group(fn() =>
    Route::get('/users/{id}', [UserController::class, 'show'])
);
```



```go
// Gin：中间件是返回 HandlerFunc 的函数（闭包用来接收配置/依赖）
func AuthToken() gin.HandlerFunc {
    return func(c *gin.Context) {
        if c.GetHeader("X-Token") == "" {
            c.AbortWithStatusJSON(401, gin.H{"error": "unauth"}) // 必须 Abort 才拦得住
            return
        }
        c.Set("uid", 1)   // ≈ $request->attributes->set()
        c.Next()          // ≈ $next()；写在它后面的代码 = 后置逻辑（如统计耗时）
    }
}
func Show(c *gin.Context) {
    uid, _ := c.Get("uid")
    c.JSON(200, gin.H{"id": c.Param("id"), "uid": uid})
}
func main() {
    r := gin.Default()                 // 全局：日志 + panic 恢复
    v1 := r.Group("/v1", AuthToken()) // ≈ prefix + middleware + group 一次搞定
    { v1.GET("/users/:id", Show); v1.POST("/users", CreateUser) }
    r.Run(":8080")
}
```

**最反直觉的一点**：Laravel 里"不调 `$next` 就终止"，Gin 里恰恰相反——中间件函数返回后引擎会自动继续执行下一个 handler，所以拦截**必须** `c.Abort()` / `AbortWithStatusJSON()`。`c.Next()` 只是用来"先把后面的 handler 跑完，再回来做后置处理"。把例 2、例 3 的函数放在同一个 `package main` 下就能直接跑。

## 其余生态对照

| Laravel（自带）             | Gin（生态里要自己装）                                        |
| --------------------------- | ------------------------------------------------------------ |
| Eloquent / Migration        | GORM + `gorm.io/driver/mysql`，或手写 SQL；迁移用 `golang-migrate` |
| Blade                       | `html/template` + `r.LoadHTMLGlob("templates/*")`、`c.HTML()` |
| `.env` + `config()`         | `os.Getenv` / `godotenv` / `viper`                           |
| Log / Session / Auth / CSRF | `log` 或 `zap`；`gin-contrib/sessions`；JWT 用 `golang-jwt`  |
| Artisan / Queue / Scheduler | 无对应物；队列用 `asynq`，定时任务用 cron 库或系统 `crontab` |
| PHPUnit Feature Test        | `httptest` + `r.ServeHTTP(w, req)`，标准库自带               |
| Nginx + PHP-FPM 部署        | `go build` 出一个二进制直接跑（可挂 Nginx 反代）             |