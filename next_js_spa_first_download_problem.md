# SPA (Single Page Application) challenge

When the user visits SPA application for the first time he has to wait a long time, until js is downloaded, all REST API calls made, data retrieved and finally js renders DOM.

NEXT.js can solve this problem



## Installing Next

Next.js simplifies creating server side rendered react apps

To install

```shel
$ mkdir -p myApp
$ cd myApp
$ npm init -y
$ npm install react react-dom next --save
```

Next add 3 runs into scripts section in package.json

```json
...
"scripts":{
  	"dev": "next",
    "build": "next build",
    "start": "next start"
}
...
```

Next looks for files to render in ```pages``` directory. So you will have to make ```pages``` directory in your application's root directory and create ```index.js``` there which will be found by ```Next``` and rendered.

```Next``` has no ```index.html```, so how do you include ```css```? You can achieve that by installing ```@zeit/next-css``` 

```shell
$ npm install @zeit/next-css --save
```

Then in ```next.config.js```

```javascript
const withCSS=require("@zeit/next-css");
module.exports = withCSS();
```

With above configurations you can use ```css``` by importing it. For example lets say in ```src``` directory you have ```MyCSS.css``` file. Then you can import it in your component and use css classes defined within it

```react
import './MyCSS.css';
```

But remember that this kind of importing css files in local components only works with Next "next": "^7.0.2",

So we built a simple nice looking DigitalClock. But when we open condole we see an error saying ```Text between server and client didn't match```

## static method getInitialProps

### Server side and client side rendering

#### Server side

- NodeJS runs Javascript
- React started and all component constructors run
- componentDidMount not run
- component render methods called
- all rendered static HTML sent to the browser

#### Client side

- HTML downloaded from server
- script tags download JavaScript files
- JavaScript executes including all React components
- Component constructors run
- Component lifecycle events including ```componentDidMount``` run



Because of above flow you get warning server side text didn't match client side.

To solve this

- React properties serialized to static HTML page
- React properties picked up from static HTML page and passed to constructor

```Next``` calls ```getInitialProps``` before the component under ```pages``` directory is created.

Next calls static method ```getInitialProps``` and passes ```time``` property to component constructor, serializes it into static HTML page. Client deserializes static HTML page with its props and passes it to component constructor when calling it.

As we explained above react components run twice, on the server and on the client side. ```getInitialProps``` static method only runs on the server side. Thanks to that we can synch property values between the server and the client. Client side instead looks at ```_NEXT_DATA__``` variable and locates props in it and passes it to the constructor of ```Index``` component.

As mentioned before ```Next``` runs static method ```getInitialProps``` before the component is created and passes its return value as ```props``` to the component constructor. A simple example where we returned a ```time``` variable as a props demonstrated that behavior. You might be wondering if it will work with asynchronous data. What if we want to return asynchronoous data as props from ```getInitialProps``` static method. It turns out that ```Next``` can handle that too. It will wait until data is ready and then passes props to the component.

### simulating rest api

you can use ```axios``` and ```json-server``` that reads from ```db.json``` file

```shell
$ npm install axios --save
$ npm install json-server --save-dev
```

then define ```json-server``` run in ```scripts``` section of ```package.json```

```json
"scripts":{
  ...,
  "json-server":"json-server --watch db.json --port 4000 --delay 200"
}
```

### ES6 frustrating shit

An arrow function can get tricky, if you use fucking curly braced then you have to return value, if you don't use curly braces then the stupid shit implies return

```react
{this.state.speakersData.map((speaker) => (
            <li key={speaker.id}>
              {speaker.firstName} {speaker.lastName}
            </li>
          ))}
```

## React client side routing

```react
import Link from "next/link";

...
  <Link href="/sessions"><a>Sessions</a></Link>
```

## To install bootstrap

Below will install bootstrap under node_modules

```shell
$ npm i bootstrtap --save
```

You will need to create ```_document.js``` for bootstrap to work

## Standard Page Layout

You usually want a Standard Page Layout with sections like Header, Body and Footer.

