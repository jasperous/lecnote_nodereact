#### 1. MongoDB 설정

* 프로젝트에서 생성된 데이터를 저장하기 위해 No Rel 데이터베이스인 MongoDB 를 사용.

> ##### MongoDB 설치 방법
>
> 1. 회원가입
> 2. 무료버전 Cluster 생성
> 3. 서버위치 선택
> 4. Database Access 메뉴에서 유저 계정을 생성 / [Username](paperwareceo) ,  [PW](HXD0aEjXiFOxlHYJ)
> 5. Network Access 메뉴에서 IP 를 추가
> 6. Database / Collection 만들기
>    * Cluster 는 하나의 호스팅 공간이며, 이 안에 Database 를 만들어야 데이터를 저장할 수 있음
> 7. Database Deployment 메뉴에서 Connect 버튼 > Connect your Application 후 접속 [Connection String](mongodb+srv://paperwareceo:HXD0aEjXiFOxlHYJ@cluster0.nzwqn.mongodb.net/node-react?retryWrites=true&w=majority) 복사
>    * Choose your driver and version 에서 Node.js 선택

1. 프로젝트에 MongoDB 라이브러리 설치.

   * MongoDB 호스팅 클라우드에 접속을 쉽게 도와주는 라이브러리.

   ```bash
   PWD : /node-react
   $ yarn add mongodb
   ```

2. MongoDB 불러오기.

   * server.js 상단에 다음 코드 추가.

   ```js
   const MongoClient = require('mongodb').MongoClient;
   ```

3. 코드 작성.

   * MongoDB 에 정상적으로 접속이 되었을 경우, "listening on 8080" 메세지를 띄움.

   ```js
    MongoClient.connect('Connection String', function(error, client){
     if (error) return console.log(error);
     app.listen('8080', function(){   // 서버띄우는 코드(아래 코드)를 여기로 옮기기
       console.log('listening on 8000')
     });
   })
   ```

   

#### 2. Database 에 자료 저장하는 법

* 자료 저장을 위한 Database 와 Table(MongoDB 에서는 Collection 이라 함) 을 생성.

  > Database : todoapp
  >
  > Collection : post

* server.js 에서 todoapp DB 에 접속하여, post Collection 에 데이터 등록.

  * 아래 코드는 todoapp 이라는 DB 에 접속하고, 접속했을 때 다음과 같은 콜백 함수를 실행하라는 내용.
  * 코드의 작동 방법을 이해하기 보단, 패턴 사용법을 익힐 것.

  ```js
  // server.js
  let db;
  MongoClient.connect(connnectionString, {useUnifiedTopology: true}, (error, client)=>{
    if(error){return console.log(error)}
    db = client.db('todoapp');
    app.listen(8000, ()=>{
      console.log("listening on 8000")
    });
  });
  // 그리고 /write 경로의 form 에 데이터를 submit 했을 때, DB 에 데이터를 추가
  app.post('/add', (request, response)=>{
    db.collection('post').insertOne({
      _id : 1,
      title : request.body.title,
      date : request.body.date
    }, (error, result)=>{
      console.log(result);
    })
    response.send('Sent')
  })
  ```



#### 3. HTML 에 DB 데이터 적용하는 법 (EJS)

> 서버에 데이터를 전송한 후, response.send() 부분은 필히 존재해야 함. 전송의 성공/실패를 떠나 뭔가 서버에 응답을 보내야 함.

* /list 경로로 접속할 경우 EJS 파일을 보여줌.

  * 그냥 HTML 파일을 보내주면, Static Page 가 됨.
  * 이럴 경우 실제 DB 데이터를 데이터바인딩하여 노출시키지 못함.
  * 따라서 EJS, Pug  같은 템플릿 엔진을 사용함.
    * 서버 데이터를 HTML 에 쉽게 넣을 수 있게 돕는 HTML 렌더링 엔진 라이브러리.

  ```bash
  $ yarn add ejs
  ```

  ```js
  // server.js
  app.set('view engine', 'ejs');
  ```

* EJS 파일은 HTML 과 동일하게 만들어서 작성하면 됨. 단, 중간중간 ESJ 문법으로 데이터바인딩을 할 뿐.

  * 사용하기 위해서는 views 라는 이름의 폴더를 만들어야하며, 
  * 폴더 내에 `list.ejs` 라는 이름의 파일을 생성.

* EJS 파일 기본 문법

  * HTML 태그 중간에 서버 데이터를 바인딩할 경우 아래처럼 사용.

  ```ejs
  <h2><%= user.name %></h2>
  ```

  * HTML 에 if 문을 적용하는 등, JavaScript 문법을 담을 경우 아래처럼 사용.

  ```ejs
  <% if (user) { %>
    <h2><%= user.name %></h2>
  <% } %>
  ```



#### 4. HTML 에 DB 데이터 적용하는 법 : DB 데이터 읽어오기

* MongoDB 에서 데이터를 꺼내고 싶다면, 다음과 같은 메소드를 사용.

  ```js
  db.colleciton('post').find();
  db.colleciton('post').findOne();
  ```

* post Collection 에 저장된 모든 데이터를 요청할 경우

  * DB 에서 받은 데이터를 toArray method 를 이용하여 배열 형태로 받아옴.
  * 해당 배열을 ejs 파일에 렌더링.

  ```js
  // server.js
  app.get('/list', (request, response)=>{
    db.collection('post').find().toArray((error, result)=>{
      console.log(result);
      response.render('list.ejs', {posts : result});
    });
  });
  ```

* 위 코드에서 `{ posts : result }` 라는 것은, 배열 데이터를 ejs 파일에서 post 라는 이름으로 바인딩한다는 의미.

* EJS 파일의 HTML 수정

  ```ejs
  <body>
    	...
      <h5>Data From Server</h5>
      <% for (var i=0; i<posts.length; i++){ %>
          <h6> Todo Title : <%= posts[i].title %></h6>
          <p>Due Date :<%= posts[i].date %></p>
      <% } %>
  </body>
  ```



> ##### DB 의 종류와 특징
>
> * 데이터베이스의 종류는 계층형, 네트워크, Graph 형태까지 다양.
> * 그러나 웹 서비스를 개발할 경우 사용하는 DB 는 크게 **Relational Database** 와 **NoSQL Database** 로 나뉨.
>
> 1. Relational Database
>    * 엑셀의 시트처럼 '테이블' 이라고 불리는 공간을 생성하고, Row-Col 에 맞춰 데이터를 저장.
>    * 구조화된 데이터를 저장하기 좋음.
>    * 보통 SQL 이라는 언어를 이용하여 데이터를 입출력.
>    * "이 열에는 {자료형} 이 들어옴" 라고 스키마를 미리 정의하기 때문에 관리에 용이.
>    * 구조화된 데이터 덕분에 원하는 데이터를 뽑기 수월.
>    * Transaction, Rollback 등의 기능을 이용해 데이터 무결성을 보존하기 쉬움.
> 2. NoSQL Database
>    * SQL 문 없이도 사용할 수 있는 데이터베이스. 
>    * 따라서 테이블에 국한되어 저장하지 않고, 자유로운 형식으로 데이터를 분산 저장할 수 있음.
>    * 여러가지 종류.
>      * Key-value 모델 : Object, JSON 자료형 형식으로 데이터를 쉽게 저장, 출력이 가능. 가장 심플함.
>      * Document 모델 : 테이블 대신 Collection이라는 문서 기반으로 데이터를 분류하고 저장. 테이블보다는 훨씬 유연. (본 프로젝트에서 사용하고 있는 MongoDB도 Key-value, Document 모델 저장방식을 차용하고 있음)
>      * Graph 모델 : 데이터를 노드의 형태로 저장하고 노드간의 흐름 또는 관계를 저장할 수 있음.
>      * Wide-column 모델 : 한 행마다 각각 다른 수, 다른 종류의 열을 가질 수 있음. (스키마가 자유로움) 



#### 5. 게시물마다 번호를 지정

* 게시물 고유 번호(id 값)를 저장하기 위해 `counter` collection 을 생성하고, 값 + 1 으로 id 를 부여.

  ```js
  app.post('/add', (request, response)=>{
    db.collection('counter').findOne({name:"totalPosts"}, (error, result)=>{
      let totalPost = result.totalPosts;
      db.collection('post').insertOne({
        _id : totalPost,
        title : request.body.title,
        date : request.body.date
      }, (error, result)=>{
        // 여기에 counter collection 의 수 증가시키기
      })
    })
    response.send('Sent');
  })
  ```



#### 6. 게시물마다 번호를 지정 : DB Update 함수와 inc 연산자

* DB 데이터를 수정하기 위해선 updateOne 메소드를 사용하면 됨.

* updateOne 메소드에는 인자가 세 개 들어감.

  ```js
  db.collection('counter').updateOne({findThisDate},{andChangeLikeThis},(err, result)=>{
    ...
  })
  ```

  * 첫 번째 인자를 사용하여, 수정하고자 하는 데이터의 인덱스를 찾음.

  * 두 번째 인자를 사용하여, 수정하고자 하는 데이터의 key 를 입력하고, 연산자를 입력.

    ```js
    {$inc : {totalPosts: 1}} // totalPost 데이터를 1 만큼 increment
    ```

  * 세 번째 인자는 updateOne 메소드에 대한 콜백 함수.

* 따라서, counter collection 의 데이터 값을 올리는 코드는 다음과 같음.

  ```js
  db.collection('counter').updateOne({name:"totalPosts"}, {$inc:{totalPosts:1}}, (error, result)=>{
  	if(error){return console.log(error)}
    ...
  })
  ```

  

#### 7. AJAX 로 삭제 요청하기

* Request 종류는 4가지(GET, POST, PUT, DELETE)가 있으나, HTML 안에서 일반적으로 PUT, DELETE 요청을 할 수는 없음.

  ```html
  <form method="DELETE">			// 이런식의 DELETE 요청 불가
    <button>Button</button>
  </form>
  ```

  * 이는 HTML 언어를 만들 때 잘못 만들어진 탓 때문. 따라서 아래 방법으로 삭제요청을 할 수 있음.
    1. method-override 라이브러리의 도움을 받음.
    2. AJAX 로 DELETE 요청을 날림.
    3. POST 요청을 날려서 DELETE 작업을 수행.

* AJAX 란?

  * Front-End 에서 JavaScript 를 이용하여, 서버에 여러가지 요청을 할 수 있는 문법 종류.
  * 새로고침 없이도 서버에 요청할 수 있음.
  * 따라서, 새로고침 없는 자연스러운 사이트를 만들고 싶다면 AJAX 문법을 이용하여 처리.

* AJAX 를 위한 jQuery 설치.

  * VanillaJS 형태로도 AJAX 가 가능하지만, 편의성을 위해 jQuery 를 설치 (CDN 방식으로 `list.ejs` 파일에 첨부).

* AJAX 사용.

  ```jsx
  // list.ejs
  <script>
  	$.ajax({
      method : "DELETE",
      url : "/delete",
     	data : /*item to be deleted*/
    }).done((result)=>{
      // when AJAX success
    }).fail((error)=>{
      // when AJAX fail
    });
  </script>
  ```

  * 위 코드는, `/delete` 경로로 DELETE 요청을 하는데, 요청과 함께 특정 데이터를 보내는 것을 의미.
  * 그러면 서버는 요청을 처리함.
  * 이 코드는 `list.ejs` 페이지를 방문할 때 마다 바로 실행 됨.

* 서버에서 AJAX 요청을 처리.

  * AJAX 으로  `/delete` 경로로 DELTE 요청을 했기 떄문에, server.js 에서 이를 처리하는 코드를 작성.

  ```js
  // server.js
  
  app.delete('/delete', (request, response)=>{
    // delete post from DB
    response.send('deleted');
  })
  ```

  

#### 8. AJAX 로 삭제 요청하기 : 서버의 역할

* list.ejs 파일에서 삭제 요청을 보내는 것까지 구현.

* server.js 에서는 요청을 받아서, 서버 단에서 처리하는 코드가 필요.

  ```js
  app.delete('/delete', (request, response)=>{
    db.collection('post').deleteOne(request.body, (error, result)=>{
      console.log('Deleted');
    })
    response.send('Deleted');
  })
  ```

* list.ejs 에서는 삭제 요청 데이터를 `{ _id : 1 }`  인 정수형으로 요청했으나, server.js 에서 수신된 `request.body` 의 데이터는 문자열 형태로 받아짐. 

  * AJAX 요청 등으로 데이터를 서버에 전송할 때, 숫자 자료들이 가끔 문자화 되는 경우가 있음.
  * 따라서, 이를 다시 정수형으로 변형시켜 줘야 함.

  ```jsx
  request.body._id = parseInt(request.body._id);
  ```

* list.ejs 파일에서 삭제 요청을 보낼때, trigger point 가 불명확하기 때문에 list 의 버튼을 클릭했을 때 삭제될 수 있도록 구현.

  * Button 태그에 `data-id=<%= posts[i]._id %>` 형태로 글 인덱스가 바인딩되어 있으므로, `e.target.dataset.id` 으로 불러올 수 있게 됨.

  ```js
  $('.delete').click((e)=>{
    let postIndex = e.target.dataset.id;
    $.ajax({
      method : "DELETE",
      url : "/delete",
      data : { _id : postIndex }
    }).done((result)=>{
      console.log(result);
    }).fail((error)=>{
      console.log(error);
    })
  })
  ```

  

#### 9. AJAX 로 삭제 요청하기 : jQuery 를 이용한 UI 기능 및 여러가지 응답방법

* 서버가 클라이언트 요청에 응답하는 방법은 여러 가지가 있음.

  1. `send` 는 간단한 문자나 HTML 을 보낼 수 있음.

  ```js
  response.send("message");
  ```

  2. `status` 는 응답 코드를 보낼 수 있음.

  ```js
  response.status(404).send("Cannot Find Page.");
  ```

  3. `sendFile` 은 static 파일을 보낼 수 있음.

  ```js
  response.sendFile('/uploads/logo.png')
  ```

  4. `render` 은 ejs 등의 템플릿이 적용된 페이지들을 렌더링해줄 수 있음.

  ```js
  response.render('list.ejs', {Data to ejs})
  ```

  5. `json` 은 제이슨 데이터를 담아보낼 수 있음.

  ```js
  response.json(jsonData);
  ```

* 응답코드 `status` 를 이용할 경우, 요청이 성공했는지, 실패했는지 판정해줄 수 있음.

* 본 프로젝트에서도, DELETE 요청에 대한 성공/실패 코드를 보낼 것.

  ```js
  // server.js
  app.delete('/delete', (request, response)=>{
      request.body._id = parseInt(request.body._id);
      console.log(request.body);
      db.collection('post').deleteOne(request.body, (error, result)=>{
          if(error){
              response.status(400).send({ message : "Failed!" })		// Failed
          }else{
              response.status(200).send({ message : "Success!" })		// Success
          }
      })
  })
  ```

* Server 응답에 대한 Client 의 코드 실행.

  ```js
  // list.ejs
  $('.delete').click((e)=>{
    let postNumber = e.target.dataset.id;
    let selected = $(this);
    $.ajax({
      method: "DELETE",
      url :"/delete",
      data : {_id: postNumber} 
    }).done((result)=>{
      console.log("Success!");
      selected.parent('div').parent('div').fadeOut();
      console.log(result);
    }).fail((xhr, textStatus, errorThrown)=>{
      console.log(xhr, textStatus, errorThrown);
    })
  })
  ```



#### 10. 쇼핑몰 같은 상세 페이지 만들기

* 각 리스트 아이템을 선택했을 때, 이동하는 상세페이지를 생성 (`/views/detail.ejs`).

  * `/detail/:id` 경로로 GET 요청을 실행했을 때, `db.colleciton('post')` 에서 데이터를 찾고,
  * `detail.ejs` 파일에 렌더링.

  ```js
  // server.js
  app.get("/detail/:id", (request, response)=>{
      db.collection('post').findOne({_id:parseInt(request.params.id)}, (error, result)=>{
          console.log(result);
          response.render('detail.ejs', {posts : result})
      })
  })
  ```

* `detail.ejs` 파일 생성.

  ```ejs
  <div class="container">
    <br/>
    <h4>Detail</h4>
    <br/>
    <p>Title : <%= posts.title %></p>
    <br/>
    <p>Due Date : <%= posts.date %></p>
  </div>
  ```



#### 11. 



대표님 안녕하세요 오랜만에 연락드립니다

잘 지내시죠? 명절은 잘 보내셨습니까ㅎㅎ

사무실 이전했다고 들었는데 더 좋은데로 이전하신건가요 축하드립니다.

다름 아니라 프로젝트 관련해서 말씀 나눠야할 부분이 있어서 연락드렸는데요

진행 상황에 대해서 불만족스런 부분이 다소 있어서 이제는 대표님께 직접 얘기를 좀 드리려고 합니다.

결론부터 말씀드리면 지금 WBS 공유를 이지혜실장한테 요청했으나 한달가까이 회신을 못받고 있는 상황입니다.

그리고 PM 을 통한 소통이 너무 제한적인게 불만이 큽니다.

이번 토일월 연휴끝나면 딱 한달 되겠네요.

해당 자료 뿐만 아니라, 

현 프로젝트의 예상 완료 시점 안내,

하자보수건 개발의 예상 기간과 기획안 안내,

그리고 본 개발 완료 후 유지보수 요청 시 발생하는 금액도 안내받지 못하고 있습니다.

원칙상으론 2영업일 이내로 최소한 피드백이라도 받도록 계약서에 명시되어 있지만,

라인 메세지조차 그렇게 확인되지 않을 경우가 많더라구요.



일단 WBS 공유 부터 구체적으로 말씀드리면 

저희도 개발을 하는 입장이라, 허브에서 백단의 개발 결과를 주기적으로 

매번 클라이언트에 공유하기 어려운 것은 잘 알고 있습니다.

그치만 아시다시피 저희도 여러 유통사와 함께 진행하는 프로젝트라

페이퍼웨어가 해당 업체들에 진행 상황을 공유 해줘야 하는 포지션이고

현재로썬 백단 API 개발도 완료가 되가는 시점으로 알고 있어서 

작성된 WBS 문서만이라도 공유 요청을 한건데

처음엔 회신을 아예 받지 못했고, 

그 뒤엔 메인 개발자 부재 사유로 내용을 정리해서 보내준다는 답변을 받았습니다.



근데 제 생각엔 PM 이 전체 프로젝트 일정 조율과 팀 스프린트 관리를 하니깐

PM 권한으로 즉각적인 피드백이 되는게 맞다고 보는데, 

개발자 부재 사유로 또다시 열흘째 미뤄지는게 이해가 되지 않습니다.



사실 올 봄에 기획 단계에서도 전과장이랑 이미 다룬 내용을 

유선상으로 이실장한테 또 설명을 해야 했고

최신 내용을 말할때 마다 아직 이부분은 내용을 알기 전이라 확인해보고 회신하겠다는 답변을 받았었습니다.



견적서에도 소프트웨어 노임단가가 MM 기준으로 계산이 되어있는데, 

일단 솔직히 말씀드리면 개인적으로 노임단가 기준으로 견적이 맞춰질거라곤 생각하지 못했습니다.

개발사가 대게 여러 프로젝트를 함께 진행하기 때문에

본 프로젝트에서도 허브가 100% PMS 건에만 투입될거라곤 생각하지 않았고 저희는 그걸 감안하고 진행했습니다.

그렇지만 최소한 억단위 비용으로 계약한 만큼 원활한 소통과 피드백을 받는게 맞다고 보는데

그렇지 못해서 좀 유감스럽습니다.

더구나 PM 의 MM 는 전체 계약금의 30% 넘게 차지할 정도잖습니까.

허브의 내부적인 사정이라 언급하긴 죄송하지만, 최근에 이지혜실장이 개인사정으로 프로젝트에 참여를 못하고 있었다는 얘기를 뒤늦게 들었는데

왜 이런건 미리 공유되지 않는건지도 의문입니다.

계약금과는 다르게 인력이 투입된 부분에 대해서 말입니다. 



올 초에도 원래 라인 톡에 있던 한분이 나가셨는데, 

그 분이 누구인지, 그리고 마지막 계약건 진행하면서 추가된다는 

풀스택 개발자와 백단 개발자가 누구인지 정도는 더욱 투명하게 내용을 확인할 필요가 있다고 생각합니다.



일단 여하불문 하고 신속하게 개발 내용이 공유가 되었으면 합니다.

당장 소스 공유 요청을 드리는건 당연히 아니지만, 지금 페이퍼웨어 입장으로 봤을땐 최종 결과물에 깃 커밋 이력도 함께 확인해야될 것 같습니다.

그리고 무엇보다 PM 을 통한 소통에 더욱 신경을 많이 써주셨으면 합니다.



* 프로젝트 개발 기간 문의도
* 



여러가지 추가계약이 늘어나면서 트러블이 있었던건 

