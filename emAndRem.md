<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title></title>

    <style>
        html{font-size:100%;}
        body{font-size:1em;}
        #wrap{float:left;width:10em;background:yellowgreen;}
        #wrap h1{font-size:1.125em;line-height:1.778em;}
        #wrap p{font-size:0.75em;line-height:1.5em;}

        #wrap1{margin-left:100px;float:left;width:160px;background:yellowgreen;}
        #wrap1 h1{font-size:18px;line-height:32px;}
        #wrap1 p{font-size:12px;line-height:18px;}

        #wrap2{margin-left:100px;float:left;width:10rem;background:yellowgreen;}
        #wrap2 h1{font-size:1.125rem;line-height:2rem;}
        #wrap2 p{font-size:0.75rem;line-height:1.125rem;}


    </style>
</head>;
<body>

<p>em主要是靠font-size作为子元素与父元素的沟通桥梁，如果子元素设置了font-size，则子元素高度宽度等的em的参照物为子元素的font-size;如果没
有设置则为父元素的font-size;兼容性较好;</p>
<p>rem的参照物恒为父元素的font-size;不支持ie6-8</p>
<div id="wrap">
    <h1>hello world</h1>
    <p>hello worldhello worldhello worldhello worldhello worldhello worldhello worldhello worldhello worldhello worldhello worldhello worldhello worldhello  worldhello worldhello world</p>
</div>

<div id="wrap1">
    <h1>hello world</h1>
    <p>hello worldhello worldhello worldhello worldhello worldhello worldhello worldhello worldhello worldhello worldhello worldhello worldhello worldhello worldhello worldhello world</p>
</div>

<div id="wrap2">
    <h1>hello world</h1>
    <p>hello worldhello worldhello worldhello worldhello worldhello worldhello worldhello worldhello worldhello worldhello worldhello worldhello worldhello worldhello worldhello world</p>
</div>
</body>
</html>
