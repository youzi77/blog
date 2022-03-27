### 取list<Bean>中某一属性拼接成按，分割字符串

```java
StringUtils.join(assessmentTaskUserList.stream().map(AssessmentTaskUser::getUserUuid).collect(Collectors.toList()), ',')
```



