---
title: Membuat
layout: page
---
## Buat Catatan

```go
user := User{Name: "Jinzhu", Age: 18, Birthday: time.Now()}

db.NewRecord(user) // => returns `true` as primary key is blank

db.Create(&user)

db.NewRecord(user) // => return `false` after `user` created
```

## Nilai Bawaan

Anda dapat menetapkan nilai bidang bawaan dengan label, sebagai contoh:

```go
type Animal struct {
    ID   int64
    Name string `gorm:"default:'galeone'"`
    Age  int64
}
```

Kemudian memasukkan SQL akan mengecualikan bidang tersebut yang tidak memiliki nilai atau memiliki [nilai nol](https://tour.golang.org/basics/12), setelah memasukkan record ke dalam database, gorm akan memuat nilai field tersebut dari database.

```go
var animal = Animal{Age: 99, Name: ""}
db.Create(&animal)
// INSERT INTO animals("age") values('99');
// SELECT name from animals WHERE ID=111; // the returning primary key is 111
// animal.Name => 'galeone'
```

**NOTE** all fields having zero value, like ``, `''`, `false` or other [zero values](https://tour.golang.org/basics/12) won't be saved into database but will use its default value, it you want to avoid this, consider to use pointer type or scaner/valuer, e.g:

```go
// Use pointer value
type User struct {
  gorm.Model
  Name string
  Age  *int `gorm:"default:18"`
}

// Use scanner/valuer
type User struct {
  gorm.Model
  Name string
  Age  sql.NullInt64 `gorm:"default:18"`
}
```

## Menetapkan Nilai Bidang Dalam Hooks

Jika Anda ingin memperbarui nilai bidang di hook `BeforeCreate`, Anda bisa menggunakan `scope.SetColumn`, misalnya:

```go
func (user *User) BeforeCreate(scope *gorm.Scope) error {
  scope.SetColumn("ID", uuid.New())
  return nil
}
```

## Pilihan Membuat Tambahan

```go
// Add extra SQL option for inserting SQL
db.Set("gorm:insert_option", "ON CONFLICT").Create(&product)
// INSERT INTO products (name, code) VALUES ("name", "code") ON CONFLICT;
```