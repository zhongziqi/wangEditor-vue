# wangEditor-vue
### 基于大家的需要，特将代码整理出来，还有问题的话，可以一起加我的qq一起探讨（383583157）
```
<template>
		<div id="editor" >
			<div v-html='box.environmental_certification'></div>
		</div>
</template>
<script>
	import wangeditor  from 'wangeditor';
	export default{
		data(){
			return{
				editor:'',

            }
		},
		mounted:function(){
			// 获取七牛
			this.get_qiniu();
		},
		methods:{
			upload_imgs:function(){
				this.editor = new wangeditor('#editor');
				this.editor.customConfig.menus = [
					'head',  // 标题
					'bold',  // 粗体
					'italic',  // 斜体
					'underline',  // 下划线
					'strikeThrough',  // 删除线
					'foreColor',  // 文字颜色
					'backColor',  // 背景颜色
					'link',  // 插入链接
					'list',  // 列表
					'justify',  // 对齐方式
					'quote',  // 引用
					// 'emoticon',  // 表情
					'image',  // 插入图片
					'table',  // 表格
					'video',  // 插入视频
					// 'code',  // 插入代码
					'undo',  // 撤销
					'redo'  // 重复
				]
				// 这里用的是七牛华南的服务器
				this.editor.customConfig.uploadImgServer = 'http://up-z0.qiniu.com'
				// 这个地方是禁止网络上传tab
				this.editor.customConfig.showLinkImg = false;
				// 这个地方是显示上传本地图片的tab
				this.editor.customConfig.uploadFileName = 'file'
				// 这里显示的是用户标识token
				this.editor.customConfig.uploadImgParams = { token: this.qiniu_token }
				var that = this;
				this.editor.customConfig.uploadImgHooks = {
					before: function (xhr, editor, files) {
						 // 在这里你可以写一些loading什么之类的
						console.log('before',xhr,editor,files);
					},
				 	success: function (xhr, editor, result) {
						 // 在这里你可以写一些上传成功的提示
					},
					fail: function (xhr, editor, result) {
						 // 这里可以写一些上传错误的提示
				 	},
					error: function (xhr, editor) {
						// 图片上传出错时触发
					},
					timeout: function (xhr, editor) {
						 // 图片上传超时
					},
					customInsert: function (insertImg, result, editor) {
						// 这个hash就是我前面说的key值啦
						var url = that.img_url+result.hash;
						insertImg(url)
						console.log('customInsert',insertImg,result,editor);
						// result 必须是一个 JSON 格式字符串！！！否则报错
					}
			 	}
				this.editor.create();
			},
			get_qiniu:function(){
              var that = this;
              $.ajax({
                type : "GET",
                url :"这里写你的请求链接",
                success : function(result) {
                  var res = JSON.parse(result);
                  if(res.state=='0001'){
                  	  // 这个是后台返回给我的链接到时候我上传到七牛会的得到一个Key,这个链接的作用就是这个
                  		that.img_url=res.result.BucketDomain;
                       // 因为我这边的业务逻辑是需要区分用户的所以需要需要记录后台的token
                       that.qiniu_token=res.result.token;
                      //因为一些奇葩操作导致重新实例（导致在编辑器里面出现一个编辑器，很恶心啊）所以当检测之前的那个实例
					   if(that.editor!=''){that.editor.destory();}
					    // 这里初始化上传图片那个功能
	                   that.upload_imgs();
                 }else{
                    console.log('unknown problems');
                 }

            }
              })
			}
		}
	}
</script>
<style scoped>
</style>
```