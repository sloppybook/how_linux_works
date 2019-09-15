# Chapter 3
## 2段階のプロセス生成
Linuxにおける、プロセス生成２つの目的
- 同じプログラムの処理を複数のプロセスに分けて処理する（Webサーバによる複数リクエストの受付）
- まったく別のプログラムを生成する（bashから各種プログラムの新規作成）

これらの目的のため、Linuxには「fork()」「execve()」という関数がある。（「clone()」「execve()」というシステムコールを呼び出す）

### fork()関数
「同じプログラムの処理を複数のプロセスに分けて処理」という目的のために使用。
発行したプロセスをもとに、新たにプロセスを一つ生成する。
生成元を親プロセス、新規に生成されたプロセスを子プロセス。

#### 流れ
1. 子プロセス用メモリ領域を作成して、親プロセスのメモリをコピー。
2. 親プロセスと子プロセスは違うコードを実行するように分岐。これにはfork()関数の戻り値が、親プロセスと子プロセスの間で異なることを利用。

#### 確認
1. fork.cを作成

```
$ cc -o fork fork.c
$ ./fork
I'm parent ! my pid is 2128 and the pid of my child is 2129.
I'm child! my pid is 2129.
```

### execve()関数
まったく別のプログラムを生成する場合に用いられる。

#### 流れ
1. 実行ファイルを読み出して、プロセスのメモリマップに必要な情報を読み出す。
2. 現在のプロセスのメモリを新しいプロセスのデータで上書きする。
3. 新しいプロセスの最初の命令から実行開始。

つまり、まったく別のプログラムを生成する場合は、プロセス数が増えるのではなく、あるプロセスを別のプロセスで置き換える形となる。

#### Executable Linkable Format(ELF)
Linux実行ファイルのformat
readelfコマンドによって各種情報を得られる。
option
- -h 開始アドレスを得る
- -S コード、データファイル内オフセット、サイズ、開始アドレス

(ちなみにcatコマンドで見ようとすると文字化けしまくった)

```
$ readelf -h /bin/sleep
ELF ヘッダ:
  マジック:  7f 45 4c 46 01 01 01 00 00 00 00 00 00 00 00 00 
  クラス:                            ELF32
  データ:                            2 の補数、リトルエンディアン
  バージョン:                        1 (current)
  OS/ABI:                            UNIX - System V
  ABI バージョン:                    0
  型:                                EXEC (実行可能ファイル)
  マシン:                            ARM
  バージョン:                        0x1
  エントリポイントアドレス:          0x110fc
  プログラムの開始ヘッダ:            52 (バイト)
  セクションヘッダ始点:              25220 (バイト)
  フラグ:                            0x5000400, Version5 EABI, hard-float ABI
  このヘッダのサイズ:                52 (バイト)
  プログラムヘッダサイズ:            32 (バイト)
  プログラムヘッダ数:                9
  セクションヘッダ:                  40 (バイト)
  セクションヘッダサイズ:            28
  セクションヘッダ文字列表索引:      27
$ readelf -S /bin/sleep
There are 28 section headers, starting at offset 0x6284:

セクションヘッダ:
  [番] 名前              タイプ          アドレス Off    サイズ ES Flg Lk Inf Al
  [ 0]                   NULL            00000000 000000 000000 00      0   0  0
  [ 1] .interp           PROGBITS        00010154 000154 000019 00   A  0   0  1
  [ 2] .note.ABI-tag     NOTE            00010170 000170 000020 00   A  0   0  4
  [ 3] .note.gnu.build-i NOTE            00010190 000190 000024 00   A  0   0  4
  [ 4] .gnu.hash         GNU_HASH        000101b4 0001b4 0001bc 04   A  5   0  4
  [ 5] .dynsym           DYNSYM          00010370 000370 000380 10   A  6   1  4
  [ 6] .dynstr           STRTAB          000106f0 0006f0 000261 00   A  0   0  1
  [ 7] .gnu.version      VERSYM          00010952 000952 000070 02   A  5   0  2
  [ 8] .gnu.version_r    VERNEED         000109c4 0009c4 000040 00   A  6   2  4
  [ 9] .rel.dyn          REL             00010a04 000a04 000040 08   A  5   0  4
  [10] .rel.plt          REL             00010a44 000a44 000170 08  AI  5  22  4
  [11] .init             PROGBITS        00010bb4 000bb4 00000c 00  AX  0   0  4
  [12] .plt              PROGBITS        00010bc0 000bc0 00023c 04  AX  0   0  4
  [13] .text             PROGBITS        00010e00 000e00 003b24 00  AX  0   0  8
  [14] .fini             PROGBITS        00014924 004924 000008 00  AX  0   0  4
  [15] .rodata           PROGBITS        0001492c 00492c 0008f4 00   A  0   0  4
  [16] .ARM.exidx        ARM_EXIDX       00015220 005220 000008 00  AL 13   0  4
  [17] .eh_frame         PROGBITS        00015228 005228 000004 00   A  0   0  4
  [18] .init_array       INIT_ARRAY      00025f00 005f00 000004 04  WA  0   0  4
  [19] .fini_array       FINI_ARRAY      00025f04 005f04 000004 04  WA  0   0  4
  [20] .data.rel.ro      PROGBITS        00025f08 005f08 000004 00  WA  0   0  8
  [21] .dynamic          DYNAMIC         00025f0c 005f0c 0000f0 08  WA  6   0  4
  [22] .got              PROGBITS        00026000 006000 0000c8 04  WA  0   0  4
  [23] .data             PROGBITS        000260c8 0060c8 000050 00  WA  0   0  4
  [24] .bss              NOBITS          00026118 006118 000160 00  WA  0   0  8
  [25] .ARM.attributes   ARM_ATTRIBUTES  00000000 006118 00002f 00      0   0  1
  [26] .gnu_debuglink    PROGBITS        00000000 006148 000034 00      0   0  4
  [27] .shstrtab         STRTAB          00000000 00617c 000108 00      0   0  1
Key to Flags:
  W (write), A (alloc), X (execute), M (merge), S (strings), I (info),
  L (link order), O (extra OS processing required), G (group), T (TLS),
  C (compressed), x (unknown), o (OS specific), E (exclude),
  y (purecode), p (processor specific)
```

