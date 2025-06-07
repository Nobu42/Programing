# 毎日のプログラミング

## Python版：「タスク管理コマンドラインツール（簡易版）」

```
# タスクを管理する簡単なCLIツール
# ユーザーはタスクを追加・表示・削除できる

# sysモジュールを使ってコマンドライン引数を扱う
import sys

# タスクを保存するリスト
tasks = []

# コマンドヘルプを表示する関数
def show_help():
    print("コマンド一覧:")
    print("  add <タスク名>    : タスクを追加")
    print("  list              : タスクを一覧表示")
    print("  delete <番号>     : 指定した番号のタスクを削除")
    print("  help              : コマンド一覧を表示")
    print("  exit              : プログラムを終了")

# タスクを追加する関数
def add_task(task_name):
    tasks.append(task_name)
    print(f"タスクを追加しました: {task_name}")

# タスクを一覧表示する関数
def list_tasks():
    if not tasks:
        print("タスクはありません。")
    else:
        print("現在のタスク:")
        for idx, task in enumerate(tasks, 1):
            print(f"{idx}. {task}")

# タスクを削除する関数
def delete_task(index):
    try:
        removed = tasks.pop(index - 1)
        print(f"タスクを削除しました: {removed}")
    except IndexError:
        print("指定された番号のタスクは存在しません。")

# メイン処理部（インタラクティブにコマンドを受け付ける）
def main():
    print("簡易タスク管理ツールへようこそ！ 'help' と入力するとコマンド一覧を表示します。")
    while True:
        command = input("> ").strip()
        if command.startswith("add "):
            task = command[4:]
            add_task(task)
        elif command == "list":
            list_tasks()
        elif command.startswith("delete "):
            try:
                index = int(command[7:])
                delete_task(index)
            except ValueError:
                print("削除には数値を指定してください。")
        elif command == "help":
            show_help()
        elif command == "exit":
            print("終了します。お疲れさまでした！")
            break
        else:
            print("不明なコマンドです。'help' を入力してください。")

# プログラムのエントリーポイント
if __name__ == "__main__":
    main()
```

## Go版：「タスク管理コマンドラインツール（簡易版）」

```
// 簡単なタスク管理ツール（CLIベース）
// タスクの追加・表示・削除が可能

package main

import (
	"bufio"
	"fmt"
	"os"
	"strconv"
	"strings"
)

// タスクを格納するスライス
var tasks []string

// ヘルプメッセージを表示
func showHelp() {
	fmt.Println("コマンド一覧:")
	fmt.Println("  add <タスク名>    : タスクを追加")
	fmt.Println("  list              : タスクを一覧表示")
	fmt.Println("  delete <番号>     : タスクを削除")
	fmt.Println("  help              : このメッセージを表示")
	fmt.Println("  exit              : プログラム終了")
}

// タスクを追加
func addTask(task string) {
	tasks = append(tasks, task)
	fmt.Println("タスクを追加しました:", task)
}

// タスクを一覧表示
func listTasks() {
	if len(tasks) == 0 {
		fmt.Println("タスクはありません。")
	} else {
		fmt.Println("現在のタスク:")
		for i, task := range tasks {
			fmt.Printf("%d. %s\n", i+1, task)
		}
	}
}

// タスクを削除
func deleteTask(index int) {
	if index < 1 || index > len(tasks) {
		fmt.Println("その番号のタスクは存在しません。")
		return
	}
	task := tasks[index-1]
	tasks = append(tasks[:index-1], tasks[index:]...)
	fmt.Println("タスクを削除しました:", task)
}

// メイン処理
func main() {
	fmt.Println("簡易タスク管理ツールへようこそ！ 'help' を入力するとコマンドが表示されます。")
	scanner := bufio.NewScanner(os.Stdin)

	for {
		fmt.Print("> ")
		if !scanner.Scan() {
			break
		}
		line := strings.TrimSpace(scanner.Text())

		switch {
		case strings.HasPrefix(line, "add "):
			addTask(line[4:])
		case line == "list":
			listTasks()
		case strings.HasPrefix(line, "delete "):
			numStr := strings.TrimSpace(line[7:])
			index, err := strconv.Atoi(numStr)
			if err != nil {
				fmt.Println("削除する番号は整数で入力してください。")
			} else {
				deleteTask(index)
			}
		case line == "help":
			showHelp()
		case line == "exit":
			fmt.Println("終了します。お疲れさまでした！")
			return
		default:
			fmt.Println("不明なコマンドです。'help' を入力してください。")
		}
	}
}
```

## テーマ：「バブルソートを可視化して理解する」Python版

```
# バブルソートの動作を可視化しながら学ぶ
# 一番基本的なソートアルゴリズムのひとつ

import time

# リストの状態を可視化する関数
def show_list(data, current=-1, next=-1):
    for i, val in enumerate(data):
        # 比較中の要素にはマークをつける
        if i == current:
            print(f"[{val}]", end=" ")
        elif i == next:
            print(f"({val})", end=" ")
        else:
            print(f" {val} ", end=" ")
    print()

# バブルソートの本体
def bubble_sort(data):
    n = len(data)
    print("ソート前:")
    show_list(data)
    print("-" * 40)

    for i in range(n):
        for j in range(n - i - 1):
            show_list(data, j, j + 1)
            time.sleep(0.5)  # ゆっくり表示

            # 隣同士を比較して必要なら交換
            if data[j] > data[j + 1]:
                data[j], data[j + 1] = data[j + 1], data[j]

    print("-" * 40)
    print("ソート後:")
    show_list(data)

# メイン処理
if __name__ == "__main__":
    numbers = [8, 3, 5, 1, 9, 6]
    bubble_sort(numbers)
```

## テーマ：「バブルソートを可視化して理解する」Go版

```
// バブルソートの動きを表示しながら学ぶ
// 配列の基本的なソートの理解に最適

package main

import (
	"fmt"
	"time"
)

// 配列の状態を表示（比較中のインデックスにマーク）
func showList(data []int, current, next int) {
	for i, v := range data {
		if i == current {
			fmt.Printf("[%d] ", v)
		} else if i == next {
			fmt.Printf("(%d) ", v)
		} else {
			fmt.Printf(" %d  ", v)
		}
	}
	fmt.Println()
}

// バブルソート本体
func bubbleSort(data []int) {
	n := len(data)
	fmt.Println("ソート前:")
	showList(data, -1, -1)
	fmt.Println("--------------------------------")

	for i := 0; i < n; i++ {
		for j := 0; j < n-i-1; j++ {
			showList(data, j, j+1)
			time.Sleep(500 * time.Millisecond) // 表示をゆっくりにする
			if data[j] > data[j+1] {
				data[j], data[j+1] = data[j+1], data[j]
			}
		}
	}

	fmt.Println("--------------------------------")
	fmt.Println("ソート後:")
	showList(data, -1, -1)
}

// エントリポイント
func main() {
	numbers := []int{8, 3, 5, 1, 9, 6}
	bubbleSort(numbers)
}
```

