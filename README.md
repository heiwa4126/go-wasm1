# go-wasm1

Go(1.19)でWebAssemblyのサンプル。
おおむね
[WebAssembly · golang/go Wiki](https://github.com/golang/go/wiki/WebAssembly#getting-started)
に基づく。
goexecが動かなかったのでそのへんアレンジ。


## 初期手順

```powershell
mkdir wasm1 ; cd wasm1
go mod init wasm1
cp "$(go env GOROOT)/misc/wasm/wasm_exec.js" .
```
`wasm_exec.js`はGoのバージョンごとに変わるらしい。

あと、**このレポジトリには `wasm_exec.js`は入れてないので
上のcpはcloneしたところで実行してください。**

これにmain.goとindex.htmlを追加したのがこのレポジトリ。

## コンパイル

WindowsでPowerShellなら
```powershell
& { $Env:GOOS="js" ; $Env:GOARCH="wasm" ; go build -o main.wasm }
```

bashなら
```
GOOS=js GOARCH=wasm go build -o main.wasm
```

## テスト

で、Node.jsあるなら `npx http-server .`

またはgoexecの代わりに
[goeval](https://github.com/dolmen-go/goeval)
を使って
```powershell
# goevalのインストール
go install github.com/dolmen-go/goeval@master
# goevalのテスト
goeval 'fmt.Println("Hello, world!")'
# で、
goeval 'http.ListenAndServe(":8080", http.FileServer(http.Dir(".")))'
```

http://127.0.0.1:8080/ をブラウザで開いて、F12で、コンソールに
> Hello, WebAssembly!

が出力されるのを見る。

## Node.jsで実行する

昔は `wasm_exec.js` をNode.jsで実行すればよかったらしいが、
最近はNode用のがある。

```powershell
cp "$(go env GOROOT)/misc/wasm/wasm_exec_node.js" .
node wasm_exec_node.js main.wasm
```

これで
> Hello, WebAssembly!

が出力されるのを見る。


## TinyGo

[TinyGo](https://tinygo.org/getting-started/install/)があるなら、
main.wasmがどれだけ小さくなるかが確認できる。

```bash
cp "$(tinygo env TINYGOROOT)/targets/wasm_exec.js" .
```
でTinyGo版のwasm_exec.jsをコピーして (std版とかなり違う)

```bash
tinygo build -target wasm -o main.wasm
# または
tinygo build -target wasm -o main.wasm -no-debug
```
