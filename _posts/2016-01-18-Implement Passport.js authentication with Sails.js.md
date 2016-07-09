---
layout: post

#event information
title:  "Implement Passportjs authentication with Sailsjs"
cover: "img/779699537035500360-1.jpg"
date:   2016-01-26
start_time: "10:00"

#event organiser details
organiser: "Lucas Gatsas"

---






<h2 class="section-heading">sailsjs, passportjs, nodejss</h2>

Step 1: dependencies
Add these dependencies to the yourproject/package.json file:



<pre class=" language-javascript"><code class=" language-javascript"><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
  <span class="token string">"dependencies"</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
    <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
    <span class="token string">"bcrypt"</span><span class="token punctuation">:</span> <span class="token string">"~0.8.0"</span><span class="token punctuation">,</span>
    <span class="token string">"passport"</span><span class="token punctuation">:</span> <span class="token string">"~0.2.1"</span><span class="token punctuation">,</span>
    <span class="token string">"passport-local"</span><span class="token punctuation">:</span> <span class="token string">"~1.0.0"</span><span class="token punctuation">,</span>
    <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
   <span class="token punctuation">}</span>
<span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> 
</code>
</pre>
Open the terminal, go to your project folder and run:

[sudo] npm install



Step 2: create user model
To create a user model run the following command:

sails generate api user

This will automatically create a model and a controller. You can find the model at yourproject/api/models/User.js. Let's make that file look like this:



