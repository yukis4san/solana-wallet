### Sign and send

devnetでテストトークンを使ってアカウントに資金を供給した後は、その資金をどのように使うか考えましょう。実際のアプリケーションでは、SOLを他のトークンと交換したり、商品やサービスの代金をSOLで支払ったり、あるいは誰かにSOLを贈ったりしたいと思うかもしれない。そのためには、自分の口座から他の口座に資金を送金するよう、ネットワーク台帳に知らせる方法が必要です。

第一原理から考えると、送り手と受け手の2つのアドレスを受け取る関数が必要であることがわかる。また、送金するSOLまたはLamportの数を入力する必要があることも分かっています。

しかし、もう一つ重要な要素がある。それは、私たちが実際にその資金の所有者であり、送金を承認していることをネットワークに証明する必要があることだ。

### Paper checks

大家さんへの支払いに使うような、伝統的な紙の小切手を考えてみましょう。小切手の左上には、あなたの名前と住所が印刷されています。受取人の名前を記入する欄があり、支払金額を記入する欄もあります。最後に、あなたが送金を承認していることを銀行に証明するために、小切手に署名する欄があります。

### Blockchain transfers

資金移動のブロックチェーンモデルもかなり似ています。ネットワークがその取引を有効であると確認し、対応する残高を変更できるように、取引に署名する方法が必要です。

暗号署名のコンセプトは魅力的ですが、このレッスンの範囲を大きく超えています。しかし、ハッシュアルゴリズムと一方向性関数とともに、あなたの鍵はまさにこの目的のために設計されていることを思い出してください。署名は送信者を認証し、受信者に対してはメッセージが漏洩していないことを証明します。

これらの構成要素を念頭に置いて、トランザクションを送信し確認する方法をドキュメントで検索する準備が整いました。

### 導入

ウォレットダッシュボードの送信ボタンをクリックすると、引き出しコンポーネントが開き、紙の小切手のような構造のフォームが表示されます。これはトランザクションのすべてのコンポーネントを含んでいますが、SignとSendボタンはまだ動作していません。
components/TransactionLayout/index.tsx に移動すると、部分的に実装された転送関数が表示されます。
ステップ 3 とステップ 4 に基づいて、ネットワークへの接続が必要であることがわかります。もう一度、以前のコードを活用しましょう。

```javascript
const connection = new Connection(clusterApiUrl(NETWORK), "confirmed");

console.log(connection);
// > Connection {_commitment: 'confirmed', _confirmTransactionInitialTimeout: undefined, _rpcEndpoint: 'https://api.devnet.solana.com', _rpcWsEndpoint: 'wss://api.devnet.solana.com/', _rpcClient: ClientBrowser, …}
```

### Transactions

ある種のトランザクション・オブジェクトを構築し、それをコネクションを通じて送信する必要があることは推測できますが、どのようにそれを行うかはすぐにはわかりません。ドキュメントを検索してみると、トランザクションを送信する方法は2つあるようです。ConnectionのsendTransactionメソッドと一般的な関数sendAndConfirmTransactionがあります。

トランザクションを送信した後に残高を更新するために確認を要求したいので、後者を試してみるのが良さそうです。

![](/public/images/8-Solana-Wallet/3_2_1.png)

この関数の仕様を読むと、コネクション、ある種のトランザクション・オブジェクト、署名者の配列を渡す必要がありそうです。さらに、この関数は「トランザクションに署名し、送信し、確認する」と書かれているので、正しい方向に進んでいるように見えます。まずはこの関数をインポートして使い、次にその入力を構築していくことにしましょう。

```javascript
const confirmation = await sendAndConfirmTransaction(
  connection,
  transaction,
  signers
);
```

