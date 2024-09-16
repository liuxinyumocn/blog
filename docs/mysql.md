# MySQL 实用笔记

## 1.导入CSV到表中

1. 准备 CSV 文件

首先，您需要准备一个包含数据的 CSV 文件。确保文件中的列与您要导入的 MySQL 表的列匹配。

2. 创建 MySQL 表

在 MySQL 中创建一个表，用于存储 CSV 文件中的数据。您可以使用以下命令创建一个名为 `mytable` 的表：

```sql
CREATE TABLE mytable (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255),
    age INT,
    email VARCHAR(255)
);
```

请注意，这里的表结构仅供参考，您需要根据您的 CSV 文件的列来调整表结构。

3. 使用 LOAD DATA 命令导入数据

接下来，使用 MySQL 的 `LOAD DATA` 命令将 CSV 文件中的数据导入 MySQL 表中。以下是一个示例命令：

```sql
LOAD DATA LOCAL INFILE '/path/to/your/csv/file.csv'
INTO TABLE mytable
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;
```

请注意，这里的 `/path/to/your/csv/file.csv` 应该替换为您的 CSV 文件的实际路径。如果您在 Windows 上运行 MySQL，您需要使用双反斜杠 `\\` 来表示文件路径。

此外，`FIELDS TERMINATED BY ','` 表示 CSV 文件中字段之间的分隔符为逗号，`ENCLOSED BY '"'` 表示字段值用双引号括起来，`LINES TERMINATED BY '\n'` 表示行之间的分隔符为换行符，`IGNORE 1 ROWS` 表示忽略 CSV 文件中的第一行标题。

4. 检查导入结果

导入完成后，您可以使用 `SELECT` 命令检查导入的数据是否正确。例如：

```sql
SELECT * FROM mytable;
```

如果您看到正确的结果，则说明您已经成功将 CSV 数据导入到 MySQL 表中。

## 批量导出数据表

导出为SQL
```sh
mysqldump -u username -p database_name > xxx.sql
```

从SQL导入到库
```sh
mysql -u username -p database_name < xxx.sql
```

新的库需要创建后再导入

```sh
CREATE DATABASE `database_name`;
```