# 代码坏味道示例

## 1. 过长的参数列表

### 1.1 坏味道示例
```
private DataTable getSentInfo(string Pno, string Pname, string Psytle, string SentTime,string DealerNo,string DealerName) {
    string sqlStr = "select convert(decimal(18,2),round(sum(sc.Price*srd.SentNum ),2))as countPrice,sum(srd.SentNum) as num "
        + "from  LB_Sent_Rec sr inner join LB_Sent_RecDetail srd "
        + "on sr.SentID=srd.SentID inner join LB_Sale_Rec sc "
        + "on sc.SaleID=srd.SaleID where sc.cid='" + cidH.Value + "' and sc.Pno='" + Pno + "' and sc.Pname='" + Pname + "' and sc.PStyle='" + Psytle + "' "
        + "and sc.DealerNo='" + DealerNo + "' and sc.DealerName='" + DealerName + "' "
        + "and sr.SentTime between '" + SentTime + " 00:00:00' and '" + SentTime + " 23:59:59'";
    return DbHelperSQL.GetDataTable(sqlStr);
}
```

### 1.2 坏味道修复
```
public class Product
{
    public Product()
    {
        //
        //TODO: 在此处添加构造函数逻辑
        //
    }

    public string Pno { get; set; }
    public string Pnamr { get; set; }
    public string Psytle { get; set; }
    public string SentTime { get; set; }
    public string DealerNo{get;set;}
    public string DealerName { get; set; }
}

private DataTable getSentInfo(Product pr) {
    string  sqlStr = "select convert(decimal(18,2),round(sum(sc.Price*srd.SentNum ),2))as countPrice,sum(srd.SentNum) as num "
        + "from  LB_Sent_Rec sr inner join LB_Sent_RecDetail srd "
        + "on sr.SentID=srd.SentID inner join LB_Sale_Rec sc "
        + "on sc.SaleID=srd.SaleID where sc.cid='" + cidH.Value + "' and sc.Pno='" + pr.Pno + "' and sc.Pname='" + pr.Pnamr + "' and sc.PStyle='" + pr.Psytle + "' "
        + "and sc.DealerNo='" + pr.DealerNo + "' and sc.DealerName='" + pr.DealerName + "' "
        + "and sr.SentTime between '" + pr.SentTime + " 00:00:00' and '" + pr.SentTime + " 23:59:59'";
    return DbHelperSQL.GetDataTable(sqlStr);
}
```

## 2. 发散式变化

### 2.1 坏味道示例
下面的例子展现了一个问题，每次当要判断一个用户是否有某个action的权限操作时，都会要涉及到User类的改动;当要改动用户信息的操作时，也会涉及到User类的改动。发散式变化的坏味道有一个典型的特征是引起一个类发生变化的原因有多种类型。这个类一般都违背了单一职责原则。
```
class User
    def full-name
        "#{first-name} #{last-name}"
    end

    def authorized?(action)
        // logic to judge whether the user has the permission to do this access the action
    end
end
```
### 2.2 坏味道修复
```
class User
    def full-name
        "#{first-name} #{last-name}"
    end

    def is-admin?
        // logic to judge the user role
    end
end

class ActionAuthorize
    def authorized?(action, user)
        return user.is-admin?
    end
end
```

## 3. 散弹式修改
下面的例子中展现了一个问题，当想要给每个方法做日志记录的时候，需要在好多处增加日志打印的逻辑。这个问题看似正好和发散式变化相反，其实是不矛盾的。他们描述的其实是两种完全不同的代码坏味道。一般散弹式的修改可以通过面向切面编程或者元编程来修复。
### 3.1 坏味道示例
```
void MyFunc()
{
	...
}

void MyFunc2()
{
	...
}

...

void MyFuncN()
{
	...
}
```

```
void MyFunc()
{
	printf("Entering MyFunc()\n");
	...
}

void MyFunc2()
{
	printf("Entering MyFunc2()\n");
	...
}

...

void MyFuncN()
{
	printf("Entering MyFuncN()\n");
	...
}
```
### 3.2 坏味道修复
```
void MyFunc()
{
	...
}

void MyFunc2()
{
	...
}

...

void MyFuncN()
{
	...
}
```

```
class FunctionLogger{
    beforeFunctionIsExcuted( functionName ) {
        printf("Entering" + functionName + "\n");
    }
}
```

## 4. 过度耦合的消息链

[消息链坏味道示例视频](http://player.youku.com/embed/XNDAyNTYxNjkxNg==)