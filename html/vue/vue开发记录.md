## vue设置点击事件防抖时间
- main.js中设置全局指令
    ```
    Vue.directive('throttle', { 
        inserted(el, binding) { 
            el.addEventListener('click', () => { 
                el.style.pointerEvents = 'none'; 
                if (!el.disabled) { 
                    setTimeout(() => { 
                        el.style.pointerEvents = 'auto'; 
                    }, binding.value || 2000); 
                } 
            }); 
        } 
    });
    ```
- vue页面中指定防抖时间  
`<p @click="search" v-throttle="10000">搜索</p>`

## vue3基本写法   


```
export default {
	setup() {
		//变量定义
		const data = reactive({
			activeName: 'second',
			tableData:[],
			currentPage: 1,
			seach:{}
		})
		const handleClick = (tab, event) => {
			console.log(tab, event);
		}
		//方法定义
		const handleSizeChange = (val) => {
			console.log(`每页 ${val} 条`);
		}
		const handleCurrentChange = (val) => {
			console.log(`当前页: ${val}`);
		}
		return {
			...toRefs(data),
			handleClick,
			handleSizeChange,
			handleCurrentChange
		}
	},
     name: 'HelloWorld',
}
</script>
```
## vue3中使用静态资源图片   
1. 引用图片
    ```
    import refresh from '/@/assets/images/refresh.png'
    ``` 

2. 在setup中注册图片  


    ```
    setup(){
            const data = reactive({
                refresh: refresh
            });

            return {...toRefs(data)}
        }
    ```
3. 页面中使用图片   
    ```
    <el-image class="icon-image" :src="refresh"></el-image>
    ```
