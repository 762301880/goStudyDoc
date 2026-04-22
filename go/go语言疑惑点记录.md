##  为什么go语言方法 只有`func`不像别的语言一样需要`public` `private`

这是 Go 设计里一个**非常“反套路”但很优雅的点**：它压根没有 `public / private` 这种关键字，而是用**名字首字母大小写**来控制可见性。

### 🔑 核心规则（记住这一条就够了）

- **首字母大写 → 对外可见（相当于 public）**
- **首字母小写 → 仅包内可见（相当于 private）**

### 👇 举个最直观的例子

```go
package user

// 对外可见
func Login() {}

// 仅当前包可用
func validatePassword() {}
```

如果你在别的包里：

```go
import "your_project/user"

user.Login()             // ✅ 可以
user.validatePassword()  // ❌ 编译直接报错
```

