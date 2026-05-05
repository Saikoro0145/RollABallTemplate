# 第1回 プレイヤー移動だけの最小ゲーム

- 時間: 95分
- 対象: C#のif/while/変数/配列を学習済みで、クラス・インターフェースは未学習の新入生
- ゴール: 完成形を見せたうえで、ボールを動かす最小構成を作る。
- 形式: 1回95分、演習なし、講師の実装と説明で徐々に完成形へ近づける

## スライド別講師ノート

### Slide 01 完成形の提示

**画面要素**
- 最終的にはタイトル画面からゲームへ移動する
- ボールを動かし、アイテムを集め、結果を表示する
- 6回かけて小さな機能を順番に足していく
- スクリーンショット: `Assets\Screenshots\training_title_play_game_view.png`

**講師ノート**

最初にゴールを見せる。今日すべてを理解する必要はなく、毎回1つずつ部品を足す方針を伝える。

### Slide 02 今日作るもの

**画面要素**
- ボールが入力で動く最小ゲーム
- アイテム・タイマー・タイトルはまだ扱わない
- 「入力を読む」「力を加える」だけに集中する
- スクリーンショット: `Assets\Screenshots\training_player_focus.png`

**講師ノート**

完成形とのギャップを示し、今日はプレイヤー移動だけに絞ると宣言する。

### Slide 03 Unityの基本単位

**画面要素**
- GameObject: 画面に置くもの
- Component: GameObjectに付ける機能
- Script: 自分で作るComponent

**講師ノート**

クラス未学習のため、Scriptは「部品」と説明する。MonoBehaviourの詳細説明は避ける。

### Slide 04 最小シーン構成

**画面要素**
- 床、プレイヤー、カメラがあれば動きが確認できる
- まずは見える・動く状態を優先する
- UIやゲーム管理は後の回で追加する
- スクリーンショット: `Assets\Screenshots\training_ingame_scene_overview.png`

**講師ノート**

Sceneビューで最低限必要なものを確認し、情報量を増やしすぎない。

### Slide 05 Playerに必要な部品

**画面要素**
- Sphere: 見た目
- Rigidbody: 物理で動くための部品
- PlayerController: 入力を読み、力を加える部品
- スクリーンショット: `Assets\Screenshots\training_player_focus.png`

**講師ノート**

Rigidbodyは「物理計算をUnityに任せる部品」と説明する。

### Slide 06 入力の考え方

**画面要素**
- キーボード入力はVector2で受け取る
- 横方向をx、前後方向をyとして読む
- 3D空間ではxとzに変換する
- コード抜粋: `playerMove`

**講師ノート**

Vector2とVector3は数値のセットとして説明し、数学的な厳密さより使い方を優先する。

```csharp
private void FixedUpdate()
{
    if (!CanMove) return;

    Vector2 input = moveAction.ReadValue<Vector2>();
    Vector3 movement = new Vector3(input.x, 0, input.y) * Speed;
    rb.AddForce(movement);
}
```

### Slide 07 PlayerController v1

**画面要素**
- 移動に必要な変数を先に用意する
- Startで部品を取得する
- FixedUpdateで入力に応じて力を加える
- コード抜粋: `playerFields`

**講師ノート**

ファイル全体を一気に読ませず、変数、Start、FixedUpdateに分けて見る。

```csharp
public float Speed = 5f;
private Rigidbody rb;
private InputAction moveAction;
public bool CanMove = false;
```

### Slide 08 変数の準備

**画面要素**
- Speed: 調整したいのでpublic
- rb: Rigidbodyを後で使うため保存
- moveAction: 入力設定のMoveを保存
- コード抜粋: `playerFields`

**講師ノート**

public/privateは「Inspectorで触るか、内部だけで使うか」の違いとして扱う。

```csharp
public float Speed = 5f;
private Rigidbody rb;
private InputAction moveAction;
public bool CanMove = false;
```

### Slide 09 Start()

**画面要素**
- ゲーム開始時に一度だけ呼ばれる
- GetComponentで同じGameObjectのRigidbodyを取得
- InputSystemからMoveアクションを探す
- コード抜粋: `playerStart`

**講師ノート**

Startは初期準備の場所。毎フレームやる必要がない取得処理をここに置く。

```csharp
private void Start()
{
    rb = GetComponent<Rigidbody>();
    moveAction = InputSystem.actions.FindAction("Move");
}
```

### Slide 10 FixedUpdate()

**画面要素**
- 物理を使う処理はFixedUpdateに書く
- 入力を毎回読み取る
- CanMoveは第5回で効いてくる停止スイッチ
- コード抜粋: `playerMove`

**講師ノート**

CanMoveはこの時点では一旦trueにして動作確認してよい。第5回で意味を回収する。

```csharp
private void FixedUpdate()
{
    if (!CanMove) return;

    Vector2 input = moveAction.ReadValue<Vector2>();
    Vector3 movement = new Vector3(input.x, 0, input.y) * Speed;
    rb.AddForce(movement);
}
```

### Slide 11 入力を3D移動に変換

**画面要素**
- input.xを横移動へ使う
- input.yをz方向へ使う
- y方向はジャンプではないので0にする
- コード抜粋: `playerMove`

**講師ノート**

2D入力のyと3D座標のyが違う点は混乱しやすいので、画面に軸を描いて説明する。

```csharp
private void FixedUpdate()
{
    if (!CanMove) return;

    Vector2 input = moveAction.ReadValue<Vector2>();
    Vector3 movement = new Vector3(input.x, 0, input.y) * Speed;
    rb.AddForce(movement);
}
```

### Slide 12 ボールを動かす

**画面要素**
- AddForceでRigidbodyに力を加える
- 直接positionを書き換えない
- 物理らしい加速と減速が得られる
- コード抜粋: `playerMove`

**講師ノート**

position変更との比較は深入りせず、今回は物理で転がすゲームなのでAddForceを使うと説明する。

```csharp
private void FixedUpdate()
{
    if (!CanMove) return;

    Vector2 input = moveAction.ReadValue<Vector2>();
    Vector3 movement = new Vector3(input.x, 0, input.y) * Speed;
    rb.AddForce(movement);
}
```

### Slide 13 Inspectorで調整

**画面要素**
- Speedを変えると体感が変わる
- 値を変えてすぐ確認できるのがpublic変数の利点
- 講義では5前後を基準にする
- スクリーンショット: `Assets\Screenshots\training_player_focus.png`

**講師ノート**

コードとInspectorがつながっている実感を持たせる。

### Slide 14 今日の完成形

**画面要素**
- ボールが入力で動く
- プレイヤー移動の基本ができた
- 次回はカメラ追従とアイテム収集を追加する
- スクリーンショット: `Assets\Screenshots\training_ingame_play_gameplay.png`

**講師ノート**

第1回はゲームとして未完成でよい。最小の成功体験で終える。
