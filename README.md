1安装 本地webpack
  webpack webpack-cli -D

2 webpack-dev-server

3 html-webpack-plugin

4安装loader

5 mini-css-extract-plugin -D 抽离css插件
  
6 postcss-loader autoprefixer 添加css前缀

7 optimizw-css-assets-webpack-plugin 优化css

8babel-loader || @baber/core ||  babel/preset-env
  es6转es5

9@babel/plugin-proposal-decorators -D  js高级语法转换

10 babel/plugin-transform-runtime   @babel/runtime 转es6新语法

11 @babel/polyfill 转es7

12 eslint eslint-loader  校验js语法

13 expose-loader 暴露全局的loader

14 html-withimg-loader -D html img引入图片

15 url-loader 做一个限制，当我们的图片 小于多少k的时候 用base64来转化
     否则用file-loader产生真实的图片
     在module的rules配置
     {
        test：/\.(png|jpg|gif)$/,
        use:{
            loader:'url-loader',
            options:{
               limit:200*1024,//大于转bas64
               outputPath:'/img/',//导出文件地址
               publicPath:'http://www.baidu.com'//只给自己单独加前缀
         }
         }
      }

package.js  scripts{ //配置打包后的内容} 

loader 默认 是从右向左 从下到上

expose-loader暴露全局的loader   内联的loader
pre I前面执行的loader normal普通loader
内联liader  后置postlader

import $ from 'expose-loader?$!jquery';
暴露全局的$给window
在webpack.comfig.js的module的rules配置：
  {
      test：require.resolve('jquery'),
      use:'expose-loader?$'// use:'expose-loader?$!jquery'
  }

引入webpack ,webpack也是模块
let webpack=require('webpack');
在plugins配置模块插件
new webpack.ProvidePlugin({//在每一个模块注入$
    $ :' jquery'
});

引入第三方模块：
  1 expose-loader 暴露到window上
  2 providePlugin 给每一个人提供一个$
  3 引入不打包   externals:

webpack打包我们的图片
 1在js中创建图片来引入 new Image();
   || require();
   import logo from './logo.png'//把图片引入，返回的结果是一个新的图片地址
   file-loader默认会在内部生成一张图片 到build目录下，把生成的图片名字返回来

  2在css引入 background(url())  因为有css-loader默认可以直接引入
  3<img src='';> 
    html-withimg-loader -D html img引入图片

打包出口配置导出域名加前缀
output:{
  publicPath:'http://www.baidu.com'//全部加上，如果只想在单独加，在自己规则里面加
}


module.exports={
  mode:'development,//开发和生产模式  production
  entry:''//入口
  output:''//出口
  devServer:{//开发服务器配置}，
  plugins:[//数组 放着所有webpack插件]，
  module：{//模块
     rules:[]//规则
    }，
  optimization:{
   minimizer:[]//优化项
   },
  externals:{//外部引入，不需要打包
     jquery:'$'
  },
 
}

///////////////////////////////////////////////////
多入口
entry:{
  home:'./src/index.js',
  other:'./src/other.js',
},
output:{
   filename:'[name].[hash].js' //[name] home,other
}

1 html-webpack-plugin -D 插件

plugins:[
   new HtmlwebpackPlugin({
       template:'./inde.html',
       filename:'home.html',
        chunks:['home']//控制模块
   }),
    new HtmlwebpackPlugin({
       template:'./inde.html',
       filename:'other.html',
        chunks:['other']// 引入两个chunks:['other'，'home’]
   }),
]

源码映射，会单独生成一个sourcemap文件 出错了会标识当前的列和行  大和全
devtoll:'sourse-map',//增加映射文件 可以帮我们调试源代码
  
devtool:'eval-source-map’不会产生单独的文件，但是可以显示行和列

devtool:'cheap-module-source-map’不会产生列 但是是一个单独的映射文件，产生后可以保留起来

devtool:'cheap-module-eval-source-map’不会产生文件 集中在打包后的文件中 不会产生列

watch:true,实习build打包
watchOptios:{//监控的选项
  poll:1000,//每秒 1000次
 aggreateTimeout:500//防抖 我一直输入代码
ignored：/mode_modules/  不需要进行监控哪个文件
}

1cleanwebpackPlugin// 清除
  new CleanwebpackPlugin('./dist');

2copywebpackPlugin//复制
new CopywebpackPlugin({
  form:'doc',to:'./'
});

3bannerPlugin 内置
  new webpack.BannerPlugin('make 2019 by jw');

跨域问题
deServer:{
   //1
    proxy:{//配置代理
       '/api':'http://localhost:3000'
     }
   //  proxy:{//重写的方式，把请求代理到express服务器上
       '/api':{
         target:'http://localhost:3000',
         pathRewrite:{'/api':''}
     }
   }

  //2 我们前端只想单纯来模拟数据
   before(app){//提供的方法 钩子
     app.get('/user',(req,res)=>{
         res.json({name:'潘白人'})
     })
  }

  //3 有服务端 不用代理来处理 能不能再服务端中启动webpack 端口用服务端端口
    服务端 中间件 webpack-dev-middleware -D

resolve:{//解析第三方包common
  modules:[path.resolve('node_modules')],
   //mainFiles:[]入口文件的名字 index.js
  mainFields:['style','main'],
  //alias:{//别名 vue vue.runtime
  bootstrap:'bootstrap/dist/css/bootstrap.css'
   //省略后缀
  extensions:['.js','.css','.json','.vue']

}
}
}


定义环境变量
new webpack.DefinePlugin({//内置插件
  DEV:JSON.stringify('production'),
   FLAG:'true',
   EXXPORESSION:JSON.stringify('1+1')
})

区分不同环境：生产和开发写两套
webpack-merge -D

let {smart} =require('webpack-merge');

noParse:/jquery/,    //不去解析jquery中的依赖库

exclude:/node_module/,排除
include:path.resolve('src')，包含

momentjs时间库  

new webpack.IgnorePlugin(/\.\/locale/,/moment/); 忽略引入

library:'_dll_[name]',更改导出变量名字

new webpack.DllPlugin({//name==library
   name:'_dll_[name]', //打包清单
   path:path,resolve(__dirname,'dist','manifest.json')
})

模块happypack可以实现多线程来打包 进程
let happypack=require('happypack')
1 在js的rules配置use:'Happypack/loader?id=js'
 2在plugins:[
 new Happypack({
   id:'js',
  use:[] //配babel的loader
 })
 ]


//import 在生产环境下，会自动去除掉没用的代码
//tree-shaking 把没用到的代码删除掉

 require('./text.js')// es6模块会把结果放到default上

//scope hosting 作用域提升
let d=a+b+c //在webpack中自动省略一些可以简化的代码

多页面 抽离公共代码
optimization:{
  spliVChunksL:{//分割代码
     cacheGroups:{//缓存组
      common:{//公共模块
        chunks:'initial',
        minSize:0,
        minChunks:2
     }
   }
  }，
vendor:{
  prioroty:1,
  test:/node_modules/,   //把你抽离出来
  chunks:'initial',
  minSize:0,
  minChunks:2
   }
}

懒加载 
 @babel/plugin-syntax-dynamic-import -D 插件
   点击再加载
  //es6 草案中的语法 jsonp实现动态加载文件
   import('./source.js').then(data=>{
     console.log(data.default)
   })

热更新
1  devServer:{
    hot:true//启用热更新
 }
 new webpack.NamedModulesPlugin(),//打印更新的模块路径
new webpack.HotModuleReplacentPlugin()//热更新插件

if(module.hot){
  module.hot.accept('./source',()=>{
    let str=require('./source');
  console.log('文件更新了')
})
}


