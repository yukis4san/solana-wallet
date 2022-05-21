###  🖥 このレッスンの参考動画URL
[Dapp University](https://youtu.be/CgXQC4dbGUE?t=473)

### 🍽 テンプレートをあなたの GitHub にフォークする

このセクションでは、Web サイトの構築を通して、フロントエンドがスマートコントラクトとどのように関連するのか学びます。

まず、ターミナルに向かいましょう。

作業したいディレクトリに移動したら、次のコマンドを実行します。

```bash
mkdir Yield-Farm
cd Yield-Farm
```

次にテンプレートをフォークしたのちに、それをローカル環境にクローンします。

まだ GitHub のアカウントをお持ちでない方は、[こちら](https://qiita.com/okumurakengo/items/848f7177765cf25fcde0) の手順に沿ってアカウントを作成してください。

GitHub のアカウントをお持ちの方は、[こちら](https://github.com/shiftbase-xyz/yield-farm-starter-project) から、テンプレートである `yield-farm-starter-project` をあなたの GitHub にフォークしましょう。

フォークの方法は、[こちら](https://docs.github.com/ja/get-started/quickstart/fork-a-repo) を参照してください。

ご自身の GitHub アカウントにフォークした `yield-farm-starter-project` リポジトリをあなたのローカル環境にクローンしましょう。

下図のように、`Code` ボタンをクリックした後、`SSH` を選択し、Git リンクをコピーしましょう。

![](/public/images/1-ETH-dApp/section-2/2_1_1.png)

> ✍️: SSH の設定を行う
>
> Github のレポジトリをクローンする際に、SSHKey を作成し、GitHub に公開鍵を登録する必要があります。
>
> SSH（Secure SHell）はネットワークを経由してマシンを遠隔操作する仕組みのことで、通信が暗号化されているのが特徴的です。
>
> 主にクライアント（ローカル）からサーバー（リモート）に接続をするときに使われます。この SSH の暗号化について、仕組みを見ていく上で重要になるのが秘密鍵と公開鍵です。
>
> まずはクライアントのマシンで秘密鍵と公開鍵を作り、公開鍵をサーバーに渡します。そしてサーバー側で「この公開鍵はこのユーザー」というように、紐付けを行っていきます。
>
> 自分で管理して必ず見せてはいけない秘密鍵と、サーバーに渡して見せても良い公開鍵の 2 つが SSH の通信では重要になってきます。
> Github における SSH の設定は、[こちら](https://docs.github.com/ja/authentication/connecting-to-github-with-ssh) を参照してください!
ターミナルで先ほど作成した `yield-farm-starter-project` ディレクトリに移動し、先ほどコピーしたリンクを貼り付け、下記を実行してください。

```bash
git clone コピーした_github_リンク
```

この段階で、フォルダ構造は下記のようになっているはずです。

```
Yield-Farm
   |_ yield-farm-starter-project
```

ターミナル上で `yield-farm-starter-project` に移動して下記を実行しましょう。

```bash
npm install
```

`npm` コマンドを実行することで、JavaScript ライブラリのインストールが行われます。

### 💻 ネットワーク情報の編集

テンプレートファイルの中にある`truffle-config.js`というファイルを見てみましょう。ここにはテストに使うGanacheの情報を打ち込む必要があります。

下のGanacheの画像の `NETWORK ID` と書いてある部分の４桁の数字を`truffle.config.js` の `network_id` というところに打ち込んでください。

![](/public/images/8-Ganache-Yield-Farm/section-1/12_1_2.png)
![](/public/images/8-Ganache-Yield-Farm/section-1/12_1_3.png)

これで Ganache のネットワークを使ってテストができるようになりました！

### 👀 `contracts` フォルダの中身の確認する

次に `scr/contracts` にあるスマートコントラクトのコードが書かれているファイルの確認を行いましょう。

このファイルには `MockDaiToken.sol` と `DappToken.sol` が含まれています。

これらのファイルには、それぞれトークンのコードが含まれています。MockDaiトークンは、AstarやLinkなど、既存の仮想通貨を模したトークンです。

一方、Dappトークンは、ユーザーがステークしたコインやトークンに対して付与されるコミュニティ・トークンを表します。
### 🪙 ERC-20トークンのしくみ

ERC-20は、イーサリアムトークンの構築方法に関するAPI仕様です。これは、トークンを様々なユースケースでサポートできるようにするために、Ethereumコミュニティによって採用された規格です。

ERC-20規格に関する詳しい説明は[こちら](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20.md)をご覧ください。

この規格に準拠したトークンを `MockDaiToken.sol` と `DappToken.sol` で作成することになります。

ERC-20トークンの場合、スマートコントラクトはトークンの操作に関連するすべてのアクションを管理し、トークンの所有権と口座残高を追跡します。

ERC-20標準規格を使用することで、トークンは以下のユースケースに準拠することが保証されます。

- ウォレット間のトランスファー（あるアカウントから別のアカウントへのトークンの送信）
- 暗号通貨取引所での売買
- クラウドセール（ICO）でのトークンの購入

### 🧪 `DappToken.sol` を深ぼる

`DappToken.sol` と `MockDaiToken.sol` に記述されている機能はほぼ同じです。

簡単のために、ERC-20規格に準拠した機能を理解するために、`DappToken.sol` の内容を見てみましょう。

まず、`DappToken.sol` の4-10行目に注目してください。

```javascript
// DappToken.sol
// トークン名を格納
string  public name = "DApp Token";
// 暗号通貨交換用のトークンシンボルを格納
string  public symbol = "DAPP";
// 存在するトークンの総供給量を格納
uint256 public totalSupply = 1000000000000000000000000; // 1 million tokensを供給
uint8   public decimals = 18;    // トークン名を格納
string  public name = "DApp Token";
// 暗号通貨交換用のトークンシンボルを格納
string  public symbol = "DAPP";
// 存在するトークンの総供給量を格納
// 1 million tokensを供給
uint256 public totalSupply = 1000000000000000000000000;
uint8   public decimals = 18;
```

ここでは、`DappToken.sol` で使用する変数を定義し、値を格納しています。

次に、25行目を見ていきましょう。
```javascript
// DappToken.sol
mapping(address => uint256) public balanceOf;
```

この `balanceOf` というマッピングは、「Key が `address` で Value が `uint256` 」という解釈をします。

`balanceOf` は、実際のユーザーの `address` を引数に、そのユーザーアドレスの残高 (バランス) を返します。

そして、このマッピングは `public` なので、コントラクトの外から呼び出すことができます。

**1️⃣ `transfer`**

次に、`transfer` 関数（ `DappToken.sol` の31-38行）を見ていきましょう。

```javascript
// DappToken.sol
// ユーザーがトークンを別のアカウントに送信できるようにする機能を実装
function transfer(address _to, uint256 _value) public returns (bool success) {
   // 移動したい額のトークンがユーザーのアドレスに存在するか確認
   // 存在しない場合は、エラーを返す
   require(balanceOf[msg.sender] >= _value);
   // 関数を呼び出したユーザーアドレスから _value の金額を引き抜く
   balanceOf[msg.sender] -= _value;
   // 送金先のアドレスに _value の金額を足す
   balanceOf[_to] += _value;
   // Transferイベントを実行する
   emit Transfer(msg.sender, _to, _value);
   return true;
}
```

`transfer` 関数は、あるアドレス（ `_from` ）から別のアドレス（ `_to` ）へ、ある量のトークン（ `_value` ）を転送する関数です。その際に、必ず、 `Transfer` イベントを発生させなければいけません。

`Transfer` イベントを発生させるために、`DappToken.sol` の12-16行目に以下の `event` が定義されています。

```javascript
// DappToken.sol
event Transfer(
   address indexed _from,
   address indexed _to,
   uint256 _value
);
```
`Transfer` イベントには以下の変数が必要になります。
- `_from`: トークンを送る人のアドレス
- `_to`: トークンが送るられる人のアドレス
- `_value`: 送られるトークンの金額

トークンがピアツーピアで転送されるときは、必ず `Transfer` イベントを発生させる必要があります。新しいトークンを作成する際には、 `_from` アドレスを `0x0` に設定して、 `Transfer` イベントを発生させるようにします。

**2️⃣ `approve`**

次に、`approve` 関数を見ていきましょう。

まず、`allowance` マッピング（ `DappToken.sol` の26行目）に着目してください。

```javascript
mapping(address => mapping(address => uint256)) public allowance;
```

`allowance` マッピングはネストされたマッピングの形をとっていますが、至って簡単なコンセプトです。以下を見ていきましょう。

```javascript
mapping(address => mapping(address => uint256)) public allowance;
          (1)                (2)        (3)
```

まず、(1)-(3)の変数の意味を見ていきましょう。

- `(1)`: コントラクトオーナー( `owner` ）のアドレス
- `(2)`: `spender`（任意のユーザー）のアドレス
- `(3)`: `spender` が引き出すことができる金額（ `_value` ）

`allowance` マッピングは `(1)` の `owner` のアドレスを Key として、そのコントラクトを叩いたユーザー ( `spender` ) が引き出すことのできる金額 ( `_value` ) を返すマッピングです。

次に、`approve` 関数（`DappToken.sol` の41-45行目）を見ていきましょう。

```javascript
// DappToken.sol
// 別のアカウントがトークンを使用できるようにする機能を実装
function approve(address _spender, uint256 _value) public returns (bool success) {
   allowance[msg.sender][_spender] = _value;
   emit Approval(msg.sender, _spender, _value);
   return true;
}
```
`approve` 関数は、任意のユーザー( `spender` )が一定量( `_value` )のトークンを繰り返し引き出せるようにする関数です。この関数が再び呼び出された場合、現在引き出し可能な金額は新しい `_value` 値で上書きされます。

次に、26行目で定義した `allowance` マッピングが `approve` 関数の中で呼び出されていることを確認しましょう。

```javascript
// DappToken.sol
allowance[msg.sender][_spender] = _value;
```

ここでは `_spender`（任意のユーザー）が `msg.sender`（コントラクトのオーナーユーザー）からまだ引き出すことができる金額（ `_value` ）を設定しています。

最後に、`Approval` イベントを発生させていることに着目してください。

```javascript
// DappToken.sol
emit Approval(msg.sender, _spender, _value);
```

`Approval` イベントは `approve` 関数の呼び出しに成功したときに発生します。

`Approval` イベントを発生させるために、`DappToken.sol` の18-22行目に以下の `event` が定義されていることを確認してください。

```javascript
// DappToken.sol
event Approval(
   address indexed _owner,
   address indexed _spender,
   uint256 _value
);
```

**3️⃣ `transferFrom`**

最後に、`DappToken.sol` の47-56行目に記載されている `transferFrom` 関数を見ていきましょう。

```javascript
// DappToken.sol
// 別のアカウントからトークンを転送できるようにする
function transferFrom(address _from, address _to, uint256 _value) public returns (bool success) {
   require(_value <= balanceOf[_from]);
   require(_value <= allowance[_from][msg.sender]);
   balanceOf[_from] -= _value;
   balanceOf[_to] += _value;
   allowance[_from][msg.sender] -= _value;
   emit Transfer(_from, _to, _value);
   return true;
}
```

`transferFrom` は、`_value` の量のトークンをアドレス `_from` からアドレス `_to` に転送し、`Transfer` イベントを発生させる関数です。

`transferFrom` は、コントラクトがあなたに代わってトークンを転送することを許可する、トークン引き出しワークフローに使用されます。これは例えば、コントラクトがあなたに代わってトークンを転送したり、サブ通貨で手数料を請求したりすることを可能にするために使用できます。

### 🙋‍♂️ 質問する

ここまでの作業で何かわからないことがある場合は、Discord の `#section-1` で質問をしてください。

ヘルプをするときのフローが円滑になるので、エラーレポートには下記の 3 点を記載してください ✨

```
1. 質問が関連しているセクション番号とレッスン番号
2. 何をしようとしていたか
3. エラー文をコピー&ペースト
4. エラー画面のスクリーンショット
```

---
テンプレートのクローン成功おめでとうございます！次はいよいよコーディングをしていきましょう！
