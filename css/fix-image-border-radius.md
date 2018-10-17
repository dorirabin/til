# Fix border-radius ignored by image

Border-radius is not applied on a div element which contains an image, because the image is overflowed.

Html

```
<div class="container">
  <div class="content">
    <div class="left">
      <img class="image" src="https://www.gstatic.com/webp/gallery/1.jpg">
    </div>
    <div class="right">
      <h1>Heading</h1>
    </div>
  </div>
<div>
```

Css

```
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-size: 62.5%;
  background-color: #eee;
}

.content {
  display: flex;
  background-color: #ccc;
  margin: 2rem;
  border-radius: 2rem;
}

.image {
  display: block;
}
```

In order to fix, we need to add `overflow: hidden` to the `.content`.

```
.content {
  display: flex;
  background-color: #ccc;
  margin: 2rem;
  border-radius: 2rem;
  overflow: hidden;
}
```
