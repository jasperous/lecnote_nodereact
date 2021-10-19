#### 1. 개요

* To Do 애플리케이션을 만들어보면서, NodeJS, Database 에 대한 내용을 배움.
* API 개발 및 배포를 포함함.



#### 2. Server

* 서버는 클라이언트 요청에 따라 데이터를 보내주는 역할.

* 따라서, 서버 개발자는 

  > if (누군가 네이버 웹툰 메인 페이지 요청을 하면)
  >
  > ​	{ 네이버 웹툰 메인페이지의 HTML 파일을 보내줌 } 

  과 같은 코드를 짜게 됨.

  * 서버 개발자는 이 같은 코드를 구성하게 됨.

* 사용자가 웹 서버에 정식으로 요청할 수 있는 4 가지 방법(각 예시).

  1. GET : 웹 페이지를 읽을 때
  2. POST : 글을 작성하거나, 생성 또는 로그인할 때 등
  3. PUT : 글이나 댓글 수정
  4. DELETE : 글이나 댓글 삭제



#### 3. Node.js

* JavaScript 를 웹브라우저 말고 로컬PC 에서도 실행시킬 수 있는 RunTime(실행창).
* Node.js 를 실행시키면, JavaScript Shell 이 나타나며, 입력하는대로 V8 엔진으로 바로 해석.
* 성능이 좋은 V8 엔진을 이용하여 개발자들이 Node 를 활용한 웹 서버를 만들기 시작함.



#### 4. Node.js : Event Driven, Non-Blocking I/O

* Node.js 는 요청을 다 받고, 처리가 빨리 된 것부터 반환. 그렇게 설계된 Runtime.
* 동작 원리는 `Node.js Event Loop` 에 대해서 검색.
* Node.js 의 Non-Blocking I/O 가 강점을 보이는 분야.
  * SNS, 채팅 웹 서비스와 같이 한번에 많은 요청을 받는 서버.
  * `일반 서버`의 경우 1초에 여러개의 요청이 발생할 경우, 다음과 같은 방법으로 해결.
    1. Scaling : 똑같은 서버를 물리적으로 여러대 만들어서 사용.
    2. Multi-Threading 을 통해 CPU 효율을 향상.
    3. Node.js 처럼 Non-Blocking 형태로 코드를 작성.
  * Node.js 는 Non-Blocking 이지만, 이에 추가적으로 Scaling 또는 Multi-Threading 을 할 수 있음.
* 연산(처리)이 빨리 끝내는 것 부터 반환한다고 해서, 연산 속도가 빠른 것은 아님.
  * 예를 들어, 이미지 처리와 같은 연산은 실행 시간이 한참 걸림.
  * 또는 피보나치 수열 계산과 같은 연산을 요청하면 시간이 오래 걸림.



>**Node.js 로 자주 만드는 애플리케이션**
>
>1. SNS, 채팅 서비스 등.
>   * 위 같은 서비스는 요청 처리에 힘든 수학적인 연산을 요구하지 않음.
>   * 대부분, 글자 전달 (또는 여러명에게 전달) 등의 요청만 함.
>   * 따라서, 대량이지만 간단한 요청만 있는 SNS, 채팅 서비스에 많이 사용됨.
>2. 스타트업, 프로토타입 만들기에 사용됨.
>   * 빠른 prototype 서비스를 만들고 싶을 경우 사용됨.
>   * Node.js 는 초보자에게도 쉬우며, 템플릿 몇가지만 복사해도 몇시간 안에 서버를 만들 수 있음.
>   * 갑자기 사용자가 터져서 대량의 요청이 들어와도 나름 감당 가능.
>   * JavaScript 문법으로 Front, Back-end 모두 사용 가능
>##### Node.js 의 단점
>
>* 연산 속도가 떨어질 수 있음.
>* 수학 연산이나 이미지 처리 같은 라이브러리가 부족할 수 있음.
>* Node.js 에서 제공하는 Non-Blocking 처리 방식은 다른 언어에서도 비슷하게 구현 가능.



#### 5. Node.js 와 Express 라이브러리 설치

* 프로젝트 폴더 `node-react` 생성.

