---
title: 留言板
date: 2020-01-09 20:37:33
keywords: 留言板
comment: true
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.9.6/comments.jpg
---

{% raw %}
<div class="entry-content">
  <div class="hitokoto-wrap">
    <div class="hitokoto-border hitokoto-left">
    </div>
    <div class="hitokoto-border hitokoto-right">
    </div>
    <h1><a href="https://hitokoto.cn/" class="entry-content" style="background:none!important">一言Hitokoto</a></h1>
    <p id="hitokoto">Loading...</p>
    <p id="from"></p>
  </div>
</div>
<script>
update();
function update() {
    gethi = new XMLHttpRequest();
    gethi.open("GET", "https://v1.hitokoto.cn/?c=a");
    gethi.send();
    gethi.onreadystatechange = function () {
        if (gethi.readyState === 4 && gethi.status === 200) {
            var Hi = JSON.parse(gethi.responseText);
            document.getElementById('hitokoto').innerHTML = "『 "+Hi.hitokoto+" 』";
            document.getElementById('from').innerHTML = "——<b>" + Hi.from + "</b>";
            document.getElementById('from').style["margin-bottom"]="15px";
        }
    }
}
</script>
{% endraw %}

无论是对这个blog的改进意见，还是对博主垃圾水平的嘲讽，都欢迎你在下方留言吐槽<img class="emoji-coda" src="https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.9.4/emojis/menhera/2.jpg" style="height:80px;width:auto;vertical-align:middle;top:-15px">

（之前一直把「关于」当留言板用，原先的评论都搬过来了qwq）

---
