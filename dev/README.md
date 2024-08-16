## 信创版-开发备忘录

### 2024-08-16

- 大家删一下本地maven仓库中的hos-app-config的包，昨天hos修改了bug

- 因为`高斯`数据库不支持~~datetime~~数据类型，所以在导入时崔工统一修改成了date，但是测试发现date转LocalDatetime会报这个错，提个脚本将数据类型转成timestamp就可以解决这个问题

  ```cmd
  cannot convert the column of type DATE to requested type java.time.LocalDateTime.
  ```

### 2024-08-14

- 因为`高斯`数据库中没有current_date()函数，所以要检查sql语句中是否使用了~~current_date()~~函数，如果有都修改成current_date

  ```sql
  where end_date>=current_date or end_date is null
  ```

  

- current_date变量取得`当天日期`，`时分秒`都为0，now()是取的当前的时间，精确到毫秒，按实际场景选择使用

### 2024-08-13

- 因为`高斯`数据库中~~日期字段名=''~~写法不被允许，需要删除相关代码

  ```sql
  start_date is null or start_date='' or start_date>=current_date
  ```

  删除~~start_date=''~~，修改成

  ```sql
  start_date is null or start_date>=current_date
  ```

### 2024-07-08

- 引入hisui方式支持

  ```html
  <script type="text/javascript" src="../../../base/scripts/hisui.js"></script>
  ```

  实际引入了所有hisui相关js及css，也引入了websys.jquery.bsp.js

### 2024-06-29

- 润乾打印支持api打印功能可进入`http://106.63.4.7:8000/his-mediway-java/his-document/-/tree/master/润乾打印`界面查看说明