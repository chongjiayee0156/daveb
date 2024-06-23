backend for fullstack note tracking website.

note: this readme file is meant for my own learning process.

<hr>

first MERN stack project

npm init -y -> package.json
npm i express -> install express node modules
npm i --save-dev nodemon -> monitor changes for developers

change script in package.json: entry point, npm start for deployment, npm dev for development

add node_modules to .gitignore

---

server.js -> entry point
-> middleware to handle log events
-> middleware to provide static access to public folder
-> now import route folder to handle the request
-> if route doesn't return response, it will move on to next "all" function, it will return 404 error

routes/root
-> import express.Router()
-> handle get requests and respond by sending back specific html files
-> modules.export = router

views/index.html or views/404.html
-> this will be the file returnes as response from the router
-> it shall import csss under public file
-> can access from root directory, since public folder is already available from the express.static middleware
-> css/style.css

public
-> be publicly available thanks to express.static middleware
-> can be accessed firectly from other files, w/o relative path specification

------

logs and middleware folder
add logs to .gitignore
npm i date-fns uuid
under middleware, create custom middleware tht produces log and add it to a log file using date-fns and uuid
use this log middleware at the start of server

under middleware, build error handler middleware
place this middleware at the end of the server to catch all possible errors thrown

app.use(express.json())->automatically parse json objects in req.body

npm i cookie-parser
app.use(cookieParser())->automatically parse cookies as req.cookies ->     
res.cookie('username', 'john_doe', { maxAge: 900000, httpOnly: true });    
const username = req.cookies.username;

cors-> cross origin resource sharing is prohibited
npm i cross
app.use(cors()) -> now it is available publicly, any origin can access ur server
set allowerorigins and integrate it into corsoption -> now it allowes cross resource sharing, but with security
-> if origin is in the list or origin is null (eg: postman), allow it to access server

(though i cant test this out as i was being blocked by CSP content security policy)

-------

npm i dotenv 
require('dontenv').config()
-> convert environment variables in .env to be accessible by process.env.xxx

now open a mongodb database project, set accessible ip address to everyone, copy URI and paste in .env

not set up models for each database schema: userschema, noteschema

npm i mongoose-sequence

under config, add DBConn for connection to mongoose using process.env.DATABASE_URI

in server, connectdb at the start. at the end, if it is connected, only let server listen to the port.

if connection has error, print out logevent

------

create a new router for user
import router at server

create controllers for users and notes
npm i express-async-handler -> don't use many try ctach blocks when dealing w database
npm i bcrypt -> hash password

controllers means what should the database do when get, post, patch delete is called on the users directory

test them out with postman

use lean() when data is no longer used as database in the future

use exec to return a promise

---

fontend

---
2 lessons: 6 AND 7:  of REDUX & RTK QUERY,
HANDLE DATA TRANSFER BTW BACKEND FROM FRONTEND, HANDLE REFRESH RATE ETC, I SKIP CUZ IT'S TOO ADVANCED

npm i @reduxjs/toolkit react-redux

create apislice-> use it in store
import store in index js and wrap it with promise arnd app

create usersapislice

---
LESSON 8: authentication at backend

create authentication route

npm i express-rate-limit ->middleware login limiter -> maximum request per minute, use to control auth directory at auth route

create auth controller (login, logout, refresh)

npm i jsonwebtoken

auth web token
1.USE JWT TO GENERATE ACCES TOKEN AND WEB TOKEN,
2. INCLUDE PERSONAL USER INFO + SERVER TOKEN SECRET IN EACH USER'S TOKENS
3. GENRATE SERVER TOKEN SECRET FOR BOTH ACCES AND REQUEST, THEN STORE THEM AS ENVIRONMENT VARIABLES: node, 
4. THEN, SEND THEM VIA:

-access web token
 =expires quickly - 15 mins
 =granted once authentication is made
 =has to get a new access token using refresh token when it expires
 =sent in json
 =stored in memory, NOT COOKIE or LOCAL STORAGE -> accessible to hackers

-refresh web token
 =lasts longer -> 7 days
 =granted after authentication
 =sent in COOKIE, http only, https
 =this cookie is sent to server when user wants to request for new access token

middleware->verifyJWT->
assume has access token, handle incoming request, verify access token,
decompile header's authorization to get username and roles, put them as request body, to allow database operation tasks
(apply this middleware to all routes tht requires username and role, noteroutes and authroutes)

---
skipped lesson 9-12

---

deploy: 
frontend
disable react dev tools
npm i @fvilers/disable-react-devtools

import {disableReactDevTools} from '@fvilers/disable-react-devtools';

if (process.env.NODE_ENV === 'production') {
  disableReactDevTools();
}

now, change base url for all request to backend, from local host to ur backend domain name

const baseQuery = fetchBaseQuery({
    baseUrl: 'https://daveb.onrender.com',

thts it, deploy on render

backend:
changed allowed origins to frontend deployed website domain

add environment variables to render while deploying since it cant be revealed to public
