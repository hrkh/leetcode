# 142. Linked List Cycle II

https://leetcode.com/problems/linked-list-cycle-ii/description/

## 1st

* 問題
  * 連結リストの先頭ノードが与えられる
  * 連結リストが循環していたら循環の始点のノード、していなければNoneを返す
* 考えたこと
  * 前問（141）と同様にsetに入れて、最初に検知されたノードを取ってくればOK
  * フロイドの循環検出法による解法も考えたが、fastがslowに追い付いた時点のノードが始点とは限らないため復元が必要
    * 復元の方法がわからないので一旦ここでは採用しない
* 解答にかかった時間
  * 2分

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        traversed = set()
        current = head

        while current:
            if current in traversed:
                return current
            traversed.add(current)
            current = current.next
        return None
```

## 2nd / 3rd

他の方のPRコメントを見て、たしかにcurrentは微妙な気がしたので修正する。

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        traversed_nodes = set()
        current_node = head

        while current_node:
            if current_node in traversed_nodes:
                return current_node
            traversed_nodes.add(current_node)
            current_node = current_node.next
        return None
```

せっかくなので、再帰バージョンと、1stで見送ったフロイドの循環検出法を用いたバージョンを作成してみる。

### 再帰

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        def _detectCycle(
          current_node: Optional[ListNode],
          traversed_nodes: set[ListNode]
        ) -> Optional[ListNode]:
            if not current_node:
                return None
            if current_node in traversed_nodes:
                return current_node
            traversed_nodes.add(current_node)
            return _detectCycle(current_node.next, traversed_nodes)
        
        return _detectCycle(head, set())
```

### フロイドの循環検出法

fastとslowが出会った地点と、リストの始点のそれぞれから同時に同じ速度でスタートしたとき、ぶつかる位置が循環の始点となることを利用する。

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        def _findCycleStart(collision: ListNode) -> ListNode:
            from_head = head
            from_collision = collision
            while from_head is not from_collision:
                from_head = from_head.next
                from_collision = from_collision.next
            return from_head  # from_collisionを返してもOK

        fast = head
        slow = head
        while fast and fast.next:
            fast = fast.next.next
            slow = slow.next
            if fast is slow:
                return _findCycleStart(fast)  # slowを入れてもOK
        return None
```

## 4th

### 4つの構造を比較してみる

https://discord.com/channels/1084280443945353267/1221030192609493053/1225674901445283860

#### 1. 関数化

```python
while fast and fast.next:  # A
    # 短いX ↓
    fast = fast.next.next
    slow = slow.next
    # 短いX ↑
    if fast is slow:  # B
        return _findCycleStart(fast) # Y() = C
# 短いZ（今回のケースでは存在しない）
return None  # D
```

* これは3rdの解法で採用したパターン
* 処理の流れに違和感がない
* 長い処理のまとまりに名前がついていて理解しやすい

#### 2. Python独自の文法（while ~ else）

```python
while fast and fast.next:  # A
    # 短いX ↓
    fast = fast.next.next
    slow = slow.next
    # 短いX ↑
    if fast is slow:  # B
        break
else:
    # 短いZ（今回のケースでは存在しない）
    return None  # D
# 長いY ↓
from_head = head
from_collision = fast
while from_head is not from_collision:
    from_head = from_head.next
    from_collision = from_collision.next
# 長いY ↑
return from_head  # C
```

* そもそも、while ~ elseの挙動自体がわかりにくい
* AとDが遠く、BとYが離れているのが読みにくい

#### 3. 無限ループ

```python
while 1:
    if not (fast and fast.next):  # A
        # 短いZ（今回のケースでは存在しない）
        return None  # D
    # 短いX ↓
    fast = fast.next.next
    slow = slow.next
    # 短いX ↑
    if fast is slow:  # B
        break

# 長いY ↓
from_head = head
from_collision = fast
while from_head is not from_collision:
    from_head = from_head.next
    from_collision = from_collision.next
# 長いY ↑
return from_head  # C
```

