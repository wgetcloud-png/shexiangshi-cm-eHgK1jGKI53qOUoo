
### DataGridView 的默认行为是支持通过单击列头对列进行排序，但在以下情况下可能会取消该功能或无法使用：


#### 1\. 绑定的数据源不支持排序


如果 DataGridView 的数据源是绑定到一个不支持排序的集合（例如，List 或未实现 IBindingList 的对象），排序功能会被禁用。


#### 2\. 列的 SortMode 设置为 DataGridViewColumnSortMode.NotSortable


每列都有一个 SortMode 属性，默认值为 Automatic。如果将 SortMode 设置为 NotSortable，则禁用了点击列头排序功能。


#### 3\. 事件处理干扰了默认排序行为


如果在 ColumnHeaderMouseClick 事件中写了自定义代码，且未调用 DataGridView.Sort 方法，可能会导致排序功能被覆盖。




---


### \#\#\#\# 当DataGridView 的数据源是List的时候无法实现单击列头对列进行排序，需要手动写ColumnHeaderMouseClick事件实现单击列头排序


#### 1\.为当DataGridView添加ColumnHeaderMouseClick事件


![image](https://img2024.cnblogs.com/blog/3390178/202412/3390178-20241211165202138-779219299.png)


#### 2\.ColumnHeaderMouseClick代码



```


|  | private void dgvMain_ColumnHeaderMouseClick(object sender, DataGridViewCellMouseEventArgs e) |
| --- | --- |
|  | { |
|  | string columnName = dgvMain.Columns[e.ColumnIndex].DataPropertyName; // 获取绑定的字段名称 |
|  | var dataSource = bsMain.DataSource as List<GroupedKanBanInfo>;//List，实际情况换成需要的实体类 |
|  |  |
|  | if (dataSource == null) return; |
|  |  |
|  | // 根据列名排序，支持升序和降序 |
|  | if (dgvMain.Tag == null || dgvMain.Tag.ToString() != columnName) // 默认升序 |
|  | { |
|  | bsMain.DataSource = dataSource.OrderBy(item => GetPropertyValue(item, columnName)).ToList(); |
|  | dgvMain.Tag = columnName; // 保存当前排序列，用于下次判断 |
|  | } |
|  | else // 切换为降序 |
|  | { |
|  | bsMain.DataSource = dataSource.OrderByDescending(item => GetPropertyValue(item, columnName)).ToList(); |
|  | dgvMain.Tag = null; // 重置 |
|  | } |
|  |  |
|  | bsMain.ResetBindings(false); |
|  | } |
|  |  |
|  | // 通用的获取属性值方法 |
|  | private object GetPropertyValue(object obj, string propertyName) |
|  | { |
|  | if (obj == null || string.IsNullOrEmpty(propertyName)) return null; |
|  | var prop = obj.GetType().GetProperty(propertyName); |
|  | return prop?.GetValue(obj); |
|  | } |


```

 本博客参考[veee加速器](https://youhaochi.com)。转载请注明出处！
