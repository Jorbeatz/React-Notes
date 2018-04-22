# Deploying in React
## How to optimize things for production
*	Run webpack with the -p flag to set node environment to production and to minify javascript code. 
* 	A lot of the bundle size in production are from 3rd party libraries, source maps, etc. that aren't needed in prod
*  	Having our webpack config file export a function that returns the config object rather than  just returning the config object has benefits in that you can pass in arguments
*   We also want our build script to run with env set to production as well as the p-flag

## Creating Separate CSS Files
*	When we have our styles in the bundle js file, the CSS isn't rendered until after the javascript is ran. We don't want that. We want to extract it and pull it out to a separate file.
* 	We're going to use a plugin: extract-text-webpack-plugin


```
const ExtractTextPlugin = require('extract-text-webpack-plugin');

module.exports = (env) => {
	const isProduction = env === 'production';
	const CSSExtract = new ExtractTextPlugin('styles.css');
```
```
test:/\.s?css$/,
				use: CSSExtract.extract({
					use: [
						'css-loader',
						'sass-loader'
					]
				})
			}]
		},
		plugins: [
			CSSExtract
		],
```

*	We now have extracted all our styles into a separate file with a separate map. We can now link it in index.html so that the styles render before the javascript
* 	**ITS FASTER**. Load styles in parallel to JS

## Adding in CSS Maps on Dev
```
use: CSSExtract.extract({
	use: [
		{
			loader: 'css-loader',
			options: {
				sourceMap: true
			}
		},
		{
			loader: 'sass-loader',
			options: {
				sourceMap: true
			}
		},
	]
})
```

## Production Web Server with Express
*	Creating a development production server
* 	Currently we have dev-server and live-server but they arent suitable for production
*  	We're going to create a simple express server
*  	Make a root directory and call it server.js

### Express
```
const path = require('path');
const express = require('express');
const app = express();
const publicPath = path.join(__dirname, '..', 'public');
app.use(express.static(publicPath));

// If the address doesn't match serve up index.html
app.get('*', (req, res) => {
	res.sendFile(path.join(publicPath, 'index.html'));
});

app.listen(3000, () => {
	console.log("server is up");
});
```
*	Pretty much barebones express server

## Deploying with Heroku
*	Install Heroku CLI then run ```heroku create react-course-expensify```
* 	This also creates a git repository on heroku servers
*  	Heroku first tries to run the start script in your package, gotta add that in 
*  	Can't specify port, gotta have heroku set it in 

```
const port = process.env.PORT || 3000; // Use port if heroku has when else just use 3000
```

*	Need to set up a script for Heroku to install dependencies
* 	Need to specify a heroku script to run the webpack production build on heroku servers

```
"heroku-postbuild": "yarn run build:prod",
```

*	Update gitignore file to not include bundle.js, bundle.js.map styles.css.map etc because those are generated on the servers
* 	Then just push to Heroku's remote server ```git push heroku master ```
*  	It should build everything there and run it
*  	You can check logs with ``` heroku logs ```

## Regular vs Dependent Dependencies
*	There are some dependencies that don't need to be in production
* 	We want to install deps as dev ``` yarn add chalk --dev ```
*  	``` yarn install --production ``` for prod
* 	``` yarn install``` for normal
*	We also want to create a separate dist folder to store our compiled assets, this makes it easier to add .gitignore and its also cleaner
	* 	Gotta modify outputs in the webpack config for dev server and output object
* 	Devserver use virtual assets, doesn't actually write it in

## New Feature Workflow
*	It's pretty much test and make changes on dev-server, then push to heroku I believe. Not much else. What you would imagine.

## JavaScript Filter, Map and Reduce
*	Make sure to know these



