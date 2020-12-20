## JavaからSQLを送信する

#### (2020年12月19日よりだいぶ前)

```java
Class.forName("com.mysql.jdbc.Driver");
Connection connection =(Connection)DriverManager.getConnection("jdbc:mysql://localhost/DBNAME",System.getenv("DB_USERNAME"),System.getenv("DB_PASSWD"));
Statement statement = connection.createStatement();
//SELECT文の場合
ResultSet resultSet = statement.executeQuery("SELECT * FROM `TABLE` WHERE `id`='1234'");
//UPDATE,INSERT文の場合
int result = stmt.executeUpdate("INSERT INTO `Users`(`name`, `id`) VALUES ('yuua','yuuaHP')");

String namestr = null;
while (rs.next()) {
    namestr = rs.getString("name");
}
stmt.close();
con.close();
rs.close();

```

