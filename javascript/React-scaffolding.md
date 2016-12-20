# React scaffolding

As of Dec 2016 - how to get started with React.js and node.js

## Pre-requisites

* Nodejs
* NPM

## Process

Create source directories

	mkdir my-project && cd $_ && mkdir -p src www/js

Create a new package.json

	npm init

	# React library
	npm install --save react react-dom

	# Babel goodness (transpile JSX / ES6 to ES5)
	npm install --save-dev babel-cli babel-preset-react babel-preset-es2015

	# Grunt (dev time build tools / watchers / tools)
	npm install --save-dev grunt grunt-babel grunt-contrib-watch grunt-contrib-copy

	# load-grunt-tasks saves on inidivdual grunt.loadNpmTasks('...') statements
	npm install --save-dev load-grunt-tasks

Initialize .babelrc

	{ "presets": ["react","es2015"] }

Initialize Gruntfile.js

	module.exports = function (grunt) {

	    require("load-grunt-tasks")(grunt);

	    grunt.initConfig(
	        {
	            copy: {
	                main: {
	                    files: [
	                        {expand: false, src: ['node_modules/react-dom/dist/react-dom.js'], dest: 'www/js/react-dom.js', filter: 'isFile'},
	                        {expand: false, src: ['node_modules/react/dist/react-with-addons.js'], dest: 'www/js/react-with-addons.js', filter: 'isFile'},
	                    ],
	                },
	            },
	            babel: {
	                options: {
	                    sourceMap: true
	                },
	                dist: {
	                    files: [
	                        {
	                            expand: true,
	                            cwd: 'src/',
	                            src: ['*.js'],
	                            dest: 'www/js'
	                        }
	                    ]
	                }
	            },
	            watch: {
	                files: ['src/*.js'],
	                tasks: ['babel']
	            }
	        }
	    );

	    grunt.event.on('watch', function (action, filepath, target) {
	        grunt.log.writeln(target + ': ' + filepath + ' has ' + action);
	    });

	    grunt.registerTask('default', ['copy', 'babel']);
	};

Add files to src/ and then run grunt:

	grunt

To continually have grunt watch for changes and compile, run:

	grunt watch

To view your work, navigate to www and start a simple webserver

	cd www && python2 -m SimpleHTTPServer 8080


Currently there is no code, to make something work, you need HTML page, and some JS

www/index.htm

	<!doctype html>
	<html lang="en">
	<head>
	    <meta charset="UTF-8">
	    <title>family-tree POC</title>
	    <script src="js/react-with-addons.js"></script>
	    <script src="js/react-dom.js"></script>
	</head>
	<body>
	    <div id="root"></div>
	    <script src="js/test.js" type="application/javascript"></script>
	</body>
	</html>

src/test.js (will be transpiled to www/js/test.js)

	function App() {
	    return <div>{ new Date().getTime() }</div>;
	}

	ReactDOM.render(
	    <App />,
	    document.getElementById('root')
	);

Then navigate to: [http://localhost:8080/index.htm]()

If all has gone well, there should be the current timestamp displayed in the page.
