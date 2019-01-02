- 配合tab使用swiper时遇到的bug
 1. swiper的动画在tab切换的时候不能重复定义
 2. 切换tab的时候，切到另一个在切回来的时候会导致swiper停止，

 - 原因：
  - 可能是重复创建了一个相同的swiper对象导致的报错

 - 解决方案：
  - 每次切换tab是消除以前创建的swiper对象

  html
  ```
  <ul class='option clearfix'>
                        <li rel="tab-1" class="selectActive"><a href="javascript:;">企业新闻</a></li>
                        <li rel="tab-2"><a href="javascript:;">行业资讯</a></li>
                        <li rel="tab-3"><a href="javascript:;">媒体报道</a></li>
                    </ul>
  ``` 

  ```
  <div rel='tab-1>
    <div class="news_banner0 swiper-container>
  ....
  </div>
    </div>

    <div rel='tab-2>
    <div class="news_banner1 swiper-container>
  ....
  </div>
    </div>

    <div rel='tab-3>
    <div class="news_banner2 swiper-container>
  ....
  </div>
    </div>
  ```

  js
  ```
  //创建新闻轮播图
function createSwiper(index) {
    var swiper = new Swiper('.news_banner' + index, {
        autoplay: {
            delay: 3000,
            disableOnInteraction: false,
        },
        loop: true,
        // 如果需要分页器
        pagination: {
            el: '.swiper-pagination',
            clickable: true,
        }
        ,
        observer: true,
        observeParents: true,
        on: {
            init: function () {
                swiperAnimateCache(this); //隐藏动画元素
                swiperAnimate(this); //初始化完成开始动画
            }
            ,
            slideChangeTransitionEnd: function () {
                swiperAnimate(this); //每个slide切换结束时也运行当前slide动画
            }
        }
    });
    return swiper
}
//切换tab重置swiper
$(function () {
    const tIndex = $('.option .selectActive').index();
    var swiper1, swiper2, swiper3;
    swiper1 = createSwiper(tIndex)
    $('.option li').click(function () {
        const tIndex = $(this).index();
        if (tIndex === 0) {
            if (swiper2 !== undefined || swiper3 !== undefined) {
                swiper2.destroy(false) && swiper3.destroy(false);
            }
            swiper1=createSwiper(tIndex)
        }else if(tIndex ===1){
            if(swiper1!==undefined || swiper3!==undefined){
                swiper1.destroy(false) && swiper3.destroy(false);
            }
            swiper2=createSwiper(tIndex)
        }else{
            if(swiper1!==undefined || swiper2!==undefined){
                swiper1.destroy(false) && swiper2.destroy(false);
            }
            swiper3=createSwiper(tIndex)
        }
    })
});
  ```
