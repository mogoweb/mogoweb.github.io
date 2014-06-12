---
layout: page
title: "测试页面"
date: 2014-06-12 09:44
comments: true
sharing: true
footer: true
---

<script language="javascript">
function onJsAlert() {
    window.alert("onJsAlert");
}

function onJsConfirm() {
    var res = window.confirm("onJsConfirm");
    window.alert(res);
}

function onJsPrompt() {
    var res = window.prompt("onJsPrompt", "onJsPrompt");
    window.alert(res);
}

</script>

### Js test

<li><a href="javascript:onJsAlert()">Click me: onJsAlert</a></li>
<li><a href="javascript:onJsConfirm()">Click me: onJsConfirm</a></li>
<li><a href="javascript:onJsPrompt()">Click me: onJsPrompt</a></li>

