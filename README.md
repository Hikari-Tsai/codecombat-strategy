
# 沙漠決鬥(Desert Duel)
![](https://codecombat.com/file/db/level/62540bd270cb4400504ad44c/Basketball-Arena-Banner-01.jpg)
比賽網址： https://codecombat.com/play/level/desert-duel


## 賽事評論影片
<a href="http://www.youtube.com/watch?feature=player_embedded&v=8MhfDVoqpcY" target="_blank">
<img src="http://img.youtube.com/vi/8MhfDVoqpcY/0.jpg" alt="賽事頻論" width="600" border="10" />
</a>

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
|        | "threat"食人魔    | "charger" 菜刀人   | "big"犀牛      | "driver" 擲矛手   | "sniper" 薩滿法師   |
|  ----  | :----:  | :----:  | :----:  | :----:  | :----:  |
| health  | 10 | 10 | `40` | 6 | 5 |
| damage  | 10 | `20` | 6 | 15 | 5 |
| speed  | 10 | 6 | 5 | `14` | 9 |
| push  | `40` | 30 | 5 | 10 | 5 |
| range  | 15 | 7 | 7 | 15 | `30` |
| cost  | 1 | 1 | 1 | 1 | 1 |


## 魔法
### 參數說明
- cooldown : 冷卻時間，在發動魔法後會有短暫時間無法動彈，程式碼將會被block直到cooldown時間結束。若在激烈戰鬥時必須警慎考慮此參數對戰局的影響
- specificCooldown: 再次發動相同魔法所需的時間，不會直接使用，遊戲中可以用isReady檢查即可
- duration: 法術效果持續時間
### 魔法說明
- "boost" 加速魔法-增加特定賽道上我方全體球員速度
  - cooldown:0.3s
  - specificCooldown: 7s
  - duration: 2s 
  - ratio: 1.5
- "press" 減速魔法-降低特定賽道上對手速度
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
- "switch" 傳送魔法，直接對調上在下兩條賽道的我方隊員(不需要第二個參數)
  - cooldown:0.1s
  - specificCooldown: 5s
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

## 建議戰術
- 找尋即將失守的lane，發動流沙
  - 即將失守的定義？
    - 最靠近的敵人距離hero小於某個特定值?
    - lane上的敵人超過某個特定數量?
    - lane上的敵人超過我方隊員數量?個
    - lane上偵測到類型為"big"的對方球員，且被施放了goliath魔法(血量大於預設值)
- 尋找沒有敵人的lane發動快攻（fast break)
  - 不斷掃描，當發現有lane上面沒有敵人時，回傳lane的編號
  - 在沒敵人的lane上招喚速度最快的"driver"發動快攻
  - 招喚後立刻追加魔法"hot"以及"boost" ，可視情況或用邏輯閘追加"goliath"

## 範例程式
### 基礎招喚主程式
基礎程式的核心概念，在於均勻的打散五種兵力到三條賽道上，若要做到這件事情必須撰寫十五行程式碼(三條賽道與五種球員的最小公倍數)，而且每一行都會花費一秒鍾詠唱，若要加入其他魔法使用的判斷函數，就必須逐行加入，程式將會大幅膨脹。最簡單打散兵力的做法是配合list與模數(在範例程式中使用邏輯判斷將超過lenth的數字強制歸零，程度較好的小朋友可直接使用模數會更簡潔)均勻打散，而且更改兵力配置與路線都較為簡單方便(例如希望使用driver做為快攻使用，那麼只要修改```soldiers=["threat","big","charger","sniper"]```即可
```python
soldiers=["threat","big","charger","sniper","driver"]
i=0
j=0
while True:
    hero.summon(soldiers[i], j)
    i=i+1
    if i==len(soldiers):
    	i=0
    j=j+1
    if j==3:
    	j=0
```
### 尋找最少敵人的路徑與敵人數量
```python
def findMinEnemyLane():
	lane=-1
	numbers=99
	for i in range(3):
		Players = len(hero.findTheirPlayers(i))
		if Players<numbers:
			numbers=Players
			lane=i
	return (lane,numbers)
```
### 尋找最多敵人的路徑與敵人數量
請小朋友自行練習
```python
#請小朋友參考findMinEnemyLane自行練習修改
def findMaxEnemyLane():
	pass
```
### 將主程式加入quicksand 以及fast break判斷
綜合上述函數，整合到主程式內
```python
soldiers=["threat","big","charger","sniper"]#,"driver"]
i=0
j=0
while True:
	hero.summon(soldiers[i], j)
	i=i+1
	if i==len(soldiers):
		i=0
	j=j+1
	if j==3:
		j=0
	#fastBreak
	#敵人等於0，發動快攻
	(lane, numbers)=findMinEnemyLane()
	if numbers<=0:
		hero.summon("driver",lane)
		if hero.isReady("hot") and hero.isReady("boost") and hero.isReady("press") and hero.isReady("goliath"):
			hero.play("hot",lane)
			hero.play("boost",lane)
			hero.play("press",lane)
			hero.play("goliath",lane)
	(lane, numbers)=findMaxEnemyLane()

	#敵人大於3，發動流沙
	if numbers>=3:
		if hero.isReady("quicksand"):
			hero.play("quicksand",lane)

def findMinEnemyLane():
	lane=-1
	numbers=99
	for i in range(3):
		Players = len(hero.findTheirPlayers(i))
		if Players<numbers:
			numbers=Players
			lane=i
	return (lane,numbers)

#尋找最多敵人的Lane，再於主程式中加入發動quicksand條件
#請小朋友參考findMinEnemyLane自行練習修改
def findMaxEnemyLane():
	pass


```

## 自我練習
選手基本題
1. 範例程式中，我選擇同時招換了五種角色，試著改變soliders，使用不同的組合，看看能不能提升排名
2. 範例程式中，敵人數量低於2將會發動快攻，試著下修數字是否能提升排名

選手挑戰題
1. 參考上面附件內的findMinEnemyLane函數，撰寫另一個可以搜尋最多敵人的函數findMaxEnemyLane，並回傳路徑以及敵人數量。
2. 在主程式中呼叫上述函數，並加入判斷：當敵人數量超過4的時候，對著這條路徑發動quicksand(流沙魔法)
