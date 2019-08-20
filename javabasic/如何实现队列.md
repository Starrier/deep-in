# 如何实现队列

## 数组实现

```java
public class CustomQueue<T> {
    private ArrayList<T> arr =new ArrayList<T>();

    private int front;
    private int rear;

    public CustomQueue() {
        front=0;
        rear=0;
    }

    public boolean isEmpty() {
        return front == rear;
    }

    public int size() {
        return rear - front;
    }

    public T getFront() {
        if (isEmpty()) {
            return null;
        } else {
            return arr.get(front);
        }
    }

    public T getBack() {
        if (isEmpty()) {
            return null;
        } else {
            return arr.get(rear - 1);
        }
    }

    public void enQueue(T item) {
        arr.add(item);
        rear++;
    }

    public static void main(String[] args) {
        CustomQueue<Integer> queue = new CustomQueue<>();
        queue.enQueue(1);
        queue.enQueue(2);
        System.out.println("首部：" + queue.getFront());
        System.out.println("尾部： " + queue.getBack());
        System.out.println("队列大小： " + queue.size());
    }
}
```

缺点：

出兑后数组前部分的空间不能充分利用

方案：

将数组看作环状空间（循环队列）。当最后一个位置被占用后，可以从数组首位置开始循环利用。具体实现方法可以参考数据结构课本。

## 链表实现

```java

```

