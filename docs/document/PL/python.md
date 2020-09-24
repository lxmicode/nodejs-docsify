## python

##推导式
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

list1 = ['id','name']
list2 = ['001','jack']
dict = {list1[i]:list2[i] for i in len(list1)}
print(dict) # {'id': '001', 'name': 'jack'}
```

- 集合推导式
```python
list = {i for i in range(10) if i%2==0 }
print(list)
```
