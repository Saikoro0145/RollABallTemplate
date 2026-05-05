# 第4回 タイマー・ゲームオーバー・リトライ

- 時間: 95分
- 対象: C#のif/while/変数/配列を学習済みで、クラス・インターフェースは未学習の新入生
- ゴール: 時間計測、落下ゲームオーバー、リトライボタンでゲームの失敗と再挑戦を作る。
- 形式: 1回95分、演習なし、講師の実装と説明で徐々に完成形へ近づける

## スライド別講師ノート

### Slide 01 前回の状態

**画面要素**
- 全部集めるとクリアできる
- ただし時間は記録されない
- 落下しても失敗にならない
- スクリーンショット: `Assets\Screenshots\training_ingame_play_gameplay.png`

**講師ノート**

成功だけでなく失敗と記録があるとゲームらしくなると導入する。

### Slide 02 今日追加するもの

**画面要素**
- タイマーで経過時間を表示する
- 落下したらゲームオーバーにする
- Retryボタンでやり直す
- スクリーンショット: `Assets\Screenshots\training_ingame_game_view.png`

**講師ノート**

画面上のUIが増える回。ManagerとUIの接続を丁寧に扱う。

### Slide 03 TimerController

**画面要素**
- 時間だけを担当するスクリプト
- Managerから開始・停止を指示する
- 表示用TextをInspectorで受け取る
- コード抜粋: `timerFields`

**講師ノート**

タイマーをManagerに直接書かず、役割を分ける。

```csharp
public float CurrentTime = 0f;
private bool isRunning = false;
public TMP_Text TimerText;
```

### Slide 04 タイマー変数

**画面要素**
- CurrentTimeは結果表示にも使うのでpublic
- isRunningで計測中かどうかを管理
- TimerTextに表示する
- コード抜粋: `timerFields`

**講師ノート**

boolフラグの実用例としてisRunningを見る。

```csharp
public float CurrentTime = 0f;
private bool isRunning = false;
public TMP_Text TimerText;
```

### Slide 05 StartTimer()

**画面要素**
- CurrentTimeを0に戻す
- isRunningをtrueにする
- ゲーム開始時に呼ぶ
- コード抜粋: `timerMethods`

**講師ノート**

「始める」処理を関数にまとめる意味を説明する。

```csharp
public void StartTimer()
{
    CurrentTime = 0f;
    isRunning = true;
}

public void StopTimer()
{
    isRunning = false;
}
```

### Slide 06 StopTimer()

**画面要素**
- isRunningをfalseにする
- Updateでの加算が止まる
- クリア時・ゲームオーバー時に呼ぶ
- コード抜粋: `timerMethods`

**講師ノート**

時間そのものを止めるのではなく、加算しない状態にする。

```csharp
public void StartTimer()
{
    CurrentTime = 0f;
    isRunning = true;
}

public void StopTimer()
{
    isRunning = false;
}
```

### Slide 07 Update()

**画面要素**
- isRunningのときだけ実行する
- Time.deltaTimeを加算する
- Textに小数2桁で表示する
- コード抜粋: `timerUpdate`

**講師ノート**

Time.deltaTimeは「前フレームからの経過秒」と説明する。

```csharp
private void Update()
{
    if (isRunning)
    {
        CurrentTime += Time.deltaTime;
        TimerText.text = $"Time: {CurrentTime.ToString("F2")}s";
    }
}
```

### Slide 08 表示形式

**画面要素**
- ToString("F2")で小数2桁
- $"..."で文字列に値を埋め込む
- 見やすいUI表示に整える
- コード抜粋: `timerUpdate`

**講師ノート**

文字列補間は細かく文法説明せず、テンプレートに値を入れる書き方として示す。

```csharp
private void Update()
{
    if (isRunning)
    {
        CurrentTime += Time.deltaTime;
        TimerText.text = $"Time: {CurrentTime.ToString("F2")}s";
    }
}
```

### Slide 09 Managerに接続

**画面要素**
- PlayingでStartTimerを呼ぶ
- GameClearでStopTimerを呼ぶ
- 状態が変わるタイミングに処理を置く
- コード抜粋: `managerPlaying`

**講師ノート**

TimerController単体では動かず、Managerがタイミングを決める。

```csharp
case GameState.Playing:
    PlayerController.CanMove = true;
    TimerController.StartTimer();
    break;
```

### Slide 10 結果表示

**画面要素**
- クリア時のCurrentTimeを読む
- ResultTextにTimeを表示する
- ゲームの成果が残る
- コード抜粋: `managerClear`

**講師ノート**

別スクリプトのpublic変数を読む例として扱う。

```csharp
case GameState.GameClear:
    PlayerController.CanMove = false;
    TimerController.StopTimer();
    GameClearText.gameObject.SetActive(true);
    PlayerController.FreezePlayer();
    ResultText.text = $"Time: {TimerController.CurrentTime.ToString("F2")}s";
    break;
```

### Slide 11 落下判定

**画面要素**
- Playerのy座標を見る
- -10未満なら落下とみなす
- GameOver状態へ切り替える
- コード抜粋: `managerUpdate`

**講師ノート**

座標のyは高さ。しきい値はゲーム調整値として扱う。

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

### Slide 12 GameOver表示

**画面要素**
- CanMoveをfalseにする
- Timerを止める
- GameOverTextを表示する
- コード抜粋: `managerOver`

**講師ノート**

クリア時と似た処理を比較し、成功と失敗で共通する終了処理を理解させる。

```csharp
case GameState.GameOver:
    PlayerController.CanMove = false;
    TimerController.StopTimer();
    GameOverText.gameObject.SetActive(true);
    PlayerController.FreezePlayer();
    break;
```

### Slide 13 RetryButton

**画面要素**
- 現在のシーンを読み直す
- ButtonのOnClickから呼ぶ
- 最初の状態に戻して再挑戦できる
- コード抜粋: `retry`

**講師ノート**

SceneManager.GetActiveScene().buildIndexは「今いるシーン番号」と説明する。

```csharp
public void OnRetryButtonClicked()
{
    SceneManager.LoadScene(SceneManager.GetActiveScene().buildIndex);
}
```

### Slide 14 今日の完成形

**画面要素**
- 時間を測って結果表示できる
- 落下したらゲームオーバーになる
- Retryでやり直せる
- スクリーンショット: `Assets\Screenshots\training_ingame_game_view.png`

**講師ノート**

成功、失敗、再挑戦が揃い、ゲームの基本ループができたことを確認する。
