# ResultSetのgetRow()で０しか返ってこなかった

## 発生した問題

データベース(MySQL)にユーザーID,ユーザー名を保存し、そこからSELECTでID(絶対に重複しない)からユーザーの存在を確認しようとした。

今までResultSetのgetRow()は何もしなくても行数を返すものだと思ってましたが...

```java
Class.forName("com.mysql.jdbc.Driver");
Connection con = (Connection) DriverManager.getConnection(System.getenv("dburl"), System.getenv("dbuser"), System.getenv("dbpass"));
Statement stmt = con.createStatement();
ResultSet rs = stmt.executeQuery("SELECT * FROM `Users` WHERE `id`='1234567890'");

boolean isExist = false;
//存在しなかったらrs.getRow()==0
//存在したらrs.getRow()==1のはず
if (rs.getRow() == 0) {
    isExist = false;
}else{
    isExist = true;
}
stmt.close();
con.close();
rs.close();
//これだと存在しても0が返ってくるので存在しないことになっちゃう
```

| UserName | UserID     |
| -------- | ---------- |
| yuuaHP   | 1234567890 |

(ResultSetがこれだったら何もしなくても1が返ってくると思ってた。)

でも...

**0しか返ってこない**

## 解決

`rs.getRow()`は現在いる行が何番目かを返してくるので(`rs.next()`で次の行)

最後まで進めないと全体の行数が返ってこない。

| 0行目(前までここの番号が返ってきてた)               | UserName | UserID     |
| --------------------------------------------------- | -------- | ---------- |
| **1行目(ここにカーソル(?)を進めれば1が返ってくる)** | yuuaHP   | 1234567890 |

```java
Class.forName("com.mysql.jdbc.Driver");
Connection con = (Connection) DriverManager.getConnection(System.getenv("jydburl"), System.getenv("dbuser"), System.getenv("dbpass"));
Statement stmt = con.createStatement();
ResultSet rs = stmt.executeQuery("SELECT * FROM `Users` WHERE `id`='1234567890'");
//↓ここを修正
int row = 0;
while (rs.next()) {
    row = rs.getRow();
}
//↑ここを修正
boolean isExist = false;
if (row == 0) {
    isExist = false;
}else{
    isExist = true;
}
stmt.close();
con.close();
rs.close();
```

これでちゃんと1か0が返ってくるようになった。

(＾o＾)ﾉ ｲｴｰ!