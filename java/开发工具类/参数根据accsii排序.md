## 参数排序加密工具类

该工具类主要是用于请求参数的排序后签名，用作一些特殊的请求需要配置做参数签名使用

```java
/**
     * 功能描述: 参数字段根据ACCSII排序
     *
     * @param characterEncoding 字符串类型
     * @param parameters        参数值
     * @return: java.lang.String
     * @author: youzi
     * @date: 2022/3/23 2:44 PM
     */
    @SneakyThrows
    public static String createAsc(String characterEncoding, SortedMap<String, Object> parameters) {
        StringBuffer sb = new StringBuffer();
        StringBuffer sbkey = new StringBuffer();
        //所有参与传参的参数按照accsii排序（升序）
        Set es = parameters.entrySet();
        Iterator it = es.iterator();
        while (it.hasNext()) {
            Map.Entry entry = (Map.Entry) it.next();
            String k = (String) entry.getKey();
            Object v = entry.getValue();
            //空值不传递，不参与签名组串
            if (null != v && !"".equals(v)) {
                sb.append(k + "=" + v + "&");
                sbkey.append(k + "=" + v + "&");
            }
        }
        //System.out.println("字符串:"+sb.toString());
        sbkey.deleteCharAt(sb.length() - 1);
//        sbkey=sbkey.append(key);
        log.info("打印排序后的字符串字符串:{}", new 		    			                      String(sbkey.toString().getBytes(),characterEncoding));
        return new String(sbkey.toString().getBytes(),characterEncoding);
    }
```

