---

layout: post
title: const_cast 与 代码重复
category: C++

---

«C++ Primer ed5» 中提到一种技术（P209)，通过使用 `const_cast` 重载函数以减少代码重复的情况：


若已有函数：

``````c++
const string& shorterString(const string &s1, const string &s2){
    return s1.size() <= s2.size() ? s1 : s2;
}
``````

<!--description-->

则可以这样使用 `const_cast` 避免代码重复：

``````c++
string &shorterString(string &s1, string &s2){
    // 理论上应使用 static_cast 来将 const 类型转换为 const 类型
    auto &r = shorterString(const_cast<const string&>(s1), const_cast<const string&>(s2));
    return const_cast<string&>(r);
}
``````

«C++ Primer» 上对 `const_cast` 是这样解释的：
>将常量对象转换成非常量对象的行为，我们一般称其为“去掉 const 性质（cast away the const）”。
>一旦我们无掉了某个对象的 const 性质，编译器就不再阻止我们对该对象进行读写操作。
>如果对象本身不是一个常量，使用强制类型转换获得写权限是合法的行为。
>**然而如果对象是一个常量，再使用 const_cast 执行写操作就会产生未定义的后果。**

以及
>只有 const_cast 能改变表达式的常量属性。

此处使用类型转换将 `string` 转化为 `const` ，因而可以调用 `const` 参数的函数来执行需要的操作，得到一个非常量版重载函数。

在 C++ 中，我们常用到成员函数。自然而然地，我们会产生一个疑问：若一个成员函数需要这样的重载，尤其是需要对 `this` 指针进行类型转换，该如何操作呢？ Google 之，得到以下解决方案。

``````c++
// 若有一个读取数组元素的函数，需要重载得到非常量版本
const int& myMatrix::element(int x, int y) const {
//try catch is omitted
    return p[x*n + y - n - 1];
}

// 非常量版本
int& myMatrix::element(int x, int y) {
    return const_cast<int&> (
        static_cast<const myMatrix&>(*this).element(x, y)
        );
}
``````

对于防止代码重复，还有一些应当注意的（来自 Primer）：
1. 有时需要定义一个严格弱序（P396），譬如使用 `multiset` 时。定义了一个 `operator<` 后，可以很快定义出 `operator>` 和 `operator==`

    ``````c++
    bool operator<(T a);
    bool operator>(T a){
        return *this < a;
    }
    bool operator==(T a){
        return ! ((*this < a) || (*this > a));
    {
    ``````

2. 同理，定义前置和后置 `++` 时在后置运算符 `operator++(int)` 中调用前置运算符  `operator++`
