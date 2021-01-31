cmake_minimum_required(VERSION 3.10)

# プロジェクトネーム バージョン
project(Tutorial VERSION 1.0)

# configure_file 入力 出力
configure_file(TutorialConfig.h.in TutorialConfig.h)

# 実行可能ファイルに加える　executableは実行可能ファイルと言う意味
add_executable(Tutorial main.cpp)

# configure fileはBinaryツリーに書き込まれるため、
# includeファイルを検索するパスのリストにそのディレクトリを追加する必要がある
target_include_directories(Tutorial PUBLIC
                           "${PROJECT_BINARY_DIR}"
                           )