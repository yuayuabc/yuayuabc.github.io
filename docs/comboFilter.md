## combo下拉框实现模糊匹配

**1、 将combo的的属性queryMode设置为local**

缺点：只能从开头匹配combo输入的值，相当于`/^reg/`



**2、前端过滤**

在combo触发change事件时使用过滤机制

```js
change: function(combo, newValue, oldValue, options) {
// 示例代码
  var reg = new RegExp(newValue);
  app.comboStore.clearFilter();
  app.comboStore.filter('COLUMN',reg);
}
```

缺点：接口给的是全量的数据，当数据量过大时会耗费加载时间



**3、后端过滤1**

combo每次触发change事件时将发送一次网络请求，并将combo输入框中的值作为参数query传递给数据接口。接口使用query参数对数据进行过滤后再传给前端

```sql
-- 示例sql
select * from table where column like '%' || {?query?} || '%'
```

缺点：只能对当前combo的值进行过滤，无法联动其他组件



**4、后端过滤2**

在combo的store加载前，定义好需要模糊匹配的网络请求参数。接口针对参数进行模糊匹配

前端：
```js
// 示例代码
beforeload: function(store, operation, options) {
  operation.params.filterValue = 'reg';
}
```

接口：
```sql
-- 示例sql
select * from table where column like '%' || {?filterValue?} || '%'
```
