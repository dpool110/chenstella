---
title: 层次分析法代码实现
categories: 数学建模
tags:
  - 层次分析
  - 数学建模
  - matlab
cover: 'https://i.loli.net/2020/07/23/5Q4GBWqydsfTMlj.jpg'
abbrlink: 34334
date: 2020-07-20 21:08:35
---

# 输入判断矩阵

	```
	clear;clc %清除变量和命令窗口
	disp('请输入判断矩阵A： ') %输入函数 自定义矩阵输入
	```

# 计算权重

## 方法1：算数平均法求权重

**1、将判断矩阵按照列进行归一化处理（每一个元素初一其所在列的和）**

	```
	Sum_A = sum(A) %矩阵按列相加
	[m,n] = size(A) %取矩阵的行数m，列数n
	SUM_A=repmat(Sum_A,n,1); %按行重复n次sum_A函数
	Stand_A = A ./ SUM_A; %A和SUM_A矩阵对应行相除；即将判断矩阵按照列归一化
	disp('算术平均法求权重的结果为：'); %输出函数
	```

**2、将归一化的各列相加(按行求和)**
	```
	sum(Stand_A,2);
	```


**3、将相加后得到的向量中每个元素除以n即可得到权重向量**
	```
	disp('算术平均法求权重的结果为：'); %输出函数
	disp(sum(Stand_A,2) / n)
	```

## 方法2：几何平均法求权重

**1、将A的元素按照行相乘得到一个新的列向量**

	```
	clc;A
	Prduct_A = prod(A,2) % prod函数和sum函数类似，用于矩阵元素相乘  dim = 2 维度是行，意思是每一行上元素相加
	```

**2、将新的向量的每个分量开n次方**

	```
	Prduct_n_A = Prduct_A .^ (1/n)
	% “.”是对每个元素进行乘方操作，^符号表示乘方，这里是开n次方，所以我们等价求1/n次方
	
	```

**3、对该列向量进行归一化即可得到权重向量**

	```
	disp('几何平均法求权重的结果为：');
	disp(Prduct_n_A ./ sum(Prduct_n_A))
	% 将“Prduct_n_A”列向量中的每一个元素除以“Prduct_n_A”向量的和即可
	```

## 方法3：特征值法求权重

**1、求出矩阵A的最大特征值以及其对应的特征向量**

	```
	[r,c] = find(D == Max_eig , 1);% 找到D中第一个与最大特征值相等的元素的位置，记录它的行和列。
	
	```

**2、对求出的特征向量进行归一化即可得到我们的权重**

	```
	disp('特征值法求权重的结果为：'); %输出函数
	disp( V(:,c) ./ sum(V(:,c))) %对求出的特征向量进行归一化即可得到权重
	
	```

## 计算一致性比例CR

	```
		clc
		CI = (Max_eig - n) / (n-1); %计算一致性指标CI
		RI=[0 0 0.52 0.89 1.12 1.26 1.36 1.41 1.46 1.49 1.52 1.54 1.56 1.58 1.59];  %查找对应的平均随机一致性指标RI，这里的RI最多支持 n = 15
		CR=CI/RI(n); %计算一致性比例CR
		% 输出函数
		disp('一致性指标CI=');disp(CI);
		disp('一致性比例CR=');disp(CR);
		% if循环，判断矩阵A的一致性是否可以接受
		if CR<0.10
		    disp('因为CR < 0.10，所以该判断矩阵A的一致性可以接受!');
		else
		    disp('注意：CR >= 0.10，因此该判断矩阵A需要进行修改!');
		end
	```