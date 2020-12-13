学习笔记

插入排序

​	private static void insertSort(int[] arr) {

​		int j; *// 已排序列表下标*

​		int t; *// 待排序元素*

​		for (int i = 1; i < arr.length; i++) {

​			if (arr[i] < arr[i - 1]) { 

​				t = arr[i]; *// 赋值给待排序元素*

​				for (j = i - 1; j >= 0 && arr[j] > t; j--) {

​					arr[j + 1] = arr[j]; *// 从后往前遍历已排序列表，逐个和待排序元素比较，如果已排序元素较大，则将它后移*

​				}

​				arr[j + 1] = t; *// 将待排序元素插入到正确的位置*

​			}

​		}

​	}

冒泡排序

​	public static void bubbleSort(int[] data) {

 

​		for (int i = 0; i < data.length - 1; i++) {

​			for (int j = 0; j < data.length - i - 1; j++) {

 

​				if (data[j] > data[j + 1]) {

​					int tmp = data[j];

​					data[j] = data[j + 1];

​					data[j + 1] = tmp;

​				}

​			}

​		}

 

选择排序

​	public static void selectSort(int[] data) {

​		if (data == null || data.length == 0) {

​			return;

​		}

 

​		for (int i = 0; i < data.length - 1; i++) {

​			int min = i;*// 将当前下标定为最小值下标*

​			for (int j = i + 1; j < data.length; j++) {

​				if (data[j] < data[min]) {

​					min = j;

​				}

​			}

 

​			if (i != min) {

​				int tmp = data[i];

​				data[i] = data[min];

​				data[min] = tmp;

​			}

​		}

​	}



