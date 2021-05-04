# chair-server
의자소통 API 서버입니다. 

## 포스터
<img src="http://www.ideaboom.net/upload/project_img/project_image_view_5739.jpg" height=700></img>

## 수행 목적
현대인들의 잘못된 자세가 습관이 되버리고 어떤 자세를 취하고 있는지 잘 모릅니다. <br>
의자소통은 이를 해결하기 위하여 나쁜 자세로 앉는 습관을 바로잡고 올바르게 의자를 이용하도록 만들어 졌습니다.

#### Reference
- **순천향대 IT의료 공학과**
  - 압력센서를 이용한 실시간 앉은 자세 모니터링 시스템
- **E나라표준인증**
   - 표준번호 KS A ISO 7250-1
- **세브란스병원**
- **대구 재활운동센터**
- **시엘척추병원**
- **cm병원 관절전문병원**

#### 기능
- 실시간 앉은 자세 모니터링
- 사용자의 자세 데이터에 대한 통계 정보 시각화

## 프로젝트 구조
```
src
├── app.ts                 # App entry point
├── api                    # Express route controllers for all the endpoints of the app 
│   ├── middlewares            # Express middlewares
│   └── routes                 # Endpoints
├── config                 # Environment variables and configuration related stuff
├── interface              # Applying Open-Closed Principle (OCP)
├── loaders                # Split the startup process into modules
├── models                 # Database models
├── services               # All the business logic is here
├── subscribers            # # Event handlers for async task
├── types                  # Type declaration files (d.ts) for Typescript
│   └── express                #  express.d.ts
└── views                  # Simple html files
```

## DB

#### 사용자
| 속성   |      타입      |  설명 |
|:----------:|:-------------:|:------:|
| p0 |  int | 바른 자세 |
| p1 |  int | 숙인 자세 |
| p2 |  int | 둔부 앞 자세 |
| p3 |  int | 왼쪽으로 기울어진 자세 |
| p4 |  int | 오른쪽으로 기울어진 자세 |
| p5 |  int | 걸터 앉은 자세 |
| nonp |  int | 그 외 |
| user_id |  int | 사용자 pk |

#### 자세
| 속성   |      타입      |  설명 |
|:----------:|:-------------:|:------:|
| email | varchar | 사용자 이메일 |
| name |    varchar   | 사용자 이름 |
| password | varchar | 비밀번호 |

## API
- 반환 객체(JSON)
- Content-Type : 'application/json'
- Content-Type : 'application/x-www-form-urlencoded'
- 날짜 포멧 : YYYY-MM-DD hh:mm:ss
- **statusCode**
  - **200** : 성공
  - **400** : 요청 처리 실패
  - **403** : 권한 없음 (토큰이 유효하지 않을 때)
  - **404** : 없는 리소스 (존재하지 않는 데이터나 경로)
  - **409** : 데이터 중복 (가입 시 이메일 중복일 때)
  - **500** : 서버 에러 (내가 잡을 수 없던 에러)
- 응답 
    ```json
    /*
    * @this response.body
    * @description success가 false일 때 메세지 확인
    * @attributes { success!, message!, user?, statistics, error? }
    */
    {
    	"success": boolean,
    	"message": string,
    	"user"? : User
    	"error"? : { "message": string } // 없는 경로로 들어올 때 에러만 찍힘
    }
    ```
    
 
|기능       |메서드   |URI                             |요청 헤더                      |요청 바디                               |응답 헤더, 바디                                              |
|:---------|:------:|:--------------------------------|:---------------------------|:------------------------------------|:-------------------------------------------------------|
|로그인      |post  |api/users/login                 |                           |email<str>, password<str>           |header:token<str>, message<str>, success<bool>         |
|첫 로그인 체크 |put   |api/users/login/check           |token<str>                 |check<int>                          |message<str>, success<bool>                            |
|회원가입     |post  |api/users/signup                |                           |email<str>, name<str>, password<str>|message<str>, success<bool>                            |
|계정 정보 보기 |get   |api/users/account               |token<str>                 |                                    |message<str>, success<bool>, user<obj>                 |
|이메일 수정   |put   |api/users/account/emailmodify   |token<str>                 |newEmail<str>                       |message<str>, success<bool>                            |
|비밀번호 수정  |put   |api/users/account/passwordmodify|token<str>                 |newPassword<str>, password<str>     |message<str>, success<bool>                            |
|비밀번호 찾기  |post  |api/users/find/password         |                           |email<str>, name<str>               |message<str>, success<bool>                            |
|회원탈퇴     |delete|api/users/withdrawal            |token<str>                 |password<str>                       |message<str>, success<bool>                            |
|자세 데이터 저장|post  |api/postures/save               |token<str>                 |postures<arr>                       |message<str>, success<bool>                            |
|일간 통계    |get   |api/statistic/date              |query:date<str>, token<str>|                                    |message<str>, statistics<arr>, success<bool>, time<str>|
|월간 통계    |get   |api/statistic/month             |query:date<str>, token<str>|                                    |message<str>, statistics<arr>, success<bool>, time<str>|
|총 통계     |get   |api/statistic/all               |token<str>                 |                                    |message<str>, statistics<arr>, success<bool>, time<str>|
|총 통계 비교  |get   |api/statistic/all/comparison    |token<str>                 |                                    |message<str>, statistics<arr>, success<bool>           |
|자세 추측    |post  |api/postures/inference          |token<str>                 |posture<arr>                        |inference<str>, message<str>, success<bool>            |

