```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Document</title>
  <style>
    *{margin: 0;padding: 0;}
    body {
      position: relative;
    }
    #box {
      position: absolute;
      top: 0; left: 0;
      width: 100px;height: 100px;border: 1px solid #000;
    }
  </style>
</head>
<body>
  <div id="box"></div>
  <script>
    let dragging = false
    let position = null

    box.addEventListener('mousedown', (e) => {
      dragging = true
      position = [e.clientX, e.clientY]
    })

    document.addEventListener('mousemove', (e) => {
      if (dragging === false) return
      const x = e.clientX
      const y = e.clientY
      const deltaX = x - position[0]
      const deltaY = y - position[1]
      const left = parseInt(box.style.left || 0)
      const top = parseInt(box.style.top || 0)
      box.style.left = left + deltaX + 'px'
      box.style.top = top + deltaY + 'px'
      position = [x, y]
    })
    document.addEventListener('mouseup', (e) => {
      dragging = false
    })
  </script>
</body>
</html>
```