* 処理の流れとしてはわかりやすい
* 長い処理のまとまりに名前があった方がよりわかりやすいと感じる

#### 4. 関数化

```python
def has_cycle():  # F
    while fast and fast.next:  # A
        # 短いX ↓
        fast = fast.next.next
        slow = slow.next
        # 短いX ↑
        if fast is slow:  # B
            return True
        return False

if not has_cycle():  # F
    # 短いZ（今回のケースでは存在しない）
    return None  # D

# 長いY ↓
from_head = head
from_collision =  ... # これが取れない！
```

そのままだとうまくいかないので、少し書き換えてみる。

-> booleanを返すF()の代わりに、Optional[ListNode]を返すF'()を採用する。つまり、
  * Trueのときは対応する値を返す
  * FalseのときはNoneを返す

```python
def find_collision():  # F'
    while fast and fast.next:  # A
        # 短いX ↓
        fast = fast.next.next
        slow = slow.next
        # 短いX ↑
        if fast is slow:  # B
            return fast
        return None

if not (collision := find_collision()):  # F'
    # 短いZ（今回のケースでは存在しない）
    return None  # D

# 長いY ↓
from_head = head
from_collision = collision
while from_head is not from_collision:
    from_head = from_head.next
    from_collision = from_collision.next
# 長いY ↑
return from_head  # C
```

* 処理の流れがわかりやすい
* 長い処理のまとまりに名前がついていてわかりやすい
* 長いYにも名前があってもよいのでは？と感じた

#### まとめ

* 長い処理には名前をつけるとわかりやすい
* 処理の流れが素直だとわかりやすい

-> 4.関数化をベースにしつつ、長いYも関数化する（1.関数化を取り入れる）のがわかりやすそう

### 指摘事項の反映

* 関数を内部で定義せず外に出す
* フロイドの循環検出法を使っていることを書く
* 4つの構造を比較した結果を活かす
  * 4.関数化をベースにしつつ、長いYも関数化する（=1.関数化を取り入れる）

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def findCollision(self, head: Optional[ListNode]) -> Optional[ListNode]:
        """Floyd's cycle-finding algorithm を実行し、先行ポインタが追いついた時点のノードを返す。
        ただし、先行ポインタが追いつかず、リストの末尾に到達してしまった場合はNoneを返す。
        """
        fast = head
        slow = head

        while fast and fast.next:
            fast = fast.next.next
            slow = slow.next
            if fast is slow:
                return fast
        return None

    def findCycleStart(self, head: ListNode, collision: ListNode) -> ListNode:
        """Floyd's cycle-finding algorithm を実行し、循環の先頭ノードを求める。"""
        from_head = head
        from_collision = collision

        while from_head is not from_collision:
            from_head = from_head.next
            from_collision = from_collision.next
        return from_head

    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if (collision := self.findCollision(head)):
            return self.findCycleStart(head, collision)
        return None
```

## 気になったこと、調べたこと

* ListNodeを普通にset()に入れているけど、問題ないのか？
  * 結論
    * 問題なし
  * 理由
    * ユーザ定義クラスはデフォルトでhashable
    * ハッシュ値はid()に由来するので、同じ値を持つノードが複数あっても別のノードと認識される
    * cf. https://docs.python.org/3/glossary.html#term-hashable
      >  Objects which are instances of user-defined classes are hashable by default. They all compare unequal (except with themselves), and their hash value is derived from their id(). 
* フロイトの循環検出法の実装ではslowとfastを==で比較しているが、問題ないのか？
  * 結論
    * 問題なし
  * 理由
    * ユーザ定義クラスの等号判定はデフォルトでisが利用される
    * cf. https://docs.python.org/3/reference/datamodel.html#object.\_\_eq\_\_
      > By default, object implements \_\_eq\_\_() by using is
