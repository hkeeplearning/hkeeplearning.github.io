---
title: C++实现多维数组
date: 2020-05-02 20:29:49
categories:
	- 编程
tags: 
	- CPP
---
在Visual Studio 2010的版本上想要使用多维数组实现一个算法，但是，无奈`boost`提供的`multi_array`坑太多，在多维数组降维赋值时经常发生错误，`eigen`提供的只有二维矩阵，最新版本中有`tensor`，但在目前的产品代码库里用不了，opencv？太大。。。最后还是自己实现了一个，拿出来，抛砖引玉吧。

<!-- more -->

这里用到了`C++11`列表初始化；如果你的编译器比较旧（没错，我用的就是vs2010），可以使用C语言的`stdarg.h`进行不定参数解析，当然这里也有些小坑。

```C++
#pragma once
// custom_multi_array.hpp
#include <vector>
#include <string>
#include <stdexcept>

template<typename T, int Dims>
class MultiArray
{
public:
	MultiArray() : mTotalDimCnt(0)
	{
		memset(mDimCnt, 0, sizeof(T)*Dims);
	}
	MultiArray(const std::vector<size_t> &dimCnt) : mTotalDimCnt(1)
	{
		SetMemberVariable(dimCnt);
	}
	void Reset(const std::vector<size_t> &dimCnt)
	{
		mTotalDimCnt = 1;
		SetMemberVariable(dimCnt);
	}
	size_t GetDimCnt(size_t dimIdx) const
	{
		if (dimIdx < Dims)
		{
			return mDimCnt[dimIdx];
		}
		return 0;
	}
	size_t GetTotalDimCnt() const
	{
		return mTotalDimCnt;
	}
	T *Get()
	{
		if (mContent.empty())
		{
			return nullptr;
		}
		return &mContent[0];
	}
	const T *Get() const
	{
		if (mContent.empty())
		{
			return nullptr;
		}
		return &mContent[0];
	}
	T &operator()(const std::vector<size_t> &args)
	{
		try
		{
			size_t idx = GetIndex(args);
			return mContent[idx];
		}
		catch (const std::exception&e)
		{
			throw std::invalid_argument(e.what());
		}
	}
	const T &operator()(const std::vector<size_t> &args) const
	{
		try
		{
			size_t idx = GetIndex(args);
			return mContent[idx];
		}
		catch (const std::exception&e)
		{
			throw std::invalid_argument(e.what());
		}
	}

	/// Copy constructor
	MultiArray<T, Dims>(const MultiArray<T, Dims> &lhs)
	{
		mTotalDimCnt = 1;
		for (size_t i = 0; i < Dims; i++)
		{
			mDimCnt[i] = lhs.mDimCnt[i];
			mTotalDimCnt *= mDimCnt[i];
		}
		mContent = lhs.mContent;
	}
	/// Assignment constructor
	MultiArray<T, Dims> &operator=(const MultiArray<T, Dims> &lhs)
	{
		for (size_t i = 0; i < Dims; i++)
		{
			mDimCnt[i] = lhs.mDimCnt[i];
		}

		if (lhs.mTotalDimCnt != mTotalDimCnt)
		{
			mTotalDimCnt = 1;
			for (size_t i = 0; i < Dims; i++)
			{
				mTotalDimCnt *= mDimCnt[i];
			}
			mContent.resize(mTotalDimCnt);
		}
		std::copy(lhs.mContent.begin(), lhs.mContent.end(), mContent.begin());
		return *this;
	}

private:
	void SetMemberVariable(const std::vector<size_t> &dimCnt)
	{
		static const std::string exceptionPrefix = "GetIndex Exception! ";
		if (dimCnt.size() == Dims)
		{
			for (size_t i = 0; i < Dims; i++)
			{
				mDimCnt[i] = dimCnt[i];
				mTotalDimCnt *= mDimCnt[i];
			}
			if (mTotalDimCnt > 0)
			{
				mContent.resize(mTotalDimCnt, 0);
			}
			else
			{
				throw std::invalid_argument(exceptionPrefix + "Dimension count error (equal 0)!");
			}
		}
		else
		{
			throw std::invalid_argument(exceptionPrefix + "Dimension count size error!");
		}
	}
	size_t GetIndex(const std::vector<size_t> &args)
	{
		static const std::string exceptionPrefix = "GetIndex Exception! ";
		/// Parameters check
		if (args.size() > Dims || args.empty())
		{
			throw std::invalid_argument(exceptionPrefix + "Dimension count size error!");
		}
		/// Assign to local variable
		size_t dimCnt[Dims] = { 0 };
		for (size_t i = 0; i < args.size(); i++)
		{
			dimCnt[i] = args[i];
		}
		/// Get current index from args
		size_t idx = 0;
		for (size_t i = 0; i < Dims; i++)
		{
			if (dimCnt[i] >= mDimCnt[i])
			{
				throw std::invalid_argument(exceptionPrefix + "Dimension count error!");
			}
			// Get current index corresponding element count
			size_t  t = 1;
			for (size_t j = i + 1; j < Dims; j++)
			{
				t *= mDimCnt[j];
			}
			// Updata index
			idx += (t * dimCnt[i]);
		}

		return idx;
	}

private:
	size_t         mDimCnt[Dims];
	size_t         mTotalDimCnt;
	std::vector<T> mContent;
};
```

