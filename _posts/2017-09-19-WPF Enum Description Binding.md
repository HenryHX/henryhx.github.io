---
layout: post
title: 'WPF Enum Description Binding'
date: 2017-09-19
author: HenryHX
cover: '/assets/img/2017-09-19-WPF Enum Description Binding/tech.jpg'
tags: WPF Binding
---

> 主要介绍WPF中comboBox绑定有Description的枚举类型。

### 前言

C#用Enum关键字用于声明枚举，即一种由一组称为枚举数列表的命名常数组成的独特类型。每种枚举类型都有基础类型，该类型可以是除char 以外的任何整型。即：(byte, sbyte, short, ushort, int, uint, long和ulong)，如果想要Enumeration变量返回一点有意义的string，从而用户能知道分别代表什么，则可以添加DescriptionAttribute，如下

{% highlight csharp %}
    /// <summary>
    /// HedgeFlagType是一个投机套保标志类型
    /// </summary>
    public enum eSTPHedgeFlag
    {
        [Description("投机")]
        STP_HF_Speculation = '1',
        [Description("套利")]
        STP_HF_Arbitrage = '2',
        [Description("套保")]
        STP_HF_Hedge = '3',
    }
{% endhighlight %}

上述枚举类型eSTPHedgeFlag是期权交易使用的一个自定义类型，要使用户可以在界面中选择对应的变量，通常可以采用`ObjectDataProvider`进行MVVM模式下的绑定：

ObjectDataProvider 使你能够在 XAML 中创建可用作绑定源的对象。

```C#
        <ObjectDataProvider x:Key="odpSTPDirection"
                            MethodName="GetValues"
                            ObjectType="{x:Type sys:Enum}">
            <ObjectDataProvider.MethodParameters>
                <x:Type TypeName="local:eSTPHedgeFlag"/>
            </ObjectDataProvider.MethodParameters>
        </ObjectDataProvider>
```

如上，在使用的时候直接指定ItemsSource的静态资源

```C#
<ComboBox ItemsSource="{Binding Source={StaticResource odpSTPDirection}}"></ComboBox>
```

![ObjectDataProvider.jpg](/assets/img/2017-09-19-WPF Enum Description Binding/ObjectDataProvider.jpg "ObjectDataProvider Enum")


但是，上述绑定只能显示Enum的左边值，不够直观，怎样才能将Enum的DescriptionAttribute进行界面绑定将是今天要讨论的问题


### 自定义MarkupExtension

MarkupExtension为可以由 .NET Framework XAML 服务及其他 XAML 读取器和 XAML 编写器支持的 XAML 标记扩展实现提供基类。

下面利用可以基类可以编写EnumerationExtension，帮助我们为XAML提供用于扩展的属性上设置的对象值<Description, Value>，起到上面的ObjectDataProvider作用。

```C#
  public class EnumerationExtension : MarkupExtension
    {
        private Type _enumType;

        public EnumerationExtension(Type enumType)
        {
            if (enumType == null)
                throw new ArgumentNullException("enumType");

            EnumType = enumType;
        }

        public Type EnumType
        {
            get { return _enumType; }
            private set
            {
                if (_enumType == value)
                    return;

                var enumType = Nullable.GetUnderlyingType(value) ?? value;

                if (enumType.IsEnum == false)
                    throw new ArgumentException("Type must be an Enum.");

                _enumType = value;
            }
        }

        public override object ProvideValue(IServiceProvider serviceProvider)
        {
            var enumValues = Enum.GetValues(EnumType);

            return (
                from object enumValue in enumValues
                select new EnumerationMember
                {
                    Value = enumValue,
                    Description = GetDescription(enumValue)
                }).ToArray();
        }

        private string GetDescription(object enumValue)
        {
            var descriptionAttribute = EnumType
                .GetField(enumValue.ToString())
                .GetCustomAttributes(typeof(DescriptionAttribute), false)
                .FirstOrDefault() as DescriptionAttribute;


            return descriptionAttribute != null
                ? descriptionAttribute.Description
                : enumValue.ToString();
        }

        public class EnumerationMember
        {
            public string Description { get; set; }
            public object Value { get; set; }
        }
    }
```

接下来，直接在XAML上进行应用

```C#
    <ComboBox ItemsSource="{Binding Source={local:Enumeration {x:Type local:eSTPHedgeFlag}}}" 
              DisplayMemberPath="Description" 
              SelectedValue="{Binding HedgeFlag, RelativeSource={RelativeSource AncestorType=Window, Mode=FindAncestor}}" 
              SelectedValuePath="Value"  />
```

`local:Enumeration`即为刚才定义的EnumerationExtension类型。`DisplayMemberPath`绑定string类型的`Description`,`SelectedValuePath`绑定Enum类型的`Value`。

运行结果如下：

![EnumerationExtension.jpg](/assets/img/2017-09-19-WPF Enum Description Binding/EnumerationExtension.jpg "EnumerationExtension Enum")

事实上，还有几种实现方法，比如在ViewModel中创建`Dictionary<ExampleEnum, string>`的键值对，具体参考[Databinding an enum property to a ComboBox in WPF
](https://stackoverflow.com/questions/58743/databinding-an-enum-property-to-a-combobox-in-wpf)，有时间可以试试。

### 总结
**今天一小步 未来一大步**
![summary.jpg](/assets/img/2017-09-19-WPF Enum Description Binding/summary.jpg "总结")
