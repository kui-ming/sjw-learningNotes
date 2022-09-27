# Vue+elmentUi 图片上传、分页组件及路由跳转传参问题

1. 图片上传

   

2. 分页

   [element-ui 官方文档](https://element.eleme.io/#/zh-CN/component/pagination)

   Q：分页的使用

   A:

   ​		首先要把它引用到项目中，然后再vue模板区使用`<el-pagination>`标签来使用分页组件，具体方法看官方文档。在我看来，分页组件展示价值大于应用，应为我无法用它管理我的数据。它主要通过三个整数来展示出分页的效果，分别是`current-page(当前页数)`、`page-size(一页数量)`和`total(数据总数)`。

   ```vue
   <template>
   	<!---分页组件-->
   	<el-pagination
             background
             layout="prev, pager, next"
             :page-size.sync="page.size"
             :total="page.total"
             @current-change="pageChange"
             :current-page.sync="page.current"
             v-if="page.current"
             >
   	</el-pagination>
   </template>
   ```

   > 在上方代码中page-size和current-page都可以通过.sync进行双向绑定

   

   Q：难用之处

   A:

   ​		我想让它在路由间来回跳转时能够保持它的当前页不变，如在第2页点击编辑按钮跳转到B路由，编辑完成后回到原路由时应该还保持2页。为了实现它，我使用了路由`$route`的`params`参数——跳转B路由时将当前页信息和编辑信息都通过`params`参数传递过去，在B路由中通过方法跳转回来时，将`params`中的当前页信息带回来，然后由原路由的生命周期函数`mounted`处理，如果`params`参数中存在正确的当前页信息，则将它赋给分页组件的`current-page`绑定的参数。问题来了，按我的想法，我使用`.sync`对当前页进行了双向绑定，那分页显示应该是第二页，可参数已经为2。

   ​		经过百般调试，得出结果：当分页组件的`total`值及`page-size`为0时修改`current-page.sync`的绑定值是无效的，因为0没有第二页，并且会导致分页显示不同步的现象，组件展示为1，后台参数为2，就算之后给`total`赋值后再修改当前页，还是会出现界面与数据不同步，直到手动点击分页组件调用回调方法后才会正常。

   ​		解决办法：如果不确定`total`值就赋值为999，因为999条信息肯定有第二页。

   

3. 路由间的传值与跳转