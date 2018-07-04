#Generator函数与状态机

最近在看《react小书》复习react，开头的一个例子是实现最简单的一个点赞按钮，在两种状态中切换：

```html
<body>
    <div class='wrapper'>
      <button class='like-btn'>
        <span class='like-text'>点赞</span>
      </button>
    </div>
</body>
```

```javascript
const button = document.querySelector('.like-btn')
const buttonText = button.querySelector('.like-text')
let isLiked = false
button.addEventListener('click', () => {
    isLiked = !isLiked
    if (isLiked) {
      buttonText.innerHTML = '取消'
    } else {
      buttonText.innerHTML = '点赞'
    }
}, false)
```

突然想到阮一峰对于Generator函数的阐述：http://es6.ruanyifeng.com/#docs/generator#%E5%90%AB%E4%B9%89 
于是尝试用Generator改写：

```JavaScript
const btn = document.querySelector('.like-btn');
const btnText = document.querySelector('.like-text');
const clickHandler = (function* (){//这里立即执行，返回了遍历器
    while (true) {
        btnText.innerHTML = '取消';
        yield;
        btnText.innerHTML = '点赞';
        yield;
    }
})();
btn.addEventListener('click', ()=>{clickHandler.next()})
```


期间遇到了两个问题：
第一，箭头函数不能用作generator函数（脸红[捂脸]这个是之前豹哥说过的）；
第二，generator函数返回的遍历器对象的next方法，不能直接作为事件处理函数，也就是说，这样会报错`btn.addEventListener('click', clickHandler.next)`

```
Firefox：TypeError: next method called on incompatible HTMLButtonElement 

Chrome：Uncaught TypeError: Method [Generator].prototype.next called on incompatible receiver #<HTMLButtonElement>
    at HTMLButtonElement.next (<anonymous>)
```

继续探究一下，其实是this指向的问题。虽然说，但凡一个函数，this就不应该忽略，但是next方法时怎么和this挂上的，还是想不明白，大概是和内部实现有关系？
总之知道这一点，解决方法就有了：

```javascript
btn.addEventListener('click', clickHandler.next.bind(clickHandler))
btn.addEventListener('click', ()=>{clickHandler.next()})
```

最后，还要注意遍历器函数内，必须保证是个死循环，才能使得生成的遍历器对象可以无限调用next()。