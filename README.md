<div align="center">
  <h1>⏱Gatsby Plugin Webpack Speed Measure</h1>

  <a href="https://npmjs.com/gatsby-plugin-webpack-speed-measure"><img src="https://img.shields.io/npm/dw/gatsby-plugin-webpack-speed-measure.svg" /></a>
  <a href="https://npmjs.com/package/gatsby-plugin-webpack-speed-measure"><img src="https://img.shields.io/node/v/gatsby-plugin-webpack-speed-measure.svg" /></a>
</div>

Shows you how long each Webpack plugin takes during `gatsby build` which may help you pinpoint problems in slow builds.

![Preview of Gatsby Plugin Webpack Speed Measure output](https://github.com/crunchtime-ali/gatsby-plugin-webpack-speed-measure/raw/master/preview-smp.png)

## Background

The `gatsby build` command does not give you detailed stats how long each Webpack plugin took during the build. Even
[Performance tracing](https://www.gatsbyjs.org/docs/performance-tracing/) only lists the `Building production JavaScript and CSS bundle` step as a single item. Therefore I created this plugin which currently uses the [Speed Measure Plugin (for webpack)](https://github.com/stephencookdev/speed-measure-webpack-plugin)

## Install

```bash
npm install --save-dev gatsby-plugin-webpack-speed-measure
```

or

```bash
yarn add -D gatsby-plugin-webpack-speed-measure
```

## Usage

Add `gatsby-plugin-webpack-speed-measure` to the plugin array of your `gatsby-config.js`.

```javascript
plugins: [
	'gatsby-plugin-webpack-speed-measure'
]
```

**Note:** This plugin impacts the build performance negatively because it wraps all other plugins. 
If you put the plugin in front of all other plugins the performance hit is not nearly as drastic as when you put it at the end. In my example build the build time is only 400ms slower when the plugin is in the beginning vs. 4 seconds slower when it is at the end.
My recommendation is to use the plugin only once in a while and check the option `disable: true` 

## Limitations

- You get the name and execution times of the webpack-plugins the respective Gatsby-plugins are based on. This might be solved by removing the dependency on on the `Speed Measure Plugin (for webpack)`.
- The plugin impacts the build performance negatively (see more details regarding this in the Note below the `Usage` section.)
- The underlying `Speed Measure Plugin (for webpack)` causes build problem with [a lot of webpack plugins](https://github.com/stephencookdev/speed-measure-webpack-plugin/issues). If this is true for your setup as well please feel free to file an issue!

## Options

Pass these to the options object of the plugin:

```javascript
plugins: [
	{
		resolve: 'gatsby-plugin-webpack-speed-measure',
		options: {
			disable: true,
		},
	},
]
```

### `options.disable`

Type: `Boolean`<br>
Default: `false`

If truthy, this plugin does nothing at all.

### `options.outputFormat`

Type: `String|Function`<br>
Default: `"human"`

Determines in what format this plugin prints its measurements

 * `"json"` - produces a JSON blob
 * `"human"` - produces a human readable output
 * `"humanVerbose"` - produces a more verbose version of the human readable output
 * If a function, it will call the function with the JSON blob, and output the response

### `options.outputTarget`

Type: `String|Function`<br>
Default: `console.log`

* If a string, it specifies the path to a file to output to.
* If a function, it will call the function with the output as the first parameter

### `options.pluginNames`

Type: `Object`<br>
Default: `{}`

By default, SMP derives plugin names through `plugin.constructor.name`. For some
plugins this doesn't work (or you may want to override this default). This option
takes an object of `pluginName: PluginConstructor`, e.g.

```javascript
const uglify = new UglifyJSPlugin();
plugins: [
	{
		resolve: 'gatsby-plugin-webpack-speed-measure',
		options: {
            pluginNames: {
                customUglifyName: uglify
            }
		},
	},
]
```

### `options.granularLoaderData` _(experimental)_

Type: `Boolean`<br>
Default: `false`

By default, SMP measures loaders in groups. If truthy, this plugin will give per-loader timing information.

This flag is _experimental_. Some loaders will have inaccurate results:

 * loaders using separate processes (e.g. `thread-loader`)
 * loaders emitting file output (e.g. `file-loader`)

We will find solutions to these issues before removing the _(experimental)_ flag on this option.

## License

[MIT](/LICENSE)