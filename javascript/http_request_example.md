

### XMLHttpRequest

```javascript
function doXMLHttpRequest() {
    var xhr = new XMLHttpRequest();

    xhr.open("GET", url);
    xhr.onreadystatechange = function() {
        if(this.readyState == 4) {
            const responseJson = JSON.parse(this.responseText);
            console.log(responseJson)
        }
    }

    xhr.send("");
}
```

### AJAX

```javascript
function doAjax() {
    $.ajax({
        url 			: 'http://geon.wavus.co.kr:14062/addrgeo/administ/ctpv/list'
        , contentType   : "application/json; charset=utf-8"
        , type 			: 'get'
        , success 		: function(response){
            console.log(response.json())
        }
    })
}
```

### Fetch API

```javascript
function doFetch() {
    fetch(url)
        .then(response => response.json())
        .then(response => console.log(response))
}
```