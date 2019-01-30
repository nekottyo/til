gorm で primary key を uuid にしたい
=====

http://doc.gorm.io/callbacks.html
http://gorm.io/docs/update.html#Change-Values-In-Hooks

`BeforeCreate()` で SetColumn を呼んで設定してあげる

```go
import (
	_ "github.com/go-sql-driver/mysql"
	"github.com/google/uuid"
	"github.com/jinzhu/gorm"
)

type User struct {
	ID uuid.UUID `gorm:"primary_key;type:char(36)"`

	Name string
}

func (u *User) BeforeCreate(scope *gorm.Scope) err {
	uuid := uuid.New()
	scope.SetColumn("ID", uuid)
	return nil
}
```
