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