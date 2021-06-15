---
layout: post
title:  "Test Highlight"
date: 2021-06-15
author: Excaive
color: rgb(158,235,235)
cover: '../assets/images/7574671_p0.jpg'
tags: test
---

{% highlight python %}
from random import randint
import time


def generate_random_array(n, lower=0, upper=100_000):
    """生成随机数列"""
    arr = [randint(lower, upper) for i in range(n)]
    return arr


def generate_nearly_ordered_array(n, swap_times):
    """生成基本有序的数列"""
    arr = []
    for i in range(n):
        arr.append(i)
    for j in range(swap_times):
        pos_x = randint(0, n-1)
        pos_y = randint(0, n-1)
        arr[pos_x], arr[pos_y] = arr[pos_y], arr[pos_x]
    return arr


def is_sorted(list_in):
    """判断数列是否有序（算法是否正确）"""
    for i in range(0, len(list_in) - 1):
        if list_in[i] > list_in[i + 1]:
            return False
    return True


def test_sort(func, list_in):
    """func表示要检测的算法函数，alist为传入的数列"""
    list_in =  func(list_in)
    assert is_sorted(list_in), "排序算法错误\n"


def timing(func):
    """计时"""
    def wrapper(list_in, **kwargs):
        log = kwargs.get('log', True)
        if log:
            print('-' * 30)
            print(func.__doc__)
            print(f'排序前 {list_in[:10_000]}')
            time1 = time.time()
            list_out = func(list_in, log)
            print(f'排序后 {list_out[:10_000]}')
            time2 = time.time()
            print(f'规模 {len(list_in)}  用时 {time2 - time1:.2f} s')
            print('-' * 30)
        else:
            list_out = func(list_in, log)
        return list_out

    return wrapper


@timing
def bubble_sort(list_in, log):
    """冒泡排序"""
    length = len(list_in)
    for i in range(length):
        for j in range(1, length - i):
            if list_in[j - 1] > list_in[j]:
                list_in[j - 1], list_in[j] = list_in[j], list_in[j - 1]
    return list_in


@timing
def selection_sort(list_in, log):
    """选择排序"""
    length = len(list_in)
    for i in range(length):
        min_ele = i
        for j in range(i + 1, length):
            if list_in[j] < list_in[min_ele]:
                min_ele = j
        list_in[min_ele], list_in[i] = list_in[i], list_in[min_ele]
    return list_in


@timing
def insert_sort(list_in, log):
    """插入排序"""
    length = len(list_in)
    for i in range(1, length):
        if list_in[i] < list_in[i - 1]:
            temp = list_in[i]
            index = i
            for j in range(i - 1, -1, -1):
                if list_in[j] > temp:
                    list_in[j + 1] = list_in[j]
                    index = j
                else:
                    break
            list_in[index] = temp
    return list_in


@timing
def shell_sort(list_in, log):
    """希尔排序"""
    length = len(list_in)
    # 初始步长
    gap = length // 2
    while gap > 0:
        for i in range(gap, length):
            # 每个步长进行插入排序
            temp = list_in[i]
            j = i
            # 插入排序
            while j >= gap and list_in[j - gap] > temp:
                list_in[j] = list_in[j - gap]
                j -= gap
            list_in[j] = temp
        # 得到新的步长
        gap = gap // 2
    return list_in


@timing
def quick_sort(list_in, log):
    """快速排序"""
    if len(list_in) <= 1:
        return list_in
    else:
        middle = list_in[0]
        equal_list = [middle]
        smaller_list = []
        bigger_list = []
        for i in list_in[1:]:
            if i < middle:
                smaller_list.append(i)
            elif i > middle:
                bigger_list.append(i)
            else:
                equal_list.append(i)
        smaller_list = quick_sort(smaller_list, log=False)
        bigger_list = quick_sort(bigger_list, log=False)
        return smaller_list + equal_list + bigger_list


@timing
def merge_sort(list_in, log):
    """归并排序"""

    def merge(list_i, list_j):
        i, j = 0, 0
        result = []
        while i < len(list_i) and j < len(list_j):
            if list_i[i] < list_j[j]:
                result.append(list_i[i])
                i += 1
            else:
                result.append(list_j[j])
                j += 1
        result += list_i[i:]
        result += list_j[j:]
        return result

    # 认为长度不大于1的数列是有序的
    if len(list_in) <= 1:
        return list_in
    # 二分列表
    middle = len(list_in) // 2
    left_list = merge_sort(list_in[:middle], log=False)
    right_list = merge_sort(list_in[middle:], log=False)

    # 最后一次合并
    return merge(left_list, right_list)


array = generate_random_array(5000)

bubble_sort(array.copy())
selection_sort(array.copy())
insert_sort(array.copy())
shell_sort(array.copy())
quick_sort(array.copy())
merge_sort(array.copy())
{% endhighlight %}
