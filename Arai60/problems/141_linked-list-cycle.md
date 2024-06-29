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
