
# 沙漠決鬥(Desert Duel)
比賽網址： https://codecombat.com/play/level/desert-duel

## 規則
- 先獲得五十分的人致勝，如果兩分鐘時沒有人獲得五十分，則高分者勝出，若平分則進入PK驟死賽，只要有任何人獲得分數則勝出。
- 比賽有三條路線(Lane)，分別用數字0,1,2代表中、上、下三條賽道
- 可以在lane上招喚(summon)選手，選手被招喚後將自動前進
  - 將會從我方罰球線招喚並直接往對方前進 
  - 接近對方的罰球線時將會自動射門並保證100%命中
  - 如果遇到對方球員會被往後推，被推動的幅度與force參數相關
  - 球員有多種類型可供招喚，球場上最多只能招喚三隻同樣類型的球員
    - 第一個招喚出來的將是主力，能力值最強
    - 第二個則為二軍，能力打八五折，第三個為三軍，能力打七五折
    - 當有球員完成投籃或是死掉的話，會回收進入候補區可以再次招喚
- 可以在Lane上招喚魔法(play)進行戰術變化
- 
## 球員
總共有五種類型的球員可以招喚，每個球員有以下參數
### 參數(Parameters)說明
- health: 目前生命值
- maxHealth: 最大生命值
- damage: 攻擊力
- speed: 每秒移動速度
- push: 撞到對手時，對手會後退多少格
- cost: 招喚詠唱時間
- color: 所屬單位顏色，可以用來跟英雄的顏色比較``` player.color==hero.color```判斷屬於我方或是敵方(不要直接判斷顏色，顏色會變換)
- lane:目前角色所在賽道
- x: 角色所在x座標(注意沒有pos)
- y: 角色所在y座標(注意沒有pos)

```python
#遊戲中都可以使用 
enemy.health
#或是
friend.health
#偷看參數等資料
```

### 類型(Types)
請注意，以下參數都是主力球員，如果是二軍需要乘以0.85(打八五折)，三軍0.7(打七折)
## 球員類型(Type)
- "threat"
  - health: 10
  - damage: 10
  - speed: 10
  - push: 40
  - range: 15
  - cost: 1
- "charger"
  - health: 10
  - damage: 20
  - speed: 60
  - push: 30
  - range: 7
  - cost: 1
- "big"
  - health: 40
  - damage: 6
  - speed: 5
  - push: 5
  - range: 7
  - cost: 1
- "driver"
  - health: 6
  - damage: 15
  - speed: 14
  - push: 10
  - range: 15
  - cost: 1
- "sniper"
  - health: 5
  - damage: 5
  - speed: 9
  - push: 5
  - range: 30
  - cost: 1
## 魔法
### 參數說明
- cooldown : 冷卻時間，在發動魔法後會有短暫時間無法動彈，程式碼將會被block直到cooldown時間結束。若在激烈戰鬥時必須警慎考慮此參數對戰局的影響
- specificCooldown: 再次發動相同魔法所需的時間，不會直接使用，遊戲中可以用isReady檢查即可
- duration: 法術效果持續時間
### 魔法說明
- "boost" 加速魔法-增加我方速度
  - cooldown:0.3s
  - specificCooldown: 7s
  - duration: 2s 
  - ratio: 1.5
- "press" 減速魔法-降低對手速度
  - cooldown:0.3s
  - specificCooldown: 5s
  - duration: 2s
  - ratio: 0.5
- "quicksand" 流沙魔法-殺死特定賽道上全部球員(包含我方)
  - cooldown:0.5s
  - specificCooldown: 15s
- "goliath" 哥利雅巨大魔法-特定賽道上的第一位球員可以獲得兩倍生命值
  - cooldown:0.2s
  - specificCooldown: 8s
  - ratio: 2
- "hot" 狂暴魔法，可以增加特定賽道第一名球員的速度，並且獲得三分射籃的能力
  - cooldown:0.2s
  - specificCooldown: 10s
  - ratio: 1.5
## 程式方法
### 招喚程式
於特定賽道上招喚特定球員
``` hero.summon("球員類型", 賽道編號)```
### 招喚魔法
於特定賽道上招喚特定魔法
``` hero.play("魔法類型", 賽道編號)```
也可以跟isReady組合搭配
```
if hero.isReady("press"):
    hero.play("press", 1)
```
### 掃描我方單位
掃描當下特定賽道上的我方球員，資料將以陣列形式回傳
``` hero.findPlayers(賽道編號)```
- 如果要掃描全畫面，請使用for迴圈逐行掃描
- 如果要取得數量，可以多加一個len()，變成
```
len(hero.findPlayers(賽道編號))
```
- 如果要逐一比較或是處理，請使用for迴圈逐一判斷
### 掃描對方單位
掃描當下特定賽道上的對方球員，資料將以陣列形式回傳
``` hero.findTheirPlayer(賽道編號)```
- 如果要掃描全畫面，請使用for迴圈逐行掃描
- 如果要取得數量，可以多加一個len()

### 掃描雙方單位
掃描當下特定賽道上的雙方球員，資料將以陣列形式回傳
``` hero.findPlayers(賽道編號)```
- 如果要掃描全畫面，請使用for迴圈逐行掃描
- 如果要取得數量，可以多加一個len()
- 如果要判斷屬於敵方或是我方，可以使用color判斷，如```player.color==hero.color```

### 掃描雙方特定類型單位
掃描當下所有賽道上的雙方球員，資料將以陣列形式回傳
``` hero.findPlayers("球員類型")```
- 如果要掃描全畫面，請使用for迴圈逐行掃描
- 如果要取得數量，可以多加一個len()
- 如果要判斷屬於敵方或是我方，可以使用color判斷，如```player.color==hero.color```

### 測量距離
測量目標球員與英雄的距離
``` hero.distanceTo(球員物件)```
- 通常會搭配Nearest使用，只判斷距離英雄最近的敵人距離，針對最靠近的敵人做出回應
- 也可以使用player或是hero.opponent去測量距離
```python
#當敵人距離英雄小於30公尺，則發動流沙魔法
players = hero.findTheirPlayers()
nearest = hero.findNearest(players)
if hero.distanceTo(nearest) < 30:
    hero.play('quicksand', nearest.lane)
```
### 尋找物件最近的敵人
``` hero.findNearest(球員陣列)```
將會返回距離英雄最近的敵人，也可以使用player.findNearest(球員陣列)，則可以返回與該名player最近的目標，角色也可以切換為對方英雄如```hero.opponent.findNearest(球員陣列)```
通常會跟上面搭配使用

###  取得遊戲時間
可以利用```hero.time```判斷遊戲時間，在不同時間採取不同策略

### 取得目前分數
可以利用```hero.score```或是```hero.opponent```去獲得我方或是對方得分

### 取得隊伍顏色
```hero.color```或是```hero.opponent.color```去取得隊伍顏色，將會返回"red"或是"blue"

##建議戰術
- 找尋最多敵人(或是敵人數量-我方球員數量)最多的lane發動流沙
- 尋找沒有敵人的lane發動快攻
- 
