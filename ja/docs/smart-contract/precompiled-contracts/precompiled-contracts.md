# プリコンパイル済み契約 <a id="precompiled-contracts"></a>

Klaytnはいくつかの有用な事前コンパイル済み契約を提供します。 これらの契約は、ネイティブ実装としてプラットフォーム自体に実装されています。 アドレス0x01から0x09までの事前編集された契約はEthereumの契約と同じです。 Klaytnはさらに、0x3fdから0x3ffまでのプリコンパイル済みコントラクトを実装して、新しいKlaytn機能をサポートしています。

{% hint style="success" %}
注: 3 つのプリコンパイル済みのコントラクトアドレスが変更され、 **blake2F** が `イスタンブールEVM` プロトコルのアップグレード、または "ハードフォーク" の後に追加されました。

`IstanbulEVM` protocol upgrade block number is as follows.
* Baobab Testnet: `#75373312`
* Cypress Mainnet: `#86816005`

プロトコルアップグレード前にデプロイされた契約は、元のアドレスを使用する必要があります。
* ケース 1) ブロック番号 `#75373310 にバオバブに展開された契約` は 0x09を認識している。 vmLog、feePayer、validateSenderのアドレスとして0x0a、および0x0bはそれぞれ使用できません。
* ケース 2) ブロック番号 `#75373314 にバオバブに展開された契約` は 0x09 を blake2f のアドレスとして認識している。 そして、0x3fd、0x3fe、0xffをvmLog、feePayer、validateSenderのアドレスとして認識します。

前のドキュメントをご希望の場合は、 [前のドキュメント](precompiled-contracts-previous.md) をご参照ください。
{% endhint %}

| precompiled contract | v1.7.0プロトコル更新の有効化前にデプロイされたコントラクトで使用されるアドレス | v1.7.0プロトコル更新の有効化後にデプロイされたコントラクトに使用されるアドレス |
|:-------------------- |:------------------------------------------ |:------------------------------------------ |
| vmLog                | 0x09                                       | 0x3fd                                      |
| feePayer             | 0x0a                                       | 0x3fe                                      |
| validateSender       | 0x0b                                       | 0x3ff                                      |

## アドレス 0x01: ecrecover\(hash, v, r, s\' <a id="address-0x-01-ecrecover-hash-v-r-s"></a>

アドレス0x01はecrecoverを実装しています。 ECDSAの回復関数を計算することにより、与えられた署名からアドレスを返します。 機能のプロトタイプは以下の通りです。

```text
function ecrecover(bytes32 hash, bytes8 v, bytes32 r, bytes32 s) returns (address);
```

## アドレス 0x02: sha256\(data\) <a id="address-0x-02-sha-256-data"></a>

アドレス0x02はSHA256ハッシュを実装しています。 与えられたデータから SHA256 ハッシュを返します。 機能のプロトタイプは以下の通りです。

```text
function sha256(bytes data) returns (bytes32);
```

## アドレス 0x03: ripemd160\(data\) <a id="address-0x-03-ripemd-160-data"></a>

アドレス0x03はRIPEMD160ハッシュを実装しています。 与えられたデータから RIPEMD160 ハッシュを返します。 機能のプロトタイプは以下の通りです。

```text
function ripemd160(bytes data) returns (bytes32);
```

## アドレス 0x04: datacopy\(data\) <a id="address-0x-04-datacopy-data"></a>

アドレス0x04はデータコピを実装しています \(すなわち、ID関数\)。 変更なしに直接入力データを返します。 このプリコンパイル済みコントラクトは、Solidity コンパイラではサポートされていません。 インラインアセンブリのコードは、このプリコンパイル済みコントラクトを呼び出すために使用できます。

```text
function callDatacopy(bytes memory data) public returns (bytes memory) {
    bytes memory ret = new bytes(data.length);
    assembly {
        let len := mload(data)
        if iszero(call(gas, 0x04, 0, add(data, 0x20), len, add(ret,0x20), len)) {
            invalid()
        }
    }

    return ret;
}     
```

## Address 0x05: bigModExp\(base, exp, mod\) <a id="address-0x05-bigmodexp-base-exp-mod"></a>

アドレス0x05は式 `base**exp % mod` を実装します。 与えられたデータから結果を返します。 このプリコンパイル済みコントラクトは、Solidity コンパイラではサポートされていません。 このプリコンパイル済みコントラクトを呼び出すには、次のコードを使用できます。 このプリコンパイルされたコントラクトは任意の長さの入力をサポートしますが、以下のコードでは、一定の長さの入力を例として使用します。

