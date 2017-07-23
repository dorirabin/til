# Use html-webpack-plugin with webpack-dev-middleware

Since webpack-dev-middleware writes file in memory, we need to get the content from memory to serve the request.

Source: https://github.com/jantimon/html-webpack-plugin/issues/145#issuecomment-170554832

```javascript
app.get('*', function(req, res) {
  const filename = path.join(compiler.outputPath, 'index.html');
  compiler.outputFileSystem.readFile(filename, function(err, result){
    if (err) {
      return next(err);
    }
    res.set('content-type','text/html');
    res.send(result);
    res.end();
  });
});
```
