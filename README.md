# mifmonitor

## 注意事項
- snmp コマンド
  - ツール本体では以下のように定義しているため、パスが異なる場合は書き換えること
```
my $SNMPGET   = '/usr/bin/snmpget -v 2c -c';
my $SNMPWALK  = '/usr/bin/snmpbulkwalk -v 2c -c';
```
- date コマンド
  - bps 計算に使用する時間情報には GNU date の `date +%S%N` を使用しているため、BSD系OS では gdate を使用すること
  - ツール本体の `$DATECMD` を書き換えること

## 使用方法
```
mifmonitor [-c <SNMP community>] [-i <取得間隔(秒)>] [-l <取得回数>] 対象ノード ifIndex ... 対象ノード ifIndex ...
mifmonitor [-c <SNMP community>] -q 対象ノード

  対象ノード: IPアドレス または ホスト名で指定 (複数ノード指定可能)
  ifIndex: モニター対象の ifIndex を指定 (スペース区切りで複数指定可能)

  -c  SNMP community
      文字列で指定
      Default: public

  -i  取得間隔(秒)
      数字で指定
      Default: 10秒

  -l  取得回数
      数字で指定
      Default: 300回

  -q  ifIndex を検索するために ifName を snmpbulkwalk で取得して表示し終了
```

## 終了方法
- 終了方法は以下の 2パターン
  - 取得回数分の実施で終了
  - `Ctrl-c` で終了
- いずれのパターンでも終了時に文字色と背景色を元に戻すようにしている

## 表示内容説明
```
2020/12/08 10:59:41
sysName              ifName          oper IN  bps Error  OUT bps Error  Description
--------------------+---------------+----+--------------+--------------+----------------------------
tokyo-router001      Ethernet001     up   111.11M 99999* 222.22M 99999* hogehoge hugahuga
```

- sysName
  - sysName を表示
  - 初回のみ取得し、以降は初回に取得した情報を表示
  - 表示文字数は自動で取得対象の最大文字数に調整
- ifName
  - ifName を表示
  - 初回のみ取得し、以降は初回に取得した情報を表示
  - 表示文字数は自動で取得対象の最大文字数に調整
- oper
  - ifOperStatus を表示
    - `up` または `down`
- IN, OUT
  - bps
    - ifHCInOctets, ifHCOutOctets の差分を取得間隔で割った値を bps で表示
      - Gbps/Mbps/Kbps を整数 3桁と小数点以下 2桁で表示
      - `0.00K` はカウンタ増加ありを示す
      - `0` はカウンタ増加なしを示す
      - ifOperStatus によって文字色を変更 (up:緑、down:赤)
  - Error
    - ifInErrors, ifOutErrors の差分を表示
      - カウンタ増加があった場合には、太文字、文字背景色が赤で表示
      - 取得中にカウンタ増加があれば、以降は数字の後に `*` を表示
- Description
  - ifAlias を表示
  - 初回のみ取得し、以降は初回に取得した情報を表示
  - 表示文字数は自動で取得対象の最大文字数に調整

