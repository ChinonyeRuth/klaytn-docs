# Kaikas <a id="kaikas"></a>

Kaikas is a browser extension wallet for the Klaytn Network. Google Chromeで利用可能なKaikasは、Webブラウザ経由でKlaytnネットワークとやり取りするための安全で使いやすい手段を提供します。 Kaikasを使用すると、KLAYとKlaytnベースのトークンを保存して取引することができます。 また、WebベースのdApps(分散アプリケーション)からのリクエストに リアルタイムで署名することもできます。

* Chrome ウェブストアからダウンロード: [リンク](https://chrome.google.com/webstore/detail/kaikas/jblndlipeogpafnldhgmapagcccfchpi)

開発者向けには、 [https://docs.kaikas.io](https://docs.kaikas.io) をご覧ください。

## PCウェブブラウザベースの分散型HDウォレット

KaikasはChromeで利用可能なWebブラウザ拡張機能です。 Kaikasはデスクトップ環境に最適化されています。

Kaikasはユーザーアカウントとキーの管理性を提供します。 すべてのトランザクションはKlaytnブロックチェーンに透過的に記録されるため、誰でも [Klaytnscope][] を使用してトランザクション履歴にアクセスできます。

Kaikasは階層的決定論的な(HD)ウォレットであり、これは単一のシードフレーズから秘密鍵/公開鍵の階層的なツリー構造を無期限に生成することを意味します。 シードフレーズは、ランダムな英数字で作られたフレーズよりも覚えやすいニーモニックコードワードで構成されています。 ユーザーの秘密鍵は暗号化され、ブラウザに保存されます。

上記の機能により、Kaikasは現在のブロックチェーンエクスペリエンスのセキュリティ、透明性、使いやすさを向上させました。 ただし、ユーザーが個人アカウントを管理する責任を負うことは不可欠です。 例えば、ユーザーがシードフレーズを覚えていなければ、アカウントを復元する方法はありません。

## 様々なKlaytnネットワークとトークンをサポートしています

Kaikasは、KLAYを含むすべてのKlaytnベースのトークンを保存して取引することができます。 デフォルトで読み込まれていないトークンは、コントラクトアドレスに貼り付けることで挿入できます。 KlaytnベースのカスタムトークンをKaikasに保存して取引することもできます!

Klaytnのバオバブテストネットとサイプレスメインネットをサポートしています。 さらに、Kaikasは、プライベートネットワークでカスタムトークンを流通させたいKlaytnベースのdApp開発者向けのプライベートチェーンをサポートしています。

## WebベースのdAppトランザクションの署名

Kaikasは、dAppsとの間のギャップを埋めるだけで、DAppsのアカウントで取引/データに署名できるようになります。 Kaikasはまた、 [手数料委託取引](/klaytn/design/transactions/README.md#fee-delegation) を開発者が処理するための有用なユーティリティです。 Kaikasを使用すると、取引送信者と手数料支払者の両方が手数料委任された取引に迅速に署名することができます。


[Klaytnscope]: ./klaytnscope.md