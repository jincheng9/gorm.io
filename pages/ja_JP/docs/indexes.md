---
title: インデックス
layout: page
---

GORMでは `index` や `uniqueIndex` タグを使用して、インデックスを作成することができます。定義されたインデックスは [GORMの AutoMigrate や CreateTable](migration.html) 実行時に作成されます。

## indexタグ

`class`, `type`, `where`, `comment`, `expression`, `sort`, `collate`, `option` など、多くのindexの設定を指定できます。

使用方法については、以下の例を参照してください。

```go
type User struct {
    Name  string `gorm:"index"`
    Name2 string `gorm:"index:idx_name,unique"`
    Name3 string `gorm:"index:,sort:desc,collate:utf8,type:btree,length:10,where:name3 != 'jinzhu'"`
    Name4 string `gorm:"uniqueIndex"`
    Age   int64  `gorm:"index:,class:FULLTEXT,comment:hello \\, world,where:age > 10"`
    Age2  int64  `gorm:"index:,expression:ABS(age)"`
}

// MySQL option
type User struct {
    Name string `gorm:"index:,class:FULLTEXT,option:WITH PARSER ngram INVISIBLE"`
}

// PostgreSQL option
type User struct {
    Name string `gorm:"index:,option:CONCURRENTLY"`
}
```

### uniqueIndex

`uniqueIndex` タグは `index` と似た働きを持ち、 `index:,unique` を指定するのと等しくなります。

```go
type User struct {
    Name1 string `gorm:"uniqueIndex"`
    Name2 string `gorm:"uniqueIndex:idx_name,sort:desc"`
}
```

## 複合インデックス

2つのフィールドに同じインデックス名を使用すると、複合インデックスが作成されます。例：

```go
type User struct {
    Name   string `gorm:"index:idx_member"`
    Number string `gorm:"index:idx_member"`
}
```

### フィールドの優先度

複合インデックスを指定する際、カラムの順序がパフォーマンスに影響を与えます。そのため、順序は慎重に指定する必要があります。

`priority` オプションで順序を指定できます。デフォルトの優先度の値は `10`です。 priorityの値が同じ場合、順序は構造体のフィールドの並びに基づいて設定されます。

```go
type User struct {
    Name   string `gorm:"index:idx_member"`
    Number string `gorm:"index:idx_member"`
}
// column order: name, number

type User struct {
    Name   string `gorm:"index:idx_member,priority:2"`
    Number string `gorm:"index:idx_member,priority:1"`
}
// column order: number, name

type User struct {
    Name   string `gorm:"index:idx_member,priority:12"`
    Number string `gorm:"index:idx_member"`
}
// column order: number, name
```

## 複数のインデックス

1つのフィールドには複数の `index`, `uniqueIndex` タグを指定できます。その場合、1つのフィールドに対して複数のインデックスが作成されます。

```go
type UserIndex struct {
    OID          int64  `gorm:"index:idx_id;index:idx_oid,unique"`
    MemberNumber string `gorm:"index:idx_id"`
}
```