* 프로젝트 폴더 초기화.

  ```bash
  $ yarn init
  ```

* init 시 entry point 설정.

  ```bash
  question entry point : server.js
  ```

* Express 라이브러리 설치

  * Express 는 서버를 쉽게 짤 수 있게 도와주는 라이브러리.
  
  ```bash
  $ yarn add express
  or npm install express
  ```
  
  

#### 6. GET 요청

1. Server.js 작성.

   * 서버를 띄우기 위한 기본 템플릿 작성.

   ```jsx
   // server.js
   // express 라이브러리 첨부와 사용.
   const express = require('express');
   const app = express();
   // 원하는 포트 번호에 서버를 오픈하는 문법.
   app.listen(8080, ()=>{
     console.log("Listening on 8080");
   })
   ```


> **Port**
>
> * 컴퓨터는 외부 컴퓨터와 통신할 수 있게 설계되어 있는데, 통신할 수 있는 구멍이 약 60000개 정도 있음.
> * 이를 포트라고 하는데, 외부와 접속하기 위해선 이 포트를 열어줘야 함.
> * 이처럼 위에서는 8080 번 포트를 이용하여 서버를 띄운것일 뿐.

2. GET 요청을 하는 부분을 생성.

   * `/pet` 경로로 접속하면 안내 메세지를 띄우는 서버를 생성.

   ```jsx
   // server.js
   app.get('/pet', (request, response)=>{
     response.send('Hello Pet');
   });
   ```

   * 콜백 함수의 파라미터 (request, response) 를 임의로 입력하니 에러 발생하였음.

3. 실행 (또는 nodemon 사용 - 다음 목차)

   ```bash
   $ node server.js
   ```




> **Server.js**
>
> * Node.js 는 모듈 시스템을 구축하고 있음.
>
>   * 모듈 시스템이란, 필요한 것만 불러오는 것을 의미.
>
> * http 서버가 필요하므로, http 모듈을 require 메소드를 통해 불러와 http 변수에 저장함.
>
>   * npm 패키지와 비슷한 개념.
>   * 단, http 패키지는 Node.js 에서 기본적으로 제공하는 패키지이므로 설치할 필요가 없음.
>   * 서버가 실행된 후의 동작을 콜백 함수로 등록.
>   * http 패키지의 createServer 메소드의 인자로 Callback 함수를 넣어주고, #### 번 포트에 listen(연결) 시켜줌.
>
> * 만약 8080 번 포트 대신에, 일반 주소를 사용하고 싶을 경우 80 번 포트로 listen 하면 됨.
>
>   * 80 번 포트는 기본이기 때문에 :80 을 쓰지 않아도 됨.
>   * 네이버같은 사이트도 80 번 포트를 사용하고 있기 때문에 www.naver.com:80 이렇게 입력하지 않는 것.
>   * 대신 80 포트는 이미 다른 프로그램에서 사용하고 있는 경우가 많아서, 충돌이 자주 발생.
>   * 따라서, 자기 컴퓨터로 할 때는 80 외의 포트를 사용하고, 서버에 올릴 때는 80 포트로 올리는 것이 좋음.
>
>   ```jsx
>   const http = require('http');
>   console.log(http);
>   http.createServer((request, response)=>{
>       console.log("Server Start");
>   }).listen(8080);
>   ```


