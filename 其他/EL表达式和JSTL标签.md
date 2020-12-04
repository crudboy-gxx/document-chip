
El表达式：
1.${user.name}和${user["name"]}相等，后者当要取出数据名中有特殊符号是使用，并且可用于动态取值${seesionScope.user[data]}--data事业个变量。
2.对于${username}取值，会依次在Page、Request、Session、Application范围内查找，找到第一个就会听停止，全部找不到就是null。
3.EL表达式可以操作常量、变量和隐式对象。${param}表示返回请求参数中单个字符串的值. ${paramValues}表示返回请求参数的一组值。--相当于Request.getParameter。
pageScope表示页面范围的变量.requestScope表示请求对象的变量.sessionScope表示会话范围内的变量.applicationScope表示应用范围的变量。--相当于Request.getAttribute。
4.<%@ page isELIgnored="true"%> 表示是否禁用EL语言,TRUE表示禁止.FALSE表示不禁止。
5.EL语言可显示 逻辑表达式如${true and false}结果是false，关系表达式如${5>6} 结果是false，算术表达式如 ${5+5} 结果是10。

