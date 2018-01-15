# Symfony sample

## requirements

You need PHP (7.x), composer, and npm
You also need to configure your php with curl and openssl
You have to setup the certificates [download pem file](https://curl.haxx.se/docs/caextract.html), put it somewhere on your system and set your php.ini with those values:
 
 * curl.cainfo = PATH_TO_YOUR_CERTIFICATE/cacert.pe
 * openssl.cafile = PATH_TO_YOUR_CERTIFICATE/cacert.pem
 
You can run the app with a PHP Built_in server (all npm command relys on it), but some features may not work finely. In fact, 
Api-platform package rely on some HTTP SERVER features that are not implemented by the PHP Built-in server. So the api indexes
like /api/index.json, /api/index.jsonld or /api/index.html will just return a 404 Not Found. There is also the react-admin 
app generated by the Api-platform that will not work because it relies on api json index.

## explanation

This application has been realized to get a sample front app with sf3+ & vuejs but it also show basic controllers with what most developpers do :
basic controller, controller with twig, http call to external API, logging, API... We will try to not use front manipulation outside of VueJS (the sample with twig are really basic and won't use form per example)
Here is how it has been created:

* composer create-project symfony/skeleton sf-flex-encore-vuejs
* cd sf-flex-encore-vuejs
* composer req encore annotations twig api http profiler log doctrine-migrations admin webonyx/graphql-php
* composer require --dev doctrine/doctrine-fixtures-bundle
* yarn add vue vue-router quasar-framework quasar-extras vuelidate vue-apollo@next graphql apollo-client apollo-link apollo-link-http apollo-link-error apollo-cache-inmemory graphql-tag
* yarn add --dev vue-loader vue-template-compiler vue-router babel-preset-es2017 testcafe sass-loader node-sass bootstrap@4.0.0-beta.2
* yarn install 

Then some php controllers has been created on following routes :
 
 * / : DefaultController with the menu to navigate throught different controllers
 * /demo/simple : SimpleController with route config in routes.yaml and logger injection with autowiring
 * /demo/hello/:name : HelloController with route config in annotations and twig template
 * /demo/vuejs : VuejsController with route config in annotations and VueJS app with specific js/css import
 * /demo/quasar : QuasarController like VuejsController but with the Quasar framework for UX components
 * /demo/http-plug : HttpPlugController to show how to call external API from your controller
 * /demo/login : LoginController for standard login by Symfony
 * /demo/vuejs/login : LoginJsonController for json login with JS applications
 * /demo/form : [Work in progress] authentification with javascript, and a full web application with vuejs and api-platform(rest/graphql)
 * /api : access ApiPlatform api doc (you need to be authentified from /demo/form if you want to play with it)
 * /api/graphql : access ApiPlatform GraphQL implementation (beta release)
 * /admin : use the easy admin bundle to allow a comparison between fullstack PHP and PHP/VueJS

But, Vuejs, ReactJS and Angular together ? with Symfony4, WTF ???
Yes it can seems completely stupid to use all this technologies together, but don't forget one thing : this is a POC !
The aim is not to help you to mix all those techs, but just to help you to use some of them finely.
The biggest problem in my case is the dependancy management : all those JS libraries may need the same deps but in different
version... For instance it seems to be ok, but i think that in future it could be a real breain-teaser.

## configuration

You can change the php executable (if you want to use a php version with xdebug, or just another version of php) using
package.json in the config section. Default php uses the one in path if exists.
You can also change the web server port and the asset server port in the same config section.

```
    "config": {
        "php": "php",
        "server_port_web": "80",
        "server_port_asset": "8080",
        "test_browser": "chrome,firefox"
    },
```

The test_browser section represent all the browsers you want to use with the testcafe testing tool.

 
## components

* flex: new symfony system to make web dev life easier ; it works with recipes
* vuejs: top js framework to build SPA, or just widget on classic page
* quasar: UX component library based on VueJS
* encore: symfony solution to wrap webpack config and, once again, make your life simpler
* annotations: use annotations everywhere in your PHP code
* twig: symfony template solution, useless if you don't want to render template with symfony, but usefull to be able to use assets twig helper with webpack encore
* api: api-platform to build REST api(instead of fosrestbundle)
* http: a cool library to do http call from http (you could switch it with Guzzle)
* doctrine-migrations: based on Doctrine ORM, it make it easy to change your db during a project life
* doctrine-fixture: also based on Doctrine to help you to add fixtures in your DB (for your tests or for project init)
* admin: easy admin component to build quick backend with auto form
* profiler: for debugging purpose
* log: a logger for symfony
* babel-preset-es2017: do you really need explanation ?
* testcafe: a test framework (might be changed with chimp or anything else, gimme better idea)
* sass: hey, we are not in nineties, we don't write css now
* bootstrap: the beta 4 version of the first class css framework (not used with quasar)

## run

* install the project with `npm run init-project` which will launch :
  1. copy the env file (or set them on your system) : `cp .env.dist .env`
  2. php dependancies installation: `composer install`
  3. nodejs tooling installation: `npm install`
  4. assets generation: `npm run dev`
  5. db init: `php bin/console doctrine:database:create` & `doctrine:schema:create` & `doctrine:fixtures:load`
* Run your application with php built-in server:
  1. Change to the project directory
  2. Execute the `npm run dev-server-hot` command to start the asset server that will build your assets and your manifest.json and serve the assets with hot module replacment when you do a modification on a vuejs file 
  3. Execute the `npm run sf-dev` command;
  4. Browse to the http://localhost:80/ URL.

    Quit the server with CTRL-C.
    Run composer require symfony/web-server-bundle for a better web server.
    And launch `php bin/console server:start 127.0.0.1:80`
    
  5. Run frontend tests with `npm run test`

Don't forget to prefer an nginx/apache server to be able to use full features of api-platform.

* Read the documentation at https://symfony.com/doc

If you want to change default ports, you can use package.json > config : server_port_web for the web server (php built in server), and server_port_asset for the asset server.
Default ports are 80 and 8080.

If you update the project:

 * Don't forget to run `doctrine:migrations:migrate` to take care of DB modifications.
 * Do the following command `npm run dump-js-config:linux` (or windows) to create the js config file

## webpack

Everything is managed by 'encore' symfony package, so have a look at the webpack.config.js and then read their [docs](http://symfony.com/doc/current/frontend.html)
 * npm run dev : will build your assets (in this project it's /public/build/)
 * npm run watch : does the same thing than npm run dev, but it watches files modification to re-generate the assets
 * npm run dev-server :  build the manifest.json that map your assets qith their url from the asset server and start a web server that will serve those assets
 * npm run dev-server-hot : does the same thing as previously, but with vuejs framework it also does Hot Module Replacement 
 * npm run build : build your assets for production
 
Take care, the asset server listen to port 8080 so don't start your main server on that port, or specify another port for the dev-server using ` --port 9999` for example

Also, if you want to use the asset server finely, you have to add the assets configuration in the config/packages/framework.yaml file :
`json_manifest_path: '%kernel.project_dir%/public/build/manifest.json'`. In fact the npm command will build asset in memory only, and modify the manifest file to map asset to a new url served by the asset server instead of the main web server.

# code quality
## PHP
The project uses 2 packages to lint and fix the code style of PHP code :You can install phpcs to check your code
 * squizlabs/PHP_CodeSniffer to lint and follow PSR1/PSR2 rules. 
 * friendsofphp/php-cs-fixer to fix the code
 
Lint with this command `vendor/bin/phpcs src -n --standard=PSR1,PSR2 --report=summary` to get a summary of errors.
Fix with this command `vendor/bin/php-cs-fixer fix src --rules=@PSR1,@PSR2`

## Javascript
For Javascript the following packages has been used: 
 `npm install prettier`
 
To lint the code: `node bin-prettier.js assets/js/**`
To fix it: `node bin-prettier.js assets/js/** --write`

## IDE

For PHP you should configure your IDE to follow PSR1/PSR2 code style (or anything else if you prefer). For JS you will
have to install [prettier tool](https://prettier.io/docs/en/editors.html).

## todo

* improve this tutorial with ~~an API Route built with Api platform (without DB)~~ and install the vue-generator from api-platform for a crud sample :
    * The question for instance is `How to override ApiPlatform routing: i want some route to be overloaded: POST/PUT Book should be able to add also Auhtors and/or Editors`
* ~~add db fixtures at init ! almost 40 books and some reviews (at least 3 for 5 1st books)~~ all sqlite fixtures is converted into the final db model
* customize easyAdminBundle to add author/editor from Book and display those related infos on Book admin page (same for other author/editor entities and serie/reviews)
* manage Entity orphanRemoval / CASCADE onDelete
* find a good way to add a `pre-commit` hook that lint PHP and JS code, and run the PHP / JS tests
