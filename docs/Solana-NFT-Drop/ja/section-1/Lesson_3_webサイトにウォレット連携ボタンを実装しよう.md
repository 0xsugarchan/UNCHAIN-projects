### 🛍 ウォレットに接続するボタンをレンダリングする

WebアプリケーションからPhantom Walletにアプリケーションへの接続を促すため、`connectWallet`ボタンを作成する必要があります。

web3の世界では、ウォレット接続ボタンは「サインアップ/ログイン」ボタンの役割を果たします。

`App.js`ファイルを下記の通り変更してください。

```jsx
// App.js
import React, { useEffect } from "react";
import "./App.css";
import twitterLogo from "./assets/twitter-logo.svg";

// 定数の宣言
const TWITTER_HANDLE = "_buildspace";
const TWITTER_LINK = `https://twitter.com/${TWITTER_HANDLE}`;

const App = () => {
  const checkIfWalletIsConnected = async () => {
    try {
      const { solana } = window;

      if (solana) {
        if (solana.isPhantom) {
          console.log("Phantom wallet found!");
          const response = await solana.connect({ onlyIfTrusted: true });
          console.log(
            "Connected with Public Key:",
            response.publicKey.toString()
          );
        }
      } else {
        alert("Solana object not found! Get a Phantom Wallet 👻");
      }
    } catch (error) {
      console.error(error);
    }
  };

  /*
   * コードが壊れないように、下記関数を定義しましょう。
   * 下記はその関数の実装です。
   */
  const connectWallet = async () => {};

  /*
   * ユーザーがまだウォレットをアプリに接続していないときに
   * このUIを表示します。
   */
  const renderNotConnectedContainer = () => (
    <button
      className="cta-button connect-wallet-button"
      onClick={connectWallet}
    >
      Connect to Wallet
    </button>
  );

  useEffect(() => {
    const onLoad = async () => {
      await checkIfWalletIsConnected();
    };
    window.addEventListener("load", onLoad);
    return () => window.removeEventListener("load", onLoad);
  }, []);

  return (
    <div className="App">
      <div className="container">
        <div className="header-container">
          <p className="header">🍭 Candy Drop</p>
          <p className="sub-text">NFT drop machine with fair mint</p>
          {/* Render your connect to wallet button right here */}
          {renderNotConnectedContainer()}
        </div>
        <div className="footer-container">
          <img alt="Twitter Logo" className="twitter-logo" src={twitterLogo} />
          <a
            className="footer-text"
            href={TWITTER_LINK}
            target="_blank"
            rel="noreferrer"
          >{`built on @${TWITTER_HANDLE}`}</a>
        </div>
      </div>
    </div>
  );
};

export default App;
```

これで、Webアプリケーションに「ウォレットに接続」というグラデーションボタンが表示されます。

![無題](/public/images/Solana-NFT-Drop/section-1/1_2_1.png)


**ユーザーがウォレットを Web アプリケーションに接続していない場合にのみ、`Connect to Wallet`ボタンが表示されます。**

そこで、このウォレットのデータをReactのstateに格納してみてましょう。そうすればボタンを表示するかどうかを判断するフラグとしても使えます。

`App.js`を修正します。

まずは下記のように`useState`をコンポーネントにインポートする必要があります。

```jsx
// App.js
import React, { useEffect, useState } from "react";
```

次に、 `checkIfWalletIsConnected`関数のすぐ上に進み、下記の`state`の宣言を追加します。

```jsx
// App.js
// State
const [walletAddress, setWalletAddress] = useState(null);
```

`state`を保持する準備ができたので、ここでいくつかコードを更新しましょう。

`App.js`を下記の通り修正してください。

```jsx
// App.js
import React, { useEffect, useState } from "react";
import "./App.css";
import twitterLogo from "./assets/twitter-logo.svg";

// Constants
const TWITTER_HANDLE = "ta_ka_sea0";
const TWITTER_LINK = `https://twitter.com/${TWITTER_HANDLE}`;

const App = () => {
  // State
  const [walletAddress, setWalletAddress] = useState(null);

  // Actions
  const checkIfWalletIsConnected = async () => {
    try {
      const { solana } = window;

      if (solana) {
        if (solana.isPhantom) {
          console.log("Phantom wallet found!");
          const response = await solana.connect({ onlyIfTrusted: true });
          console.log(
            "Connected with Public Key:",
            response.publicKey.toString()
          );

          /*
           * ユーザーの公開鍵を後から使える状態にします。
           */
          setWalletAddress(response.publicKey.toString());
        }
      } else {
        alert("Solana object not found! Get a Phantom Wallet 👻");
      }
    } catch (error) {
      console.error(error);
    }
  };

  const connectWallet = async () => {};

  const renderNotConnectedContainer = () => (
    <button
      className="cta-button connect-wallet-button"
      onClick={connectWallet}
    >
      Connect to Wallet
    </button>
  );

  useEffect(() => {
    const onLoad = async () => {
      await checkIfWalletIsConnected();
    };
    window.addEventListener("load", onLoad);
    return () => window.removeEventListener("load", onLoad);
  }, []);

  return (
    <div className="App">
      <div className="container">
        <div className="header-container">
          <p className="header">🍭 Candy Drop</p>
          <p className="sub-text">NFT drop machine with fair mint</p>
          {/* ウォレットアドレスを持っていない場合にのみ表示する条件を追加する */}
          {!walletAddress && renderNotConnectedContainer()}
        </div>
        <div className="footer-container">
          <img alt="Twitter Logo" className="twitter-logo" src={twitterLogo} />
          <a
            className="footer-text"
            href={TWITTER_LINK}
            target="_blank"
            rel="noreferrer"
          >{`built on @${TWITTER_HANDLE}`}</a>
        </div>
      </div>
    </div>
  );
};