プログラム実行時に作成されたプロセスのメモリマップは`/proc/(pid)/maps`というファイルによって得られる。

```
$ /bin/sleep 1000 &
[1] 2439
$ cat /proc/2439/maps
00010000-00016000 r-xp 00000000 b3:07 524372     /bin/sleep
00025000-00026000 r--p 00005000 b3:07 524372     /bin/sleep
00026000-00027000 rw-p 00006000 b3:07 524372     /bin/sleep
00091000-000b2000 rw-p 00000000 00:00 0          [heap]
76cbf000-76ddb000 r--p 00000000 b3:07 279496     /usr/lib/locale/locale-archive
76ddb000-76f13000 r-xp 00000000 b3:07 407782     /lib/arm-linux-gnueabihf/libc-2.28.so
76f13000-76f23000 ---p 00138000 b3:07 407782     /lib/arm-linux-gnueabihf/libc-2.28.so
76f23000-76f25000 r--p 00138000 b3:07 407782     /lib/arm-linux-gnueabihf/libc-2.28.so
76f25000-76f26000 rw-p 0013a000 b3:07 407782     /lib/arm-linux-gnueabihf/libc-2.28.so
76f26000-76f29000 rw-p 00000000 00:00 0 
76f3d000-76f41000 r-xp 00000000 b3:07 272693     /usr/lib/arm-linux-gnueabihf/libarmmem-v7l.so
76f41000-76f50000 ---p 00004000 b3:07 272693     /usr/lib/arm-linux-gnueabihf/libarmmem-v7l.so
76f50000-76f51000 r--p 00003000 b3:07 272693     /usr/lib/arm-linux-gnueabihf/libarmmem-v7l.so
76f51000-76f52000 rw-p 00004000 b3:07 272693     /usr/lib/arm-linux-gnueabihf/libarmmem-v7l.so
76f52000-76f72000 r-xp 00000000 b3:07 407850     /lib/arm-linux-gnueabihf/ld-2.28.so
76f80000-76f82000 rw-p 00000000 00:00 0 
76f82000-76f83000 r--p 00020000 b3:07 407850     /lib/arm-linux-gnueabihf/ld-2.28.so
76f83000-76f84000 rw-p 00021000 b3:07 407850     /lib/arm-linux-gnueabihf/ld-2.28.so
7eb02000-7eb23000 rw-p 00000000 00:00 0          [stack]
7eb9d000-7eb9e000 r-xp 00000000 00:00 0          [sigpage]
7eb9e000-7eb9f000 r--p 00000000 00:00 0          [vvar]
7eb9f000-7eba0000 r-xp 00000000 00:00 0          [vdso]
ffff0000-ffff1000 r-xp 00000000 00:00 0          [vectors]
```

#### fork and exec
まったく別のプロセスを新規作成する場合は、親となるプロセスからfork()を発行し、復帰後に子プロセスがexec()を呼ぶ、「fork and exec」の流れになることが多い。

##### 流れ
1. プロセスを新規作成
2. 親プロセスは、自身のプロセスIDと子プロセスのプロセスIDを出力したあとに、「echo hello」プログラムを生成。子プロセスは自身のプロセスIDをを出力して終了する。

#### やってみる
1. fork-and-exec.c作成
```
$ cc -o fork-and-exec fork-and-exec.c
$ ./fork-and-exec 
I'm parent! my pid is 2695 and the pid of my child is 2696.
I'm child! my pid is 2696.
$ hello
```

