# JDBCType and javaType

## Why should we use JDBCType in mapper.xml.

1. 执行 mapping 文件时，如果有映射参数为空，那么就无法确定它的类型，这时可用jdbcType 来确定类型。
2. mybatis insert/update 字段为 null，无 JDBCType 报错。
3. 参数为泛型时（待定）

| JDBCType | javaType |
| :--- | :--- |
| CHAR | String |
| VARCHAR | String |
| LONGVARCHR | String |
| NUMBER |  |
|  |  |