> **Request, Response**
>
> * createServer 메소드의 Callback 함수 매개변수로 request / response 를 사용.
>
>   * request / response 에는 header / body 가 들어 있음.
>  * header 에는 req / res 에 대한 정보(종류, 크기, 캐시 여부 등)이 들어있으며,
>   * body 에는 실제로 주고 받고자 하는 내요이 들어 있음.
>
> * request 는 요청을 담당하며, 서버로 보내는 요청에 대한 정보가 들어있음.
> 
>   * 주소창에 localhost:8080 이라고 친 것도, 서버에 그 주소에 해당하는 정보를 요청한 것.
>   * 서버는 항상 대기중이다가, request 가 들어올 때 반응하고, 그 처리 결과를 response 객체로 반환.
>
> * response 는 클라이언트(브라우저)로 돌려줄 응답을 담당.
>
>   * 어떤 정보를 보내고 싶다면, response 객체를 이용.
>   * response 가 없다면 브라우저에 아무것도 전달되지 않음.
>   * response 는 대부분 페이지를 내보내지만, JSON / AJAX  또는 이미지 같은 것도 보낼 수 있음.
> 
>* request → 서버 처리 → response 순서임을 기억.
> 
>   ```jsx
>   const http = require('http');
>   // console.log(http);
>   http.createServer((request, response)=>{
>       return request.on('error', (err)=>{
>           console.error(err);
>      }).on('data', (data)=>{
>           console.log(data);
>      }).on('end', ()=>{
>           response.on('error', (err)=>{
>              console.error(err);
>           });
>           response.statusCode = 200;
>           response.setHeader('Content-Type', 'text/plain');
>          response.write('hi!\n');
>           response.end('the end!'); 
>      })
>   }).listen(8080);
>   ```

> **Express Framework**
>
> * Express 외에도 Koa, Hapi 등의 프레임워크가 있으나, Express 가 가장 대중적.
>
> * 서버의 페이지 개수가 늘어나고, 사이즈가 커지면 코드가 복잡해지는데, Express 는 코드의 양도 줄여주고, 유지보수도 쉽게 만들어주기 때문에 사용.
>
> * http, path, fs, url 등은 Node.js 에서 기본적으로 제공하는 패키지이므로 설치할 필요가 없으나, Express 는 패키지를 별도 설치해야 함.
>
>   ```bash
>   $ yarn add express
>   ```
>
> * **http 모듈은 express 에서 내부적으로 처리하기 때문에, 더이상 사용하지 않아도 됨.**
>
>   * `const app = express()` 를 사용해서 만든 Express app 객체로 모든 서버의 일을 처리함.
>   * 마지막의 `app.listen()` 메소드로 `http.listen()` 과 같이 8080 포트에서 요청을 대기함. 
>
>   ```jsx
>   const express = require('express');
>   const app = express();
>   app.get('/', (request, response)=>{
>       response.send('Hello World');
>   });
>   app.listen(8080, ()=>{
>       console.log('Express App on port 8080');
>   })
>   ```

> **Routing**
>
> * `app.use` 는 미들웨어를 사용하는 부분.
>
> * `express.static` 은 정적 파일들이 있는 위치를 지정하는 부분.
>
> * `app.get('/about', (req, res))` 는 REST API 로써 라우팅을 처리하는 부분.
>
>   ```jsx
>   const express = require('express');
>   const path = require('path');
>    const app = express();
>   app.use(express.static(path.join(__dirname, 'html')));
>    app.get('/', (req, res) => {
>     res.sendFile(path.join(__dirname, 'html', 'main.html'));
>   });
>    app.get('/about', (req, res) => {
>     res.sendFile(path.join(__dirname, 'html', 'about.html'));
>   });
>   app.listen(8080, () => {
>     console.log('Express App on port 8080!');
>   });
>   ```



#### 7. 서버에서 HTML 파일 전송하기 & Nodemon 으로 자동화

* 특정 경로에 GET 요청을 했을 때, HTML 파일을 전송.

* `index.html` 파일을 생성하고 임의로 스크립트를 생성.

* server.js 에서 다음과 같이 작성.

  ```js
  // server.js
  app.get("/", (request, response)=>{
    response.sendFile(__dirname + "/index.html")
  });
  ```

  * 클라이언트가 `/` 경로로 접속할 경우(슬래시 하나만 있는 것은 홈페이지를 의미), server.js 와 같은 경로에 있는 /index.html 파일을 보냄.
  * `__dirname` 은 현재 파일의 경로를 의미.



> **React 프로젝트 파일을 보낼 경우**
>
> * React 프로젝트를 서비스할 경우, node 와 React 를 연동해야 함.
>
> * 프로젝트가 완료되면 Build 를 하는데, 해당 Build 경로에는 html, css, js 파일만 남게됨.
>
> * 생성되는 html 파일을 연동시켜 줌.
>
>   ```jsx
>   // server.js
>   app.use(express.static(path.join(__dirname, 'react-project/build')));
>   app.get('/', function (request, response) {
>     response.sendFile(path.join(__dirname, '/react-project/build/index.html'));
>   });
>   ```

