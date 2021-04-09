# 1.el-table 高度动态变化
```
<el-table  :height="tableHeight">
//data数据中增加
tableHeight: window.innerHeight-x
//增加监听方法
mounted() {
    window.onresize = () => {
    return (() => {
        this.tableHeight = window.innerHeight-280;
    })();
    };
}

```