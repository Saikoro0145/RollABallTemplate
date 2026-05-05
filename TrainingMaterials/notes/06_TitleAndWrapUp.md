# 第6回 タイトル画面と全体まとめ

- 時間: 95分
- 対象: C#のif/while/変数/配列を学習済みで、クラス・インターフェースは未学習の新入生
- ゴール: タイトル画面からInGameへ遷移し、6回で作った全スクリプトの関係を確認する。
- 形式: 1回95分、演習なし、講師の実装と説明で徐々に完成形へ近づける

## スライド別講師ノート

### Slide 01 前回の状態

**画面要素**
- InGameシーン内のゲームループは完成
- 開始、プレイ、クリア、ゲームオーバーが揃った
- ただし入口となるタイトル画面がない
- スクリーンショット: `Assets\Screenshots\training_ingame_play_countdown.png`

**講師ノート**

ゲーム本編はできているので、最後は入口と全体整理を行う。

### Slide 02 今日追加するもの

**画面要素**
- Titleシーンを用意する
- StartボタンでInGameへ移動する
- 全スクリプトの役割を整理する
- スクリーンショット: `Assets\Screenshots\training_title_play_game_view.png`

**講師ノート**

TitleManagerとBuild Settingsを扱う回。

### Slide 03 シーンを分ける理由

**画面要素**
- タイトル画面とゲーム本編は役割が違う
- SceneManagerで画面を切り替える
- 小さな単位に分けると管理しやすい

**講師ノート**

Webページの画面遷移のようなものとして説明すると伝わりやすい。

### Slide 04 Title.unity

**画面要素**
- Canvasにタイトルとボタンを置く
- StartButtonをTitleManagerに渡す
- 見た目と処理をつなぐ
- スクリーンショット: `Assets\Screenshots\training_title_scene_view.png`

**講師ノート**

SceneビューまたはGameビューでUI構成を確認する。

### Slide 05 Buttonの仕組み

**画面要素**
- クリックされたときに関数を呼ぶ
- OnClickに処理を登録する
- コードからAddListenerで登録できる

**講師ノート**

イベントという言葉は出してもよいが、詳細には入らない。

### Slide 06 TitleManager

**画面要素**
- タイトル画面だけを担当する
- StartButtonをInspectorで受け取る
- 押されたらInGameを読み込む
- コード抜粋: `titleFields`

**講師ノート**

InGameManagerとは別の管理役。シーンごとに担当を分ける。

```csharp
public Button StartButton;
```

### Slide 07 StartButton

**画面要素**
- Button型の変数でUIボタンを参照する
- Inspectorからドラッグ&ドロップする
- Startでクリック時の処理を登録する
- コード抜粋: `titleFields`

**講師ノート**

Button型はUnity UIの部品として説明する。

```csharp
public Button StartButton;
```

### Slide 08 Start()

**画面要素**
- AddListenerで押された時の関数を登録
- 関数名だけを渡す
- ゲーム開始前の準備として一度だけ行う
- コード抜粋: `titleStart`

**講師ノート**

OnStartButtonClicked()ではなくOnStartButtonClickedを渡す点は軽く触れる。

```csharp
private void Start()
{
    StartButton.onClick.AddListener(OnStartButtonClicked);
}
```

### Slide 09 OnStartButtonClicked()

**画面要素**
- SceneManager.LoadSceneを使う
- シーン名InGameを指定する
- Build Settingsへの登録が必要
- コード抜粋: `titleClick`

**講師ノート**

文字列のシーン名とBuild Settingsの登録名が一致している必要がある。

```csharp
private void OnStartButtonClicked()
{
    SceneManager.LoadScene("InGame");
}
```

### Slide 10 Build Settings

**画面要素**
- TitleとInGameを登録する
- タイトルからゲーム本編へ遷移できるようにする
- シーン名の打ち間違いに注意する
- スクリーンショット: `Assets\Screenshots\training_title_play_game_view.png`

**講師ノート**

Unity側の設定を忘れるとコードが正しくても遷移しない。

### Slide 11 OnDestroy()

**画面要素**
- 登録したListenerを解除する
- 不要な参照が残ることを防ぐ
- AddListenerとRemoveListenerをセットで見る
- コード抜粋: `titleDestroy`

**講師ノート**

初心者には高度だが、完成コードとして丁寧な後片付けをしていると説明する。

```csharp
private void OnDestroy()
{
    StartButton.onClick.RemoveListener(OnStartButtonClicked);
}
```

### Slide 12 完成ゲームの流れ

**画面要素**
- TitleでStartを押す
- Countdown後にPlayingへ入る
- ClearまたはGameOverで終了しRetryできる

**講師ノート**

6回で作った流れをプレイ順で確認する。

### Slide 13 全スクリプト関係図

**画面要素**
- PlayerController: 移動と収集
- InGameManager: 状態管理
- UI系Controller: 表示と入力の補助

**講師ノート**

ここで全ファイルが登場していることを確認する。

### Slide 14 6回のまとめ

**画面要素**
- 小さい機能を足して完成形に近づけた
- 既習のif/while/変数/配列をゲーム内で使った
- 次に学ぶクラス・参照・イベントの入口にもなった
- スクリーンショット: `Assets\Screenshots\training_title_play_game_view.png`

**講師ノート**

新人研修として、今後のC#学習につながるポイントを締める。