> **Nodemon 을 이용한 서버 실행 자동화**
>
> * 이전에는 server.js 를 수정한 후, 실행하기 위해서 서버를 껐다 켜는 작업을 반복했음.
>
> * 이런 번거로움을 줄이기 위해 nodemon 을 설치하여 서버를 껐다 켜는 작업을 자동화 시키기.
>
>   ```bash
>   $ yarn add global nodemon 
>   ```
>
>   ```bash
>   $ yarn nodemon server.js
>   ```



#### 8. Bootstrap 을 이용한 빠른 UI 개발

* Bootstrap 을 이용한 UI 를 개발.
  * Bootstrap Input Form 템플릿을 이용하여 입력란 생성.
* `write.html` 작성.
  * server.js 에서 `/write` 경로에 대해 HTML write.html 을 불러오는 메소드 작성.

> **React 를 이용한 FE 개발**
>
> * 라이브러리 설치.
>
>   PWD : node-react/client
>
>   ```bash
>   $ yarn add react-bootstrap bootstrap
>   ```
>
>   ```bash
>   $ yarn add react-router-dom
>   ```
>
> * Write.js 컴포넌트 생성.
>
>   * App.js 컴포넌트에 import.
>   * /write 경로 Route.
>   * Write.js 컴포넌트에 React-BootStrap 라이브러리로 UI 생성.
>
> * React Project Build.
>
> * Server.js 일부 수정.
>
>   ```jsx
>   app.use(express.static(path.join(__dirname, 'react-project/build')));
>   app.get("*", (req, res)=>{
>       res.sendFile(__dirname + "/react-project/build/index.html")
>   })
>   ```



#### 9. POST 요청 : 입력 폼에 작성한 데이터를 서버에 전송하는 법

> ##### Callback Function
>
> * JavaScript 에서는 무엇인가 순차적으로 실행하기 위해서 콜백함수를 사용함.
> * 본 예제에서는, 클라이언트가 write 경로로 요청할 경우, 특정 코드를 실행하는 것을 작성하기 위해 사용.

1. form 태그 세팅

   * HTML form 태그 내의 input 에 입력한 데이터를 서버로 전송하기 위함.
   * form 태그에 여러가지 전송 방법을 명시해주면 됨.

   ```jsx
   // Write.js
   function InputForm(){
     return(
       <Form>
         <Form.Group className="mb-3">
           <Form.Label>Todo</Form.Label>
           <Form.Control type="text" name="title"/>
         </Form.Group>
         <Form.Group className="mb-3">
           <Form.Label>Due Date</Form.Label>
           <Form.Control type="text" name="date"/>
         </Form.Group>
         <Button variant="primary" type="submit">Save</Button>
       </Form>
     )
   }
   ```

   * Save 버튼을 누를 경우, /add 경로로 POST 요청하는 form.
   * action 은 어떤 경로로 요청할 것인지 정하는 부분.
   * method 는 GET/POST 중 어떤 요청을 할 것인지 정하는 부분.
   * (중요) input 태그에는 name 속성을 이용하여 각각의 input 이름을 설정. 
     * form 을 전송할 때, 이름이 없을 경우 서버에 어떤 input 인지 구분이 되지 않기 때문.

2. form 데이터를 서버로 전송 (body-parser 설치).

   * 1번만 작성해도 데이터가 서버로 잘 전송되지만, 
   * body-parser 라이브러리가 있어야 서버로 보낸 데이터 처리가 쉽게 가능함.

   ```bash
   // PWD : /node-react
   $ yarn add body-parser
   ```

   * server.js 에 다음 코드를 작성.
     * 21년 이후의 프로젝트는 express 라이브러리에 bodyparser 포함되어 있으므로 아래를 사용.

   ```js
   // server.js
   // (21' 이전 프로젝트)
   const bodyParser = require('body-parser');
   app.use(bodyParser.urlencoded({extend:true}));
   // (21' 이후 프로젝트)
   app.use(express.urlencoded({extend:true}));
   ```

