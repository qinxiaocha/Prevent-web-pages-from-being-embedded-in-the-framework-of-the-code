# Prevent-web-pages-from-being-embedded-in-the-framework-of-the-code
两年前，我写过一段代码，防止网页被嵌入框架（Frame）。
<script type="text/javascript">
　　if (window!=top) // 判断当前的window对象是否是top对象
　　top.location.href = window.location.href; // 如果不是，将top对象的网址自动导向被嵌入网页的网址
</script>
这段代码是有效的。但是，有一个问题：使用后，任何人都无法再把你的网页嵌入框架了，包括你自己在内。
于是，我今天就在考虑，有没有一种方法，使得我的网页只能被嵌入我自己的框架，而不是别人的框架？
表面上看，这个问题很简单。只要做一个判断：当前框架和顶层框架的域名是否相同，如果答案是否，就做了一个URL重定向。
if (top.location.hostname != window.location.hostname) {
　　top.location.href = window.location.href;
}
但是，出乎意料的是，这样写是错误的，根本无法运行。你能看出，错在哪里吗？
假定 top.location.hostname 是 www.111.com，而 window.location.hostname 是 www.222.com。也就是说，111.com把222.com嵌入了它的网页中。这时，比较 top.location.hostname != window.location.hostname 会有什么结果？
浏览器会提示代码出错！
因为它们跨域（cross-domain）了，浏览器的安全政策不允许222.com的网页操作111.com的网页，反之亦然。IE把这种错误叫做"没有权限"。进一步说，浏览器甚至不允许你查看top.location.hostname，跨域情况下，一看到这个对象，就直接报错。
那么，代码应该如何修改？
事实上，这提示我们，只要查看top.location.hostname是否报错就可以了。如果报错了，表明存在跨域，就对top对象进行URL重导向；如果不报错，表明不存在跨域（或者未使用框架），就不采取操作。
try{
　　top.location.hostname;
}
catch(e){
　　top.location.href = window.location.href;
}
这样写已经正确了，在IE和Firefox中可以正确运行。但是，Chrome浏览器会出现错误，不知为何，在跨域情况下，Chrome对top.location.hostname不报错！
没办法，只能为了Chrome，再加一段补充代码。
try{
　　top.location.hostname;
　　if (top.location.hostname != window.location.hostname) {
　　　　top.location.href =window.location.href;
　　}
}
catch(e){
　　top.location.href = window.location.href;
}
好了，升级版代码完成。除了本地域名以外，其他域名一律无法将你的网页嵌入框架。我的Blog现在就使用这段代码。
