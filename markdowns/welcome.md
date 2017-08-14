# What will I learn?
You will learn how to create playgrounds on tech.io with a viewer that communicates with a server.
All the code of this playground can be found on GitHub, feel free to fork or make pull request on it to improve it: [GitHub repository](https://github.com/jeromecance/techio-viewer-server.git)

# Creating the structure
The static viewer provided by tech.io using the `-s` parameter of the [open command](https://tech.io/playgrounds/408/tech-io-documentation/open) is perfect for displaying static content but limited when you want to have a dynamic viewer that interacts with the server.

To unleash the full power of viewer, you can manage your own server. In this example I'll show you how to create a basic server with Node and then creates a viewer that communicates with this server.

## Creating the index.html
Create an `index.html` file in the `nodejs-project` folder:

```html
<html>
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.2.1/jquery.min.js"></script>

    <script type="text/javascript">
      function callServer() {
        $.get( "/ajax", function( data ) {
          $("#result").html(data);
        });
      }
    </script>
  </head>
  <body>
    <button onclick="callServer()">Execute the code on the server</button>
    Result: <div id="result"></div>
  </body>
</html>
```

This html will be displayed in the viewer. It displays a button that will call the server using ajax.

## Creating an interactive code for the user
Rename the file `nodejs-project/universe.js` to `nodejs-project/code.js` and change it like this:

```JavaScript
function hello() {
  return "Hello";
}

// { autofold
module.exports = {
  hello: hello
};
// }
```

This code will be displayed to the user so that he can change it. This code will be called on the server side when the user presses the button on the viewer. Then, the result of this code will be displayed in the viewer.

## Creating the server
To finish, rename `nodejs-project/universe.spec.js` to `nodejs-project/server.js` and change its content by:

```javascript
var code = require('./code.js');
var express = require('express');
var app = express();
var path = require('path');

app.get('/', function(req, res) {
  console.log("/ is called on server");
  res.sendFile(path.join(__dirname + '/index.html'));
});

app.get('/ajax', function(req, res) {
  console.log("/ajax is called on server");
  res.send(code.hello());
});

console.log("Opening the server");
app.listen(8080);
console.log('TECHIO> open -p 8080 /');
console.log("Server opened");

```

This code will be called when the users presses the Run button. It will create an express server that handles two routes:

- `/` : it will serve the index.html file
- `/ajax`: it will execute the code of the user on the server and returns the result when the user presses the button in the `index.html` file (the viewer).

When the server is created, we use the `open` command to ask to tech.io to open a viewer on the run instance. This is done by using the following command on the standard output:

```
TECHIO> open -p 8080 /
```

It just says: open a connection on this instance by opening the port 8080 and then displays the route `/` in the viewer panel (so it will renders the `index.html` file).

## Updating the dependencies
Our server uses `express` and `path` package. So you need to add them to the `package.json` file:

```json
{
  "name": "nodejs-project-template",
  "version": "1.0.0",
  "dependencies": {
    "express": "*",
    "path": "*"
  }
}

```


# Test
To finish we need to add the run command to a markdown file. Edit the `markdowns/welcome.md`, erase everything and add this command:
```
@[Launch the server]({ "stubs": ["code.js"], "command": "node server.js" })
```

- This command will display the `code.js` file to the user.
- When he will press the Run button, it will call the `server.js` file that will open a server and then display the `/` route in the viewer (and so displaying the `index.js` file).
- If the user presses the button inside the viewer, it will call the server with ajax by calling the route `/ajax`.
- The server will executes the `code.js` file and return the result.
- Then the viewer will display the result.

# Final example
Here is the same command as above but with all files exposed so that you can play with them.

```
@[Launch the server]({ "stubs": ["code.js", "index.html", "server.js"], "command": "node server.js" })
```

renders as:

@[Launch the server]({ "stubs": ["code.js", "index.html", "server.js"], "command": "node server.js" })
