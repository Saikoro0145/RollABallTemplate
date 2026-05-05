# 第2回 カメラ追従とアイテム収集

- 時間: 95分
- 対象: C#のif/while/変数/配列を学習済みで、クラス・インターフェースは未学習の新入生
- ゴール: プレイヤーを見失わないカメラと、触れたら消えるアイテムを追加する。
- 形式: 1回95分、演習なし、講師の実装と説明で徐々に完成形へ近づける

## スライド別講師ノート

### Slide 01 前回の状態

**画面要素**
- ボールは入力で動く
- ただしカメラは固定のまま
- 集める対象もまだない
- スクリーンショット: `Assets\Screenshots\training_player_focus.png`

**講師ノート**

前回の成果を短く確認し、今回はゲームらしさを足す回だと位置づける。

### Slide 02 今日追加するもの

**画面要素**
- カメラがプレイヤーを追う
- アイテムに触れると消える
- アイテムが回転・浮遊して見つけやすくなる
- スクリーンショット: `Assets\Screenshots\training_items_focus.png`

**講師ノート**

視点、目的物、見た目の動きの3つを追加する。

### Slide 03 カメラ追従の考え方

**画面要素**
- 最初の距離をoffsetとして保存する
- 毎フレーム、Player位置 + offsetへ移動する
- プレイヤーの動きが終わった後に追従する

**講師ノート**

カメラをプレイヤーの子にする方法ではなく、スクリプトで追う方法を学ぶ。

### Slide 04 PlayerFollower

**画面要素**
- Cameraに付けるスクリプト
- PlayerTransformをInspectorで割り当てる
- offsetを内部で保持する
- コード抜粋: `followerFields`

**講師ノート**

参照をInspectorで渡すというUnityらしい接続方法を初めて扱う。

```csharp
public Transform PlayerTransform;
private Vector3 offset;
```

### Slide 05 Start()

**画面要素**
- 開始時の位置差を計算する
- transform.positionは自分自身の位置
- PlayerTransform.positionはプレイヤーの位置
- コード抜粋: `followerStart`

**講師ノート**

引き算で差を保存するだけなので、式の意味を画面上で確認する。

```csharp
private void Start()
{
    offset = transform.position - PlayerTransform.position;
}
```

### Slide 06 LateUpdate()

**画面要素**
- Updateの後に呼ばれる
- プレイヤーの移動後にカメラを合わせる
- カメラのガタつきを抑えやすい
- コード抜粋: `followerLate`

**講師ノート**

FixedUpdateとの違いを厳密に説明しすぎず、カメラはLateUpdateと覚えさせる。

```csharp
private void LateUpdate()
{
    transform.position = PlayerTransform.position + offset;
}
```

### Slide 07 Itemの構成

**画面要素**
- ColliderをTriggerにする
- TagをItemにする
- 見た目はPrefabにして複数配置する
- スクリーンショット: `Assets\Screenshots\training_items_focus.png`

**講師ノート**

TriggerとTagの設定はコードとセットで見せる。

### Slide 08 Tagの役割

**画面要素**
- 名前ではなくTagで種類を判定する
- Itemタグなら収集対象
- タグを使うと複数アイテムを同じ処理で扱える

**講師ノート**

配列未満の集合処理として、タグで同じ種類をまとめる感覚を持たせる。

### Slide 09 OnTriggerEnter()

**画面要素**
- Triggerに触れた瞬間に呼ばれる
- otherは触れた相手
- Itemなら処理を行う
- コード抜粋: `playerTrigger`

**講師ノート**

OnTriggerEnterはUnityが自動で呼ぶ関数。自分で呼び出す関数ではない点を強調する。

```csharp
private void OnTriggerEnter(Collider other)
{
    if (other.CompareTag("Item"))
    {
        Destroy(other.gameObject);
    }
}
```

### Slide 10 CompareTag("Item")

**画面要素**
- 条件分岐ifを使う
- 相手がItemかどうかを判定する
- 文字列の打ち間違いに注意する
- コード抜粋: `playerTrigger`

**講師ノート**

既習のif文とつなげる。条件がtrueのときだけDestroyされる。

```csharp
private void OnTriggerEnter(Collider other)
{
    if (other.CompareTag("Item"))
    {
        Destroy(other.gameObject);
    }
}
```

### Slide 11 Destroy()

**画面要素**
- GameObjectをシーンから削除する
- 触れたアイテムだけが消える
- 収集できたことが画面でわかる
- スクリーンショット: `Assets\Screenshots\training_items_focus.png`
- コード抜粋: `playerTrigger`

**講師ノート**

Destroy(other.gameObject)のotherが「触れた相手」であることを確認する。

```csharp
private void OnTriggerEnter(Collider other)
{
    if (other.CompareTag("Item"))
    {
        Destroy(other.gameObject);
    }
}
```

### Slide 12 ItemRotator

**画面要素**
- アイテムを目立たせる演出用スクリプト
- RotationSpeedで回転速度を調整
- FloatAmplitudeとFloatFrequencyで上下移動を調整
- コード抜粋: `itemFields`

**講師ノート**

ゲームロジックではなく見た目を良くするスクリプトとして扱う。

```csharp
public float RotationSpeed = 50f;
public float FloatAmplitude = 0.5f;
public float FloatFrequency = 1f;
private Vector3 initialPosition;
```

### Slide 13 回転と浮遊

**画面要素**
- Rotateで毎フレーム少し回す
- Mathf.Sinで上下に揺らす
- Time.deltaTimeでフレーム差を吸収する
- コード抜粋: `itemUpdate`

**講師ノート**

Sinの数学説明は最小限にし、-1から1を繰り返す波として説明する。

```csharp
private void Start()
{
    initialPosition = transform.position;
}

private void Update()
{
    transform.Rotate(Vector3.up, RotationSpeed * Time.deltaTime, Space.World);
    transform.position = initialPosition
        + Vector3.up * Mathf.Sin(Time.time * FloatFrequency) * FloatAmplitude;
}
```

### Slide 14 今日の完成形

**画面要素**
- カメラがボールを追う
- アイテムに触れると消える
- 次回は全部集めたらクリアにする
- スクリーンショット: `Assets\Screenshots\training_ingame_play_gameplay.png`

**講師ノート**

次回のクリア判定に自然につなげる。
