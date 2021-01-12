# gitpodでmysqlのDockerの起動のしかた  

1. Dockerファイルとymlファイルを準備する
`.gitpod.Dockerfile`と`.gitpod.yml`を用意する  
※gp init でも作成化  

2. ファイルを書き換える  
 - `.gitpod.Dockerfile`
 ```
 FROM gitpod/workspace-mysql

 USER gitpod
 
 RUN sudo apt update && \
    sudo rm -rf /var/lib/apt/lists/*
 ```

 - `.gitpod.yml`  
 ```
 image:
  file: .gitpod.Dockerfile
 
 tasks:
  - init: 'echo "TODO: Replace with init/build command"'
    command: 'echo "TODO: Replace with command to start project"'
 ```

3. Workspaceをリビルドする。  
`gitpod.io/#prebuild/`をWorkspaceのURLに付け加えて開きなおすとプレビルドを手動で実行することができる  
例：`gitpod.io/#prebuild/https://github.com/[ユーザー名]/[リポジトリ名]`  
※リビルドには時間かかります。  

4. リビルド後、Workspaceを開いてmysqlの状態確認をする  
Workspaceを開くとmysqlが起動されている状態のはずなので、  
mysqlにログインして`select 1`してみる  


5. 参考URL  
 - Docker構成  
 https://www.gitpod.io/docs/config-docker/  

 - プレビルドの手動実行  
 https://www.gitpod.io/docs/prebuilds/  

 - Gitpodで開発するときのアレコレをまとめておく  
 https://qiita.com/sterashima78/items/fca961e285355715d466  

 - Gitpod で MySQL を勉強する
 https://devlights.hatenablog.com/entry/2021/01/11/031642


# mysqlへの接続
0. 下記のURLを参考にして進めていく  
https://spring.pleiades.io/guides/gs/accessing-data-mysql/

1. pluginの準備  
Spring Web, Spring Data JPA, MySQLのドライバーを依存関係で準備する  
 →Spring Initializrで準備すると良い  
  https://start.spring.io/  
  
2. DBの作成  
mysqlにログインして下記のようなコマンドを打っていきDB作成、User作成、権限付与していく  
```
create user 'springuser'@'%' identified by 'password';
grant all on mydb.* to springuser;
```

3. Mysqlのポート確認する  
root権限をもつユーザーでWorkspaceにログインしておらずnetstatが使えないので、  
下記のように確認する。  
```
mysql> SHOW GLOBAL VARIABLES LIKE 'PORT';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| port          | 3306  |
+---------------+-------+
1 row in set (0.01 sec)
```

4. 各種ソースを準備する。  
 - application.propertiesを準備する  
  ```
  spring.jpa.hibernate.ddl-auto=update
  spring.datasource.url=jdbc:mysql://localhost:3306/mydb
  spring.datasource.username=springuser
  spring.datasource.password=password
  ```
  
 - モデルとリポジトリ、コントローラーを作成する。  
  上記URLを参考に作る。

5. ビルド＆実行する
 ```
 ./gradlew bootRun
 ~~~~~~~~~~~
 <==========---> 80% EXECUTING [15m 29s]
 > :bootRun
 ```
 上記の状態で、別Terminalを開きApiを叩いていき下記のレスポンスがあればOK  
 ```
 $ curl localhost:8080/demo/add -d name=First -d email=someemail@someemailprovider.com
 Saved
 $ curl 'localhost:8080/demo/all'
 [{"id":1,"name":"First","email":"someemail@someemailprovider.com"}] 
 ```

6. Mysqlで中身を確認する  
 下記のようになってればOK
 ```
 $ mysql
 mysql> show tables from mydb;
 +--------------------+
 | Tables_in_mydb     |
 +--------------------+
 | hibernate_sequence |
 | user               |
 +--------------------+
 2 rows in set (0.00 sec)

 mysql> show columns from user from mydb;
 +-------+--------------+------+-----+---------+-------+
 | Field | Type         | Null | Key | Default | Extra |
 +-------+--------------+------+-----+---------+-------+
 | id    | int          | NO   | PRI | NULL    |       |
 | email | varchar(255) | YES  |     | NULL    |       |
 | name  | varchar(255) | YES  |     | NULL    |       |
 +-------+--------------+------+-----+---------+-------+
 3 rows in set (0.00 sec)

 mysql> use mydb;
 Reading table information for completion of table and column names
 You can turn off this feature to get a quicker startup with -A
 
 Database changed
 mysql> select * from user;
 +----+---------------------------------+-------+
 | id | email                           | name  |
 +----+---------------------------------+-------+
 |  1 | someemail@someemailprovider.com | First |
 +----+---------------------------------+-------+
 1 row in set (0.00 sec)
 ```

 以上