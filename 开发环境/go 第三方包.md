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

