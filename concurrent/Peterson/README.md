- `PetersonLock`是一个结构体，表示分层锁。
- 它包含两个切片：`level`和`waiting`。level表示线程的等待级别，waiting模拟了一个忙等待队列。

### 锁的工作原理

- **`Lock`方法**：
  - 这个方法被一个线程或进程调用，以尝试获取锁。
  - 它通过遍历所有“层级”（由`waiting`切片的长度决定），并设置当前线程或进程的层级。
  - 方法中使用原子操作`atomic.StoreInt32`和`atomic.LoadInt32`来保证多线程环境下的数据一致性和防止数据竞争。
  - 如果发现有其他线程或进程在相同或更高的层级等待，则当前线程或进程会进入忙等。

- **`Unlock`方法**：
  - 当一个线程或进程完成其临界区的工作后，它会调用此方法来释放锁。
  - 这通过将线程或进程的`level`设置为`-1`来实现。

- **`existsOtherWithLevelGreaterOrEqual`私有方法**：
  - 这是一个辅助方法，用于检查是否有其他线程或进程在更高或相同的层级上等待。
  - 如果发现这样的线程或进程，则返回`true`；否则返回`false`。

### 问题

- 这是一种忙等待锁，这意味着当一个线程或进程等待获取锁时，它会在一个循环中不断检查条件，直到能够获取锁。这可能会导致CPU使用率较高。
- 这种锁适用于线程数不多的情况，因为随着线程数的增加，忙等待可能会导致效率降低。
```
Peterson git:(main) ✗ go test

进程 0 已进入临界区.
进程 0 已退出临界区.
进程 3 已进入临界区.
进程 3 已退出临界区.
进程 99 已进入临界区.
进程 99 已退出临界区.
进程 1 已进入临界区.
进程 1 已退出临界区.
进程 2 已进入临界区.
进程 2 已退出临界区.
进程 51 已进入临界区.
进程 51 已退出临界区.
进程 52 已进入临界区.
进程 52 已退出临界区.
进程 75 已进入临界区.
进程 75 已退出临界区.
进程 63 已进入临界区.
进程 63 已退出临界区.
进程 4 已进入临界区.
进程 4 已退出临界区.
进程 64 已进入临界区.
进程 64 已退出临界区.
进程 53 已进入临界区.
进程 53 已退出临界区.
进程 5 已进入临界区.
进程 5 已退出临界区.
进程 87 已进入临界区.
进程 87 已退出临界区.
进程 65 已进入临界区.
进程 65 已退出临界区.
进程 54 已进入临界区.
进程 54 已退出临界区.
进程 6 已进入临界区.
进程 6 已退出临界区.
进程 77 已进入临界区.
进程 77 已退出临界区.
进程 66 已进入临界区.
进程 66 已退出临界区.
进程 55 已进入临界区.
进程 55 已退出临界区.
进程 76 已进入临界区.
进程 76 已退出临界区.
进程 78 已进入临界区.
进程 78 已退出临界区.
进程 70 已进入临界区.
进程 70 已退出临界区.
进程 7 已进入临界区.
进程 7 已退出临界区.
进程 82 已进入临界区.
进程 82 已退出临界区.
进程 29 已进入临界区.
进程 29 已退出临界区.
进程 71 已进入临界区.
进程 71 已退出临界区.
进程 56 已进入临界区.
进程 56 已退出临界区.
进程 88 已进入临界区.
进程 88 已退出临界区.
进程 83 已进入临界区.
进程 83 已退出临界区.
进程 67 已进入临界区.
进程 67 已退出临界区.
进程 30 已进入临界区.
进程 30 已退出临界区.
进程 79 已进入临界区.
进程 79 已退出临界区.
进程 8 已进入临界区.
进程 8 已退出临界区.
进程 72 已进入临界区.
进程 72 已退出临界区.
进程 57 已进入临界区.
进程 57 已退出临界区.
进程 89 已进入临界区.
进程 89 已退出临界区.
进程 84 已进入临界区.
进程 84 已退出临界区.
进程 68 已进入临界区.
进程 68 已退出临界区.
进程 31 已进入临界区.
进程 31 已退出临界区.
进程 80 已进入临界区.
进程 80 已退出临界区.
进程 9 已进入临界区.
进程 9 已退出临界区.
进程 73 已进入临界区.
进程 73 已退出临界区.
进程 58 已进入临界区.
进程 58 已退出临界区.
进程 90 已进入临界区.
进程 90 已退出临界区.
进程 85 已进入临界区.
进程 85 已退出临界区.
进程 69 已进入临界区.
进程 69 已退出临界区.
进程 81 已进入临界区.
进程 81 已退出临界区.
进程 32 已进入临界区.
进程 32 已退出临界区.
进程 74 已进入临界区.
进程 74 已退出临界区.
进程 10 已进入临界区.
进程 10 已退出临界区.
进程 91 已进入临界区.
进程 91 已退出临界区.
进程 59 已进入临界区.
进程 59 已退出临界区.
进程 61 已进入临界区.
进程 61 已退出临界区.
进程 86 已进入临界区.
进程 86 已退出临界区.
进程 62 已进入临界区.
进程 62 已退出临界区.
进程 34 已进入临界区.
进程 34 已退出临界区.
进程 11 已进入临界区.
进程 11 已退出临界区.
进程 33 已进入临界区.
进程 33 已退出临界区.
进程 60 已进入临界区.
进程 60 已退出临界区.
进程 92 已进入临界区.
进程 92 已退出临界区.
进程 20 已进入临界区.
进程 20 已退出临界区.
进程 24 已进入临界区.
进程 24 已退出临界区.
进程 95 已进入临界区.
进程 95 已退出临界区.
进程 35 已进入临界区.
进程 35 已退出临界区.
进程 25 已进入临界区.
进程 25 已退出临界区.
进程 93 已进入临界区.
进程 93 已退出临界区.
进程 97 已进入临界区.
进程 97 已退出临界区.
进程 98 已进入临界区.
进程 98 已退出临界区.
进程 12 已进入临界区.
进程 12 已退出临界区.
进程 21 已进入临界区.
进程 21 已退出临界区.
进程 94 已进入临界区.
进程 94 已退出临界区.
进程 36 已进入临界区.
进程 36 已退出临界区.
进程 26 已进入临界区.
进程 26 已退出临界区.
进程 96 已进入临界区.
进程 96 已退出临界区.
进程 22 已进入临界区.
进程 22 已退出临界区.
进程 43 已进入临界区.
进程 43 已退出临界区.
进程 13 已进入临界区.
进程 13 已退出临界区.
进程 23 已进入临界区.
进程 23 已退出临界区.
进程 39 已进入临界区.
进程 39 已退出临界区.
进程 44 已进入临界区.
进程 44 已退出临界区.
进程 27 已进入临界区.
进程 27 已退出临界区.
进程 47 已进入临界区.
进程 47 已退出临界区.
进程 40 已进入临界区.
进程 40 已退出临界区.
进程 16 已进入临界区.
进程 16 已退出临界区.
进程 17 已进入临界区.
进程 17 已退出临界区.
进程 14 已进入临界区.
进程 14 已退出临界区.
进程 48 已进入临界区.
进程 48 已退出临界区.
进程 28 已进入临界区.
进程 28 已退出临界区.
进程 45 已进入临界区.
进程 45 已退出临界区.
进程 41 已进入临界区.
进程 41 已退出临界区.
进程 15 已进入临界区.
进程 15 已退出临界区.
进程 18 已进入临界区.
进程 18 已退出临界区.
进程 42 已进入临界区.
进程 42 已退出临界区.
进程 49 已进入临界区.
进程 49 已退出临界区.
进程 38 已进入临界区.
进程 38 已退出临界区.
进程 46 已进入临界区.
进程 46 已退出临界区.
进程 19 已进入临界区.
进程 19 已退出临界区.
进程 37 已进入临界区.
进程 37 已退出临界区.
进程 50 已进入临界区.
进程 50 已退出临界区.
PASS
ok      plock   0.508s
```