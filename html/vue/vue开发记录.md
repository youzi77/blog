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