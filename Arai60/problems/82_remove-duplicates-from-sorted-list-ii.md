# 82. Remove Duplicates from Sorted List II

## 1st

* 問題
  * 昇順にソートされていることが保証された、連結リストの先頭ノードが与えられる
  * ソート順を崩さずに重複を排除した後のリスト（の先頭ノード）を返す
    * ただし、重複していた値はすべて削除する（＝1つ残すわけではない）
* 考えたこと
  * ダミーの先頭ノード（headをnextに持つ）を用意し、これをcurrentとする
  * nextとnext.nextが一致していたらループに入る
    * 値が一致する限りnextをnext.nextにする
    * 最後にもう一度nextをnext.nextにする（1つも残さないようにするため）
  * 一致していなかったらcurrentを1つ進める
  * 最後に、ダミーの先頭ノードのnextを返す（先頭ノードの値が重複している場合があるため素直にheadを返せない）
* 解答時間
  * 15分

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        dummy_head = ListNode(None, head)
        current_node = dummy_head

        while current_node.next and current_node.next.next:
            if current_node.next.val == current_node.next.next.val:
                while (
                    current_node.next
                    and current_node.next.next
                    and current_node.next.val == current_node.next.next.val
                ):
                    current_node.next = current_node.next.next
                current_node.next = current_node.next.next
            else:
                current_node = current_node.next

        return dummy_head.next
```

## 2nd / 3rd

* このようなダミーノードはsentinel (番兵) と呼ばれるらしいので変数名を変えてみる

```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        sentinel_node = ListNode(None, head)
        current_node = sentinel_node

        while current_node.next and current_node.next.next:
            if current_node.next.val == current_node.next.next.val:
                while (
                    current_node.next
                    and current_node.next.next
                    and current_node.next.val == current_node.next.next.val
                ):
                    current_node.next = current_node.next.next
                current_node.next = current_node.next.next
            else:
                current_node = current_node.next
        return sentinel_node.next
```

* 再帰を用いた解法
* 重複ノードの最後の1つは普通にやると重複していない扱いになってしまう
  * これが残ってしまうと困るので、最後の2つはまとめて削除する

```python
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if not head or not head.next:
            return head
        
        if head.val != head.next.val:
            head.next = self.deleteDuplicates(head.next)
            return head
        else:
            if not head.next.next or head.next.val != head.next.next.val:
                # 重複ノードを最後に1つ残すと、重複していないと判定されてしまうため、2つまとめて削除する
                return self.deleteDuplicates(head.next.next)
            return self.deleteDuplicates(head.next)
```
