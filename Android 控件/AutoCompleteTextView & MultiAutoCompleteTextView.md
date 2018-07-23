### AutoCompleteTextView & MultiAutoCompleteTextView

----用于输入信息的TextView(输入的内容自动完成)

**1. AutoCompleteTextView**

----动态匹配输入的内容

例如：搜索引擎在输入框输入信息时，会有一个下拉列表显示与当前输入内容有关的信息。

**特有属性：**
- completionThreshold：设置当输入多少字符时，控件开始进行自动匹配
- completionHint：设置出现在下拉菜单中的提示标题
- dropDownHorizontalOffset：下拉菜单于文本框之间的水平偏移。默认与文本框左对齐
- dropDownHeight：下拉菜单的高度
- dropDownWidth：下拉菜单的宽度
- singleLine：单行显示
- dropDownVerticalOffset：垂直偏移量

**使用步骤：**
- 实例化控件：通过findViewById找到控件
- 准备数据源：数据源里的数据将与输入内容进行匹配，匹配的就显示在下拉列表里
- 设置适配器：适配器将数据与界面连接起来


**2. MultiAutoCompleteTextView**

----支持用分隔符来分开选择多个值（如：在发送邮件时的选择联系人）。

在输入第一个值时，它能自动匹配；选择完后，输入分隔符。再输入内容时，它又可以自动匹配。

**使用步骤：**
- 实例化控件：通过findViewById找到控件
- 准备数据源：数据源里的数据将与输入内容进行匹配，匹配的就显示在下拉列表里
- 设置适配器：适配器将数据与界面连接起来
- 设置分隔符：可以自己创建分隔符Tokenizer，也可以使用内部的默认以英文逗号为分隔符的Tokenizer