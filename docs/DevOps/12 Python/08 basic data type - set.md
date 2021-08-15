
集合，不可重复的列表，可变类型


> difference s1中存在，s2中不存在

```
s1 = {'xmzncc','fcc'}
s2 = {'shabi','oka'}
v = s1.difference(s2)
print(v)
```


> s2中存在，s1中不存在

```
s1 = {'xmzncc','fcc','test'}
s2 = {'shabi','oka','xmzncc'}
v = s2.difference(s1)
print(v)
```

> difference_update s1中存在，s2中不存在，然后对s1清空，然后在重新赋值

```
s1 = {'xmzncc','fcc','test'}
s2 = {'shabi','oka','xmzncc'}
s1.difference_update(s2)
print(s1)
```

> symmetric_difference s1与s2bu不同处

```
s1 = {'xmzncc','fcc','test'}
s2 = {'shabi','oka','xmzncc'}
v = s1.symmetric_difference(s2)
print(v)
```

> intersection s1与s2交集处

```
s1 = {'xmzncc','fcc','test'}
s2 = {'shabi','oka','xmzncc'}
v = s1.intersection(s2)
print(v)
```

> union 并集

```
s1 = {'xmzncc','fcc','test'}
s2 = {'shabi','oka','xmzncc'}
v = s1.union(s2)
print(v)
```

> discard 移除

```
s1 = {'xmzncc','fcc','test'}
s1.discard('xmzncc')
print(s1)
```

> update 更新

```
s1 = {"shabi",'eric','tony','李泉','李泉11'}
s1.update({'shabi','123123','fff'})
print(s1)
```