<pre class=" language-javascript"><code class=" language-javascript"><span class="token keyword">var</span> bcrypt <span class="token operator">=</span> <span class="token function">require</span><span class="token punctuation">(</span><span class="token string">'bcrypt'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

module<span class="token punctuation">.</span>exports <span class="token operator">=</span> <span class="token punctuation">{</span>
    attributes<span class="token punctuation">:</span> <span class="token punctuation">{</span>
        email<span class="token punctuation">:</span> <span class="token punctuation">{</span>
            type<span class="token punctuation">:</span> <span class="token string">'email'</span><span class="token punctuation">,</span>
            required<span class="token punctuation">:</span> <span class="token keyword">true</span><span class="token punctuation">,</span>
            unique<span class="token punctuation">:</span> <span class="token keyword">true</span>
        <span class="token punctuation">}</span><span class="token punctuation">,</span>
        password<span class="token punctuation">:</span> <span class="token punctuation">{</span>
            type<span class="token punctuation">:</span> <span class="token string">'string'</span><span class="token punctuation">,</span>
            minLength<span class="token punctuation">:</span> <span class="token number">6</span><span class="token punctuation">,</span>
            required<span class="token punctuation">:</span> <span class="token keyword">true</span>
        <span class="token punctuation">}</span><span class="token punctuation">,</span>
        toJSON<span class="token punctuation">:</span> <span class="token keyword">function</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">var</span> obj <span class="token operator">=</span> <span class="token keyword">this</span><span class="token punctuation">.</span><span class="token function">toObject</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">delete</span> obj<span class="token punctuation">.</span>password<span class="token punctuation">;</span>
            <span class="token keyword">return</span> obj<span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span><span class="token punctuation">,</span>
    beforeCreate<span class="token punctuation">:</span> <span class="token keyword">function</span><span class="token punctuation">(</span>user<span class="token punctuation">,</span> cb<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        bcrypt<span class="token punctuation">.</span><span class="token function">genSalt</span><span class="token punctuation">(</span><span class="token number">10</span><span class="token punctuation">,</span> <span class="token keyword">function</span><span class="token punctuation">(</span>err<span class="token punctuation">,</span> salt<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            bcrypt<span class="token punctuation">.</span><span class="token function">hash</span><span class="token punctuation">(</span>user<span class="token punctuation">.</span>password<span class="token punctuation">,</span> salt<span class="token punctuation">,</span> <span class="token keyword">function</span><span class="token punctuation">(</span>err<span class="token punctuation">,</span> hash<span class="token punctuation">)</span> <span class="token punctuation">{</span>
                <span class="token keyword">if</span> <span class="token punctuation">(</span>err<span class="token punctuation">)</span> <span class="token punctuation">{</span>
                    console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span>err<span class="token punctuation">)</span><span class="token punctuation">;</span>
                    <span class="token function">cb</span><span class="token punctuation">(</span>err<span class="token punctuation">)</span><span class="token punctuation">;</span>
                <span class="token punctuation">}</span> <span class="token keyword">else</span> <span class="token punctuation">{</span>
                    user<span class="token punctuation">.</span>password <span class="token operator">=</span> hash<span class="token punctuation">;</span>
                    <span class="token function">cb</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                <span class="token punctuation">}</span>
            <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code>
</pre>



Step 3: create AuthController
Run this command from your terminal: 
sails generate controller auth

Then go to yourproject/api/controllers/AuthController.js and alter it in this way:




<pre class=" language-javascript"><code class=" language-javascript"><span class="token keyword">var</span> passport <span class="token operator">=</span> <span class="token function">require</span><span class="token punctuation">(</span><span class="token string">'passport'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

module<span class="token punctuation">.</span>exports <span class="token operator">=</span> <span class="token punctuation">{</span>

    _config<span class="token punctuation">:</span> <span class="token punctuation">{</span>
        actions<span class="token punctuation">:</span> <span class="token keyword">false</span><span class="token punctuation">,</span>
        shortcuts<span class="token punctuation">:</span> <span class="token keyword">false</span><span class="token punctuation">,</span>
        rest<span class="token punctuation">:</span> <span class="token keyword">false</span>
    <span class="token punctuation">}</span><span class="token punctuation">,</span>

    login<span class="token punctuation">:</span> <span class="token keyword">function</span><span class="token punctuation">(</span>req<span class="token punctuation">,</span> res<span class="token punctuation">)</span> <span class="token punctuation">{</span>

        passport<span class="token punctuation">.</span><span class="token function">authenticate</span><span class="token punctuation">(</span><span class="token string">'local'</span><span class="token punctuation">,</span> <span class="token keyword">function</span><span class="token punctuation">(</span>err<span class="token punctuation">,</span> user<span class="token punctuation">,</span> info<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token punctuation">(</span>err<span class="token punctuation">)</span> <span class="token operator">||</span> <span class="token punctuation">(</span><span class="token operator">!</span>user<span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
                <span class="token keyword">return</span> res<span class="token punctuation">.</span><span class="token function">send</span><span class="token punctuation">(</span><span class="token punctuation">{</span>
                    message<span class="token punctuation">:</span> info<span class="token punctuation">.</span>message<span class="token punctuation">,</span>
                    user<span class="token punctuation">:</span> user
                <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
            req<span class="token punctuation">.</span><span class="token function">logIn</span><span class="token punctuation">(</span>user<span class="token punctuation">,</span> <span class="token keyword">function</span><span class="token punctuation">(</span>err<span class="token punctuation">)</span> <span class="token punctuation">{</span>
                <span class="token keyword">if</span> <span class="token punctuation">(</span>err<span class="token punctuation">)</span> res<span class="token punctuation">.</span><span class="token function">send</span><span class="token punctuation">(</span>err<span class="token punctuation">)</span><span class="token punctuation">;</span>
                <span class="token keyword">return</span> res<span class="token punctuation">.</span><span class="token function">send</span><span class="token punctuation">(</span><span class="token punctuation">{</span>
                    message<span class="token punctuation">:</span> info<span class="token punctuation">.</span>message<span class="token punctuation">,</span>
                    user<span class="token punctuation">:</span> user
                <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

        <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">(</span>req<span class="token punctuation">,</span> res<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">,</span>

    logout<span class="token punctuation">:</span> <span class="token keyword">function</span><span class="token punctuation">(</span>req<span class="token punctuation">,</span> res<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        req<span class="token punctuation">.</span><span class="token function">logout</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        res<span class="token punctuation">.</span><span class="token function">redirect</span><span class="token punctuation">(</span><span class="token string">'/'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code>
</pre>



Step 4: create login and signup views
Create this file yourproject/views/signup.ejs:


<pre class=" language-markup"><code class=" language-markup"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>h1</span><span class="token punctuation">&gt;</span></span>Signup<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>h1</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>form</span> <span class="token attr-name">method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>POST<span class="token punctuation">"</span></span> <span class="token attr-name">action</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>/user<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>input</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>email<span class="token punctuation">"</span></span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>email<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>input</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>password<span class="token punctuation">"</span></span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>password<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>input</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>submit<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>submit<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>form</span><span class="token punctuation">&gt;</span></span>
</code>
</pre>



And then this yourproject/views/login.ejs:



<pre class=" language-markup"><code class=" language-markup"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>h1</span><span class="token punctuation">&gt;</span></span>Login<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>h1</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>form</span> <span class="token attr-name">method</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>POST<span class="token punctuation">"</span></span> <span class="token attr-name">action</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>/login<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>input</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>email<span class="token punctuation">"</span></span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>email<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>input</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>password<span class="token punctuation">"</span></span> <span class="token attr-name">name</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>password<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>input</span> <span class="token attr-name">type</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>submit<span class="token punctuation">"</span></span> <span class="token attr-name">value</span><span class="token attr-value"><span class="token punctuation">=</span><span class="token punctuation">"</span>submit<span class="token punctuation">"</span></span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>form</span><span class="token punctuation">&gt;</span></span>
</code>
</pre>


These forms look the same, besides the action.






Step 5: configuring the routes
Let's set up our routes in yourproject/config/routes.js like this:





<pre class=" language-javascript"><code class=" language-javascript">module<span class="token punctuation">.</span>exports<span class="token punctuation">.</span>routes <span class="token operator">=</span> <span class="token punctuation">{</span>
  <span class="token string">'/'</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
    view<span class="token punctuation">:</span> <span class="token string">'homepage'</span>
  <span class="token punctuation">}</span><span class="token punctuation">,</span>

  <span class="token string">'get /login'</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
       view<span class="token punctuation">:</span> <span class="token string">'login'</span>
  <span class="token punctuation">}</span><span class="token punctuation">,</span>

  <span class="token string">'post /login'</span><span class="token punctuation">:</span> <span class="token string">'AuthController.login'</span><span class="token punctuation">,</span>

  <span class="token string">'/logout'</span><span class="token punctuation">:</span> <span class="token string">'AuthController.logout'</span><span class="token punctuation">,</span>

  <span class="token string">'get /signup'</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
    view<span class="token punctuation">:</span> <span class="token string">'signup'</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code>
</pre>




If you notice we are leveraging HTTP verbs, we have two login actions but different routes.



Step 6: configuring Express Middleware
Since we are using Passport we have to customize our middleware in yourproject/config/http.js:




<pre class=" language-javascript"><code class=" language-javascript">module<span class="token punctuation">.</span>exports<span class="token punctuation">.</span>http <span class="token operator">=</span> <span class="token punctuation">{</span>
   middleware<span class="token punctuation">:</span> <span class="token punctuation">{</span>

    passportInit    <span class="token punctuation">:</span> <span class="token function">require</span><span class="token punctuation">(</span><span class="token string">'passport'</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">initialize</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span>
    passportSession <span class="token punctuation">:</span> <span class="token function">require</span><span class="token punctuation">(</span><span class="token string">'passport'</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">session</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span>

     order<span class="token punctuation">:</span> <span class="token punctuation">[</span>
            <span class="token string">'startRequestTimer'</span><span class="token punctuation">,</span>
            <span class="token string">'cookieParser'</span><span class="token punctuation">,</span>
            <span class="token string">'session'</span><span class="token punctuation">,</span>
            <span class="token string">'passportInit'</span><span class="token punctuation">,</span>     
            <span class="token string">'passportSession'</span><span class="token punctuation">,</span> 
            <span class="token string">'myRequestLogger'</span><span class="token punctuation">,</span>
            <span class="token string">'bodyParser'</span><span class="token punctuation">,</span>
            <span class="token string">'handleBodyParserError'</span><span class="token punctuation">,</span>
            <span class="token string">'compress'</span><span class="token punctuation">,</span>
            <span class="token string">'methodOverride'</span><span class="token punctuation">,</span>
            <span class="token string">'poweredBy'</span><span class="token punctuation">,</span>
            <span class="token string">'router'</span><span class="token punctuation">,</span>
            <span class="token string">'www'</span><span class="token punctuation">,</span>
            <span class="token string">'favicon'</span><span class="token punctuation">,</span>
            <span class="token string">'404'</span><span class="token punctuation">,</span>
            <span class="token string">'500'</span>
          <span class="token punctuation">]</span><span class="token punctuation">,</span>
     <span class="token punctuation">}</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code>
</pre>


Into the order array we added passportInit and passportSession after the session element. The name of this array is quite self-explanatory, the elements must be ordered this way.



Step 7: define Passport local strategy for authentication
Into yourproject/config/ folder let's create a new file called passport.js:



<pre class=" language-javascript"><code class=" language-javascript"><span class="token keyword">var</span> passport <span class="token operator">=</span> <span class="token function">require</span><span class="token punctuation">(</span><span class="token string">'passport'</span><span class="token punctuation">)</span><span class="token punctuation">,</span>
LocalStrategy <span class="token operator">=</span> <span class="token function">require</span><span class="token punctuation">(</span><span class="token string">'passport-local'</span><span class="token punctuation">)</span><span class="token punctuation">.</span>Strategy<span class="token punctuation">,</span>
bcrypt <span class="token operator">=</span> <span class="token function">require</span><span class="token punctuation">(</span><span class="token string">'bcrypt'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

passport<span class="token punctuation">.</span><span class="token function">serializeUser</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>user<span class="token punctuation">,</span> done<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token function">done</span><span class="token punctuation">(</span><span class="token keyword">null</span><span class="token punctuation">,</span> user<span class="token punctuation">.</span>id<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

passport<span class="token punctuation">.</span><span class="token function">deserializeUser</span><span class="token punctuation">(</span><span class="token keyword">function</span><span class="token punctuation">(</span>id<span class="token punctuation">,</span> done<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    User<span class="token punctuation">.</span><span class="token function">findOne</span><span class="token punctuation">(</span><span class="token punctuation">{</span> id<span class="token punctuation">:</span> id <span class="token punctuation">}</span> <span class="token punctuation">,</span> <span class="token keyword">function</span> <span class="token punctuation">(</span>err<span class="token punctuation">,</span> user<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token function">done</span><span class="token punctuation">(</span>err<span class="token punctuation">,</span> user<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

passport<span class="token punctuation">.</span><span class="token function">use</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">LocalStrategy</span><span class="token punctuation">(</span><span class="token punctuation">{</span>
    usernameField<span class="token punctuation">:</span> <span class="token string">'email'</span><span class="token punctuation">,</span>
    passwordField<span class="token punctuation">:</span> <span class="token string">'password'</span>
  <span class="token punctuation">}</span><span class="token punctuation">,</span>
  <span class="token keyword">function</span><span class="token punctuation">(</span>email<span class="token punctuation">,</span> password<span class="token punctuation">,</span> done<span class="token punctuation">)</span> <span class="token punctuation">{</span>

    User<span class="token punctuation">.</span><span class="token function">findOne</span><span class="token punctuation">(</span><span class="token punctuation">{</span> email<span class="token punctuation">:</span> email <span class="token punctuation">}</span><span class="token punctuation">,</span> <span class="token keyword">function</span> <span class="token punctuation">(</span>err<span class="token punctuation">,</span> user<span class="token punctuation">)</span> <span class="token punctuation">{</span>
      <span class="token keyword">if</span> <span class="token punctuation">(</span>err<span class="token punctuation">)</span> <span class="token punctuation">{</span> <span class="token keyword">return</span> <span class="token function">done</span><span class="token punctuation">(</span>err<span class="token punctuation">)</span><span class="token punctuation">;</span> <span class="token punctuation">}</span>
      <span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token operator">!</span>user<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token function">done</span><span class="token punctuation">(</span><span class="token keyword">null</span><span class="token punctuation">,</span> <span class="token keyword">false</span><span class="token punctuation">,</span> <span class="token punctuation">{</span> message<span class="token punctuation">:</span> <span class="token string">'Incorrect email.'</span> <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
      <span class="token punctuation">}</span>

      bcrypt<span class="token punctuation">.</span><span class="token function">compare</span><span class="token punctuation">(</span>password<span class="token punctuation">,</span> user<span class="token punctuation">.</span>password<span class="token punctuation">,</span> <span class="token keyword">function</span> <span class="token punctuation">(</span>err<span class="token punctuation">,</span> res<span class="token punctuation">)</span> <span class="token punctuation">{</span>
          <span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token operator">!</span>res<span class="token punctuation">)</span>
            <span class="token keyword">return</span> <span class="token function">done</span><span class="token punctuation">(</span><span class="token keyword">null</span><span class="token punctuation">,</span> <span class="token keyword">false</span><span class="token punctuation">,</span> <span class="token punctuation">{</span>
              message<span class="token punctuation">:</span> <span class="token string">'Invalid Password'</span>
            <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
          <span class="token keyword">var</span> returnUser <span class="token operator">=</span> <span class="token punctuation">{</span>
            email<span class="token punctuation">:</span> user<span class="token punctuation">.</span>email<span class="token punctuation">,</span>
            createdAt<span class="token punctuation">:</span> user<span class="token punctuation">.</span>createdAt<span class="token punctuation">,</span>
            id<span class="token punctuation">:</span> user<span class="token punctuation">.</span>id
          <span class="token punctuation">}</span><span class="token punctuation">;</span>
          <span class="token keyword">return</span> <span class="token function">done</span><span class="token punctuation">(</span><span class="token keyword">null</span><span class="token punctuation">,</span> returnUser<span class="token punctuation">,</span> <span class="token punctuation">{</span>
            message<span class="token punctuation">:</span> <span class="token string">'Logged In Successfully'</span>
          <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
  <span class="token punctuation">}</span>
<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code>
</pre>


Step 8: add a new policy
Let's create a new policy isAuthenticated.js into the yourproject/api/policies/ folder:




<pre class=" language-javascript"><code class=" language-javascript">module<span class="token punctuation">.</span>exports <span class="token operator">=</span> <span class="token keyword">function</span><span class="token punctuation">(</span>req<span class="token punctuation">,</span> res<span class="token punctuation">,</span> next<span class="token punctuation">)</span> <span class="token punctuation">{</span>
   <span class="token keyword">if</span> <span class="token punctuation">(</span>req<span class="token punctuation">.</span><span class="token function">isAuthenticated</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">return</span> <span class="token function">next</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    <span class="token keyword">else</span><span class="token punctuation">{</span>
        <span class="token keyword">return</span> res<span class="token punctuation">.</span><span class="token function">redirect</span><span class="token punctuation">(</span><span class="token string">'/login'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code>
</pre>



Briefly, we are telling Sails what to do if a user is not authenticated, then we can bind this policy to any of the controllers in the app.




Step 9: bind the policy to a controller
Let's create a controller and a model just for testing what we have done. I am going to call this api post.

sails generate api post

Then let's bind the policy to the PostController by updating yourproject/config/policies.js:




<pre class=" language-javascript"><code class=" language-javascript">module<span class="token punctuation">.</span>exports<span class="token punctuation">.</span>policies <span class="token operator">=</span> <span class="token punctuation">{</span>

   <span class="token string">'*'</span><span class="token punctuation">:</span> <span class="token keyword">true</span><span class="token punctuation">,</span>

  <span class="token string">'PostController'</span><span class="token punctuation">:</span> <span class="token punctuation">{</span>
    <span class="token string">'*'</span><span class="token punctuation">:</span> <span class="token string">'isAuthenticated'</span>
  <span class="token punctuation">}</span><span class="token punctuation">,</span>

<span class="token punctuation">}</span><span class="token punctuation">;</span>
</code>
</pre>


Now the 'GET /post' route (and all the other routes automatically generated for this controller ) is available only to authenticated users.


Step 10: run a test
Lift your application 
sails lift 
And then go and play with these routes:

http://localhost:1337/post - the very first time it should redirect you to the login page

go to http://localhost:1337/signup for signing up

go to http://localhost:1337/login for logging in

now you should be able to access this page http://localhost:1337/post, even though it's going to show you just an empty array (there aren't any posts yet)

go to http://localhost:1337/logout for logging out



[→ Get the repository on Github](https://github.com/iliketomatoes/passport_with_sails) 


I would very much appreciate your comments, especially if you find an error or you need a further explanation.
References:

[→ Sails.js](http://sailsjs.org/#/documentation/concepts) 
[→ Passport.js](http://passportjs.org/) 
[→ Waterline](hhttps://github.com/balderdashy/waterline) 






<blockquote>When in doubt, use brute force.

        — Ken Thompson

</blockquote>