```text
function callBigModExp(bytes32 base, bytes32 exponent, bytes32 modulus) public returns (bytes32 result) {
    assembly {
        // free memory pointer
        let memPtr := mload(0x40)

        // length of base, exponent, modulus
        mstore(memPtr, 0x20)
        mstore(add(memPtr, 0x20), 0x20)
        mstore(add(memPtr, 0x40), 0x20)

        // assign base, exponent, modulus
        mstore(add(memPtr, 0x60), base)
        mstore(add(memPtr, 0x80), exponent)
        mstore(add(memPtr, 0xa0), modulus)

        // call the precompiled contract BigModExp (0x05)
        let success := call(gas, 0x05, 0x0, memPtr, 0xc0, memPtr, 0x20)
        switch success
        case 0 {
            revert(0x0, 0x0)
        } default {
            result := mload(memPtr)
        }
    }
}
```

## アドレス 0x06: bn256Add\(ax, ay, bx, by\) <a id="address-0x-06-bn-256-add-ax-ay-bx-by"></a>

アドレス 0x06 は、ネイティブの楕円曲線の点の追加を実装しています。 `(ax, ay) + (bx) を表す楕円曲線点を返します。 ) <code>` \(ax, ay\) と \(bx, by\) が曲線上の有効な点であるように、 bn256. このプリコンパイル済みコントラクトは、Solidity コンパイラではサポートされていません。 このプリコンパイル済みコントラクトを呼び出すには、次のコードを使用できます。

```text
function callBn256Add(bytes32 ax, bytes32 ay, bytes32 bx, bytes32 by) public returns (bytes32[2] memory result) {
    bytes32[4] memory input;
    input[0] = ax;
    input[1] = ay;
    input[2] = bx;
    input[3] = by;
    assembly {
        let success := call(gas, 0x06, 0, input, 0x80, result, 0x40)
        switch success
        case 0 {
            revert(0,0)
        }
    }
}
```

## アドレス 0x07: bn256ScalarMul\(x, y, scalar\' <a id="address-0x-07-bn-256-scalarmul-x-y-scalar"></a>

アドレス 0x07 は、スカラー値を持つネイティブの楕円曲線の掛け算を実装しています。 これは `スカラー * (x) を表す 楕円曲線点を返します。 y) <code>` \(x, y\) が曲線上の有効な曲線点であるように、 bn256. このプリコンパイル済みコントラクトは、Solidity コンパイラではサポートされていません。 このプリコンパイル済みコントラクトを呼び出すには、次のコードを使用できます。

```text
function callBn256ScalarMul(bytes32 x, bytes32 y, bytes32 scalar) public returns (bytes32[2] memory result) {
    bytes32[3] memory input;
    input[0] = x;
    input[1] = y;
    input[2] = scalar;
    assembly {
        let success := call(gas, 0x07, 0, input, 0x60, result, 0x40)
        switch success
        case 0 {
            revert(0,0)
        }
    }
}
```

## アドレス 0x08: bn256Pairing\(a1, b1, a2, b2, a3, b3, ..., ak, bk\) <a id="address-0x-08-bn-256-pairing-a-1-b-1-a-2-b-2-a-3-b-3-ak-bk"></a>

アドレス0x08は、zkSNARK検証を実行する楕円曲線のパーシング操作を実装しています。 詳細については、 [EIP-197](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-197.md) を参照してください。 このプリコンパイル済みコントラクトは、Solidity コンパイラではサポートされていません。 このプリコンパイル済みコントラクトを呼び出すには、次のコードを使用できます。

```text
function callBn256Pairing(bytes memory input) public returns (bytes32 result) {
    // input is a serialized bytes stream of (a1, b1, a2, b2, ..., ak, bk) from (G_1 x G_2)^k
    uint256 len = input.length;
    require(len % 192 == 0);
    assembly {
        let memPtr := mload(0x40)
        let success := call(gas, 0x08, 0, add(input, 0x20), len, memPtr, 0x20)
        switch success
        case 0 {
            revert(0,0)
        } default {
            result := mload(memPtr)
        }
    }
}
```

