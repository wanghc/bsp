## 后台方法说明

### 登录信息

```java
// 获得当前登录信息 - 即会话信息
// 先从线程中获取，为空时从缓存中取，缓存中为空时从表中取信息
LoginUserInfo loginUserInfo = LoginInfoContext.getLoginUser();
loginUserInfo.setBizEventTitle = "医嘱审核";   // 来表明当前会话事件，写入数据变更表中
```

```java
// 模拟出登录信息对象
try{
    Long hospId = 1L; // 测试id为1
    LoginUserInfo loginUserInfo = new LoginUserInfo();
    loginUserInfo.setHospId(hospId);
    LoginInfoContext.mock(loginUserInfo);
    // testService.run();
}finally{
    LoginInfoContext.clear();
}
```
### 线程缓存

```java
String key = "myConfigKey"
String val = ThreadContextUtil.set(key,val);  // 写入值
String val = ThreadContextUtil.get(key);    // 获取值
```

### Redis缓存

```java
// 代码表缓存管理界面, Arg1=[规则别名],Arg2=表名对应的实现类名
HisCacheUtils.getByKey("ruleCode",CTOrgLocationPO.class,locCode);