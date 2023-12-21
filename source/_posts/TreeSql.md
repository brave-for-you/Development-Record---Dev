---
title: 树状表Sql
date: 2023-08-30 15:00:00
tags:
---
只记录了左右值和路径值的树型结构，个人感觉路径值更好用一点



## 左右值(lft, rgt)
    查询很好用，但是新增删除时需要操作执行整个表的内容，存储失败时难以回滚，还会有并发冲突的问题，只适合内容稳定修改较少的表类型

### 新增
``` java
// +----+-------------+-------+--------+-----+-----+----------+-------------+
// | id | name        | value | type   | lft | rgt | parentId | valuePrefix |
// +----+-------------+-------+--------+-----+-----+----------+-------------+
// |  1 | SYSTEM_NAME | 0     | system |   1 |  28 | NULL     | sys:        |
// |  2 | 系统维护    | 0     | menu   |   2 |  21 |        1 | sys:0:      |
// |  3 | 人员管理    | 1     | page   |  22 |  27 |        1 | sys:0:      |
// |  4 | 组织管理    | 0     | page   |   3 |   4 |        2 | sys:0:0:    |
// |  5 | 角色管理    | 1     | page   |   5 |  10 |        2 | sys:0:0:    |
// |  6 | 权限管理    | 2     | page   |  11 |  16 |        2 | sys:0:0:    |
// |  7 | 操作日志    | 3     | page   |  17 |  18 |        2 | sys:0:0:    |
// |  8 | 系统设置    | 4     | page   |  19 |  20 |        2 | sys:0:0:    |
// |  9 | 新增        | 0     | button |   6 |   7 |        5 | sys:0:0:1:  |
// | 10 | 新增        | 0     | button |  12 |  13 |        6 | sys:0:0:2:  |
// | 11 | 编辑        | 1     | button |   8 |   9 |        5 | sys:0:0:1:  |
// | 12 | 编辑        | 1     | button |  14 |  15 |        6 | sys:0:0:2:  |
// | 13 | 新增        | 0     | button |  23 |  24 |        3 | sys:0:1:    |
// | 14 | 编辑        | 1     | button |  25 |  26 |        3 | sys:0:1:    |
// +----+-------------+-------+--------+-----+-----+----------+-------------+
SET @lft := 7;/*新部门的左值*/
SET @rgt := 8;/*新部门的右值*/
SET @level := 5;/*新部门的层级*/
begin;
/*将插入的后续边缘的节点左右数+2*/
UPDATE department SET lft=lft+2 WHERE lft > @lft;
UPDATE department SET rgt=rgt+2 WHERE rgt >= @lft;
/*插入数据*/
INSERT INTO department(name,lft,rgt,level) VALUES('新部门',@lft,@rgt,level);
/*新增影响行数为0时，必须回滚*/
commit;
/*rollback;*/
```

### 删除
``` java
SET @lft := 7;/*要删除的节点左值*/
SET @rgt := 8;/*要删除的节点右值*/
begin;
UPDATE department SET lft=lft-2 WHERE lft > @lft;
UPDATE department SET rgt=rgt-2 WHERE rgt > @lft;

/*删除节点*/
DELETE FROM department WHERE lft=@lft AND rgt=@rgt;
/*删除影响行数为0时，必须回滚*/
commit;
/*rollback*/
```

### 查询
#### 查询所有子孙
``` java
SET @lft := 9;
SET @rgt := 18;
SELECT * FROM department WHERE lft BETWEEN @lft AND @rgt ORDER BY lft ASC;
// `SELECT user_name FROM temp_pro_user WHERE lft >= @lft AND lft <= @rgt ORDER BY lft ASC`;
/*例子中用BETWEEN将被查部门本身也查了出来。实际中可以用大于小于*/
```
#### 查询下属总数
``` java
// 总数 = (右值 - 左值 - 1) / 2
```
#### 查询一级下属
``` java
SET @level := 2;/*总经理的level*/
SET @lft := 2;/*总经理的左值*/
SET @rgt := 19;/*总经理的右值*/

SELECT * FROM department WHERE lft > @lft AND rgt < @rgt AND level = @level+1;
```
#### 查询祖链路径
``` java
SET @lft := 3;/*产品部左值*/
SET @rgt := 8;/*产品部右值*/

SELECT * FROM department WHERE lft < @lft AND rgt > @rgt ORDER BY lft ASC;
```
#### 查询后转成树结构
``` javascript
let list = [//模拟sql查出来的列表。
    {id:1,name:'root',lft:1,rgt:8,level:1},
    {id:2,name:'child',lft:2,rgt:7,level:2},
    {id:3,name:'grandson',lft:3,rgt:4,level:3},
    {id:4,name:'grandson2',lft:5,rgt:6,level:3}
];
let rights = [] /*类似于一个栈结构（后进先出）*/
let mp = {}
//list.sort((a,b) => a.lft - b.lft)//如果你在sql中没有进行排序，需要在这里给他排序。
list.forEach(item => {
    if(rights.length > 0) {
        while(rights[rights.length-1] < item.rgt) {
            rights.splice(-1, 1)//从rights末尾去除
        }
    }
    let _level = rights.length;
    item._level = _level;
    mp[_level] = item.id
    item.parent_id = _level - 1 in mp ? mp[_level - 1] : null;//计算出上级部门编号
    item.is_leaf = item.lft === item.rgt - 1;//判断是否叶子部门
    rights.push(item.rgt)
})

/*上级部门计算出来了，和存parent_id的效果就一样了，后面只需要递归即可*/
/*递归函数 示例*/
let recursive = (_list, parent_id = null) => {
    let _tree = [];
    _list.forEach(item => {
        if(item.parent_id === parent_id) {
            let childs = recursive(_list, item.id)
            _tree.push({
                ...item,
                children: childs.length > 0 ? childs : (item.isLeaf ? null : [])
            })
        }
    })
    return _tree
}
console.log(recursive(list))
```

## 路径值（1/2/3/4/5/6/...）
    查询难度低，需要拼接path内容，存储基本不会出现并发冲突

### 查询
```java
// +----+----------+------------+
// | id | userName | path       |
// +----+----------+------------+
// |  1 | Admin    | 0          |
// |  2 | Ron      | 0/1        |
// |  3 | Ron0     | 0/1/2      |
// |  4 | Bio      | 0/1        |
// |  5 | Ron1     | 0/1/2      |
// |  6 | Bio0     | 0/1/4      |
// |  7 | Bio00    | 0/1/4/6    |
// |  8 | Bio000   | 0/1/4/6/7  |
// +----+----------+------------+
// 查询所有子结构内容(当前id: 2)
const @userPath = user.path + '/' + user.id + '%'
SELECT name FROM TABLENAME WHERE path like @userPath

// 查询所有父结构内容(当前id: 8)
const @pathArr = user.path.split('/')
SELECT name FROM TABLENAME WHERE id IN @pathArr
```
### 查询后转树状
```javascript
/*递归函数 示例*/
let recursive = (_list, path = 0) => {
    let _tree = [];
    _list.forEach(item => {
        if(item.path === path) {
            let childs = recursive(_list, (path + '/' + item.id))
            _tree.push({
                ...item,
                children: childs.length > 0 ? childs : []
            })
        }
    })
    return _tree
}
console.log(recursive(list))
```