# 83. Remove Duplicates from Sorted List

# 1st

* 問題
  * 昇順にソートされていることが保証された、連結リストの先頭ノードが与えられる
  * ソート順を崩さずに重複を排除した後のリスト（の先頭ノード）を返す
* 考えたこと
  * 直前のノードを保持した上でノードを順に見ていく
    * 2つのノードの値が一致した場合 -> 直前ノードのnextを現在ノードのnextにする
    * 2つのノードの値が一致しない場合 -> 直前ノードを現在ノードにする
    * どちらにしても現在ノードを現在ノードのnextにする
  * ただし、リストが空の場合は2つノードを取れないので即座に空のリストをそのまま返す
* 解答時間
  * 5分

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if not head:
            return head

        previous_node = head
        current_node = head.next
        while current_node:
            if current_node.val == previous_node.val:
                previous_node.next = current_node.next
            else:
                previous_node = current_node
            current_node = current_node.next
        
        return head
```

# 2nd / 3rd

* 次のノードには名前 (`next_node`) を付けた方がわかりやすそうなので付けてみる

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if not head:
            return head
        previous_node = head
        current_node = head.next

        while current_node:
            next_node = current_node.next
            if current_node.val == previous_node.val:
                previous_node.next = next_node
            else:
                previous_node = current_node
            current_node = next_node
        return head
```

* currentを起点にしてnextを決定していく方法
  * 前を見たり後ろを見たりしなくてよいのでわかりやすい
  * 空リストを例外として処理する必要がない
  * 二重ループになるが、上記のわかりやすさを考慮すると十分採用する価値がありそう

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        current_node = head
        while current_node:
            while current_node.next and current_node.next.val == current_node.val:
                current_node.next = current_node.next.next
            current_node = current_node.next
        return head
```

* next_nodeを名付けたバージョン
* 「次ノードと現在ノードの値が異なる」という条件をwhileではなく、whileの中のifに書く
  * こうしないと、whileを抜けたあとにもう1回次ノードを進める必要が出てきてしまうため
* すっきりしていてこれが一番わかりやすそう

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        current_node = head
        while current_node:
            while next_node := current_node.next:
                if next_node.val != current_node.val:
                    break
                current_node.next = next_node.next
            current_node = current_node.next 
        return head
```
