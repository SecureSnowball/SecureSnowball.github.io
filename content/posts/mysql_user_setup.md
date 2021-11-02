+++
categories = ["MySQL"]
date = "2021-11-02"
description = ""
featured = ""
featuredalt = ""
featuredpath = ""
linktitle = ""
slug = "mysql-user-setup"
title = "MySQL User Setup"
type = ["posts","post"]
[ author ]
  name = "Captain Snowball"
+++

## MySQL User Setup
Creating a new user in MySQL and restricting it to limited database to ensure security and other common commands

### Create a user with permission to all databases
```bash
sudo mysql -uroot # Fresh Ubuntu system
```

In the following command `user`, `localhost` and `password` should be replaced with real values
```sql
# MySQL 8 and above
CREATE USER 'user'@'localhost'IDENTIFIED WITH mysql_native_password BY 'password';

# Before MySQL 8
CREATE USER 'user'@'localhost' IDENTIFIED BY 'password';
```

### Grant full access to all databases

```sql
GRANT ALL PRIVILEGES ON * . * TO 'user'@'localhost';
FLUSH PRIVILEGES;
```

### Grant full access to only one databases
This is more common since it is best practice to at least create one user per project. Replace `dbname` with real database name

```sql
GRANT ALL PRIVILEGES ON dbname.* TO 'user'@'localhost';
```

For example process for a project called presentation should be following
```sql
CREATE DATABASE presentation;
CREATE USER 'presentation_user'@'localhost' IDENTIFIED WITH mysql_native_password BY 'presentation_password';
GRANT ALL PRIVILEGES ON presentation.* TO 'user'@'localhost';
FLUSH PRIVILEGES;
```

### Grant read only access to only one databases
Useful for auditing use case where want to share read access but don't want to share write access
```sql
GRANT SELECT, SHOW VIEW ON dbname.* TO 'user'@'localhost';
FLUSH PRIVILEGES;
```