# 数据结构——堆排序（C语言）

> 昨天裸考软考，下午有一题就是堆排序。我21年的时候学过堆排序，可到现在全忘光了，结果就是面对那题发呆良久，脑子里始终想不起堆排序的核心流程，真是后悔莫及，现在重新来复习一遍堆排序。

##### 性能

时间复杂度：O(N*logN)
空间复杂度：O(1)
不稳定性排序

##### 堆排序原理

​		堆排序，本质就是利用大小根堆的特性，升序时使用大根堆，降序时使用小根堆。

##### 排序方法

​		以升序为例——先将数组构建成一个大根堆，然后取堆顶数与数组最后一个数进行交换，然后把最后一个数(最大数)剔除出数组，随后接着再次把剩余数组排序成大根堆，又将堆顶数与最后一个数交换剔除，如此循环往复，直到排序完成。降序则按照此方法构建小根堆即可。

##### 算法（C语言）

```C
#include <stdio.h>
#include <stdlib.h>

#define MAXITEM 100


void printArray(int arr[], int n) {
	int i;
	for (i = 1; i < n; i++)
	{
		printf("%d  ", arr[i]);
	}
	printf("\n");
}


/*
	生成大根堆
	R:待排序数组
	v:传入的非叶子节点下标
	n:最后一个下标
*/
void Heapify(int R[MAXITEM], int v, int n) {
	int i, j;
	i = v; // 记录传入的非叶子节点下标(一个子大根堆中的父节点)
	j = i * 2;	// R[i]的左子节点
	// 记住后面 i指向父节点 j指向子节点
	R[0] = R[i]; // 暂存父节点的值，方便最终替换时赋值
    
	// 开始一个子大根堆的比较与替换
	while (j <= n) { // 当前叶子节点是存在的就继续循环（大于等于数组长度n的话就不存在）
		// 先判断左右子节点的大小，再与父节点进行比较
		// 其中 j< n 说明他存在兄弟节点(右节点)
		if (j<n && R[j]<R[j+1])
		{
			j++; // 右节点比左子节点大，则用右节点与父节点比较,这里j++就指向了右节点的下标了
		}
        
        // 父节点开始与子节点比较
		// 如果父节点的值小于子节点值
		if (R[i] < R[j]) {
			R[i] = R[j]; // 替换父节点的值
			i = j; // 将要比较的父节点变成当前子节点
			j = 2 * i;	// 将要比较的子节点变成当前子节点的子节点
		}
		else { // 如果父节点不小于则直接退出循环，因为根堆是从下往上构建的，如果父节点不需要替换，子节点自然不用再重新替换
			j = n + 1;
		}
		R[i] = R[0]; // 将父节点的值赋值到当前节点
	}
	
}
/*
	堆排序算法
	本算法舍弃了数组下标0的空间，直接从下标1开始排序
*/
void HeapSort(int R[MAXITEM], int n) {
	int i;
	// 将数组先变成一个大根堆
	// n/2 一定是最后一个非叶子节点，其前面的都是非叶子节点
	for (i = n / 2; i >= 1; i--) {
		Heapify(R, i, n);
	}
	// 大根堆只需要保证其父节点的值大于左右子节点的值即可，无需排列的很整齐
	printf("初始构建大根堆：");
	printArray(R, n + 1);
	// 开始排序，将根节点移除在外，然后用最后一个叶子节点代替根节点
	for (i = n; i > 1; i--)
	{
		// 将根节点与最后一个叶子节点交换位置,R[0]作为临时空间存在，用于存放要交换的数据
		R[0] = R[i];
		R[i] = R[1];
		R[1] = R[0];
		// 将最后一个数（也就是根节点）排除在外后再进行构建堆操作
		Heapify(R, 1, i - 1);
		printf("第%d次排序：",n-i+1);
		printArray(R, n + 1);
	}
}

int main() {
	int arr[] = {0, 7, 10, 13, 15, 4, 20, 19, 8 };
	// 这里我舍弃了下标0的空间，从下标1开始排序
	HeapSort(arr, sizeof(arr)/sizeof(int)-1);
	printf("最终结果：");
	printArray(arr, sizeof(arr) / sizeof(int));
}

```

**运行结果**

![image-20221106194512552](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211062156918.png)



##### 算法（C语言）