すでにコネクションはできているので、すぐにトランザクション・パラメータに目を向けることができます。ドキュメントにある[Transactionクラスへのリンク](https://solana-labs.github.io/solana-web3.js/classes/Transaction.html)をたどると、トランザクションオブジェクトを作成するには、そのコンストラクタを使用することができるようです。

```javascript
// サンプルコード
const transaction = new Transaction();

console.log(transaction);
// > Transaction {signatures: Array(0), feePayer: undefined, instructions: Array(0), recentBlockhash: undefined, nonceInfo: undefined}
```

作成したトランザクションを見ると、送信者、受信者、金額といった有効なトランザクションに必要な構成要素が明らかに欠けていますが、入力できるはずの使い慣れた構造を持っているようです。それぞれを調査することもできますが、手始めとして有望だと思われるインストラクション・プロパティにすぐに飛びつきましょう。

残念ながら、ドキュメントには直感的な進め方が書かれていませんが、私たちの技術的な洗練さを活用することができます。 `SystemProgram` という非常に便利なクラスに `transfer` メソッドがあり、「ある口座から別の口座にランプを移す取引命令を生成する」のだそうです。これはまさに私たちが必要としているもののように思えます。

### Transfer

`transfer` メソッドは `TransferParams` オブジェクトを受け取ります。

![](/public/images/8-Solana-Wallet/3_2_2.png)

- 送信者
- 受信者
- 金額(lamports)

この3つを要求します。これは私たちが取引に使いたいデータと一致しており、コンテキストのアカウントとフォームの入力から取得することができます。その結果、次のような命令が得られます。

```javascript
// サンプルコード
const instructions = SystemProgram.transfer({
  fromPubkey: account.publicKey,
  toPubkey: new PublicKey("受信者のアドレス"),
  lamports: LAMPORTS_PER_SOL,
});
```

フォームでは文字列としてキーを渡していますが、toPubkeyプロパティではPublicKeyの型を想定しているため、受信者用のPublicKeyをインスタンス化しなければならないことに注意してください。

これらをトランザクションに組み込むには、addメソッドを使用します。

```javascript
transaction.add(instructions);
```

あるいは、ちょっとしたオンザフライのリファクタリングで、命令を作成した後にトランザクションをインスタンス化し、すぐに追加することもできる。

```javascript
const transaction = new Transaction().add(instructions);
```

`sendAndConfirmTransaction` 関数の3つのパラメータのうち、2つ（`connection` と `transaction` ）を用意しました

### Signing

次に、`signers` の配列が必要です。この関数の仕様から、`signers` は少なくとも1つの `Signer` オブジェクトを含む配列になることが分かっています。[ドキュメント](https://solana-labs.github.io/solana-web3.js/interfaces/Signer.html)で `Signer` の型を確認すると、`publicKey`と `secretKey` という2つのプロパティを持つオブジェクトのようです。

![](/public/images/8-Solana-Wallet/3_2_3.png)

`account` から両方を取得できるので、`signers` の配列を作成することができます。

```javascript
const signers = [
  {
    publicKey: account.publicKey,
    secretKey: account.secretKey,
  },
];
```

### Send and confirm

これで3つのパラメータがすべて完了したので、最後に `sendAndConfirmTransaction` を呼び出して、その確認を待つことができるようになりました。

```javascript
const confirmation = await sendAndConfirmTransaction(
  connection,
  transaction,
  signers
);
```

これで、Solanaアカウント間で資金を移動することができる、完全に機能する機能が完成しました。この機能を完成させるには、資金移動後にアカウントの残高を更新するために `refreshBalance` 関数を呼び出すことを確認する必要があります。

```javascript
await refreshBalance();
```

### 送金機能を完成させよう

以上を踏まえて、 送金機能を完成させていきましょう！

まず、必要な関数やクラスをインポートします。

```javascript
import { Keypair, Connection, clusterApiUrl, LAMPORTS_PER_SOL, SystemProgram, PublicKey, Transaction, sendAndConfirmTransaction }
```

送金処理を行う `handleTransfer` 関数を定義し、中身を書いていきましょう。

```javascript
const handleTransfer = async (e) => {
  e.preventDefault();

  // 受信者のアドレスはフォームに入力された値を使用
  const toAddress = e.target[0].value;
  console.log('toAddress', toAddress);

  try {
    console.log('送金中...')
    setTransactionSig("");

    const connection = new Connection(clusterApiUrl(NETWORK), "confirmed");

    const instructions = SystemProgram.transfer({
      fromPubkey: account.publicKey,
      toPubkey: new PublicKey(toAddress),
      lamports: LAMPORTS_PER_SOL,
    });

    const transaction = new Transaction().add(instructions);

    const signers = [
      {
        publicKey: account.publicKey,
        secretKey: account.secretKey,
      },
    ];

    const confirmation = await sendAndConfirmTransaction(
      connection,
      transaction,
      signers
    );
    console.log('confirmation', confirmation);

    setTransactionSig(confirmation);

    await refreshBalance();

    console.log('送金が完了しました!!!')
  } catch (error) {
    console.log('error', error);
  }
};
```

そして、受信者アドレスを入力するフォームと、送金ボタンをレンダリングします。
ついでに、実際のトランザクションをあとで確認できるように、送金完了したら `Solana Explorer` へのリンクを表示してあげると良さそうです！

```javascript
<div>
  <h2 className="p-2 border-dotted border-l-4 border-l-indigo-400">STEP5: 送金機能を実装する</h2>
  {account && (
    <>
      <form onSubmit={handleTransfer} className="my-6">
        <div className="flex items-center border-b border-indigo-500 py-2">
          <input
            type="text"
            className="w-full text-gray-700 mr-3 p-1 focus:outline-none"
            placeholder="送金先のウォレットアドレス"
          />
          <input
            type="submit"
            className="p-2 text-white bg-indigo-500 focus:ring focus:ring-indigo-300 rounded-lg cursor-pointer"
            value="送金"
          />
        </div>
      </form>
      {transactionSig && (
        <>
          <span className="text-red-600">送金が完了しました！</span>
          <a
            href={`https://explorer.solana.com/tx/${transactionSig}?cluster=${NETWORK}`}
            className="border-double border-b-4 border-b-indigo-600"
            target='_blank'
          >
            Solana Block Explorer でトランザクションを確認する
          </a>
        </>
      )}
    </>
  )}
