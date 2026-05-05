# 第5回 カウントダウンと状態管理

- 時間: 95分
- 対象: C#のif/while/変数/配列を学習済みで、クラス・インターフェースは未学習の新入生
- ゴール: 開始前のカウントダウンとGameStateで、ゲームの流れを明確に制御する。
- 形式: 1回95分、演習なし、講師の実装と説明で徐々に完成形へ近づける

## スライド別講師ノート

### Slide 01 前回の状態

**画面要素**
- 成功・失敗・リトライができる
- ただし開始直後から動ける
- 開始前の準備時間がない
- スクリーンショット: `Assets\Screenshots\training_ingame_play_gameplay.png`

**講師ノート**

開始直後に動ける問題を体験させ、カウントダウンの必要性を出す。

### Slide 02 今日追加するもの

**画面要素**
- 3,2,1,Go!を表示する
- カウント中はプレイヤーを動けなくする
- GameStateで流れを整理する
- スクリーンショット: `Assets\Screenshots\training_ingame_play_countdown.png`

**講師ノート**

この回は少し抽象度が上がるため、画面の変化から入る。

### Slide 03 開始前に動ける問題

**画面要素**
- プレイヤーが準備前に移動できる
- タイマー開始とのズレが起きる
- 開始状態を明確に分ける必要がある

**講師ノート**

状態を分ける理由を、実際の不具合として説明する。

### Slide 04 CanMove

**画面要素**
- falseならFixedUpdateをすぐ抜ける
- trueになったら入力を読む
- Managerがタイミングを決める
- コード抜粋: `playerMove`

**講師ノート**

第1回で先に入れたCanMoveの意味をここで回収する。

```csharp
private void FixedUpdate()
{
    if (!CanMove) return;

    Vector2 input = moveAction.ReadValue<Vector2>();
    Vector3 movement = new Vector3(input.x, 0, input.y) * Speed;
    rb.AddForce(movement);
}
```

### Slide 05 CountDownController

**画面要素**
- 表示だけを担当するスクリプト
- CountDownTimeで秒数を設定する
- CountDownTextに数字を表示する
- コード抜粋: `countdownFields`

**講師ノート**

タイマーと同じく、UI表示を担当する専用スクリプトとして扱う。

```csharp
public int CountDownTime = 3;
private int currentCountDownTime = 0;
public TMP_Text CountDownText;
```

### Slide 06 カウントダウン変数

**画面要素**
- CountDownTimeは開始秒数
- currentCountDownTimeは内部で減らす値
- TextはInspectorで接続する
- コード抜粋: `countdownFields`

**講師ノート**

publicとprivateの違いを再確認する。

```csharp
public int CountDownTime = 3;
private int currentCountDownTime = 0;
public TMP_Text CountDownText;
```

### Slide 07 コルーチンとは

**画面要素**
- 途中で待てる処理
- yield returnで一時停止する
- ゲーム全体は止めずに時間待ちできる

**講師ノート**

while文は学習済みなので、whileの中で1秒待つ処理として見せる。

### Slide 08 StartCountDown()

**画面要素**
- whileで0になるまで繰り返す
- WaitForSeconds(1f)で1秒待つ
- 最後にGo!を表示して非表示にする
- コード抜粋: `countdownMethod`

**講師ノート**

処理の順番を声に出して追う。構文の細部より流れを優先する。

```csharp
public IEnumerator StartCountDown()
{
    currentCountDownTime = CountDownTime;
    while (currentCountDownTime > 0)
    {
        CountDownText.text = currentCountDownTime.ToString();
        yield return new WaitForSeconds(1f);
        currentCountDownTime--;
    }
    CountDownText.text = "Go!";
    yield return new WaitForSeconds(1f);
    CountDownText.gameObject.SetActive(false);
}
```

### Slide 09 GameStart()

**画面要素**
- CountDownControllerの処理が終わるまで待つ
- 終わったらPlayingへ切り替える
- 開始処理を1か所に集める
- コード抜粋: `managerGameStart`

**講師ノート**

yield return StartCoroutineで「別の待てる処理を待つ」と説明する。

```csharp
private IEnumerator GameStart()
{
    yield return StartCoroutine(CountDownController.StartCountDown());
    ChangeState(GameState.Playing);
}
```

### Slide 10 状態管理の必要性

**画面要素**
- 開始中、プレイ中、クリア、ゲームオーバーで処理が違う
- 今どの状態かを変数で持つ
- 状態ごとの処理をまとめる

**講師ノート**

複雑に見えるが、ifが増えすぎるのを整理する道具として導入する。

### Slide 11 enum GameState

**画面要素**
- 状態名をまとめて定義する
- 文字列より間違いにくい
- currentStateに現在の状態を保存する
- コード抜粋: `managerEnum`

**講師ノート**

enumは深入りせず、選択肢を名前でまとめるものとして説明する。

```csharp
private enum GameState
{
    GameStart,
    Playing,
    GameClear,
    GameOver
}

private GameState currentState = GameState.GameStart;
```

### Slide 12 ChangeState()

**画面要素**
- 状態を更新する
- switchで状態ごとの処理を分ける
- 開始・プレイ・終了処理の入口にする
- コード抜粋: `managerPlaying`

**講師ノート**

switchはifが複数並ぶ場合の整理方法として説明する。

```csharp
case GameState.Playing:
    PlayerController.CanMove = true;
    TimerController.StartTimer();
    break;
```

### Slide 13 switchで整理

**画面要素**
- GameStartでカウントダウン開始
- Playingで移動とタイマー開始
- GameClear/GameOverで停止と表示

**講師ノート**

全体の流れを1枚で復習する。

### Slide 14 今日の完成形

**画面要素**
- 開始前にカウントダウンする
- Go!後に操作とタイマーが始まる
- ゲームの状態が整理された
- スクリーンショット: `Assets\Screenshots\training_ingame_play_countdown.png`

**講師ノート**

最終回でタイトル画面を付ければ、開始から終了までの流れが完成する。
