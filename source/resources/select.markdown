---
layout: page
title: "Select Element" 
date: 2014-06-13 09:19
comments: true
sharing: true
footer: true
---

<form>
<p>
Default:
<select name="cars">
<option value="volvo">Volvo</option>
<option value="saab">Saab</option>
<option value="fiat">Fiat</option>
<option value="audi">Audi</option>
</select>
</p>

<p>
Select:
<select name="cars2">
<option value="volvo">Volvo</option>
<option value="saab">Saab</option>
<option value="fiat" selected="select">Fiat</option>
<option value="audi">Audi</option>
</select>
</p>

<p>
Group:
<select name="cars4">
<optgroup label="Swedish Cars">
<option value="volvo">Volvo</option>
<option value="saab">Saab</option>
</optgroup>
<optgroup label="German Cars">
<option value="fiat" selected="selected">Fiat</option>
<option value="audi">Audi</option>
</optgroup>
</select>
</p>

<p>
Multiple:
<select name="cars3" multiple="multiple">
<option value="volvo">Volvo</option>
<option value="saab">Saab</option>
<option value="fiat" selected="selected">Fiat</option>
<option value="audi">Audi</option>
</select>
</p>

<p>
Multiple with group:
<select name="cars5" multiple="multiple">
<optgroup label="Swedish Cars">
<option value="volvo">Volvo</option>
<option value="saab">Saab</option>
</optgroup>
<optgroup label="German Cars">
<option value="fiat" selected="selected">Fiat</option>
<option value="audi">Audi</option>
</optgroup></select>
</p>

</form>

