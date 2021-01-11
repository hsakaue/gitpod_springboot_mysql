gitpodでmysqlのDockerの起動のしかた  

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


9. 参考URL  
 - Docker構成  
 https://www.gitpod.io/docs/config-docker/  

 - プレビルドの手動実行  
 https://www.gitpod.io/docs/prebuilds/  

 - Gitpodで開発するときのアレコレをまとめておく  
 https://qiita.com/sterashima78/items/fca961e285355715d466  

 - Gitpod で MySQL を勉強する
 https://devlights.hatenablog.com/entry/2021/01/11/031642
