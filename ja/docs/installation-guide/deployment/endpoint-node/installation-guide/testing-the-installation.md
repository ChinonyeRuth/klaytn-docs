# インストールのテスト <a id="testing-the-installation"></a>

エンドポイントノードが正常にインストールされ、インストール後に期待どおりに動作していることを確認します。

## プロセスの状態 <a id="process-status"></a>

ステータスコマンド `systemctl` と `kend` を使用して、ENのプロセスの状態をチェックすることができます。

### systemctl <a id="systemctl"></a>

`systemctl` がRPMとともにインストールされ、ENの状態を以下のように確認することができます。

```bash
$ systemctl status kend.service
● kend.service - (null)
   Loaded: loaded (/etc/rc.d/init.d/kend; bad; vendor preset: disabled)
   Active: active (running) since Wed 2019-01-09 11:42:39 UTC; 1 months 4 days ago
     Docs: man:systemd-sysv-generator(8)
  Process: 29636 ExecStart=/etc/rc.d/init.d/kend start (code=exited, status=0/SUCCESS)
 Main PID: 29641 (ken)
   CGroup: /system.slice/kend.service
           └─29641 /usr/local/bin/ken --networkid 1000 --datadir /kend_home --port 32323 --srvtype fasthttp --metrics --prometheus --verbosity 3 --txpool.global...

Jan 09 11:42:39 ip-10-11-2-101.ap-northeast-2.compute.internal systemd[1]: 開始 (null) ...
Jan 09 11:42:39 ip-10-11-2-101.ap-northeast-2.compute.internal kend[29636]: Starting kend: [ OK ]
Jan 09 11:42:39 ip-10-11-2-101.ap-northeast-2.compute.internal systemd[1]: Started (null).
```

上記の例では、 `Active: active (running)` のような現在のステータスを確認できます。

### kend <a id="kend"></a>

`kend` はパッケージとともにインストールされ、ENの状態を以下のように確認することができます。

```bash
$ kend status
kend is running
```

## ログ <a id="logs"></a>

The log is stored in `kend.out` file located in the path defined in the `LOG_DIR` field of the `kend.conf` file. ノードが正常に動作すると、各ブロックが以下のようにインポートされます。

例

```bash
$ tail kend.out
INFO[02/13,07:02:24 Z] [35] Commit new mining work                    number=11572924 txs=0 elapsed=488.336µs
INFO[02/13,07:02:25 Z] [5] Imported new chain segment                blocks=1 txs=0 mgas=0.000     elapsed=1.800ms   mgasps=0.000       number=11572924 hash=f46d09…ffb2dc cache=1.59mB
INFO[02/13,07:02:25 Z] [35] Commit new mining work                    number=11572925 txs=0 elapsed=460.485µs
INFO[02/13,07:02:25 Z] [35] 🔗 block reached canonical chain           number=11572919 hash=01e889…524f02
INFO[02/13,07:02:26 Z] [14] Committed                                 address=0x1d4E05BB72677cB8fa576149c945b57d13F855e4 hash=1fabd3…af66fe number=11572925
INFO[02/13,07:02:26 Z] [5] Imported new chain segment                blocks=1 txs=0 mgas=0.000     elapsed=1.777ms   mgasps=0.000       number=11572925 hash=1fabd3…af66fe cache=1.59mB
INFO[02/13,07:02:26 Z] [35] Commit new mining work                    number=11572926 txs=0 elapsed=458.665µs
INFO[02/13,07:02:27 Z] [14] Committed                                 address=0x1d4E05BB72677cB8fa576149c945b57d13F855e4 hash=60b9aa…94f648 number=11572926
INFO[02/13,07:02:27 Z] [5] Imported new chain segment                blocks=1 txs=0 mgas=0.000     elapsed=1.783ms   mgasps=0.000       number=11572926 hash=60b9aa…94f648 cache=1.59mB
INFO[02/13,07:02:27 Z] [35] Commit new mining work      
```

## クエリ <a id="queries"></a>

### kenコンソール <a id="ken-console"></a>

KlaytnはCLIクライアントを提供します: `kenコンソール`。 クライアントを使用するもう一つの方法は、IPC(プロセス間通信)を介してプロセスに接続することです。 IPCファイル `klay.ipc` は ENの `data` ディレクトリにあります。

以下のコマンドを実行し、結果を確認してください。

```text
$ ken attach /var/kend/data/klay.ipc
Klaytn JavaScript コンソールへようこそ!

instance: Klaytn/vX.X.X/XXXX-XXXX-XXXX/goX.X
 datadir: /var/kend/data
 modules: admin:1.0 debug:1.0 governance:1.0 klay:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0
 >
```

使用可能なコマンドは [API ドキュメント](../../../dapp/json-rpc/README.md) で確認できます。

ENの状態を確認するのに役立つAPI:

* `klay.blockNumber` (最新のブロック番号を取得する)
* `net.peerCount` (現在接続されている Klaytn ノードの数を取得する)

### klay.blockNumber <a id="klay-blocknumber"></a>

最新のブロック番号を取得して、ブロックが正しく伝播されているかどうかを確認できます。

```text
> klay.blockNumber
11573819
```

### net.peerCount <a id="net-peercount"></a>

```text
> net.peerCount
14
```

上記のコマンドラインは、ENが接続するノードの数を返します。





