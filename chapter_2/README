# Chapter 2
## プロセスのシステムコールを確認
システムコール：ユーザーモードのプロセスからカーネルモードに処理を依頼
ユーザーモードとカーネルモードの境界線はOSライブラリとカーネルの間

ユーザーモード：プロセス固有のコード、OS外ライブラリ、OSライブラリ
カーネルモード：カーネル、ハードウェア

### C言語
1. hello.c 作成
2. コンパイル
```
$ cc -o hello hello.c
$ ./hello
hello world
```
3. strace コマンドでシステムコールを確認
```
$ strace -o hello.log ./hello
hello world
$ cat hello.log
...
write(1, "hello world\n", 12)	= 12
...
```

### Python
1. hello.py 作成
2. strace

```
$ strace -o hello.py.log python3 ./hello.py
hello world
$ cat hello.py.log
...
write(1, "hello world\n", 12)	= 12
...
```

## プロセスのユーザーモードとカーネルモードの割合を確認
1. sar コマンドで確認

ユーザーモード：%user + %nice
カーネルモード：%system
アイドル状態：%idle

```
$ sar -P ALL 1
15時36分22秒     CPU     %user     %nice   %system   %iowait    %steal     %idle
15時36分23秒     all      1.50      0.00      0.25      0.25      0.00     98.00
15時36分23秒       0      2.04      0.00      0.00      1.02      0.00     96.94
15時36分23秒       1      0.99      0.00      0.00      0.00      0.00     99.01
15時36分23秒       2      0.00      0.00      1.00      0.00      0.00     99.00
15時36分23秒       3      2.97      0.00      0.00      0.00      0.00     97.03
^C

平均値:      CPU     %user     %nice   %system   %iowait    %steal     %idle
平均値:      all      1.58      0.00      0.33      0.08      0.00     98.00
平均値:        0      2.03      0.00      0.34      0.34      0.00     97.29
平均値:        1      0.66      0.00      0.00      0.00      0.00     99.34
平均値:        2      0.33      0.00      0.99      0.00      0.00     98.68
平均値:        3      3.32      0.00      0.00      0.00      0.00     96.68
```
2. loopを実行しsar で確認

```
$ cc -o loop loop.c
$ ./loop &
[1] 2643
$ sar -P ALL 1 1
15時41分18秒     CPU     %user     %nice   %system   %iowait    %steal     %idle
15時41分19秒     all     25.75      0.00      0.25      0.00      0.00     74.00
15時41分19秒       0      1.98      0.00      0.99      0.00      0.00     97.03
15時41分19秒       1      0.00      0.00      0.00      0.00      0.00    100.00
15時41分19秒       2      1.01      0.00      0.00      0.00      0.00     98.99
15時41分19秒       3    100.00      0.00      0.00      0.00      0.00      0.00

平均値:      CPU     %user     %nice   %system   %iowait    %steal     %idle
平均値:      all     25.75      0.00      0.25      0.00      0.00     74.00
平均値:        0      1.98      0.00      0.99      0.00      0.00     97.03
平均値:        1      0.00      0.00      0.00      0.00      0.00    100.00
平均値:        2      1.01      0.00      0.00      0.00      0.00     98.99
平均値:        3    100.00      0.00      0.00      0.00      0.00      0.00

& kill 2643
```

3. 親プロセスのプロセスを得るシステムコールのloopでsarを確認
```
$ cc -o ppidloop ppidloop.c
$ ./ppidloop &
[2] 2692
$ sar -P ALL 1 1
15時45分06秒     CPU     %user     %nice   %system   %iowait    %steal     %idle
15時45分07秒     all     33.67      0.00     16.83      0.00      0.00     49.50
15時45分07秒       0      1.01      0.00      0.00      0.00      0.00     98.99
15時45分07秒       1    100.00      0.00      0.00      0.00      0.00      0.00
15時45分07秒       2      0.00      0.00      0.00      0.00      0.00    100.00
15時45分07秒       3     33.00      0.00     67.00      0.00      0.00      0.00

平均値:      CPU     %user     %nice   %system   %iowait    %steal     %idle
平均値:      all     33.67      0.00     16.83      0.00      0.00     49.50
平均値:        0      1.01      0.00      0.00      0.00      0.00     98.99
平均値:        1    100.00      0.00      0.00      0.00      0.00      0.00
平均値:        2      0.00      0.00      0.00      0.00      0.00    100.00
平均値:        3     33.00      0.00     67.00      0.00      0.00      0.00
$ kill 2692
```
### おまけ
straceにオプション-Tをつけるとシステムコールにかかった時間を表示

## 標準Cライブラリを確認
ldd: プログラムにどのようなライブラリがリンクしているか確認できる
```
$ which pwd
/bin/pwd
$ ldd /bin/pwd
	linux-vdso.so.1 (0x7ee11000)
	/usr/lib/arm-linux-gnueabihf/libarmmem-${PLATFORM}.so => /usr/lib/arm-linux-gnueabihf/libarmmem-v7l.so (0x76f1a000)
	libc.so.6 => /lib/arm-linux-gnueabihf/libc.so.6 (0x76db8000)
	/lib/ld-linux-armhf.so.3 (0x76f2f000)
```
libcというのが標準Cライブラリです。
ls, echo, python3等確認してみよう！

## OSが提供するプログラム
- システム初期化：init
- OSの挙動を変える：sysctl, nice, sync
- ファイル操作：touch, mkdir
- テキストデータの加工：grep, sort, uniq
- 性能測定：sar, iostat
- コンパイラ：gcc
- スクリプト言語実行環境：perl, python, ruby
- シェル：bash
- ウィンドウシステム：X