React on its own is agnostic or non-prescriptive on how you should do that.

Learn how to use ```Next.js``` ```_app.js``` to implement page layouts

This special component wraps any components that get launched from ```pages``` directory

What this means is that it gives us ability to wrap components defined in ```pages``` directory with our own components like ```Header``` and ```Footer```

It also gives us a convenient place to put our bootstrap and css imports

We will only use layout capability of _app.js. It has other capabilities like defining super layer getInitialProps which makes user authentication super easy.

In below snippet we are leveraging Layout component to wrap our main body container with common components such as Header and Navbar and footer

```react
import React from 'react';
import App, {Container} from 'next/app';
import {Layout} from "../src/Layout";

export default class MyApp extends App {
    render() {
        const {Component, pageProps} = this.props
        return (
            <Container>
                <Layout>
                    <Component {...pageProps} />
                </Layout>
            </Container>
        );
    }
}
```

We need to create Layout component

```react
import React from "react";

import "bootstrap/dist/css/bootstrap.min.css";
import "../css/site.css";

import Header from "./Header";
import Menu from "./Menu";
import Footer from "./Footer";

export class Layout extends React.Component {
  render() {
    const { children } = this.props;

    return (
      <React.Fragment>
        <Header />
        <Menu />
        {children}
        <Footer />
      </React.Fragment>
    );
  }
}
export default Layout
```

```<Component {...pageProps} />``` renders ```index.js``` first. Then as you route using navbar links it renders other components defined in ```pages``` directory.

### Client side routing with parameters

Suppose we wanted to show certain speaker info when we route to ```/speakers/{speakerId}```

To do that you have to enhance href attribute of Next Link. as attribute specified the future url that will show in our browser after routing to the new page

pathname should refer to valid component file name under ```pages``` directory

```JSX
<Link href={{pathname:"/speaker",query:{speakerId:this.props.speakerId}}
            as={`speakers/${this.props.speakerId}`}><a>Speaker A</a>
</Link>
```

Above works for client side rendering, but as soon as I refresh my page I will get an error because I haven't defined server side route that matches my url speaker/{speakerId}

When we run ```npm run dev``` behind the scenes ```next``` is launching node ```express``` server

We can define our own ```server.js``` to launch ```express``` server explicitly and define server side routes.

```javascript
const express = require("express");
const next = require("next");

const dev = process.env.NODE_ENV !== "production";
const app = next({ dev });
const handle = app.getRequestHandler();

app
  .prepare()
  .then(() => {
    const server = express();

    server.get("/speaker/:speakerId", (req, res) => {
      const actualPage = "/speaker";
      const queryParams = { speakerId: req.params.speakerId };
      app.render(req, res, actualPage, queryParams);
    });

    server.get("*", (req, res) => {
      return handle(req, res);
    });

    server.listen(3000, (err) => {
      if (err) throw err;
      console.log("> Ready on http://localhost:3000");
    });
  })
  .catch((ex) => {
    console.error(ex.stack);
    process.exit(1);
  });

```

# Publishing your nextjs app to Docker

## Environment variables

You can define your env variables in .env file and access them with node ```process`` variable

But to let next read .env we first need to install ```dotenv-webpack``` and ```path```

```shell
$ npm i dotenv-webpack path --save
```

To make nextjs read .env file we need to add below into next.config.js

```javascript
const withCSS = require("@zeit/next-css");

require("dotenv").config();
const path = require("path");
const Dotenv = require("dotenv-webpack");

/* Without CSS Modules, with PostCSS */
module.exports = withCSS({
  webpack(config, options) {
    config.plugins = config.plugins || [];
    config.plugins = [
      ...config.plugins,
      // Read the .env file
      new Dotenv({
        path: path.join(__dirname, ".env"),
        systemvars: true,
      }),
    ];
    return config;
  },
});

```

What if you want to set default values to your environment variables

You can do it using getConfig from next/config

```javascript
const withCSS = require("@zeit/next-css");

require("dotenv").config();
const path = require("path");
const Dotenv = require("dotenv-webpack");

