# 使用Vue实现拖拽文件上传时无法阻止默认事件触发的原因

:date: 2022年5月7日15:27

部分源代码

```vue
<template>
	<div class="input_area" 
     @drag.prevent="dragFinish" 
  	 @dragover.prevent="dragOver">
      	<input type="file" accept=".docx" placeholder="请选择文件" ref="file"/>
 	</div>
</template>

<script>
export default {
  data() {
    return {
      
    }
  },
  methods: {
    
    dragFinish(e){// 文件已经在拖动区，并松开鼠标时，触发 dragFinish 事件
      console.log(e)
    },
    dragEnter(e){//文件进入拖动区时，触发 dragEnter 事件
    },
    dragLeave(e){//文件离开拖动区时，不断触发dragleave 事件
    },
    dragOver(e){//文件被拖拽时，不断触发 dragOver 事件
      //console.log(e)
    },
  },
}
</script>
```



##### 原因

​	拖拽完成事件的单词为 `drop` ，而我错写为了 `drag` ，导致连 `dragFnish` 事件都无法触发。



##### 解决

将 `@drag.prevent` 改为 `@drop.prevent`

```vue
……

<template>
	<div class="input_area" 
     @drop.prevent="dragFinish" 
  	 @dragover.prevent="dragOver">
      	<input type="file" accept=".docx" placeholder="请选择文件" ref="file"/>
 	</div>
</template>

……
```

