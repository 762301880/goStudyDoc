##  [官方包仓库](https://pkg.go.dev)



## [kardianos](https://github.com/kardianos)/**[govendor](https://github.com/kardianos/govendor)**  包管理工具

**参考资料**

| 名称 | 地址                                           |
| ---- | ---------------------------------------------- |
| 博客 | [link](https://zhuanlan.zhihu.com/p/103914406) |



##  Go-MySQL 包-go-sql-driver

| name               | url                                                          |
| ------------------ | ------------------------------------------------------------ |
| go-sql-drive包地址 | [link](https://pkg.go.dev/github.com/go-sql-driver/mysql#section-readme) |

### 安装

> 使用shell 中的[go 工具](https://golang.org/cmd/go/)简单地将软件包安装到[$GOPATH](https://github.com/golang/go/wiki/GOPATH)中：

```shell
go get -u github.com/go-sql-driver/mysql
```

**基本使用**

```go
package main

import (
	"database/sql"
	"fmt"
	_ "github.com/go-sql-driver/mysql"
	"log"
)

const (
	dbDriver = "mysql"
	dbUser   = "root"          //your_username
	dbPass   = "123456"        //your_password
	dbName   = "laravel_study" //your_database_name
)

func main() {
	// 连接数据库
	db, err := sql.Open(dbDriver, fmt.Sprintf("%s:%s@/%s", dbUser, dbPass, dbName))
	if err != nil {
		log.Fatal(err)
	}
	defer db.Close()

	// 测试连接
	err = db.Ping()
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("Connected to the database")

	//// 创建表
	//createTableQuery := `
	//	CREATE TABLE IF NOT EXISTS users (
	//		id INT AUTO_INCREMENT PRIMARY KEY,
	//		username VARCHAR(50) NOT NULL,
	//		email VARCHAR(100) NOT NULL
	//	)
	//`
	//_, err = db.Exec(createTableQuery)
	//if err != nil {
	//	log.Fatal(err)
	//}
	//fmt.Println("Table created successfully")

	// 插入数据
	insertDataQuery := "INSERT INTO users (username, email) VALUES (?, ?)"
	_, err = db.Exec(insertDataQuery, "user1", "user1@example.com")
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println("Data inserted successfully")

	// 查询数据
	query := "SELECT id, username, email FROM users"
	rows, err := db.Query(query)
	if err != nil {
		log.Fatal(err)
	}
	defer rows.Close()

	fmt.Println("Query results:")
	for rows.Next() {
		var id int
		var username, email string
		err := rows.Scan(&id, &username, &email)
		if err != nil {
			log.Fatal(err)
		}
		fmt.Printf("ID: %d, Username: %s, Email: %s\n", id, username, email)
	}

	//// 更新数据
	//updateDataQuery := "UPDATE users SET email = ? WHERE username = ?"
	//_, err = db.Exec(updateDataQuery, "new_email@example.com", "user1")
	//if err != nil {
	//	log.Fatal(err)
	//}
	//fmt.Println("Data updated successfully")

	//// 删除数据
	//deleteDataQuery := "DELETE FROM users WHERE username = ?"
	//_, err = db.Exec(deleteDataQuery, "user1")
	//if err != nil {
	//	log.Fatal(err)
	//}
	//fmt.Println("Data deleted successfully")
}

```

**添加地址&驱动**

> 在Go中，`github.com/go-sql-driver/mysql`包是一个MySQL数据库驱动，它主要用于连接MySQL数据库和执行SQL操作。该包的主要作用是通过DSN（数据源名称）来指定数据库的连接信息，包括数据库的IP地址、端口、用户名、密码等。
>
> DSN的格式通常为：
>
> > MySQL DSN的全称是数据源名称（Data Source Name），它是一种标识数据源的名称，可以MySQL DSN的全称是数据源名称（Data Source Name），它是一种标识数据源的名称，可以方便地连接到数据库。更具体来说，MySQL的DSN字符串格式示例为：`"<username>:<password>@tcp(<hostname>:<port>)/<database_name>?charset=utf8mb4&parseTime=True&loc=Local"`。其中，`<username>`是MySQL服务器的用户名，`<password>`是MySQL服务器的密码，`<hostname>`是MySQL服务器的主机名或IP地址，`<port>`是MySQL服务器的端口号，`<database_name>`是要连接的数据库名称。
>
> ```shell
> username:password@protocol(address)/dbname?param=value
> ```
>
> 其中，`address`部分就包括了IP地址。所以，你可以通过修改DSN中的`address`部分来更改MySQL数据库的IP地址。下面是一个简单的示例：

```go
package main

import (
	"database/sql"
	"fmt"
	"log"

	_ "github.com/go-sql-driver/mysql"
)

const (
	dbDriver = "mysql"
	dbUser   = "your_username"
	dbPass   = "your_password"
	dbName   = "your_database_name"
	dbHost   = "new_ip_address" // 修改为新的IP地址
	dbPort   = "3306"            // 修改为新的端口号
)

func main() {
	// 构建新的DSN
	dsn := fmt.Sprintf("%s:%s@tcp(%s:%s)/%s", dbUser, dbPass, dbHost, dbPort, dbName)

	// 连接数据库
	db, err := sql.Open(dbDriver, dsn)
	if err != nil {
		log.Fatal(err)
	}
	defer db.Close()

	// 其他数据库操作...
}
```

> 在上面的示例中，你需要将`new_ip_address`替换为你想连接的新MySQL数据库的IP地址，并根据需要修改端口号。然后，通过构建新的DSN来连接数据库。
>
> 请注意，这只是一个示例，实际应用中你可能还需要根据需要进行其他配置项的调整。在修改连接信息时，确保考虑到网络安全性和访问权限等因素。