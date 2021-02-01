# CMake tutorial (小)
https://cmake.org/cmake/help/latest/guide/tutorial/index.html
---
## はじまりはじまり
```
# このバージョン以上のCMakeを必要とする
cmake_minimum_required(VERSION 3.10)

# プロジェクト名 バージョン バージョン番号
project(Tutorial VERSION 1.0)

# main.cppを実行可能ファイルに加える　executableは実行可能ファイルと言う意味
add_executable(Tutorial main.cpp)
```

### C++のバージョンを指定する
project()の下に以下を追加
```
# C++ standardの指定
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED True)
```

### header fileを設定する
```
# configure_file 入力 出力
configure_file(TutorialConfig.h.in TutorialConfig.h)
```
https://theolizer.com/cpp-school3/cpp-school3-11/  
>configure_fileはバイナリのファイル名やバージョン名など複数のソースへ反映したい様々な設定を１箇所にまとめたい時などによく使う

### バイナリツリーをインクルードに含める
以下をCMakeLists.txtの最後に追加する
```
# configure fileはバイナリツリーに書き込まれるため、
# includeファイルを検索するパスのリストにそのディレクトリを追加する必要がある
target_include_directories(Tutorial PUBLIC
                           "${PROJECT_BINARY_DIR}"
                           )
```
### コンフィグオプションと設定
TutorialConfig.h.in
```
# コンフィグオプションと設定
#define Tutorial_VERSION_MAJOR @Tutorial_VERSION_MAJOR@
#define Tutorial_VERSION_MINOR @Tutorial_VERSION_MINOR@
```
CMakeがこのヘッダファイルを設定すると、@Tutorial_VERSION_MAJOR@と@Tutorial_VERSION_MINOR@の値が置き換えられる

main.cpp
```
#include <iostream>
#include <TutorialConfig.h>

int main(int argc, char const *argv[])
{
  if (argc < 2) {
    // バージョンを表示する
    std::cout << argv[0] << " Version " << Tutorial_VERSION_MAJOR << "."
              << Tutorial_VERSION_MINOR << std::endl;
    std::cout << "Usage: " << argv[0] << " number" << std::endl;
    return 1;
  }
}
```
TutorialConfig.hはconfigure_file(TutorialConfig.h.in TutorialConfig.h)で出力される

###  ビルド
```
// ビルドディレクトリを作成
$ mkdir build
// buildに移動
$ cd build
// cmake CMakeLists.txtのパス
$ cmake ..
// cmake -G ジェネレータ(Makefileやninja,VisualStudioなど) CMakeLists.txtのパス
$ cmake -G "Ninja" ..
```
- ` cmake -G ` でジェネレータのリストが確認できる
- ビルドディレクトリは移動しなくてもCMakeLists.txtでも指定できる
	- https://qiita.com/osamu0329/items/7de2b190df3cfb4ad0ca
- ジェネレーターはコマンドオプション以外に、CMakeLists.txtでも指定できる
	- https://stackoverflow.com/questions/11269833/cmake-selecting-a-generator-within-cmakelists-txt
    	- PreLoad.cmakeを作成し、CMAKE_GENERATOR環境変数にジェネレーター名をセットする

## ライブラリの追加
平方根を扱うライブラリ作成のために,add_libraryでMathFunctionsを指定する
```
# add_library(ライブラリ名 ソース)
add_library(MathFunctions mysqrt.cxx)
```
MathFunctionsをインクルードディレクトリとして追加し、mqsqrt.hを見つけられるようにする
```
add_subdirectory(MathFunctions)
```
実行可能ファイルにMathFunctionsをリンクする
```
target_link_libraries(Tutorial PUBLIC MathFunctions)
```
検索パスにバイナリツリーを追加する
```
target_include_directories(Tutorial PUBLIC
                          "${PROJECT_BINARY_DIR}"
                          "${PROJECT_SOURCE_DIR}/MathFunctions"
                          )
```
### MathFunctionsをオプションにしてみる