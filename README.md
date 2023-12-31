## Basic API

This is a basic api system that has custom auth api token(bearer) with expiry date (NO Sanctum or Passport). 
Has XSS stripping and Http Headers under the middleware. Using Service Pattern. Also build error handler with job 
so any errors caught in try catch will email user. Try catch is global. Have a helper for the json return responses. 
Unit feature testing is also been added for the 3 functions. Basic Caching added to Quotes. Using Laravel 10.

## Middleware added
app\Http\Middleware\XssSanitization.php

app\Http\Middleware\SecureHeaders.php

## Job Added - without job db table this won't function. It is in the migration
app\Jobs\DevMailNotification.php

## Mailing
app\Mail\ErrorAlert.php

resources\views\emails\error_alert.blade.php

## Sections of code modified for task
app\Exceptions\Handler.php -> Error catching and try catch global.

app\Providers\AuthServiceProvider.php

app\Services\AuthService.php

app\Helpers\helpers.php

composer.json -> added autoload helper function


## Steps to use the api from Testing to Postman
```bash
Step 1 - git clone https://github.com/marcovie/laravel_10_basic_api_without_auth_package.git
```
[https://github.com/marcovie/laravel_10_basic_api_without_auth_package.git](https://github.com/marcovie/laravel_10_basic_api_without_auth_package.git)
```bash
Step 2 - "composer install"

Step 3 - Make copy of the ".env.example" file and change the copy name to .env open 
file and change settings required.

Step 4 - Update in .env file the "APP_DEV_EMAIL" and "APP_QUOTE_REQUEST_COUNT". "APP_DEV_EMAIL" 
is email address where errors will be sent. "APP_QUOTE_REQUEST_COUNT" is quote limit.

Step 5 - For full experience if Critical errors occur create account on "https://mailtrap.io/" 
and put username and password into .env file but not required as shouldnt have Critical error but it 
is there for real system etc..

Step 6 - Create DB in your mysql/mariadb and take name of DB create and add it into .env file at the 
variable "DB_DATABASE" make sure to "update username password" that is related to your connections string

Step 7 - In command prompt in the root of this laravel project. Run this commands below:

Step 8 - "php artisan key:generate"

Step 9 - "php artisan migrate"

Step 10 - "composer dump-autoload"

Step 11 - To create a user run a basic seed file for test purpose. Please run 
"php artisan db:seed --class=UsersTableSeeder" the create user details are as follow:
"email => user@email.com, password => password". 
Password is encrypted in DB. Other ways to to create user for production system
would be to have register section or admin to send email out to client or create from the admin system. 

Step 12 - "php artisan serve" - Which should give you url -> "http://127.0.0.1:8000"

Step 13 - You can use file "marco_postman requests.postman_collection.json" in root to import 
into Postman. Remember the seed user must be in the DB to use this json file
"php artisan db:seed --class=UsersTableSeeder". You need to make sure update Bearer Token
from login on the other postman calls. See section after step 15.

Step 14 - To run tests go to command prompt run: "php artisan test" to get tests to run. 

Step 15 - In Postman can call these URL if "php artisan serve" running and IP/address is same. Please see steps below
```
### Login
```bash
method    - post
url       - http://127.0.0.1:8000/api/v1.0/login

form-data - if you seeded the db with that one record
Email     - user@email.com
password  -  password
```
### Quotes - Cached (file) - Cached for 60 secs
```bash
method    - get
url       - http://127.0.0.1:8000/api/v1.0/quote or http://127.0.0.1:8000/api/v1.0/quote/1

Authorization
Bearer Token: get token from Login response above.
```

### Quotes - NOT Cached - Skips cached
```bash
method    - get
url       - http://127.0.0.1:8000/api/v1.0/quote/0

Authorization
Bearer Token: get token from Login response above.
```

### Logout
```bash
method    - post
url       - http://127.0.0.1:8000/api/v1.0/logout

Authorization
Bearer Token: get token from Login response above.
```

## Must have Requirements 

The challenge will contain a few core features most applications have. That includes connecting to an
API, basic MVC, exposing an API, and finally, tests.

The API we want you to connect to is [https://api.kanye.rest/](https://api.kanye.rest/)

The application should have the following features

• A rest API that shows 5 random Kayne West quotes.

• There should be an endpoint to refresh the quotes and fetch the next five random quotes.

• Authentication for these APIs should be done with an API token, not using any package.

• Above features are tested with Feature tests.

• Provide a README so we can set up and test the application.


## What needs to be done to run this code

## Routes List
```bash
POST       api/v1.0/login ........................................ login › Api\AuthController@login

POST       api/v1.0/logout ....................................... logout › Api\AuthController@logout
  
GET|HEAD   api/v1.0/quote/{cache?} ............................... quote › Api\QuoteController@index
  
```

## Routes Functions
### Login (POST) -> app\Http\Controllers\Api\AuthController.php

User can login with Email and Password. Which will update User table with a api_token and expiry date and return a 
Bearer Token which is used to access the rest of functions. Token has "1 week life span". You can poll the same login 
function it will give you new token and update expiry date.

### Logout (Post) -> app\Http\Controllers\Api\AuthController.php

Logout basically you send post to it with Bearer token then it will update User table by resetting token and expiry 
date to null.

### Quote (Get) -> app\Http\Controllers\Api\QuoteController.php

This function returns by default 5 quotes from the [https://api.kanye.rest/](https://api.kanye.rest/) API. This function can be polled 
multiple times to return new quotes depending on if you are using the caching or non caching section. Quotes limit value
can be updated to return more than 5 via .env with a limit of 15 max. Also has basic caching, by default caching on.
