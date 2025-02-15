# Debugging in VS Code

Every application reaches a point where it's necessary to understand failures, small to large. In this recipe, we explore a few workflows for VS Code users who would like to debug their application in the browser.

This recipe shows how to debug [Vue CLI](https://github.com/vuejs/vue-cli) applications in VS Code as they run in the browser.

## Prerequisites

Make sure you have VS Code and the browser of your choice installed.

Install and create a project with the [vue-cli](https://github.com/vuejs/vue-cli), following the instructions in the [Vue CLI Guide](https://cli.vuejs.org/). Change into the newly created application directory and open VS Code.

### Displaying Source Code in the Browser

Before you can debug your Vue components from VS Code, you need to update the generated Webpack config to build sourcemaps. We do this so that our debugger has a way to map the code within a compressed file back to its position in the original file. This ensures that you can debug an application even after your assets have been optimized by Webpack.

If you use Vue CLI 2, set or update the `devtool` property inside `config/index.js`:

```json
devtool: 'source-map',
```

If you use Vue CLI 3, set or update the `devtool` property inside `vue.config.js`:

```js
module.exports = {
  configureWebpack: {
    devtool: 'source-map',
  },
}
```

### Launching the Application from VS Code

::: info
We're assuming the port to be `8080` here. If it's not the case (for instance, if `8080` has been taken and Vue CLI automatically picks another port for you), just modify the configuration accordingly.
:::

Click on the Debugging icon in the Activity Bar to bring up the Debug view, then click on the gear icon to configure a launch.json file, selecting **Chrome/Firefox: Launch** as the environment. Replace content of the generated launch.json with the corresponding configuration:

<div style="padding: 10px 25px 30px"><img src="/images/config_add.png" alt="Add Chrome Configuration" style="width: 690px; border-radius: 3px; box-shadow: 0 10px 15px rgb(0 0 0 / 50%)"></div>

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "chrome",
      "request": "launch",
      "name": "vuejs: chrome",
      "url": "http://localhost:8080",
      "webRoot": "${workspaceFolder}/src",
      "breakOnLoad": true,
      "sourceMapPathOverrides": {
        "webpack:///src/*": "${webRoot}/*"
      }
    },
    {
      "type": "firefox",
      "request": "launch",
      "name": "vuejs: firefox",
      "url": "http://localhost:8080",
      "webRoot": "${workspaceFolder}/src",
      "pathMappings": [{ "url": "webpack:///src/", "path": "${webRoot}/" }]
    }
  ]
}
```

## Setting a Breakpoint

1.  Set a breakpoint in **src/components/HelloWorld.vue** on `line 90` where the `data` function returns a string.

<div style="padding: 10px 25px 30px"><img src="/images/breakpoint_set.png" alt="Breakpoint Renderer" style="width: 690px; border-radius: 3px; box-shadow: 0 10px 15px rgb(0 0 0 / 50%)"></div>


2.  Open your favorite terminal at the root folder and serve the app using Vue CLI:

```
npm run serve
```

3.  Go to the Debug view, select the **'vuejs: chrome/firefox'** configuration, then press F5 or click the green play button.

4.  Your breakpoint should now be hit as a new browser instance opens `http://localhost:8080`.

<div style="padding: 10px 25px 30px"><img src="/images/breakpoint_hit.png" alt="Breakpoint Hit" style="width: 690px; border-radius: 3px; box-shadow: 0 10px 15px rgb(0 0 0 / 50%)"></div>

## Alternative Patterns

### Vue Devtools

There are other methods of debugging, varying in complexity. The most popular and simple of which is to use the excellent Vue.js devtools [for Chrome](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd) and [for Firefox](https://addons.mozilla.org/en-US/firefox/addon/vue-js-devtools/). Some of the benefits of working with the devtools are that they enable you to live-edit data properties and see the changes reflected immediately. The other major benefit is the ability to do time travel debugging for Vuex.

![Devtools Timetravel Debugger](/images/devtools-timetravel.gif)

Please note that if the page uses a production/minified build of Vue.js (such as the standard link from a CDN), devtools inspection is disabled by default so the Vue pane won't show up. If you switch to an unminified version, you may have to give the page a hard refresh to see them.

### Simple Debugger Statement

The example above has a great workflow. However, there is an alternative option where you can use the [native debugger statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/debugger) directly in your code. If you choose to work this way, it's important that you remember to remove the statements when you're done.

```vue
<script>
export default {
  data() {
    return {
      message: ''
    }
  },
  mounted() {
    const hello = 'Hello World!'
    debugger
    this.message = hello
  }
};
</script>
```

## Acknowledgements

This recipe was based on a contribution from [Kenneth Auchenberg](https://twitter.com/auchenberg), [available here](https://github.com/Microsoft/VSCode-recipes/tree/master/vuejs-cli).
