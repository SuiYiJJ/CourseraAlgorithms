# 实现 A*  算法过程的感悟

## 敲代码之前先看懂题意

做之前三个 assignment 时经常看过两遍题目就准备打开 IDEA 实现代码，然而总是会在提交的时候遇到各种问题，比如 API，无法编译，这些还是格式上的小问题，比较大的问题就是由很多 case 跑不过。究其原因，最后的最后才发现自己其实对题目理解不是很深，做出来的只能跑过自己想当然的一些 case，__并不是通解__。所以这些 assignment 过去，明白的首先应该是__多读题，想好通解再动手，__即使是想根据后面 submission 的反馈修正思路也最好有个对于题目通解的理解。

## 学会自己造“轮子“

如果现有的数据结构无法满足自己的使用时，不要想着使用别扭的数据结构完成任务，这样会让代码冗余而且思路混乱，先实现一个适用的轮子有时更有用。比如实现 A* 算法时如果自己实现 有指向前一个 search node 的数据结构而不是将前一个 search node 保存在循环中的实例，算法不但更加简单，而且正确性也大大提高。

### 之前的代码：

```java
public Solver(Board initial) {
        if (initial == null) {
            throw new IllegalArgumentException();
        }
        board = initial;
        moves = 0;
        boardComparator = new BoardComparator();
        pq = new MinPQ<>(boardComparator);
        solution = new Queue<>();
        pq.insert(board);

        while (!pq.isEmpty() && !pq.min().isGoal()) {
            Board minBoard = pq.delMin();
            solution.enqueue(minBoard);
            moves++;
            System.out.println(minBoard);
            System.out.println(minBoard.manhattan() + "\t" + moves);
            for (Board neighbor : minBoard.neighbors()) {
                if (!neighbor.equals(predecessor)) {
                    pq.insert(neighbor);
                }
            }
            predecessor = minBoard;
        }
        if (!pq.isEmpty()) {
            solution.enqueue(pq.min());
        }
    }
```



### 自己实现的数据结构：

```java
private class Node implements Comparable<Node> {
        private Board board;
        private int moves;
        private Node predecessor;
        public Node(Board board, Node predecessor){
            this.board = board;
            this.predecessor = predecessor;
            if (predecessor == null) {
                this.moves = 0;
            } else {
                this.moves = predecessor.moves + 1;
            }
        }
        @Override
        public int compareTo(Node o) {
            return (this.board.manhattan() - o.board.manhattan()) + (this.moves - o.moves);
        }

    }
```





### 修改后的代码：

```java
public Solver(Board initial) {
        if (initial == null) {
            throw new IllegalArgumentException();
        }
        pq = new MinPQ<>();
        pq.insert(new Node(initial, null));

        MinPQ<Node> twinPq = new MinPQ<>();
        twinPq.insert(new Node(initial.twin(), null));

        boolean solvable = false;
        boolean twinSolvable = false;

        while (!solvable && !twinSolvable) {
            node = expand(pq);
            solvable = (node != null);
            twinSolvable = (expand(twinPq) != null);
        }
    }
```



## 学会利用好别人的意见

平时敲代码经常会在 GitHub 找一下优秀的第三方库，完成 assignment 时，遇到题目理解的难点，或是代码实现时的迷茫，可以去论坛看看别人有没有遇到过以及解决方法；在代码实现时有难度时，可以先看一下 checklist 上给出的建议步骤，对于整个代码的实现有一个比较清晰的思路。

## 好好看课程视频

看视频并不是为了简单的标记为完成，有个又完成了一小步的成就感，而应该认真看下来，思考课上老师的讲解和一些提出的问题。视频不好好看，看过的书上的内容就相当于没有好好回顾，效果自然就打了个折扣，而且容易在 assignment 中遇到一些看着眼熟但忘了怎么处理的问题。