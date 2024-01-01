# dotnet_nuget_create_package_example1

## 概要

* Nuget パッケージを作成するサンプル
* 作成したパッケージをローカルで利用する
* 同一バージョンのままパッケージを更新する

## 参考

dotnet CLI を使用して NuGet パッケージを作成する  
https://learn.microsoft.com/ja-jp/nuget/create-packages/creating-a-package-dotnet-cli

## パッケージ作成

```ps1
dotnet new classlib -o ./HelloNugetPackage
# dotnet pack ./HelloNugetPackage
dotnet build ./HelloNugetPackage -c Release
```
※pack コマンドだとコードを変更して再実行しても既に生成済みの場合は再生成されない。  
　（たぶん同一バージョンだとダメなんだと思われ）  
　build コマンドであれば常に再生成されるので無難。  
　ただし、build コマンドでパッケージ生成させるにはプロジェクトファイルに以下の記述が必用。
  ```xml
  <PropertyGroup>
    <!-- ビルド時に自動的にパッケージを生成する -->
    <GeneratePackageOnBuild>true</GeneratePackageOnBuild>
  </PropertyGroup>
  ```

## 作成したパッケージをローカルで使用

nuget.config
```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <add key="LocalNuget" value="HelloNugetPackage\bin\Release" />
  </packageSources>
</configuration>
```

```
dotnet new console -o ./UseHelloNugetPackageExample
dotnet add ./UseHelloNugetPackageExample package com.github.Tobotobo.HelloNugetPackage --version 1.0.0
dotnet run --project ./UseHelloNugetPackageExample
```

## パッケージの更新　※バージョン変更なしで

通常はバージョンを変えてパッケージを作成すればよいが、開発中は同じバージョンのまま中身だけ更新したい場合がある。    
一度利用したパッケージはキャッシュされてしまうので、リビルドしても反映されない。  
手動または以下のようなコマンドでキャッシュから削除する必要がある。

cmd
```bat
rmdir /s "%USERPROFILE%\.nuget\packages\com.github.tobotobo.hellonugetpackage"
```

powershell
```ps1
Remove-Item -Path "$env:USERPROFILE\.nuget\packages\com.github.tobotobo.hellonugetpackage" -Recurse -Confirm
```