/* Without CSS Modules, with PostCSS */
module.exports = withCSS({
  serverRuntimeConfig: {
    // Will only be available on the server side
  },
  publicRuntimeConfig: {
    // Will be available on both server and client
    RESTURL_SPEAKERS_PROD:
      "https://www.siliconvalley-codecamp.com/rest/speakers/ps",
    RESTURL_SPEAKER_PROD: "https://www.siliconvalley-codecamp.com/rest/speaker",
    RESTURL_SESSIONS_PROD:
      "https://www.siliconvalley-codecamp.com/rest/sessions",
  },
  webpack(config, options) {
    config.plugins = config.plugins || [];
    config.plugins = [
      ...config.plugins,
      // Read the .env file
      new Dotenv({
        path: path.join(__dirname, ".env"),
        systemvars: true,
      }),
    ];
    return config;
  },
});

```



speakers.js example

```javascript
import React from "react";
import axios from "axios";
import SpeakerCard from "../src/SpeakerCard";

import getConfig from "next/config";
const { serverRuntimeConfig, publicRuntimeConfig } = getConfig();

class Speakers extends React.Component {
  static async getInitialProps() {
    var promise = axios
      .get(Speakers.GetSpeakersUrl())
      .then((response) => {
        return { hasErrored: false, speakersData: response.data };
      })
      .catch((error) => {
        return { hasErrored: true, message: error.message };
      });
    return promise;
  }

  constructor(props) {
    super(props);
    this.state = {
      hasErrored: props.hasErrored,
      message: props.message,
      speakersData: props.speakersData,
    };
  }

  static GetSpeakersUrl() {
    if (process.env.NODE_ENV === "production") {
      return (
        process.env.RESTURL_SPEAKERS_PROD ||
        publicRuntimeConfig.RESTURL_SPEAKERS_PROD
      );
    } else {
      return process.env.RESTURL_SPEAKERS_DEV;
    }
  }

  render() {
    return (
      <div className="container">
        <div className="row">
          <div className="card-deck">
            {this.state.speakersData.map((speaker) => (
              <div
                className="card col-4 cardmin margintopbottom20"
                key={speaker.id}
              >
                <SpeakerCard speaker={speaker} />
              </div>
            ))}
          </div>
        </div>
      </div>
    );
  }
}

export default Speakers;

```

## Build image

Make a Dockerfile as below

```dockerfile
FROM node:16-alpine AS deps

# create app directory
RUN mkdir -p /usr/src
WORKDIR /usr/src

# install app dependencies
COPY package.json /usr/src
COPY package-lock.json /usr/src
RUN npm install

# bundle app source
COPY . /usr/src

RUN npm run build
EXPOSE 3000

CMD ["npm","start"]
```

Build the image and push it to docker hub

```bash
docker build -t samrullo/next-node .
docker push samrullo/next-node
```

# Performance

## Image optimization

With ```next-images``` you can optimize images. First you install it

```bash
npm install next-images
```

Then you include it in ```next.config.js```

```javascript
...
const withImage = require("next-images")
...
module.exports = ...(
  withImages(...)
)
```

Above way of including librarires in next.config.js looks ugly. If you find yourself using many plugins you might want to add an extra layer of complexity and use ```next-compose-plugins```

We add another config variable ```inlineImageLimit``` If the resulting image in img tags is bigger than this number Next.js will render it as usual otherwise it will render it as encoded data

We can import images as variables and pass them to src attribute of the img tag

```javascript
...
import svccImage from "../static/SVCClogo.png"
...
<img src={svccImage}/>
...
```

## Adding page caching

With server rendering, first time we hit landing page node runs complext javascript to generate javascript html and send it to the client. With sites that hit server frequently this can easily damage user experience. Luckily we can use page caching.

We will need ```lru-cache``` to use page caching. With page caching, when we hit the server for a certain page, instead of re-generating that html page by running complext javascript, server grabs it from its memory and returns immediately.

```bash
npm install lru-cache --save
```

lru-cache helps delete recently used items

