# 163-music
### 启动应用：
1. npm i -g http-server
2. 本地开启 http-server -c-1
3. 本地起一个后台服务器 node server 8888（地址为http://localhost:8888/uptoken）
4. 打开 http://127.0.0.1:8080/src/admin.html

### 获取外链
encodeURI：认为要转译的内容是一个完整的网址

encodeURIComponent：想要将转译内容作为网址的一部分或者是参数的情况

### 渲染song-list时的小麻烦
由于没有vue的v-for语法，在渲染song-list时需要自己添加li元素
```
template: `
    <ul class="songList">
    </ul>
`,
render(data) {
    let $el = $(this.el)
    $el.html(this.template)
    let {songs} = data
    let liList = songs.map((song)=> $('<li></li>').text(song.name))
    $el.find('ul').empty()
    liList.map((domLi)=>{
        $el.find('ul').append(domLi)
    })
}
```

如果使用 v-for 指令，那么就可以直接根据一组数组的选项列表进行渲染
```
template: `
    <ul class="songList">
        <li v-for="song in songs"></li>
    </ul>
`
```
### 深拷贝
在某一个模块操作数据库的时候，如果没有深拷贝，他对数据库的更改很可能会影响到另一个模块对数据库的使用，在song-form模块下对歌曲数据使用深拷贝：JSON.parse(JSON.stringify(object))，先将这个对象变成字符串，再从字符串里生成一个新对象，在这个项目中的具体实现：
```
let string = JSON.stringify(this.model.data)
let object = JSON.parse(string)
```
### 上传加锁
在上传歌曲时，页面处于灰色加载状态，为了避免用户反复点击上传歌曲的按钮，有两个常用方法：

1、用一个div遮住整个屏幕，在这个项目中给类名为 site-loading 的div元素加上 z-index: 1;

2、当这个div不能挡住用户点击的操作（给类名为 site-loading的元素加上 pointer-events: none;），此时当页面处于加载状态时，用户仍然可以点击歌曲上传的按钮。

解决方法：创建一个变量锁定当前页面正在上传的状态，当这个状态结束，这个锁变为打开状态，用户又可以点击上传按钮

eg：
在upload-song模块
```
let model = {
    data: {
        status: 'open'  // 创建一个变量来切换状态
    }
}

let controller = {
    ... ...
    // 在歌曲上传之前的函数里
    if(this.model.data.status === 'closed'){
        return false
    }else{
        this.model.data.status = 'closed'
        return true
    }

    // 在歌曲上传成功之后的函数里
    this.model.data.status = 'open'
}
```