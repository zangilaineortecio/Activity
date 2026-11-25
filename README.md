# Activity
Activity 8 – Heaps and Priority Queues

import random


class MinHeap:

    def __init__(self):
        self.data = []

    def size(self):
        return len(self.data)

    def peek(self):
        if not self.data:
            raise IndexError("peek from empty heap")
        return self.data[0]

    def insert(self, x):
        self.data.append(x)
        self._sift_up(len(self.data) - 1)

    def extract(self):
        if not self.data:
            raise IndexError("extract from empty heap")
        top = self.data[0]
        last = self.data.pop()
        if self.data:
            self.data[0] = last
            self._sift_down(0)
        return top

    def _sift_up(self, i):
        while i > 0:
            p = (i - 1) // 2
            if self.data[i] < self.data[p]:
                self.data[i], self.data[p] = self.data[p], self.data[i]
                i = p
            else:
                break

    def _sift_down(self, i):
        n = len(self.data)
        while True:
            l, r, s = 2 * i + 1, 2 * i + 2, i
            if l < n and self.data[l] < self.data[s]:
                s = l
            if r < n and self.data[r] < self.data[s]:
                s = r
            if s == i:
                break
            self.data[i], self.data[s] = self.data[s], self.data[i]
            i = s


class MaxHeap:

    def __init__(self):
        self.data = []

    def size(self):
        return len(self.data)

    def peek(self):
        if not self.data:
            raise IndexError("peek from empty heap")
        return self.data[0]

    def insert(self, x):
        self.data.append(x)
        i = len(self.data) - 1
        while i > 0:
            p = (i - 1) // 2
            if self.data[i] > self.data[p]:
                self.data[i], self.data[p] = self.data[p], self.data[i]
                i = p
            else:
                break

    def extract(self):
        if not self.data:
            raise IndexError("extract from empty heap")
        top = self.data[0]
        last = self.data.pop()
        if self.data:
            self.data[0] = last
            i, n = 0, len(self.data)
            while True:
                l, r, s = 2 * i + 1, 2 * i + 2, i
                if l < n and self.data[l] > self.data[s]:
                    s = l
                if r < n and self.data[r] > self.data[s]:
                    s = r
                if s == i:
                    break
                self.data[i], self.data[s] = self.data[s], self.data[i]
                i = s
        return top


def heap_sort(arr):
    h = MaxHeap()
    for x in arr:
        h.insert(x)
    out = [0] * len(arr)
    for i in range(len(arr) - 1, -1, -1):
        out[i] = h.extract()
    return out


def kth_largest(nums, k):
    if k < 1 or k > len(nums):
        raise ValueError("k out of range")
    h = MinHeap()
    for x in nums:
        if h.size() < k:
            h.insert(x)
        elif x > h.peek():
            h.extract()
            h.insert(x)
    return h.peek()


def merge_k_sorted(arrays):
    h = MinHeap()
    for i, a in enumerate(arrays):
        if a:
            h.insert((a[0], i, 0))
    out = []
    while h.size():
        val, i, j = h.extract()
        out.append(val)
        if j + 1 < len(arrays[i]):
            h.insert((arrays[i][j + 1], i, j + 1))
    return out


def schedule(jobs):
    h = MaxHeap()
    for order, (pri, name) in enumerate(jobs):
        h.insert((pri, -order, name))
    released = []
    step = 1
    while h.size():
        pri, _, name = h.extract()
        print(f"Step {step}: release {name} (priority {pri})")
        released.append(name)
        step += 1
    return released


def drain(h):
    out = []
    while h.size():
        out.append(h.extract())
    return out


if __name__ == "__main__":
    data = [5, 9, 11, 14, 19, 17, 18, 13]

    h = MinHeap()
    for x in data:
        h.insert(x)
    assert h.size() == 8 and h.peek() == 5
    assert drain(h) == [5, 9, 11, 13, 14, 17, 18, 19]
    assert drain(MinHeap()) == []
    h = MinHeap(); h.insert(7); assert drain(h) == [7]
    h = MinHeap()
    for x in [3, 1, 3, 1, 2]:
        h.insert(x)
    assert drain(h) == [1, 1, 2, 3, 3]
    r = [random.randint(0, 100) for _ in range(50)]
    h = MinHeap()
    for x in r:
        h.insert(x)
    assert drain(h) == sorted(r)
    print("Activity 1 OK")

    h = MaxHeap()
    for x in data:
        h.insert(x)
    assert drain(h) == [19, 18, 17, 14, 13, 11, 9, 5]
    assert drain(MaxHeap()) == []
    h = MaxHeap(); h.insert(7); assert drain(h) == [7]
    h = MaxHeap()
    for x in [3, 1, 3, 1, 2]:
        h.insert(x)
    assert drain(h) == [3, 3, 2, 1, 1]
    h = MaxHeap()
    for x in r:
        h.insert(x)
    assert drain(h) == sorted(r, reverse=True)
    print("Activity 2 OK")

    for case in ([4, 10, 3, 5, 1], [1, 2, 3, 4], [4, 3, 2, 1],
                 [3, 1, 3, 2, 1], [], [7], r):
        assert heap_sort(case) == sorted(case), case
    print("Activity 3 OK:", heap_sort([4, 10, 3, 5, 1]))

    nums = [3, 2, 1, 5, 6, 4]
    for k in range(1, len(nums) + 1):
        assert kth_largest(nums, k) == sorted(nums, reverse=True)[k - 1]
    assert kth_largest([5], 1) == 5
    assert kth_largest([2, 2, 2, 1], 3) == 2
    for k in (1, 10, 50):
        assert kth_largest(r, k) == sorted(r, reverse=True)[k - 1]
    print("Activity 4 OK")

    a, b, c = [1, 4, 9, 12], [2, 3], [0, 5, 6, 7, 8, 10]
    m = merge_k_sorted([a, b, c])
    assert m == sorted(a + b + c)
    assert merge_k_sorted([[], [], []]) == []
    assert merge_k_sorted([[1], [], [1, 1]]) == [1, 1, 1]
    print("Activity 5 OK:", m)

    jobs = [(2, "backup"), (5, "deploy"), (1, "cleanup"),
            (5, "hotfix"), (3, "report")]
    order = schedule(jobs)
    assert order == ["deploy", "hotfix", "report", "backup", "cleanup"]
    assert schedule([]) == []
    print("Activity 6 OK:", order)