export default App;
```

簡単に修正点を確認しましょう。

```jsx
// App.js
const checkIfWalletIsConnected = async () => {
  try {
    const { solana } = window;

    if (solana) {
      if (solana.isPhantom) {
        console.log("Phantom wallet found!");
        const response = await solana.connect({ onlyIfTrusted: true });
        console.log(
          "Connected with Public Key:",
          response.publicKey.toString()
        );

        /*
         * ユーザーの公開鍵を後から使える状態にします。
         */
        setWalletAddress(response.publicKey.toString());
      }
    } else {
      alert("Solana object not found! Get a Phantom Wallet 👻");
    }
  } catch (error) {
    console.error(error);
  }
};
```

ファントムウォレットを接続するとユーザーのウォレットからデータを受信しました。

これで、後で使用できるように状態に保存してみましょう。

```jsx
// App.js
{
  /* ウォレットアドレスを持っていない場合にのみ表示する条件を追加する */
}
{
  !walletAddress && renderNotConnectedContainer();
}
```

ここでは` state`に`walletAddress`が設定されていない場合にのみ、この`render`関数を呼び出すように指示しています。

したがって、ユーザーがウォレットを接続していないことを意味するウォレットアドレスがない場合は、ウォレットを接続するためのボタンを表示します。

### 😅 実際にウォレット接続する

このままだとボタンをクリックしても何も起こりません。

`connectWallet`関数のロジックをコーディングします。
`App.js`の`connectWallet`関数を下記の通り修正しましょう。

```jsx
// App.js
const connectWallet = async () => {
  const { solana } = window;

  if (solana) {
    const response = await solana.connect();
    console.log("Connected with Public Key:", response.publicKey.toString());
    setWalletAddress(response.publicKey.toString());
  }
};
```

ユーザーがウォレットを接続したい場合、`solana`オブジェクトで`connect`関数を呼び出して、ユーザーのウォレットでWebアプリケーションを承認を実施します。

そうすると、ユーザーのウォレット情報（ウォレットアドレスなど）にアクセスできます。

`walletAddress`関数を実装できたら、Webアプリケーションからウォレットを接続し、その後`Connect to Wallet`ボタンが表示されないか確認します。

ここからはブラウザで動作を確認します。Webアプリケーションを開き、`Connect to Wallet`ボタンをクリックしましょう。

まずはWebアプリケーション上のページを更新し、`Connect to Wallet`ボタンをクリックするとポップアップが表示されるので、指示に従って接続してください。

ボタンが消えることを確認できたら、Webアプリケーションを更新してみてください。

`checkIfWalletIsConnected`関数が呼び出され、ボタンがすぐに消えます。コンソールには公開鍵も出力されています。

基本的なUIとユーザー認証が実装できました。

次のセクションでは、Solanaプログラムを呼び出すために必要な関数を使用してすべてのセットアップを取得し、データを取得します。このままではWebアプリケーションが寂しいので機能を実装してみましょう。

> ⚠️: 注意
>
> Phantom Wallet の設定画面(歯車をクリック)に、[信頼済みアプリ]があります。これを開くと、ウォレット接続している WEB アプリが表示されます。
>
> ローカルで実行している場合は、 `localhost：3000`が表示されます。これを取り消すことで簡単に連携解除可能です。
>
> 実際に連携解除しアプリを更新すると、`Connect to Wallet`ボタンが表示されます。

### 🙋‍♂️ 質問する

ここまでの作業で何かわからないことがある場合は、Discordの`#solana`で質問をしてください。

ヘルプをするときのフローが円滑になるので、エラーレポートには下記の3点を記載してください ✨

```
1. 質問が関連しているセクション番号とレッスン番号
2. 何をしようとしていたか
3. エラー文をコピー&ペースト
4. エラー画面のスクリーンショット
```

---

ぜひ、あなたのフロントエンドのスクリーンショットを`#solana`に投稿してください 😊

あなたの成功をコミュニティで祝いましょう 🎉

次のレッスンでは、Solanaの開発環境を構築します!
