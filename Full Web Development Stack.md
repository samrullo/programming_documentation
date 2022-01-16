# Full Web Development Stack

## jQuery introduction

You can include jQuery CDN in your code and then you are able to use it

```html
<!doctype html>
<html lang="en">
  <head>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">

    <title>Hello, world!</title>
  </head>
  <body>
    <h1>Hello, world!</h1>

    <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>

  </body>
</html>
```



Traditional way to access DOM element and manipulate it

```javascript
document.querySelector("h1").style.color="blue"
```

jQuery way

```javascript
$("h1").css("color","blue")
```

In order for DOM element manipulations to work, we either have to put it at the end of the ```body``` tag so that we are accessing them after they rendered or use below

```javascript
$(document).ready(
	function(){
    $("h1").css("color","blue")
  }
)
```

```www.minifier.org``` minifies javascript code by removing white spaces and comments

jQuery selectors are similar to ```document.querySelector```. We can select html items with high specify.

```javascript
$("h1") // select h1 element
$("h1.title") // select h1 element with title class
$("#header h1") // select h1 element within an element with id header
```

With pure javascript we use ```document.querySelectorAll``` to select all elements

With jQuery $("...") serves both selecting single or multiple elements

```javascript
$("button") // this will select all button elements in DOM
```

## jQuery element manipulation

Using ```css`` function we can manipulate properties of an element

```javascript
$("h1").css("color","red")
$("button").css("margin","10 10")
```

You can get css property value

```javascript
$("h1").css("font-size")
```

You can add and remove classes

```javascript
$("h1").addClass("big-title")
$("h1").removeClass("big-title")
```

You can find out if an element has a certain class

```javascript
$("h1").hasClass("big-title")
```

To change element text

```javascript
$("h1").text("some new text here")
```

To change inner html of the element

```javascript
$("h1").html("<div> I am new element</div>")
```

To access element attribute

```javascript
$("img").attr("src")
$("a").attr("href")
```

To set the element's attribute

```javascript
$("img").attr("src","new/source/path")
$("a").attr("href","yahoo.com")
```

## jQuery add event listeners

To add an event listener to an element

```javascript
$("button").click(()=>{console.log("I was clicked")})
```

To add keypress event listener

```javascript
$(document).keypress((event)=>{
  console.log(event.key);
})
```

In below example we append any key pressed to the text of the element

```javascript
$(document).keypress((event)=>{
  $("h1").text($("h1").text()+event.key);
})
```

Another way to add an event listener to the element

```javascript
$("h1").on("mouseover",()=>{
  $(this).css("color","purple");
})

$("h1").on("mouseout",()=>{
  $(this).css("color","black")
})
```

## jQuery adding and removing elements

To add element before the target element on the fly

```javascript
$("h1").before("<button>button before h1</button>")
$("h1").after("<button>button after h1</button>")
```

```prepend``` adds html element inside the target element before its content, while ```append``` adds after its content

```javascript
$("h1").prepend("<button>button inside h1 before its content</button>")
$("h1").append("<button>button inside h1 after its content</button>")
```

To remove all buttons

```javascript
$("button").remove()
```

## jQuery simple animations

To hide, show, toggle element visibility

```javascript
$("button").on("click",()=>{
  $("h1").show();
})
```

```javascript
$("target").show()
$("target").hide()
$("target").toggle()
```

Hide is a sudden animation. Instead if we want our element to fade out

These only come with full version of jQuery CDNs

```javascript
$("target").fadeOut()
$("target").fadeIn()
$("target").fadeToggle()
```

To slide up, down and slide toggle

```javascript
$("#slideup").on("click", () => {
    $(".content").slideUp();
});

$("#slidedown").on("click", () => {
    $(".content").slideDown();
});

$("#slidetoggle").on("click", () => {
    $(".content").slideToggle();
});
```

To apply some specific animations. With ```animate``` you can only apply css rules that accept numerical values, meaning you can't change color with it

```javascript
$("target").animate({opacity:0.5})
```

You can chain all animations

```javascript
$("target").slideUp().slideDown().animate({opacity:0.5})
```