## 住所 0x09: blake2F\(rounds, h, m, t, f\' <a id="address-0x-3fc-vmlog-str"></a>
アドレス0x09は、BLAKE2b F圧縮機能を実装しています。 詳細については、 [EIP-152](https://eips.ethereum.org/EIPS/eip-152) を参照してください。 このプリコンパイル済みコントラクトは、Solidity コンパイラではサポートされていません。 このプリコンパイル済みコントラクトを呼び出すには、次のコードを使用できます。

```text
function callBlake2F(uint32 rounds, bytes32[2] memory h, bytes32[4] memory m, bytes8[2] memory t, bool f) public view returns (bytes32[2] memory) {
    bytes32[2] memory output;

    bytes memory args = abi.encodePacked(rounds, h[0], h[1], m[0], m[1], m[2], m[3], t[0], t[1], f);

    assembly {
        if iszero(staticcall(not(0), 0x09, add(args, 32), 0xd5, output, 0x40)) {
            revert(0, 0)
        }
    }

    return output;
}
```

## アドレス 0x3fd: vmLog\(str\) <a id="address-0x-3fc-vmlog-str"></a>

アドレス 0x3FD は、指定された文字列 `str` を特定のファイルに出力するか、またはそれをロガーモジュールに渡します。 詳細については、 [debug\_setVMLogTarget](../../../dapp/json-rpc/api-references/debug/logging.md#debug_setvmlogtarget) を参照してください。 このプリコンパイル済みコントラクトはデバッグ目的でのみ使用することに注意してください。 そして、Klaytn ノードの開始時に `--vmlog` オプションを有効にする必要があります。 また、vmLog の出力を確認するには、 Klaytn ノードのログレベルを 4 以上にする必要があります。 このプリコンパイル済みコントラクトは、Solidity コンパイラではサポートされていません。 このプリコンパイル済みコントラクトを呼び出すには、次のコードを使用できます。

```text
function callVmLog(bytes memory str) public {
    address(0x3fd).call(str);
}
```

## 住所 0x3fe: feePayer\(\) <a id="address-0x-3fd-feepayer"></a>

アドレス 0x3FE は、実行中のトランザクションの手数料支払者を返します。 このプリコンパイル済みコントラクトは、Solidity コンパイラではサポートされていません。 このプリコンパイル済みコントラクトを呼び出すには、次のコードを使用できます。

```text
function feePayer() internal returns (address addr) {
    assembly {
        let freemem := mload(0x40)
        let start_addr := add(freemem, 12)
        if iszero(call(gas, 0x3fe, 0, 0, 0, start_addr, 20)) {
          invalid()
        }
        addr := mload(freemem)
    }
}
```

## アドレス 0x3ff: validateSender\(\) <a id="address-0x-3fe-validatesender"></a>

アドレス0x3FFは送信者の署名をメッセージで検証します。 Klaytn [はアドレス](../../../klaytn/design/accounts.md#decoupling-key-pairs-from-addresses)から鍵ペアを分離するため、署名が対応する送信者によって適切に署名されていることを検証する必要があります。 これを行うには、このプリコンパイルされたコントラクトは 3 つのパラメータを受け取ります。

* 公開鍵を取得するための送信者のアドレス
* 署名の生成に使用されるメッセージハッシュです。
* 指定されたメッセージハッシュを持つ送信者の秘密鍵によって署名された署名

プリコンパイルされたコントラクトは、与えられた署名が送信者の秘密鍵によって適切に署名されていることを検証します。 Klaytn は複数の署名をネイティブにサポートしていることに注意してください。これは複数の署名が存在する可能性があることを意味します。 署名は 65 バイトの長さでなければなりません。

```text
function ValidateSender(address sender, bytes32 msgHash, bytes sigs) public returns (bool) {
    require(sigs.length % 65 == 0);
    bytes memory data = new bytes(20+32+sigs.length);
    uint idx = 0;
    uint i;
    for( i = 0; i < 20; i++) {
        data[idx++] = (bytes20)(sender)[i];
    }
    for( i = 0; i < 32; i++ ) {
        data[idx++] = msgHash[i];
    }
    for( i = 0; i < sigs.length; i++) {
        data[idx++] = sigs[i];
    }
    assembly {
        // skip length header.
        let ptr := add(data, 0x20)
        if iszero(call(gas, 0x3ff, 0, ptr, idx, 31, 1)) {
          invalid()
        }
        return(0, 32)
    }
}
```

