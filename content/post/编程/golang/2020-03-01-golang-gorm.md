+++
title="go 数据库| web 编程"
tags=["golang"]
categories=["golang"]
date="2020-10-23T21:00:00+08:00"
+++

## Related
```
指定外键和关联外键

db.Model(&user).Related(&profile)

type Profile struct {
    gorm.Model
    Refer string
    Name  string
}

type User struct {
    gorm.Model
    Profile   Profile `gorm:"ForeignKey:ProfileID;AssociationForeignKey:Refer"`
    ProfileID int
}
```
## Related
```

db.Model(&user).Related(&card, "CreditCard")

指定外键和关联外键

type Profile struct {
  gorm.Model
  Name   string
  UserID uint
}

type User struct {
  gorm.Model
  Refer   string
  Profile Profile `gorm:"ForeignKey:UserID;AssociationForeignKey:Refer"`
}
```