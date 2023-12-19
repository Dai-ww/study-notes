### 杨辉三角

> 给定一个非负索引 rowIndex，返回「杨辉三角」的第 rowIndex 行。

在「杨辉三角」中，每个数是它左上方和右上方的数的和。

**解法一**

```js
const rowIndex = 4;
var getRow = function (rowIndex) {
  const res = new Array(rowIndex + 1);
  res[0] = 1;
  for (let i = 0; i < rowIndex + 1; i++) {
    res[0] = res[i] = 1;
    for (let j = i - 1; j >= 1; j--) {
      res[j] = res[j] + res[j - 1];
    }
  }
  return res;
};
getRow(rowIndex);
```

**解法二**

```js
const rowIndex = 4;
var getRow = function (rowIndex) {
  if (rowIndex === 0) {
    return [1];
  }
  const arr = getRow(rowIndex - 1);
  console.log(arr);
  const res = Array.from({ length: rowIndex + 1 }).map(
    (_, index) => (arr[index - 1] || 0) + (arr[index] || 0)
  );
  return res;
};
console.log(getRow(rowIndex));
```

### 冒泡排序

> 冒泡排序是一种简单的排序算法。它重复地走访过要排序的数列，一次比较两个元素，如果他们的顺序错误就把他们交换过来。走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。

```js
function bubbleSort(arr) {
  let len = arr.length;
  for (let i = 0; i < len; i++) {
    for (let j = 0; j < len - 1 - i; j++) {
      if (arr[j] > arr[j + 1]) {
        let temp = arr[j + 1];
        arr[j + 1] = arr[j];
        arr[j] = temp;
      }
    }
  }
  return arr;
}
```

### 快速排序

> 快速排序是对冒泡排序的一种改进。在快速排序中，我们选择一个元素作为基准（pivot），然后，我们通过一趟扫描，将待排列的数列分成两部分，一部分比基准值小，另一部分大于等于基准值。在这个分区结束之后，我们再用同样的方法递归地排序这两个子数列。

```js
function partition(arr, left, right) {
  let i = left,
    j = right,
    temp = arr[left];
  while (i < j) {
    while (i < j && arr[j] > temp) {
      j--;
    }
    if (i < j) {
      arr[i] = arr[j];
      i++;
    }
    while (i < j && arr[i] < temp) {
      i++;
    }
    if (i < j) {
      arr[j] = arr[i];
      j--;
    }
    console.log(i, ' ', j, ' ', temp, arr);
  }
  arr[i] = temp;
  return i;
}

function quickSort(arr, left, right) {
  if (left < right) {
    let index = partition(arr, left, right); // 分区，找到一个基点
    quickSort(arr, left, index - 1);
    quickSort(arr, index + 1, right);
  }
  return arr;
}

var arr = [20, 40, 30, 10, 60, 50];
console.log(quickSort(arr, 0, arr.length - 1)); // [10, 20, 30, 40, 50, 60]
```

### 插入排序

> 插入排序是一种简单直观的排序算法。它的工作原理是通过构建有序序列，对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入。

```js
function insertSort(arr) {
  if (!arr || arr.length <= 2) {
    return;
  }

  for (let i = 1; i < arr.length; i++) {
    let temp = arr[i];
    let j = i - 1;
    while (j >= 0 && arr[j] > temp) {
      // 在已排序好的队列中从后向前扫描
      arr[j + 1] = arr[j];
      j--;
    }
    arr[j + 1] = temp;
  }
  return arr;
}

let sortArr = [3, 6, 4, 2, 11, 10, 5];

console.log(insertSort(sortArr)); // [ 2, 3, 4, 5, 6, 10, 11 ]
```

### 选择排序

> 选择排序是一种简单直观的排序算法。它的工作原理是每一次迭代时，选择剩余未排序元素中的最小元素，并将其放到已排序的元素序列的末尾。

```js
function selectSort(arr) {
  let minIndex; // 无序中最小元素位置
  for (let i = 0; i < arr.length; i++) {
    // 每一趟循环比较时，min用于存放较小元素的数组下标，这样当前批次比较完毕最终存放的就是此趟内最小的元素的下标，避免每次遇到较小元素都要进行交换。
    minIndex = i;

    //找 "a[i+1]..a[n]" 之间最小元素，并赋给minIndex
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[j] < arr[minIndex]) {
        // 这里 < ，从小到大，如果是 > ，则从大到小
        minIndex = j;
      }
    }

    // 如果 minIndex !== i ，交换 arr[i] 和 arr[minIndex]
    // 交换了之后，保证了 arr[0] 到 arr[i] 之间元素有序
    if (minIndex !== i) {
      let temp = arr[minIndex];
      arr[minIndex] = arr[i];
      arr[i] = temp;
    }
  }
  return arr;
}

console.log(selectSort([20, 40, 30, 10, 60, 50])); // [ 10, 20, 30, 40, 50, 60 ]
```
