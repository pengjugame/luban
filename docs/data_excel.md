[//]: # "Author: bug"
[//]: # "Date: 2020-11-01 15:40:11"

## bool 类型

- 新增 一个字段 batch_useable，表示能否批量使用
- 用 true 或 false 表示 bool 值，只需要小写后是这两个值即可，比如 true,True,True 都是合法的值。excel 会自动将输入的值大写化。
- 配置:
  ![如图](images/adv/def_01.png)
- [定义](images/adv/def_02.png):  
  ``` xml
  <module name = "item">
    <bean name = "Item">
      <var name = "batch_usable" type = "bool" />
    </bean>
  </module>
  ```

## float 类型

- 新增一个 float 类型字段，掉落概率 drop_prob.
- ![如图](images/adv/def_03.png)
- [定义](images/adv/def_04.png):  
  ``` xml
  <module name = "item">
    <bean name = "Item">
      <var name = "drop_prob" type = "float" />
    </bean>
  </module>
  ```

## 列表类型 list,int

- 我们新增一个字段， 宝箱的随机抽取道具列表 random_item_ids。
- ![如图](images/adv/def_05.png)
- ![如图](images/adv/def_06.png)

## 向量类型 vector3

- vector3 有三个字段 float x, float y, float z, 适合用于表示坐标之类的数据。
- 我们新增一个 spawn_location 字段，表示物品产生的场景位置。
- ![如图](images/adv/def_07.png)
- ![如图](images/adv/def_08.png)

## 枚举类型

- 道具有品质，白绿蓝紫橙。 虽然可以直接填 1-5 这几个数据，但不直观，而且对程序非常不友好。
- 有一种更优雅的方式是定义枚举。
- ![如图](images/adv/def_09.png)
- 之前用 bean 来定义结构，我们引入的新的 tag “enum” 来定义 枚举。
- enum 的 name 属性表示 枚举名。
- 如果生成 c#代码的话，会生成 cfg.item.Equality 类。
- <var name=”xxx” alias=”xx” value=”xx”/> 定义检举项。
- 其中 name 为必填项，不可为空，也不能重复。
- alias 是可选项，枚举项别名。
- value 是枚举值，主要给程序使用。
- ![如图](images/adv/def_10.png)
- excel 表中，想表达一个枚举值，既可以用检举项 name,也可以用枚举项的 alias，但不能是相应的整数值。
- 注意！如果想引用其他模块定义的 enum 或者 bean, type 里必须指定全名。比如 type=”mall.TradeInfo” 。
- ![如图](images/adv/def_11.png)

## bean 类型

- 有时候希望一个字段是复合类型。
- 比如，我们想用一个字段 level_range 来表示道具可以使用的等级范围，它包含两个字段，最小等级和最大等级。
- 此时，可以通过定义 bean 来解决。
  ![如图](images/adv/def_12.png)
- 之前的字段都在一个单元格内填写，现在这个字段是 bean 类型，有两个值，该怎么填写呢？  
  如果也像 list,int 那样把两个数写在一个单元格里(如下图)，会发现工具报错了: “10,20” 不是合法的整数值。
  ![如图](images/adv/def_13.png)
- 填写这些占据多个单元格的数据有两种办法：
  1. 合并标题头  
     让 level_range 标题头占据两列，这样就能在两个单元格里分别填写最小最大等级了。  
     ![如图](images/adv/def_14.png)
  2. 使用 sep 分割单元格  
     字段定义中指定 sep 属性，用某些字符分割单元格，这样就能识别为两个整数了。
     ![如图](images/adv/def_15.png)  
     如果想用 分号; 或者 竖号 | 分割，只要 sep=”;” 或者 sep=”|“ 即可。  
     ![如图](images/adv/def_16.png)

## list,bean 类型

- 有些时候，我们需要一个 结构列表字段。  
  比如说 道具不同等级会增加不同的血量。我们定义一个 ItemLevelAttr 结构。  
  ![如图](images/adv/def_17.png)
- 对应的 excel 配置如下。  
  ![如图](images/adv/def_18.png)
- 对于多个值构成的字段，填写方式为 在标题头(level_attrs)对应的列范围内，按顺序填值。不需要跟策划的标题头名有对应关系。空白单元格会被忽略。也就是如下填法也是可以的：  
  ![如图](images/adv/def_19.png)
- 这种填法的缺点是占据在太多的列。如果想如下填，该怎么办呢？
  ![如图](images/adv/def_20.png)
- 有两种办法。
  1. bean ItemLevelAttr 增加属性 sep=”,”  
     ![如图](images/adv/def_21.png)  
     如果不想用逗号”,” ，想用;来分割单元格内的数据，只要将 sep=”;” 即可。
  2. 字段 level_attrs 增加属性 sep=”,”，即  
     ![如图](images/adv/def_22.png)  
     如果想所有数据都在一个单元格内填写，又该怎么办呢？  
     ![如图](images/adv/def_23.png)  
     想用 | 来分割不同 ItemLevelAttr ,用 , 来分割每个记录的数据。只需要 字段 level_attrs 的 sep=”,|” 即可。
     ![如图](images/adv/def_24.png)

## 多态 bean

- 多态 bean 的 Luban 类型系统的核心，没有它就不可能比较方便简洁地表达游戏内的复杂数据。
- 常见的结构都是固定，但有时候会有需求，某个字段有多种类型，每种类型之间可能有一些公共字段，但它们也有一部分不一样的字段。简单的做法是强行用一个结构包含所有字段，这在类型种类较少时还勉强能工作，但类型很多，字段个数变化极大时，最终的复合结构体过于庞大笨拙，故而难以在实际采用。
- Luban 引入了 OOP 中类型继承的概念，即多态 bean。方便表达那些复杂配置需求。
- 假设 item 有一个形状 Shape 类型的字段。Shape 有两种 Circle 和 Rectangle.  
  Cicle 有 2 个字段 int id; float radius;  
  Rectangle 有 3 个字段 int id; float width; float height;  
  ![如图](images/adv/def_25.png)  
  ![如图](images/adv/def_26.png)
- 注意到，多态 bean 与普通 bean 填写区别在于，多态 bean 需要一个类型名。这也好理解，如果没有类型名，如何知道使用哪个 bean 呢。
- 有时间策划不太习惯填写英文，或者说类型名有时候会调整，不希望调整类型名后配置也跟着改变，因为，多态 bean 支持别名的概念。
  ![如图](images/adv/def_27.png)
- 这时，可以这样填数据：  
  ![如图](images/adv/def_28.png)
- 使用类型名和别名来标识多态都是支持的，可以混合使用。

## multi_rows 多行 记录

- 使用数据表经常会遇到某个字段是列表类型的情形。有时候列表的 bean 的字段特别多，比如多达 10 个字段，列表包含了好几个 bean。如果此时配置成一行，会导致 excel 列过多，策划编辑维护不方便直观。 Luban 支持这个列表 多行配置。
- ![如图](images/adv/def_29.png)
- 和 普通 非多行记录的区别在于 lines 字段多了一个 multi_rows=”1” 字段，表示这个字段要多行配置。
- ![如图](images/adv/def_30.png)
- 和普通不包括多行数据的 excel 表比，meta 行多了 multi_rows:1 这个属性。为了防止被误识别为多行，multi_rows 需要手动打开。

## 多级标题头

- 经常会有字段占了多列，比如 Shape, 如果按顺序填写，有个问题在于，字段很多时，容易对应错误，不方便定位。
- 假设 有个 show_info 字段，包含 如下字段 string name; string desc; string tip;
- ![如图](images/adv/def_31.png)
- 填写数据如下  
  ![如图](images/adv/def_32.png)
- 有几处改动
  1. 我们新插入了一行标题头，第 2 行变成了两行。同时 A2,A3 单元格合并，表示标题头占了 2 行。
  2. show_info 下一行，填上 子字段名 （顺序不重要）
- 我们称之为多级标题头，通过多级标题头的方式，可以精确定位深层次字段的列。方便策划填。


## 单例表
  * 不是所有数据都是 类似map 这样的多记录结构。有些配置只有一份，比如 开启装备系统的最小角色等级。 这些数据 所在的表，也只有一个记录。称之为 单例表。
  * 我们创建一个单例表，来存放这些数据。  
    定义如下：  
    ![如图](images/adv/def_33.png)  
    数据如下：  
    ![如图](images/adv/def_34.png)  

## 横表与纵表
  * 之前介绍的表都是 面向行，沿着行方向填写数据。有时候我们希望 以列为方向填写。 
  * 比如 上面的单例表。 如果改成一行一个字段，看起来会更清楚。 我们引入纵表支持。
  * 定义不变，但 excel 的填法有区别，数据如下：
  * ![如图](images/adv/def_35.png)  

## 可空变量
  * 有时候会有一种变量，我们希望它 功能生效时填一个有效值，功能不生效里，用一个值来表示。 例如 int 类型，常常拿0或者-1作无效值常量。 但有时候，0或-1也是有效值时，这种做法就不生效了。或者说 项目组内 有时候拿0，有时候拿-1作无效值标记，很不统一。我们借鉴 sql及 c#,引入 可空值概念，用null表达空值。
  * 我们为Item 添加  min_use_level 字段，类型为 int? 当填有效值时，使用时要检查等级，否则不检查。
  * 定义如下  
    ![如图](images/adv/def_36.png)  
  * 数据如下  
    ![如图](images/adv/def_37.png)  

## datetime 类型
  * 时间是常用的数据类型。Luban特地提供了支持。  
    填写格式为 以下4种。  
    * yyyy-mm-dd hh:mm:ss 如 1999-08-08 01:30:29
    * yyyy-mm-dd hh:mm    如 2000-08-07 07:40
    * yyyy-mm-dd hh 	  如 2001-09-05 07
    * yyyy-mm-dd 		  如 2003-04-05
  * 为Item 新增一个 失效时间字段 expire_time 。   
    定义如下:  
    ![如图](images/adv/def_38.png)  
    数据如下:  
    ![如图](images/adv/def_39.png)  

## convert 常量替换
  * 游戏里经常会出现一些常用的类似枚举的值，比如说 升级丹的id,在很多地方都要填，如果直接它的道具id,既不直观，也容易出错。 Luban 支持常量替换。  
  * 对于需要常量替换的字段，添加 convert=”枚举类”。 如果填写的值是 枚举名或者别名，则替换为 相应的整数。否则 按照整数解析。
  *    
    定义如下:  
    ![如图](images/adv/def_40.png)  
    数据如下:  
    ![如图](images/adv/def_41.png)  
  * 添加了convert 的字段，既可以填 convert指向的枚举类里的一个合法枚举名，也可以是其他整数。 
