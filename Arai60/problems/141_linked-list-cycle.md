# 141. Linked List Cycle

https://leetcode.com/problems/linked-list-cycle/

## 1st

* 問題
  * 連結リストの先頭ノードが与えられる
  * 連結リストが循環していたらtrue、していなければfalseを返す
* 考えたこと
  * 連結リストを先頭から走査して走査済み集合（set）に入れる。途中でsetに入っているノードにあたったら循環しているのでtrueを返す。末尾のノードまで走査できたら循環していないのでfalseを返す
* 回答時間
  * 10分くらい

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        traversed = set()
        current = head
        while current:
            if current in traversed:
                return True
            traversed.add(current)
            current = current.next
        return False
```

## 2nd

1stと同じコードなので省略。

* 考えたこと
  * `while current` は `while current is not None` の方がよい？
    * `ListNode.next` が `Optional[ListNode]` であることが保証されていないので `is not None` の方が適切？
    * そもそも `1` とか `"a"` とかが入ってきてしまったら `is not None` でも弾けないのでキリがない
    * `None` でないことは `while current` で保証できるので今回は一旦これでOKとする

## 3rd

1st, 2ndと同じコードなので省略。

* 回答時間
  * 2分くらい

## 4th

### setを用いた解の亜種

1stのコードはsetを用いて「Falseの条件を満たすまでループ、Trueの条件を満たしたらreturn」という考え方のものだった。

これについては2つの亜種を考えることができる。

* Trueの条件を満たすまでループ、Falseの条件を満たしたらreturn

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        traversed = set()
        current = head
        while current not in traversed:
            if not current:
                return False
            traversed.add(current)
            current = current.next
        return True
```

* 無条件でループ、Trueの条件かFalseの条件を満たしたらreturn

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        traversed = set()
        current = head
        while True:
            if not current:
                return False
            if current in traversed:
                return True
            traversed.add(current)
            current = current.next
```

> [!TIP]
> set()について読む

https://docs.python.org/3.12/library/stdtypes.html#set

### フロイドの循環検出法（Floyd's cycle-finding algorithm）

fastとslowを同時にスタートさせると、もし循環していればfastがslowに追い付くという考え方。空間計算量がO(1)で済む。

> [!WARNING]
> ただし、もともと知らないと基本的には書けない（＝問題が与えられてから思いつくのは困難）

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        fast = head
        slow = head
        while fast and fast.next:
            fast = fast.next.next
            slow = slow.next
            if fast is slow:
                return True
        return False
```

### 再帰

setを用いた解を再帰で書いてみる。

これはMemory Limit Exceeded となるのでボツ（再帰のたびに集合が作られてしまう）。

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        def _hasCycle(
            current: Optional[ListNode],
            traversed: Optional[set[ListNode]]
        ) -> bool:
            if not current:
                return False
            traversed = traversed or set()
            if current in traversed:
                return True
            return _hasCycle(current.next, traversed | {current})
        
        return _hasCycle(head, None)
```

（同様に通らないが）レビューを元に修正。

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        def _hasCycle(
            current: Optional[ListNode],
            traversed: set[ListNode]  # Optionalは不要になる
        ) -> bool:
            if not current:
                return False
            # ここで traversed = traversed or set() しなくてよくなる
            if current in traversed:
                return True
            return _hasCycle(current.next, traversed | {current})

        return _hasCycle(head, set())  # ここでset()を与える
```

集合を外に出すパターン。これなら通る。

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        traversed = set()

        def _hasCycle(current: Optional[ListNode]) -> bool:
            if not current:
                return False
            if current in traversed:
                return True
            traversed.add(current)
            return _hasCycle(current.next)

        return _hasCycle(head)
```

フロイドの循環検出法も再帰で書いてみる。

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        def _hasCycle(fast: Optional[ListNode], slow: Optional[ListNode]) -> bool:
            if (not fast) or (not fast.next):
                return False
            fast = fast.next.next
            slow = slow.next
            if fast is slow:
                return True

            return _hasCycle(fast, slow)
        
        return _hasCycle(head, head)
```
