<p align="center"><a href="#" target="_blank"><img width="100" src="/resources/logo.svg"></a></p>
<h1>BloggerJS</h1>

**BloggerJS** es un script para modificar el formato de las URL en un blog de Blogger. Creando visualmente una mejor navegación.<br/><br/>
El script "limpia" la URL, eliminando de ella la fecha ```"/YYYY/MM"``` o el ```"/p"```, según sea el caso, así como también el ```".html"```. De esta manera resulta más cómodo compartir una URL, pues luce mucho mejor.<br/>
<p><img src="/resources/url_demo.png"></p>

## Implementación

Para implementar **BloggerJS** en tu blog, copia todo el siguiente código:
```javascript
<script type="text/javascript">
// BloggerJS v0.3.0
// Copyright (c) 2017-2018 Kenny Cruz
// Licensed under the MIT License

// Configuration -----------
var postsDatePrefix = false;
var accessOnly = false;

var useApiV3 = false;
var apiKey = "";
var blogId = "";
// -------------------------
var postsOrPages=["posts","pages"],urlTotal,jsonIndex=1,secondRequest=!0,feedPriority=0,amp="&amp;"[0],nextPageToken;function urlVal(){var url=window.location.pathname;var length=url.length;var urlEnd=url.substring(length-5);if(urlEnd===".html")return 0;else if(length>1)return 1;else return 2}
function urlMod(){var url=window.location.pathname;if(url.substring(1,2)==="p"){url=url.substring(url.indexOf("/",1)+1);url=url.substr(0,url.indexOf(".html"));history.replaceState(null,null,"../"+url)}
else{if(!postsDatePrefix)url=url.substring(url.indexOf("/",7)+1);else url=url.substring(1);url=url.substr(0,url.indexOf(".html"));history.replaceState(null,null,"../../"+url)}}
function urlSearch(url,database){var pathname=url+".html";database.forEach(function(element){var search=element.search(pathname);if(search!==-1)window.location=element})}
function urlManager(){var validation=urlVal();if(validation===0){if(!accessOnly)urlMod()}
else if(validation===1){if(!postsDatePrefix)getJSON(postsOrPages[feedPriority],1);else getJSON("posts",1)}
else if(validation===2){if(!accessOnly)history.replaceState(null,null,"/")}}
function getJSON(postsOrPages,index){var script=document.createElement('script');if(useApiV3){var jsonUrl="https://www.googleapis.com/blogger/v3/blogs/"+blogId+"/"+postsOrPages+"?key="+apiKey+"#maxResults=500#fields=nextPageToken%2Citems(url)#callback=bloggerJSON";if(nextPageToken)jsonUrl+="#pageToken="+nextPageToken;nextPageToken=undefined}
else var jsonUrl=window.location.protocol+"//"+window.location.hostname+"/feeds/"+postsOrPages+"/default?start-index="+index+"#max-results=150#orderby=published#alt=json-in-script#callback=bloggerJSON";jsonUrl=jsonUrl.replace(/#/g,amp);script.type='text/javascript';script.src=jsonUrl;document.getElementsByTagName('head')[0].appendChild(script)}
function bloggerJSON(json){var database=[];if(!useApiV3)if(urlTotal===undefined)urlTotal=parseInt(json.feed.openSearch$totalResults.$t);if(!useApiV3){json.feed.entry.forEach(function(element,index){var entry=json.feed.entry[index];entry.link.forEach(function(element,index){if(entry.link[index].rel==="alternate")database.push(entry.link[index].href)})})}
else{json.items.forEach(function(element,index){database.push(element.url)});nextPageToken=json.nextPageToken}
urlSearch(window.location.pathname,database);if(urlTotal>150){jsonIndex+=150;urlTotal-=150;getJSON(postsOrPages[feedPriority],jsonIndex)}
else if(nextPageToken){getJSON(postsOrPages[feedPriority])}
else if(secondRequest){nextPageToken=undefined;urlTotal=undefined;jsonIndex=1;secondRequest=!1;if(feedPriority===0){feedPriority=1;getJSON("pages",1)}
else if(feedPriority===1){feedPriority=0;getJSON("posts",1)}}}
function bloggerJS(priority){if(priority)feedPriority=priority;urlManager()}
bloggerJS();
</script>
```

Ya que copiaste completamente el código anterior, dirígete al código HTML de tu plantilla y busca la etiqueta ```<head>```, pega el código justo debajo de ella:

```html
<head>
<!-- Aquí va el código -->
...
```
Una vez hecho esto, sólo guarda los cambios hechos a tu plantilla. Después de ello, **BloggerJS** estará funcionando.

## Licencia
Licensed under the [MIT License](./LICENSE).<br/>
Copyright (c) 2017-2018 [Kenny Cruz](https://github.com/jokenox).
