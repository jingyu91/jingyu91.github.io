---
title: "단방향 링크드리스트 역순으로 바꾸기"
category: algorithm
---



1. 재귀함수로 구현하기
2. Iterative하게 구현하기

```java
/**
 * Created by jingyu on 2018. 10. 23..
 */
public class ReverseLinkedList {
    public static void main(String[] args) {
        new ReverseLinkedList().run();
    }

    public void run() {
        MyList list = new MyList();
        list.add(1);
        list.add(2);
        list.add(3);
        list.add(4);

        list.print();

//        list.reverse();
        list.reverseIterative();
        list.print();
    }

    class MyList {
        Node head;

        public MyList() {
            this.head = null;
        }

        public void add(int v) {
            if (head == null) {
                head = new Node(v, null);
                return;
            }

            Node node = new Node(v, null);
            Node last = head;
            while (last.next != null) {
                last = last.next;
            }
            last.next = node;
        }

        public void print() {
            if (head == null) return;
            Node last = head;
            while (last.next != null) {
                System.out.print(last.val + " ");
                last = last.next;
            }
            System.out.print(last.val);
            System.out.println();
        }

        // 1. 재귀
        private Node reverse(Node curr) {
            if (curr.next == null) {
                head = curr;
                return curr;
            }
            Node rev = reverse(curr.next);
            rev.next = curr;
            curr.next = null;
            return curr;
        }

        // 2. 비재귀
        public void reverseIterative() {
			if (head == null) return;
            Node curr = head;
            Node tmp, prev = null;
            while (curr.next != null) {
                tmp = curr.next;
                curr.next = prev;
                prev = curr;
                curr = tmp;
            }
            curr.next = prev;
            head = curr;
        }

        public void reverse() {
            reverse(head);
        }
    }

    class Node {
        int val;
        Node next;

        public Node(int val, Node next) {
            this.val = val;
            this.next = next;
        }
    }
}
```

