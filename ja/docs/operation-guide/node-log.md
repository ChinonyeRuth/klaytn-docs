# ノードログ <a id="node-log"></a>

このページでは、Klaytn ノードからの重要な、またはよく寄せられるログについて詳しく説明します。 Klaytnログが変更されたり、新規に追加/削除された場合は、このページも編集してください。

ログタイプの詳細については、 [log_modules.go](https://github.com/klaytn/klaytn/blob/dev/log/log_modules.go) を参照してください。

If you encounter any abnormal situation, please report it to the klaytn team via [github](https://github.com/klaytn/klaytn/issues), [Klaytn Forum](https://forum.klaytn.foundation/), or [Discord](https://discord.io/KlaytnOfficial).

## エラーログ
| ログの種類              | Node Type | ログメッセージ                                                                                                                                                                                                                  | Description                                                                                                                                                                 | 推奨ガイド                                                                                                                                                          |
|:------------------ |:--------- |:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |:-------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Blockchain         | CN/PN/EN  | ########## BAD BLOCK #########</br>Chain config: %v</br></br>Number: %v</br>Hash: 0x%x</br>%v</br></br>Error: %v</br>##############################                                                                      | 受信したレシートと実行結果が一致しない場合、不正なブロックが発生します。 ノードが不良ブロックログで停止する場合は、2つの理由が考えられます。 </br> - ケース 1. バイナリバージョンとして、ノードの構成は間違っています。 </br> - ケース 2. コードに問題があります。 他のノードも同じ問題を経験する可能性が非常に高いです。 | このエラーは重要なので、何か悪いブロックが見つかった場合は、Issue を作成するか、Klaytn GitHub リポジトリに報告してください。                                                                                       |
| Consensusイスタンブールコア | CN/PN/EN  | **タイムアウトチャンネルから空のメッセージをドロップ**                                                                                                                                                                                            | ラウンドチェンジタイマーが期限切れになることを意味します。 このエラーは、誤ってタイマーが閉じた場合に表示されます。                                                                                                                  | ダウンローダーの起動時にエラーが発生することがあります。 </br> 次のログも印刷されていることを確認します: `ブロック同期が開始されました`.                                                                                     |
| NetworksP2P        | CN/PN/EN  | **Protocol istanbul/64 failed** id=04680a827fa1b240 conn=staticdial err="write tcp 10.117.2.105:34396->10.117.2.27:32323: use of closed etwork connection"</br></br> **Protocol istanbul/64 failed** err="shutting down" | このログは、他のノードが切断されたときに印刷することができます。 通常は`P2Pピア` ログを切断します。                                                                                                                       | 切断されたピアが再接続されているかどうかを確認します。 接続されていない場合は、ネットワークの状態またはピア接続 [admin_peer](https://docs.klaytn.foundation/dapp/json-rpc/api-references/admin#admin_peers) を確認してください |
| NodeCN             | CN        | **SendNewBlockHashes に失敗しました** err="tcp 10.117.2.124:24108->10.117.2.108:32323: クローズされたネットワーク接続の使用" </br></br> **SendNewBlockHashes** err="シャットダウン" に失敗しました                                                              | `プロトコルistanbul/64 が失敗しました`                                                                                                                                                  | same as `Protocol istanbul/64 failed`                                                                                                                          |
| NodeCN             | CN        | **SendNewBlock** peer=d35220eccdb0de7b err="シャットダウン中"                                                                                                                                                                    | same as `Protocol istanbul/64 failed`                                                                                                                                       | same as `Protocol istanbul/64 failed`                                                                                                                          |
| NetworksRPC        | EN (大抵)   | **FastWebsocketHandler failed to upgrade message** error="websocket: version != 13"                                                                                                                                      | WebSocket 接続のバージョンの問題                                                                                                                                                       | リクエストのヘッダーには、 `Sec-Websocket-Version` フィールドが含まれ、値は 13 に設定されている必要があります。 klaytn rpc クライアントを使用していないかもしれません。                                                       |

## 警告ログ
| Log Type              | Node Type | Log Message                                                                                                                                    | Description                                                                                                       | Suggested Guide                                                                                                                                                                                                         |
|:--------------------- |:--------- |:---------------------------------------------------------------------------------------------------------------------------------------------- |:----------------------------------------------------------------------------------------------------------------- |:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Blockchain            | CN/PN/EN  | **データベースのバージョンをアップグレードする** from=N/A to=3                                                                                                       | ノードの起動時に記録されます                                                                                                    | あなたはこれを処理する必要はありません。                                                                                                                                                                                                    |
| ConsensusIstanbulCore | CN        | **[RC]** round=                                                                                                                                | ラウンド変更ログは [RC] タグで開始されます。                                                                                         | ラウンドが1〜2ラウンドで終わらず、上に進み続ける場合 ネットワークの状況やピア・コネクションを最初に分析する必要があります </br></br> peer connection api: [admin_peers](https://docs.klaytn.foundation/dapp/json-rpc/api-references/admin#admin_peers)                             |
| ConsensusIstanbulCore | CN        | **想定外のリクエスト**  address= state="リクエストを受け入れる" seq=312 err="古いメッセージ" number=311 hash=d960ea…6df6de                                                | 提案者はブロックを採掘しますが、それは予想外に判明しました。 ほとんどの場合、新しいブロックにするには古すぎます。                                                         | You don't need to handle this.                                                                                                                                                                                          |
| ノード                   | CN/PN/EN  | **Failed doConnTypeHandshake** addr=10.117.2.252:28516 conn=インバウンドconntype=-1 err="read tcp 10.117.2.78:32324->10.117.2.252:28516: i/o timeout | ダイヤルすることにより、2つのP2Pピアが接続を設定します。 このログは、セットアップに失敗した場合に印刷されます。                                                        | Check if the disconnected peer is reconnected again. そうでない場合は、ネットワークの状態またはピアの接続を確認する </br></br> peer connection check api: [admin_peers](https://docs.klaytn.foundation/dapp/json-rpc/api-references/admin#admin_peers) |
| NodeCN                | PN/EN     | **ボディのフィルタに失敗しました** peer=c02e4b4d471c56b9 lenTxs=1                                                                                             | ノードは取得時に本体の不要なブロックヘッダを受け取りました。 </br> - lenTxs: 要求されていないtxs                                                        | You don't need to handle this.                                                                                                                                                                                          |
| 仕事                    | CN        | **時間制限のためトランザクションが中止されました** ハッシュ=                                                                                                              | マイニングのブロック実行時間は250msを超えないようにするため、この制限時間により最後のトランザクションを中止することができます。                                                | 取引がブロックに入ることを確認します。                                                                                                                                                                                                     |
| Work                  | CN        | **トランザクションに失敗しました。** hash=b1b26c...6b220a err="転送に不十分な残高"</br></br>Error(v1.6.2以前)</br>警告                                                      | `` アカウント </br> の残高が不足しているため、マイニング中にトランザクションを実行できない場合 (理論的には、 取引が作成されtxpoolに入った時点で残高が十分だったときに発生します 実際の実行時間ではありません) | `アカウントの` が本当にバランスが崩れているか確認してください。                                                                                                                                                                                       |

## 情報ログ
`Info` log contains the additional information for you to know more about the node status, so you don't need to handle `Info` level log.

| Log Type       | Node Type | Log Message                                                                                                                                                                  | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|:-------------- |:--------- |:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Blockchain     | CN/PN/EN  | **再生成されたローカルトランザクション雑誌** トランザクション=0 accounts=0                                                                                                                               | nodeがシャットダウンされると、ローカルのtxsはファイルにジャーナルされます(デフォルトのファイル名はtransactions.rlp)。 journaled ファイルでノードを再起動すると、ファイルに基づいてローカルトランザクションを再生成することができます。 </br> - トランザクション: 再生成されたローカルトランザクションの数。 </br> - アカウント: 再生成されたアカウントの数(==from)                                                                                                                                                                                                                                                                                                             |
| Blockchain     | CN/PN/EN  | **新しいブロックを挿入** number=14 hash=13cbfc…f007fc txs=0 gas=0 elapsed=793.458μs processTxs=167ns finalize=157.708μs validateState=7.542μs totalWrite=443.417μs trieWrite=256.667μs | ノードがそのブロックのプロポーザではなく、コンセンサスが成功した場合、ノードはブロックを実行します(==検証)。 言い換えれば、ブロックが挿入されます。 </br> - 気体: txの実行中に費やされた総ガス。 </br> このフィールドは、ブロックごとに使用されるガスを見つけるためにネットワークをテストするときに一般的に使用されます。                                                                                                                                                                                                                                                                                                                                                      |
| NetworksP2P    | CN/PN/EN  | **[Dial] 静的ノードからダイヤル候補を追加**  id=62a08a4b9f091c4b NodeType=0 ip=10.117.2.8 mainPort=32323 port=[32323]                                                                        | 新しいP2Pピアが接続されており、静的ノードです。 static-nodes.json または addpeer api を使用して手動で追加したノードを静的ノードと呼びます。 マルチチャネルの場合は、2 つのポートを使用します。 ex. [32323, 32324]. </br> - id: dst peer id </br> - NodeType: dst node type(cn,pn,en,bn) </br> - ip: dst ip </br> - mainPort: dst TCP listening port number </br> - port: dst TCP listening port number including both the main port and sub port.                                                                                                                                                          |
| NetworksP2P    | CN/PN/EN  | **マルチチャンネルP2Pピアを追加** id=28a6760472a078fb conn=staticdial peerID=28a6760472a078fb                                                                                             | 新しいピアはマルチチャネルピアとして接続されます。 </br> - id/peerID: my node’s peer id </br> - conn: type of connection </br> - inbound: somebody connects me </br> - staticdial: static connection, such as static-nodes.json or addPeer </br> - trusteddial: trusted connection, such as trust-nodes.json. 接続数が限界を超えても常に再接続できます                                                                                                                                                                                                                      |
| NetworksP2P    | CN/PN/EN  | **マルチチャネルP2Pピアの切断** id=28a6760472a078fb conn=インバウンドpeerID=28a6760472a078fb peerName=Klaytn/v1.7.3+acae89350c/darwin-arm64/go1.18.1 err=EOF                                   | マルチチャンネルピアが切断されます。 </br> - peerName: ノード情報を表示します </br> - err: 接続が切断された理由。                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| NetworksP2P    | CN/PN/EN  | **ProtocolManager.processConsensusMsg closed** id=28a6760472a078fb conn=income PeerName=Klaytn/v1.7.3+acae89350c/darwin-arm64/go1.18.1                                       | P2Pノードが切断されると、それらの間のコンセンサスメッセージチャンネルも閉じられます。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| StorageStateDB | CN/PN/EN  | **メモリデータベースの永続化トリエ** blockN=23460 updated nodes=4 updated nodes size=499.00B time=539.959μs gcnodes=68 gctime=10.55kB gctime=226.499μs livenodes=245 livesize=37.80kB        | このログは、triedbがコミットされたことを知らせるために印刷されます。 ここで、commitは実際のdbにdbを変更するフラッシュを意味します。 </br></br> コミットは定期的に行われる。 </br> - Case 1. ノードがフルノードの場合、128 ブロック毎に trie commit が行われます。 </br> - Case 2. ノードがアーカイブノードの場合、すべてのブロックに対して trie commit が行われます。 </br></br> 次の状況でもコミットが行われる。 </br> - , ノードがシャットダウンされたときにコミットが行われる。 </br> - メモリサイズがキャップを超えたときにコミットが行われます。 </br></br> Tip. </br> - gc はガベージコレクションを表します。 ここでは、ガベージコレクションは、trieノード変更のdb書き込みをフラッシュすることを意味します。 </br> - フルノードは、128サイクルごとの情報と最新128ブロックの情報を保存します。 </br> - アーカイブノードは、すべてのブロックの情報を保存します。 |
| Work           | CN        | **新しいマイニング作業をコミット** number=14 hash=438ef7…68ca7f txs=0 elapsed=605.375μs commitTime=184.708μs finalizeTime=414.375μs                                                         | 各CNはラウンドチェンジの準備のためにブロックをマイニングします </br>- number: block number</br> - hash: block hash (それは最終バージョンではありません) </br> - txs: ブロック内のトランザクション数</br> - 経過時間: 合計ブロックマイニング時間 (commitTime + finalizeTime)</br> - commitTime: トランザクション実行時間</br> - finalizeTime: ブロック終了時間                                                                                                                                                                                                                                                                     |
| Work           | CN        | **新しいブロック** 番号=14 ハッシュ=13cbfc…f007fc                                                                                                                                         | format@@0 新しいブロックの封鎖が成功しました。 シールには次のステップが含まれています。 </br> - ブロックのためのIbftコンセンサスプロセス。 </br> - タイムスタンプとブロックの署名を更新                                                                                                                                                                                                                                                                                                                                                                                                                   |
| Work           | CN        | **マイニングブロックを正常に書き込みました** num=14 hash=13cbfc…f007fc txs=0 elapsed=617.709μs                                                                                                   | format@@0 ノードが提案者で合意形成に成功した場合、提案者はブロック実行結果をdbに格納する必要があります。 このログは、保存が成功したことを意味します。                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| Work           | CN        | **将来的にマイニングが遠すぎる** wait=1s                                                                                                                                                   | 1 秒のブロック生成期間を維持するために、ノードは「1s - 前のブロック生成/伝播/実行時間」を設定します。 </br> - 待機：ノードがスリープする時間                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| VM             | CN/PN/EN  | **addr はプログラムアカウントではないため返される** addr=                                                                                                                                         | 誰かが存在しない契約を呼び出そうとします。 </br></br> Tip. Klaytnでは、プログラムアカウントは契約アカウントと同等です。                                                                                                                                                                                                                                                                                                                                                                                                                                                        |