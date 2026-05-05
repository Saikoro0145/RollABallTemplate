# 第3回 ゲームクリア判定

- 時間: 95分
- 対象: C#のif/while/変数/配列を学習済みで、クラス・インターフェースは未学習の新入生
- ゴール: 全アイテム取得を判定し、クリア表示とプレイヤー停止まで作る。
- 形式: 1回95分、演習なし、講師の実装と説明で徐々に完成形へ近づける

## スライド別講師ノート

### Slide 01 前回の状態

**画面要素**
- アイテムに触れると消える
- ただし全て取っても何も起きない
- ゲームの終わりを管理する係が必要
- スクリーンショット: `Assets\Screenshots\training_ingame_play_gameplay.png`

**講師ノート**

「終わりがない」状態を見せ、管理役の必要性を自然に出す。

### Slide 02 今日追加するもの

**画面要素**
- 残りアイテム数を確認する
- 0個ならゲームクリアにする
- クリア表示とプレイヤー停止を行う
- スクリーンショット: `Assets\Screenshots\training_ingame_play_gameplay.png`

**講師ノート**

第3回ではタイマーはまだ扱わず、クリア判定に集中する。

### Slide 03 ゲーム全体を見る係

**画面要素**
- PlayerControllerはプレイヤーだけを見る
- InGameManagerはゲーム全体の流れを見る
- 役割を分けるとコードが追いやすい

**講師ノート**

クラス設計の深い話ではなく、係分担として説明する。

### Slide 04 UIの準備

**画面要素**
- GameClearTextを画面に置く
- 最初は非表示にする
- クリア時だけ表示する
- スクリーンショット: `Assets\Screenshots\training_ingame_play_gameplay.png`

**講師ノート**

UIはTextMeshProを前提に説明する。

### Slide 05 Inspector参照

**画面要素**
- ManagerからPlayerControllerを参照する
- GameClearTextも参照する
- public変数にドラッグ&ドロップで接続する
- コード抜粋: `managerFields`

**講師ノート**

スクリプト同士の接続は、直接newしない。Unity上の部品をInspectorで結ぶ。

```csharp
public CountDownController CountDownController;
public PlayerController PlayerController;
public TimerController TimerController;
public TMP_Text GameClearText;
public TMP_Text ResultText;
public TMP_Text GameOverText;
```

### Slide 06 InGameManager.Start()

**画面要素**
- 最初にクリア表示を隠す
- ゲーム開始状態へ切り替える
- 開始時の画面を整える
- コード抜粋: `managerStart`

**講師ノート**

Startはこの回でも初期化の場所として使う。

```csharp
private void Start()
{
    GameClearText.gameObject.SetActive(false);
    GameOverText.gameObject.SetActive(false);
    ChangeState(GameState.GameStart);
}
```

### Slide 07 毎フレーム確認する

**画面要素**
- プレイ中だけ確認する
- FindGameObjectsWithTagでItemを集める
- Lengthで個数を取る
- コード抜粋: `managerUpdate`

**講師ノート**

配列学習済みなのでLengthとつなげる。FindGameObjectsWithTagの戻り値は配列として説明する。

```csharp
private void Update()
{
    if (currentState == GameState.Playing)
    {
        int itemCount = GameObject.FindGameObjectsWithTag("Item").Length;
        if (itemCount == 0) ChangeState(GameState.GameClear);
        if (PlayerController.transform.position.y < -10f) ChangeState(GameState.GameOver);
    }
}
```

### Slide 08 Item数を数える

**画面要素**
- Itemタグのオブジェクトを配列で受け取る
- Lengthが残り個数になる
- Destroyされると次の確認で数が減る
- コード抜粋: `managerUpdate`

**講師ノート**

「配列の長さ」を実ゲーム内の残り数として使う例にする。

```csharp
private void Update()
{
    if (currentState == GameState.Playing)
    {
        int itemCount = GameObject.FindGameObjectsWithTag("Item").Length;
        if (itemCount == 0) ChangeState(GameState.GameClear);
        if (PlayerController.transform.position.y < -10f) ChangeState(GameState.GameOver);
    }
}
```

### Slide 09 クリア条件

**画面要素**
- if (itemCount == 0)
- 条件が成立したら状態を切り替える
- 条件分岐の実用例として扱う
- コード抜粋: `managerUpdate`

**講師ノート**

既習のif文をゲームのゴールに接続する。

```csharp
private void Update()
{
    if (currentState == GameState.Playing)
    {
        int itemCount = GameObject.FindGameObjectsWithTag("Item").Length;
        if (itemCount == 0) ChangeState(GameState.GameClear);
        if (PlayerController.transform.position.y < -10f) ChangeState(GameState.GameOver);
    }
}
```

### Slide 10 クリア表示

**画面要素**
- GameClearTextをSetActive(true)
- プレイヤーを動けない状態にする
- クリア後に余計な操作ができないようにする
- コード抜粋: `managerClear`

**講師ノート**

表示と入力停止を同じタイミングで行う意味を説明する。

```csharp
case GameState.GameClear:
    PlayerController.CanMove = false;
    TimerController.StopTimer();
    GameClearText.gameObject.SetActive(true);
    PlayerController.FreezePlayer();
    ResultText.text = $"Time: {TimerController.CurrentTime.ToString("F2")}s";
    break;
```

### Slide 11 プレイヤーを止める理由

**画面要素**
- クリア後も動けると状態が曖昧になる
- ゲーム終了時の操作を止める
- 見た目にも終了が分かりやすい

**講師ノート**

止める処理は演出ではなく状態管理の一部として扱う。

### Slide 12 FreezePlayer()

**画面要素**
- RigidbodyConstraints.FreezeAllを使う
- 位置と回転を固定する
- ManagerからPlayerControllerの関数を呼ぶ
- コード抜粋: `freeze`

**講師ノート**

関数を外から呼ぶ例。public関数の理由をここで示す。

```csharp
public void FreezePlayer()
{
    rb.constraints = RigidbodyConstraints.FreezeAll;
}
```

### Slide 13 ファイル間の呼び出し

**画面要素**
- InGameManagerがPlayerController.FreezePlayerを呼ぶ
- 一方のスクリプトが全てを抱え込まない
- 参照が必要なものはInspectorで接続する

**講師ノート**

クラス未習でも「別ファイルの機能を呼ぶ」として理解できるようにする。

### Slide 14 今日の完成形

**画面要素**
- 全アイテム取得でクリア表示
- プレイヤーが停止する
- 次回は時間計測・ゲームオーバー・リトライを追加する
- スクリーンショット: `Assets\Screenshots\training_ingame_play_gameplay.png`

**講師ノート**

ゲームとして初めて明確なゴールができたことを確認する。
