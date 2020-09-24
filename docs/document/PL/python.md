## python

### 推导式
- 可以从一个数据序列构建另一个新的数据序列的结构体。
- 只有列表[]、字典{key:val}、集合{} 能使用推导式


- 列表推导式-for改成推导式如下
```python
list=[]
for i in range(10):
  list.append(i)
print(list)
## for改成推导式
list = [i for i in range(10)]
print(list)
```

- 字典推导式
```python
dict = {i:i**2 for i in range(10)}
print(dict) #{1: 1, 2: 4, 3: 9, 4: 16}

##合并字段数据
list1 = ['id','name']
list2 = ['001','jack']
dict = {list1[i]:list2[i] for i in len(list1)}
print(dict) # {'id': '001', 'name': 'jack'}

## 提取字典数据
dict = {'DELL':201,'acere':99}
count = {key:val for key,val in dict.items() if val>200}
print (count) # {'DELL':201}
```

- 集合推导式
```python
set1 = {i for i in range(10) if i%2==0 }
print(set1) #{2,4,6,8}
```
