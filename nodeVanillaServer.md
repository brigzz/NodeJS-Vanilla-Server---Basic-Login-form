# NodeJS Vanilla Server - Basic Log in form

## index.html (Login Form)

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Login</title>
</head>

<body>
    <h1>Login</h1>

    <input type="text" name="username" id="username" placeholder="username">
    <input type="text" name="password" id="password" placeholder="password">
    <button type="submit" onclick="login()">Submit</button>

    <script>
        async function login() {
            // console.log('insude');
            let data = { "username":document.getElementById('username').value, "password":document.getElementById('password').value};
            // console.log(data);
            const response = await fetch('http://localhost:3000', {
                method: 'POST',
                mode: "no-cors",
                headers: {
                    'Content-Type':'application/json'
                },
                body: JSON.stringify(data)
            });
            const responseData = await response.text();
            let headers = new Headers()
            location.href = response.url;
            document.querySelector('html').innerHTML = responseData;
        }

    </script>
</body>
</html>
```

## loginSuccess.html

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Login success</title>
</head>

<body>
    <h1>Login success</h1>
</body>
</html>
```

## loginFailed.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Login failed</title>
</head>

<body>
    <h1>Login failed</h1>
</body>
</html>
```

## server.js

```js
const http = require('http');
const fs = require('fs');
const path = require('path');
const hostname = 'localhost';
const port = process.env.PORT || 3000;

//broiler plate for Vanilla Server
const server = http.createServer((request, response) => {
  const { headers, method, url } = request;
  let body = [];
  request
    .on('error', (err) => {
      console.error(err);
    })
    .on('data', (chunk) => {
      body.push(chunk);
    })
    .on('end', () => {
      body = Buffer.concat(body).toString();

      if (method === 'POST') {
        postTriggered(body, method, response);
      } else if (method === 'GET') {
        getTriggered(request, response);
      }

    });
});

server.listen(port, hostname, () => {
  console.log(`Server running: http://${hostname}:${port}`);
});
// End - broiler plate for Vanilla Server

//Function for Post Method
postTriggered = (body, method, response) => {

    let content = JSON.parse(body);

    if (content.username === 'admin' && content.password === 'password') {
      response.writeHead(301, {
        'Content-Type':'text/html',
        'Location': 'http://localhost:3000/loginSuccess.html'
      });
      response.end(JSON.stringify({ message: 'Welcome admin!' }));

    } else {

      response.writeHead(301, {
        'Content-Type':'text/html',
        'Location': 'http://localhost:3000/loginFailed.html'
      });
      response.end();
    }
  } 

getTriggered = (request, response) => {
  let file;

  console.log(`request: ${request.url}`);
  if(request.url === '/loginFailed.html') {
    file = './loginFailed.html';
  } else if(request.url === '/index.html') {
    file = './index.html';
  } else if (request.url === 'favicon.ico') {
    file = './favicon.ico';
  } else if (request.url === '/loginSuccess.html') {
    file = './loginSuccess.html';
  } 

  if(file === undefined) {
    file = './index.html';
  }

  fs.readFile(file, (err, data) => {
    if(err) throw err;
    response.writeHead(200, {'Content-Type':'text/html', 'Location': `http://localhost:3000${request.url}`});
    response.write(data);
    response.end();
  })

}
```
