# 阿里easyExcel简单用法

- 说明
  该包会和poi的导出有冲突，使用的时候需要确认是否有poi包，如果有不建议使用
- 引入依赖

    ```xml

     <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>easyexcel</artifactId>
            <version>3.0.5</version>
    </dependency>
    ```

- 代码当中使用

    1. 简单的从文件流导入示例

         ```java
         // 从文件流中导入
         EasyExcel.read(file.getInputStream(), ImportUserDto.class,  new ImportUserDataListener()).sheet().doRead();
         // 从本地文件中导入
         EasyExcel.read(FileUtil.readProjectHomeFile("file", fileName), ImportUserDto.class, new ImportUserDataListener()).sheet().headRowNumber(2).doRead();
         //其中new ImportUserDataListener 是监听器，读取excel中的内容后会到监听里面处理，这个是异步的方法同步的具体看官网文档
          /**
        * @ClassName ImportUserDataListener
        * @Description 用户导入监听类
        * @Author youzi
        * @Date 2022/3/23 1:20 PM
        * @Version 1.0
        **/
       @Slf4j
       public class ImportUserDataListener implements ReadListener<ImportUserDto> {
           final TUserService tUserService = SpringUtil.getBean(TUserService.class);

           private List<AddUserResult> addUserResultList = new ArrayList<>();


           @Override
           public void onException(Exception exception, AnalysisContext context) throws Exception {
               ReadListener.super.onException(exception, context);
           }

           @SneakyThrows
           @Override
           public void invoke(ImportUserDto importUserDto, AnalysisContext analysisContext) {
               if (StringUtils.isNotBlank(importUserDto.getUserName())) {
                   log.info("获取正常的数据：{}", importUserDto);
                   addUserResultList.add(tUserService.saveUser(importUserDto));
               }


           }

           @Override
           public void doAfterAllAnalysed(AnalysisContext analysisContext) {
               if (addUserResultList.size()>0){
                   String fileName = FileUtil.getBasePath() + "xxxxx文件" + new DateTime().toString("yyyy-MM-dd HHmmss") + ".xlsx";
                   // 这里 需要指定写用哪个class去写，然后写到第一个sheet，名字为模板 然后文件流会自动关闭
                   // 如果这里想使用03 则 传入excelType参数即可
                   EasyExcel.write(fileName, AddUserResult.class)
                           .sheet("结果")
                           .doWrite(() -> {
                               // 分页查询数据
                               return addUserResultList;
                           });
               }
           }
       }
        ```

    2. 导出文件

        ```java
        // AddUserResult 导出的实体类，循环的是实体类的list
         EasyExcel.write(fileName, AddUserResult.class)
                  .sheet("结果")
                  .doWrite(() -> {
                      // 分页查询数据
                      return addUserResultList;
                  });
        ```

    3. 实体类模板

        ```java
        @Getter
        @Setter
        @EqualsAndHashCode
        @ToString
        public class ImportUserDto {

            /**
            * 排序号
            */
            @ExcelProperty("排序号")
            private String sortNum;


            @ExcelProperty("姓名")
            private String userName;

            @ExcelProperty("分支")
            private String orgPath;

            @ExcelProperty("手机号码")
            private String phone;

            @ExcelProperty("性别")
            private String gender;


            @ExcelProperty("职务")
            private String post;

            @ExcelProperty("职级")
            private String rank;


            @ExcelProperty("是否开通邮箱")
            private String isEmail;

            @ExcelProperty("是否管理员")
            private String isAdmin;

            @ExcelProperty("身份证")
            private String idCard;

            /**
            * 籍贯
            */
            @ExcelProperty("籍贯")
            private String nativePlace;

            /**
            * 民族
            */
            @ExcelProperty("民族")
            private String nation;

            /**
            * 出生日期
            */
            @ExcelProperty("出生日期")
            private String dateBirth;
            /**
            * 发送短信状态
            */
            @ExcelProperty("发送短信状态")
            private String msgStatus;

        }

        ```

- 官方文档和github地址

    <https://www.yuque.com/easyexcel/doc/easyexcel>
    <https://github.com/alibaba/easyexcel>