3. 서버로 데이터를 전송하기

   * app.get 과 비슷하게 post method 를 사용.
   * 이는 사용자가 /add 경로로 POST  요청을 하면, Callback 함수의 내용을 실행하는 의미.
   
   ```js
   app.post('/add', ()=>{
     console.log(request.body);
     response.send("Sent");
   })
   ```

> **React Axios**
>
> * React 프로젝트의 경우, Axios 라이브러리를 이용한 POST method 를 적용.
>
>   ```jsx
>   // react-project/src/Write.js
>   function InputForm(){
>       let [todo, changeTodo] = useState("");
>       let [due, changeDue] = useState("");
>       let todoHandler = (e)=>{
>           e.preventDefault();
>           changeTodo(e.target.value);
>       };
>       let dueHandler = (e)=>{
>           e.preventDefault();
>           changeDue(e.target.value);
>       };
>       let saveHandler = (e)=>{
>           e.preventDefault();
>           let body = {
>               todo : todo,
>               due : due
>           };
>           axios.post("http://localhost:8080/add", body);
>       }
>       return(
>           <Form onSubmit={saveHandler}>
>           <Form.Group className="mb-3">
>               <Form.Label>Todo</Form.Label>
>               <Form.Control 
>                   type="text" 
>                   placeholder="Todo" 
>                   name="title"
>                   value={todo}
>                   onChange={todoHandler}
>               />
>           </Form.Group>
>           <Form.Group className="mb-3">
>               <Form.Label>Due Date</Form.Label>
>               <Form.Control 
>                   type="text" 
>                   placeholder="Until" 
>                   name="date"
>                   value={due}
>                   onChange={dueHandler}
>               />
>           </Form.Group>
>           <Button variant="primary" type="submit">
>               Save
>           </Button>
>           </Form>
>       )
>   }
>   ```
>
> * Server.js 동일.



#### 10. REST API

> ##### API (Application Programming Interface)
>
> * 서로 다른 프로그램 간에 소통할 수 있게 도와주는 통신 규약.
> * 웹 개발 시에는 웹 서버와 고객간의 소통 방법. 즉, 서버에게 요청해서 데이터를 가져오는 방법.

* REST API (Representational State Transfer)

  * 웹 API 를 작성할 때, Restful 하게 작성하도록 하는 API 디자인 명세.
  * 1~4 번 정도까지는 중요한 명세이므로 참조.

  1. Uniform Interface
     * 인터페이스는 일관성이 있어야 함.
     * 즉, 하나의 URL 로는 하나의 데이터를 가져와야 함(하나를 가져오기 위해 두 개의 URL 사용하지 말 것).
     * 간결하고 예측 가능한 URL 을 짤 수 있도록, 작명 관습을 따를 것.
  2. Client-Server 역할 분리
     * 고객에게 서버의 역할을 맡기거나, 고객에게 DB 자료를 직접 접근하게 하는 등의 코드를 작성하지 않을 것.
  3. Stateless
     * 각 요청은 독립적으로 처리되어야 함.
     * 요청1이 성공하면 요청2가 실행되는 등의 의존성 있는 코드를 작성하지 말 것.
     * 즉, 요청 하나만으로 자료를 가져오도록, 요청에 필요한 모든 정보를 충분히 실어 보낼 것.
  4. Cacheable
     * 요청을 통해 보내는 자료는 캐싱이 가능해야 함.
     * 그리고 캐싱 가능하다고 표시하거나, 캐싱 기간을 설정해주어야 함.
     * 예를 들어, 네이버에 접속할 경우 로고를 네이버 서버에서 직접 불러오지 않고, 캐싱된 하드에서 직접 불러옴.
  5. Layered System
     * 요청을 처리하는 곳, DB 에 저장하는 곳 등 여러가지 단계를 거쳐 요청을 처리해도 됨.
     * 즉, 여러개의 레이어를 거쳐 요청을 처리하게 만들어도 됨.
  6. Code on Demand
     * 서버는 고객에게 실제 실행 가능한 코드를 전송해줄 수도 있음.



























