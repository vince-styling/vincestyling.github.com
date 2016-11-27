title: 我的链表合并排序笔记
decorator: post
identifier: 3f612fc3a9710bc9f38c
description: 最近正在学习链表合并排序算法，在此记录一下学习过程。目前版本仍不够完美，后续的工余时间将探索更优的解决方案。
‡‡‡‡‡‡‡‡‡‡‡‡‡‡

最近学习链表的合并排序，记录一下第一个可行版本。这个版本是自己独立思考写出来的第一个可行版本，不满意的地方是在 `sortNodeByAscending()` 方法中插入节点时使用的是 **new** 关键字去构建新节点，对于这种简单的例子来说没有什么问题，但对于每个节点的数据相对复杂的场景，构建新节点需要复制节点值，有点不值得。后续将尝试是否可避免，当前如果遇到这种情况，可以用委托模式来构建节点，这样开销会相对较小。

```java
public class LinkedListsEnOrder {
    private static final int NODE_COUNT = 12;
    private static final int BOUND = 300;

    public static void main(String[] args) throws Exception {
//        testEqualLengthLinks();
//        testUnEqualLengthLinks();
        testEqualLengthAndInclusiveLinks();
//        testEqualLengthAndCrossoverLinks();
    }

    /**
     * 测试 等长 & 交叉 情况下
     * 时间复杂度(loopingCount == 小链表长度) 的计算输出.
     */
    private static void testEqualLengthAndCrossoverLinks() {
        Node head1 = new Node(20, new Node(30, new Node(40, new Node(50, new Node(60, new Node(70))))));
        outputNode(head1);

        Node head2 = new Node(10, new Node(32, new Node(38, new Node(51, new Node(54, new Node(89))))));
        outputNode(head2);

        Node mergedNode = mergeAndSortByAscending(head1, head2);
        outputNode(mergedNode);
    }

    /**
     * 测试 等长 & A链表最大值 < B 链表最小值 情况(最优)下
     * 时间复杂度(loopingCount == 小链表长度) 的计算输出.
     */
    private static void testEqualLengthAndInclusiveLinks() {
        Node head1 = randingAHeadNode(NODE_COUNT, 51, BOUND);
        Node sortedHead1Node = sortNodeByAscending(head1);
        outputNode(sortedHead1Node);

        Node head2 = randingAHeadNode(NODE_COUNT, 0, 50);
        Node sortedHead2Node = sortNodeByAscending(head2);
        outputNode(sortedHead2Node);

        Node mergedNode = mergeAndSortByAscending(sortedHead1Node, sortedHead2Node);
        outputNode(mergedNode);
    }

    /**
     * 测试 等长 情况
     */
    private static void testEqualLengthLinks() {
        Node head1 = randingAHeadNode(NODE_COUNT, 0, BOUND);
        Node sortedHead1Node = sortNodeByAscending(head1);
        outputNode(sortedHead1Node);

        Node head2 = randingAHeadNode(NODE_COUNT, 0, BOUND);
        Node sortedHead2Node = sortNodeByAscending(head2);
        outputNode(sortedHead2Node);

        Node mergedNode = mergeAndSortByAscending(sortedHead1Node, sortedHead2Node);
        outputNode(mergedNode);
    }

    /**
     * 测试 不等长 情况
     */
    private static void testUnEqualLengthLinks() {
        Node head1 = randingAHeadNode(NODE_COUNT, 0, BOUND);
        Node sortedHead1Node = sortNodeByAscending(head1);
        outputNode(sortedHead1Node);

        Node head2 = randingAHeadNode(5, 0, BOUND);
        Node sortedHead2Node = sortNodeByAscending(head2);
        outputNode(sortedHead2Node);

        Node mergedNode = mergeAndSortByAscending(sortedHead1Node, sortedHead2Node);
        outputNode(mergedNode);
    }

    private static Node mergeAndSortByAscending(Node head1, Node head2) {
        Node sortedNode = new Node(-1);
        Node sortedNextNode = sortedNode;
        Node loopingHead1Node = head1;
        Node loopingHead2Node = head2;
        int loopingCount = 0;
        do {
            loopingCount++;

            if (loopingHead1Node.value >= loopingHead2Node.value) {
                sortedNextNode.next = loopingHead2Node;
                loopingHead2Node = loopingHead2Node.next;
            } else {
                sortedNextNode.next = loopingHead1Node;
                loopingHead1Node = loopingHead1Node.next;
            }

            sortedNextNode = sortedNextNode.next;

            if (loopingHead1Node == null) {
                sortedNextNode.next = loopingHead2Node;
                break;
            }

            if (loopingHead2Node == null) {
                sortedNextNode.next = loopingHead1Node;
                break;
            }
        } while (true);
        System.out.println("on merge looping count : " + loopingCount);
        return sortedNode.next;
    }

    private static Node sortNodeByAscending(Node head) {
        Node sortedNode = new Node(head.value);
        Node loopingNode = head;
        do {
            loopingNode = loopingNode.next;
            if (loopingNode == null) break;
//            System.out.println("------ pop node: " + loopingNode.value);

            // 左边比右边大, 调换位置
            if (sortedNode.value >= loopingNode.value) {
                sortedNode = new Node(loopingNode.value, sortedNode);
                continue;
            }

            // 遍历前面的所有节点, 插入到正确的位置
            Node nextNode = sortedNode;
            do {
                // 没有下一个节点放最后
                if (nextNode.next == null) {
                    nextNode.next = new Node(loopingNode.value);
                    break;
                }

                nextNode = nextNode.next;

                // 如果还比当前节点大, 继续移动到下一个节点
                if (loopingNode.value > nextNode.value) continue;

                // 比当前节点小或相等, 插入到前面
                nextNode.next = new Node(nextNode.value, nextNode.next);
                nextNode.value = loopingNode.value;
                break;
            } while (true);
        } while (true);
        return sortedNode;
    }

    private static Node randingAHeadNode(int nodeCount, int min, int max) {
        Node head = new Node(nextInt(min, max));
        nodeCount--;

        Node loopNode = head;
        for (int i = 0; i < nodeCount; i++) {
            if (loopNode.next != null) loopNode = loopNode.next;
            loopNode.next = new Node(nextInt(min, max));
        }

        return head;
    }

    private static int nextInt(int min, int max) {
        return ThreadLocalRandom.current().nextInt(min, max + 1);
    }

    private static void outputNode(Node loopingNode) {
        int count = 0;
        while (loopingNode != null) {
            System.out.println(loopingNode.value);
            loopingNode = loopingNode.next;
            count++;
        }
        System.out.println("Node count : " + count);
    }

    private static class Node {
        int value;
        Node next;

        public Node(int value) {
            this.value = value;
        }

        public Node(int value, Node next) {
            this.value = value;
            this.next = next;
        }
    }
}
```

结语：在完成后通过网络及同事又了解到一些新思路，在工余时间里，将继续尝试更优的解决方案。