主函数，简单的测试和调用示例

```C++
#include "custom_multi_array.hpp"
#include <iostream>

/// main.cpp

int main()
{
	{ /// 构造函数
		try
		{
			MultiArray<float, 2> mat2d1({ 1 });
			std::cout << "Construct success" << std::endl;
		}
		catch (const std::exception& e)
		{
			std::cout << e.what() << std::endl;
		}
		try
		{
			MultiArray<float, 2> mat2d1({ 1, 0 });
			std::cout << "Construct success" << std::endl;
		}
		catch (const std::exception& e)
		{
			std::cout << e.what() << std::endl;
		}
		try
		{
			MultiArray<float, 2> mat2d1({ 1, 1, 1 });
			std::cout << "Construct success" << std::endl;
		}
		catch (const std::exception& e)
		{
			std::cout << e.what() << std::endl;
		}
		try
		{
			MultiArray<float, 2> mat2d1({ 1, 1 });
			std::cout << "Construct success" << std::endl;
		}
		catch (const std::exception& e)
		{
			std::cout << e.what() << std::endl;
		}
	}
	{ /// 复制，赋值构造函数
		MultiArray<float, 2> mat2d1({ 18, 2 });
		MultiArray<float, 2> mat2d2;
		MultiArray<float, 2> mat2d3({ 18, 2 });

		/// 复制构造函数
		MultiArray<float, 2> mat2d4(mat2d1);
		/// 赋值构造函数
		mat2d2 = mat2d1; // 大小不等
		mat2d3 = mat2d1; // 大小相等
	}
	{ /// 不同维度的赋值
		MultiArray<float, 3> mat3d({ 12, 18, 2 });
		MultiArray<float, 2> mat2d({ 18, 2 });

		memcpy(mat2d.Get(), &mat3d({ 5 }), 18 * 2 * sizeof(float));
	}
	{ /// 遍历输出
		MultiArray<float, 2> mat2d({ 18, 2 });
		std::vector<float> vec(18 * 2);
		for (size_t i = 0; i < 18*2; i++)
		{
			vec[i] = i * 1.0f;
		}
		memcpy(mat2d.Get(), &vec[0], 18 * 2 * sizeof(float));

		for (size_t i = 0; i < mat2d.GetDimCnt(0); i++)
		{
			for (size_t j = 0; j < mat2d.GetDimCnt(1); j++)
			{
				std::cout << mat2d({ i, j }) << "\t";
			}
			std::cout << "\n";
		}
	}
}
```