```c
//将排序数组当做完全二叉树
//将完全二叉树生成堆
//将堆顶的最大数，与末尾数交换，然后拿出最大数
//重新排序成堆，拿掉与末尾数做了交换的最大数
//如此循环，排序完成
//堆(heap)的特性
//	通过子节点找到父母节点(parent):(i-1)/2
//	通过父母节点找到左子节点:2*i+1
//	通过父母节点找到右子节点:2*i+2

//父节点与两个子节点成堆
void heapify(int three[],int size,int nodeIndex) {
	if (nodeIndex >= size) return;
	int clIndex = 2 * nodeIndex + 1;//左子节点下标
	int crIndex = 2 * nodeIndex + 2;//右子节点下标
	int maxIndex = nodeIndex;
	maxIndex = (clIndex<size&& three[clIndex]>three[maxIndex]) ? clIndex : maxIndex;
	maxIndex = (crIndex<size&& three[crIndex]>three[maxIndex]) ? crIndex : maxIndex;
	//如果最大数不是父节点，则交换子父节点的值，然后递归以交换后的子节点查找子节点的子节点
	if (maxIndex != nodeIndex) {
		swap(three, maxIndex, nodeIndex);
		heapify(three, size, maxIndex);
	}
}
//有些毫无规则的数组无法从顶部开始找到最大数，需要从末尾开始查找
//从树的最后一个父节点开始递减，将整个数排序成堆
void buildHeap(int *three,int size) {
	int lastNodeIndex = size - 1;//最后一个节点的下标
	int parentNodeIndex = (lastNodeIndex - 1) / 2;//调用(i-1)/2公式查找最后一个节点的父节点
	//做递减，从最后一个父节点开始向树顶(下标0)排序
	for (int i = parentNodeIndex; i >= 0; i--)
	{
		//排序成堆
		heapify(three, size, i);
	}
}

void heapSort(int* arr, int size) {
	//将数组当做完全二叉树排序成堆
	buildHeap(arr, size);
	//不断砍断尾部(就是下标i后面都是排好的大数)
	for (int i = size-1; i >= 0; i--)
	{	
		//堆顶与最后一个节点下标互换
		swap(arr, 0, i);
		//从头到尾再次成堆,此时的i代表数组的大小
		heapify(arr, i, 0);
		//为什么不用buidHeap?
		//构建堆只需调用一次，已经将杂乱无章的数组排序成堆,头尾交换并砍掉尾部后，尾部的堆都是正确的，只有头部堆需要heapify
		//接下来就只用heapify从上至下排序就好了
	}
	//当最后一个节点都被砍断后，整个数组就已经排序成功了
}
```



##### 我的难点

​		如何将数组转换成大根堆？





### 大根堆的构建

#### 特点

- 一个堆一定是一颗完全二叉树，所以数组能够完美的转换为大根堆(将下标0看做树顶，之后就是一个数一个数排三角形了)
- 每个大根堆其父节点的值一定大于其左右子节点的值
- 堆中的每个父节点都与它的子节点组成一个子大根堆，其后根节点再与所以子节点组成一个最终的大根堆。

#### 如何从零开始生成大根堆

每个个入堆的数都需从最底部开始与父节点比较，如果大于则往上替换

#### 如何将数组转换为大根堆

**规则**

- 在数组中，根节点是数组的第一个元素，而一个节点n的左右节点分别是n*2和n*2+1，一个子节点i的父节点位置是(i-1)/2

- 大根堆只需要保证每个节点的值大于他的左右节点值即可，不需要排序得右大左下这样整齐

##### 转换步骤

第一步：将数组当做一棵完全二叉树，从最后一个非叶子节点开始构建子大根堆（如果数组长度为n，那么最后一个非叶子节点一定是n/2）

第二步：如果左右子节点中的较大值大于父节点的值，那么就将父节点的值与该子节点的值进行替换，如果子节点还存在子节点，那么还需要对子节点组成的子大根堆进行重新构建

第三步：循环一二步，直到比较完根节点，一个最终的大根堆就完成了。

##### 转换图解

**将数组看做完全二叉树**

​	![image-20221106183141145](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211062156920.png)

**待构建堆的完全二叉树**

![image-20221106183854623](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211062156921.png)

**从最后一个非叶子节点开始构建一个个子大根堆**

![image-20221106183959646](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211062156922.png)

**所有子节点都构建好后的二叉树开始比较根节点**

![image-20221106184103616](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211062156923.png)

**最终形成的大根堆**

![image-20221106184156033](https://cdn.staticaly.com/gh/kui-ming/tuchuang/main/images202211062156924.png)