</div>
```

### ✅ 動作確認

おめでとうございます！これで送金機能が完成しました。

実際に送金して、相手のウォレットの残高が増えているかを確認してください。

自分ひとりで二つのウォレットを同時に作成＆表示したい場合は、もう１つターミナルを立ち上げて `npm run dev` を実行します。

そうすると、別のポートでアプリケーションが起動されますので、そちらでウォレットの作成をし、アドレスをコピーして１つ目のウォレットから送金を試してみる方法がおすすめです！

※今回の実装では、送金するSOLは `1 SOL` で固定となっていますが、送金する際にガス代と呼ばれる手数料が必要になるため、残高がちょうど `1 SOL` だと、送金が失敗します。そういったことも確かめつつ、残高を `2 SOL` などにしてから送金を試してみてくださいね🥭

### 📝 このセクションで追加したコード

```diff
 import { useState } from "react";
-import { Keypair, Connection, clusterApiUrl, LAMPORTS_PER_SOL } from "@solana/web3.js";
+import { Keypair, Connection, clusterApiUrl, LAMPORTS_PER_SOL, SystemProgram, PublicKey, Transaction, sendAndConfirmTransaction } from "@solana/web3.js";
 import * as Bip39 from "bip39";

 const NETWORK = 'devnet';
@@ -8,6 +8,7 @@ export default function Home() {
   const [mnemonic, setMnemonic] = useState(null);
   const [account, setAccount] = useState(null);
   const [balance, setBalance] = useState(null);
+  const [transactionSig, setTransactionSig] = useState("");

   const generateWallet = () => {
     const generatedMnemonic = Bip39.generateMnemonic();
@@ -61,6 +62,50 @@ export default function Home() {
     }
   };

+
+  const handleTransfer = async (e) => {
+    e.preventDefault();
+    const toAddress = e.target[0].value;
+    console.log('toAddress', toAddress);
+
+    try {
+      console.log('送金中...')
+      setTransactionSig("");
+
+      const connection = new Connection(clusterApiUrl(NETWORK), "confirmed");
+
+      const instructions = SystemProgram.transfer({
+        fromPubkey: account.publicKey,
+        toPubkey: new PublicKey(toAddress),
+        lamports: LAMPORTS_PER_SOL,
+      });
+
+      const transaction = new Transaction().add(instructions);
+
+      const signers = [
+        {
+          publicKey: account.publicKey,
+          secretKey: account.secretKey,
+        },
+      ];
+
+      const confirmation = await sendAndConfirmTransaction(
+        connection,
+        transaction,
+        signers
+      );
+      console.log('confirmation', confirmation);
+
+      setTransactionSig(confirmation);
+
+      await refreshBalance();
+
+      console.log('送金が完了しました!!!')
+    } catch (error) {
+      console.log('error', error);
+    }
+  };
+
   return (
     <div className="p-10">
       <h1 className="text-5xl font-extrabold tracking-tight text-gray-900">
@@ -156,6 +201,36 @@ export default function Home() {

       <div>
         <h2 className="p-2 border-dotted border-l-4 border-l-indigo-400">STEP5: 送金機能を実装する</h2>
+        {account && (
+          <>
+            <form onSubmit={handleTransfer} className="my-6">
+              <div className="flex items-center border-b border-indigo-500 py-2">
+                <input
+                  type="text"
+                  className="w-full text-gray-700 mr-3 p-1 focus:outline-none"
+                  placeholder="送金先のウォレットアドレス"
+                />
+                <input
+                  type="submit"
+                  className="p-2 text-white bg-indigo-500 focus:ring focus:ring-indigo-300 rounded-lg cursor-pointer"
+                  value="送金"
+                />
+              </div>
+            </form>
+            {transactionSig && (
+              <>
+                <span className="text-red-600">送金が完了しました！</span>
+                <a
+                  href={`https://explorer.solana.com/tx/${transactionSig}?cluster=${NETWORK}`}
+                  className="border-double border-b-4 border-b-indigo-600"
+                  target='_blank'
+                >
+                  Solana Block Explorer でトランザクションを確認する
+                </a>
+              </>
+            )}
+          </>
+        )}
       </div>
     </div>
   )
```

### ☕️ 豆知識

Solana Explorerは、特定のブロック、アカウント、トランザクション、コントラクト、トークンをネットワーク単位で検索できるシンプルなダッシュボードです。検索した項目に関連するすべての情報が表示されます。

この場合、チェックのリンクをクリックすると、今発行した送金の基本的な概要が表示されます。ページの真ん中には、私たちの送金の情報が表示されます。主に、送信者（あなたの公開アドレス）とあなたが送ったSOLの量、受信者（もう一方の公開アドレス）と彼らが受け取ったSOLの量が表示されます。なお、SOLを送った契約先に送り返したかどうかは、取引履歴の前の取引をクリックすることで確認できます。
手数料（SOL）」と書かれたフィールドがあることに気がつくかもしれません。Account Input(s)までスクロールすると、お客様のアカウントに請求されたこともわかります。受取人はあなたが指定した資金を受け取りましたが、あなたの口座からはあなたが送った金額と少額の手数料が差し引かれました。これらの取引手数料は、取引を処理するために費やした計算能力に対してバリデーターに報酬を与えるためのものです。
この時点で、Solanaウォレットは、1つの大きな欠陥を除いて、ほぼ完成している。ウォレットを作成し、そこから資金を移動させることはできる。しかし、既存のウォレットにアクセスすることはできない。ステップ6では、再びBip39ライブラリを活用して、ニーモニックフレーズに基づいてアカウントにアクセスすることで、この問題を解決することにします。
