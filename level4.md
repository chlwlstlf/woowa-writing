# OAuth 로그인(Github OAuth, Access Token, Refresh Token)

이번 글에서는 OAuth의 주요 개념과 용어를 알아보고, Github OAuth를 활용한 로그인 구현을 Frontend 개발자의 관점에서 다뤄보겠습니다.

## 목차

1\. [OAuth란?](#oauth란)

- [OAuth란 무엇인가?](#oauth란-무엇인가-애플리케이션과-서드파티-서비스-간의-안전한-소통)
- [OAuth의 탄생 배경](#oauth의-탄생-배경-비밀번호-없이-안전하게)
- [OAuth 1.0 vs OAuth 2.0](#oauth-10-vs-oauth-20-무엇이-달라졌을까)

2\. [OAuth 용어 한눈에 보기](#oauth-용어-한눈에-보기)

3\. [OAuth 로그인 과정](#oauth-로그인-과정)

4\. [Github OAuth로 로그인을 구현해봅시다!](#github-oauth로-로그인을-구현해봅시다)

- [OAuth application 등록](#1-oauth-application-등록)
- [로그인 클릭](#2-로그인-클릭)
- [Github OAuth로 리다이렉트](#3-github로-리다이렉트)
- [Callback 페이지 화면](#4-callback-페이지-화면)
- [OAuth 로그인 과정 최종 정리](#oauth-로그인-과정-최종-정리)

5\. [Github OAuth로 로그아웃도 구현해봅시다!](#github-oauth로-로그아웃도-구현해봅시다)

- [로그아웃은 언제 일어나나요?](#로그아웃은-언제-일어나나요)
- [로그아웃 클릭](#로그아웃-클릭)

6\. [마치며](#마치며)

## OAuth란?

### OAuth란 무엇인가? 애플리케이션과 서드파티 서비스 간의 안전한 소통

애플리케이션이 다른 서비스에 안전하게 접근하기 위해서는 사용자의 비밀번호를 직접 사용하지 않고, 안전한 방식으로 권한을 위임받을 필요가 있습니다. OAuth는 바로 이 문제를 해결하는 표준 프로토콜입니다.

<br>

**OAuth는 무엇을 해결할까요?**

OAuth는 사용자의 민감한 자격 증명 정보(예: 비밀번호)를 직접 취급하지 않고도 서드파티 서비스에 안전하게 접근할 수 있게 해줍니다. 이를 통해 애플리케이션은 사용자를 대신하여 다양한 서비스의 리소스에 접근할 수 있습니다.

대표적인 예로, 여러분이 Facebook 계정으로 다양한 웹사이트에 로그인하거나, Google Drive에 있는 파일을 다른 애플리케이션에서 접근할 수 있게 하는 방법이 있습니다.

<br>

**OAuth의 핵심 개념**

사용자는 애플리케이션이 자신의 정보를 접근할 수 있도록 일부 권한만 위임하는 액세스 위임을 합니다. 이때 비밀번호를 제공할 필요는 없습니다. OAuth는 애플리케이션이 사용자를 대신해 서드파티 API와 통합하는 방법을 제공합니다.

대표적인 예로 소셜 로그인이나, 클라우드 저장소 접근 등이 있습니다. 사용자가 음악 스트리밍 서비스에서 Spotify 계정을 연결할 때, Spotify의 로그인 정보를 해당 서비스에 직접 제공하지 않고, OAuth를 통해 Spotify 계정의 음악 라이브러리에 접근할 수 있게 됩니다.

<br>
<br>

### OAuth의 탄생 배경: 비밀번호 없이 안전하게!

2007년, 애플리케이션들이 점점 더 많은 서드파티 서비스를 활용하게 되면서, 비밀번호를 공유해야 하는 보안 문제가 커졌습니다. 그 당시에는 애플리케이션이 사용자 대신 서드파티 API에 접근하려면 사용자의 비밀번호를 저장해야 했습니다. 이는 보안에 취약했고, 관리하기도 어려웠습니다.

이 문제를 해결하기 위해 OAuth가 탄생했고, 이제는 액세스 토큰이라는 방식으로 애플리케이션이 사용자를 대신해 안전하게 서비스에 접근할 수 있게 되었습니다.

<br>
<br>

### OAuth 1.0 vs OAuth 2.0: 무엇이 달라졌을까?

OAuth는 첫 번째 버전(1.0)에서 등장했고, 시간이 지나면서 보안과 확장성을 강화한 두 번째 버전(2.0)이 나왔습니다. 두 버전의 차이점을 한눈에 살펴보겠습니다.

<br>

**OAuth 1.0**

서명 기반 인증, 즉 각 요청마다 암호화된 서명이 포함되었습니다. 이는 보안성이 뛰어나지만, 설정이 복잡하고 구현이 까다로웠습니다. 애플리케이션은 비밀 키를 사용해 요청을 서명해야 했고, 이 과정에서 오류가 자주 발생했습니다. 서명 알고리즘을 직접 구현해야 하고, 복잡한 요청 구조로 인해 개발자들이 실수를 저지르기 쉬웠습니다.

<br>

**OAuth 2.0**

토큰 기반 인증, 즉 서명 대신 액세스 토큰을 사용하여 인증을 처리합니다. 토큰 자체가 인증 수단이 되므로 구현이 훨씬 간단해졌습니다. 애플리케이션이 접근할 수 있는 **권한의 범위(scope)**를 명확하게 지정할 수 있습니다. 예를 들어, 읽기 권한만 부여하거나, 특정 데이터에만 접근할 수 있도록 제한할 수 있습니다. 모바일, 웹, 클라이언트 등 다양한 환경에서 쉽게 사용할 수 있도록 설계되었습니다.

OAuth 1.0은 마치 매번 문을 열 때마다 열쇠로 문을 잠그고 푸는 과정을 거치는 것이라면, OAuth 2.0은 신뢰할 수 있는 열쇠를 주고 한동안 마음대로 드나들 수 있게 하는 방식입니다.

<br>
<br>

## OAuth 용어 한눈에 보기

**Resource Owner (리소스 소유자)**

> 누가 권한을 가지고 있는가?

OAuth 2.0에서 Resource Owner는 리소스에 대한 접근 권한을 가진 사용자를 의미합니다. 예를 들어, 여러분이 Github 계정으로 로그인해서 특정 애플리케이션을 사용할 때, 그 서비스는 여러분의 Github 계정에 있는 데이터에 접근하게 됩니다. 이때, Resource Owner는 바로 사용자인 여러분입니다.

<br>

**Client (클라이언트)**

> 리소스를 요청하는 애플리케이션

Client는 리소스 서버의 데이터를 요청하는 애플리케이션입니다. 여러분이 만드는 웹 애플리케이션이나 모바일 앱이 바로 여기에 해당합니다. 예를 들어, Github OAuth를 통해 로그인하는 웹사이트라면, 그 웹사이트가 Client입니다.

<br>

**Resource Server (리소스 서버)**

> 리소스를 가지고 있는 서버는?

Resource Server는 말 그대로 리소스를 보유한 서버입니다. 주로 Google, Facebook, Github와 같은 플랫폼이 여기에 해당합니다. 이 서버는 클라이언트로부터의 요청이 올 때, 적절한 권한이 있는지 확인한 후 데이터를 제공합니다.

<br>

**Authorization Server (인증 서버)**

> 누가 권한을 확인하고 발급할까?

Authorization Server는 사용자를 인증하고, 액세스 토큰을 발급해주는 역할을 합니다. 예를 들어, Github OAuth를 사용할 때, 사용자가 Github 계정으로 로그인하면 Authorization Server가 사용자가 제대로 인증되었는지 확인하고, 액세스 토큰을 발급해줍니다.

참고: 어떤 경우에는 클라이언트가 Authorization Server 없이 바로 Resource Server에 접근할 수 있는 경우도 있습니다.

<br>

**Access Token (액세스 토큰)**

> 권한을 부여받은 열쇠

Access Token은 클라이언트가 리소스 서버에 접근할 수 있도록 부여받는 토큰입니다. 이 토큰은 보통 짧은 유효 기간을 가지고 있으며, 클라이언트가 리소스 서버에 API 요청을 보낼 때 **헤더(Header)**에 포함시켜 보냅니다. 예를 들어, Github API에 요청을 보낼 때 이 토큰을 사용해 사용자 정보를 불러옵니다.

<br>

**Refresh Token (리프레시 토큰)**

> 새로운 열쇠를 받기 위한 방법

Refresh Token은 Access Token이 만료된 후, 새로운 Access Token을 발급받을 때 사용하는 토큰입니다. 일반적으로 Access Token보다 더 긴 유효 기간을 가지고 있으며, 사용자가 다시 로그인하지 않고도 새로운 Access Token을 받을 수 있게 해줍니다.

<br>
<br>

## OAuth 로그인 과정

Authorization Server가 없는 경우에는 클라이언트가 사용자의 자격 증명(아이디, 비밀번호)을 사용해 직접 리소스 서버에 접근하여 데이터를 요청하고 응답을 받습니다.

Authorization Server가 있는 경우에는 클라이언트는 먼저 Authorization Server를 통해 Access Token을 발급받고, 이후 Access Token을 사용해 리소스 서버에 직접 접근하여 데이터를 요청하고 응답을 받습니다.

<br>
<br>

## Github OAuth로 로그인을 구현해봅시다!

아래에 튜토리얼로 설명이 잘 되어있습니다. 이 문서를 보면서 같이 해볼까요?

[GitHub 앱 등록 공식 문서](https://docs.github.com/en/apps/creating-github-apps/registering-a-github-app/registering-a-github-app)

<br>

❗ 참고

```
현재 서비스에서는 Authorization Server에 사용자 정보를 저장해야 하기 때문에 Authorization Server가 Resource Server로부터 발급받은 Access Token으로 User Info를 받은 후 Authorization Server가 자체 생성한 Access Token과 Refresh Token, 그리고 응답 받은 User Info를 Client에게 넘겨줍니다.
```

```
따라서 Frontend 개발자가 해야할 일은 Resource Server로부터 발급 받은 Code를 Authorization Server에 보내는 일입니다. 다음은 Frontend 개발자가 할 일에 관한 튜토리얼입니다.

1. OAuth application 등록하고 client_id 랑 client_secrets 값 발급
2. 사용자를 로그인 시켜서 임시 토큰이라고 할 수 있는 code 값 얻기
3. code값을 이용하여 Authorization Server에 로그인 요청
4. Authorization Server가 준 Access Token, Refresh Token, User Info 저장
```

<br>
<br>

### 1. OAuth application 등록

**1. OAuth Apps로 들어가기**

Settings > Developer settings > OAuth Apps

<br>

**2. New OAuth App**

Application name

- 서비스 이름
- 본인이 식별할 수 있는 이름이면 됩니다.

Homepage URL

- 서비스 URL, 개발 중이므로 `http://localhost:3000`를 넣었습니다.
- 이 URL도 본인이 식별할 수 있는 URL이면 됩니다.

Authorization callback URL

- 미리 callback 페이지를 만들어두면 좋습니다.
- 개발 중이므로 `http://localhost:3000/callback`을 넣었습니다.
- 배포 시에는 배포한 url을 넣으면 됩니다.

![3](https://github.com/user-attachments/assets/174c5cc5-6a9a-4232-9612-84c75497e1aa)

<br>

**3. Client ID, Client Secrets**

> OAuth App을 생성하면 Client ID, Client secrets를 발급 받습니다.

Client ID

- Client에서 OAuth 화면으로 리다이렉트할 때 사용합니다.
- Authorization Server에서 Resource Server로 api를 요청할 때 사용합니다.

Client secrets

- 절대 유출되면 안 되는 정보이므로 처음 발급받을 때 이후로는 깃허브에서도 확인을 할 수 없습니다.
- Authorization Server에서 Resource Server로 api를 요청할 때 사용합니다.
- Authorization Server는 서브 모듈에 Client secrets를 저장합니다.

![4](https://github.com/user-attachments/assets/9beee5dc-8cd8-4981-b431-82f4b140239d)

<br>
<br>

### 2. 로그인 클릭

- 로그인을 클릭합니다.
- 현재 localStorage는 비어있습니다.

![6](https://github.com/user-attachments/assets/34578ed3-569a-46aa-813b-e1c02d4007b8)

<br>
<br>

### 3. Github OAuth로 리다이렉트

- Resource Owner(사용자)에게 인증을 요청합니다.
- 발급 받은 client_id와 백엔드와 정한 scope로 Github OAuth URL를 생성합니다.

![7](https://github.com/user-attachments/assets/09940612-5417-43e0-a7cb-34aac53b14fc)

<br>

**<mark style='background-color: #fff5b1'>로그인 코드</mark>**

[OAuth 앱의 Scope](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/scopes-for-oauth-apps)

```tsx
const App = () => {
  const githubAuthUrl = `https://github.com/login/oauth/authorize?client_id=${발급받은 Client Id}&scope=${OAuth 앱의 Scope}`;

  const handleLogin = () => {
    window.open(githubAuthUrl, "_self");
  };

  return (
    <>
      <button onClick={handleLogin}>로그인</button>
    </>
  );
};

export default App;
```

<br>
<br>

### 4. Callback 페이지 화면

1\. Github Authorization callback URL에 지정한 callback 페이지로 넘어갑니다.

<br>

2\. callback url 뒤에 query 파라미터로 code가 넘어옵니다.

<br>

3\. 이 code를 추출하여 Authorization Server에 로그인 post 요청을 합니다.

![8](https://github.com/user-attachments/assets/8ec505c7-00ca-4e7b-a804-a708194988cb)

<br>

4\. 로그인 post 요청을 받은 Authorization Server는 client_id, client_secrets, code를 이용하여 Resource Server에 [access Token을 요청](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/authorizing-oauth-apps)합니다.

![1000](https://github.com/user-attachments/assets/b85d482d-64b3-4c64-9845-38eab699e4dc)

<br>

5\. Authorization Server는 발급 받은 access Token을 header에 담아서 Resource Server에 [user Info를 요청](https://docs.github.com/ko/rest/users/users?apiVersion=2022-11-28)합니다.

![1001](https://github.com/user-attachments/assets/705b911f-a2b4-4bf7-869e-2665a9aec7ae)

<br>

6\. Authorization Server는 자체 생성한 access Token, refresh Token 그리고 Resource Server로 부터 받은 user Info를 Client에 넘겨줍니다.

<br>

7\. Client는 이를 기기에 저장한 후 access Token을 header에 담아서 api를 요청하는 데에 사용합니다.

![9](https://github.com/user-attachments/assets/7c294a7b-c2e4-4fd6-8451-26a1b1df349a)

<br>

**Callback 페이지 코드**

Frontend 개발자가 할 일은 1, 2, 3, 7번 입니다. 4, 5, 6번은 Backend 개발자가 Authorization Server와 Resource Server 사이에서 데이터를 주고 받는 코드를 작성하면 됩니다.

```tsx
// 1. Callback 페이지 (Github 로그인을 하면 자동으로 넘어옵니다)
import { useEffect } from "react";
import { useNavigate } from "react-router-dom";

const CallbackPage = () => {
  const navigate = useNavigate();

  const handleLogin = async () => {
    try {
      const params = new URLSearchParams(window.location.search);
      const code = params.get("code"); // 2. code 추출

      if (!code) {
        throw new Error("Authorization code가 없습니다.");
      }

      // 3. 로그인 요청
      const { accessToken, refreshToken, userInfo } = await postLogin(code);

      // 7. 토큰과 사용자 정보 저장(localStorage 외에 다른 곳에 저장해도 됩니다)
      localStorage.setItem("accessToken", accessToken);
      localStorage.setItem("refreshToken", refreshToken);
      localStorage.setItem("userInfo", JSON.stringify(userInfo));

      navigate("/"); // 로그인 성공 후 메인 페이지로 이동
    } catch (error) {
      localStorage.clear();
      alert(error.message || "로그인 중 오류가 발생했습니다.");
    }
  };

  useEffect(() => {
    handleLogin();
  }, []);

  return <div>로그인 중...</div>;
};

export default CallbackPage;
```

<br>
<br>

### OAuth 로그인 과정 최종 정리

![1003](https://github.com/user-attachments/assets/d9cad189-c443-40de-b17a-cec5e5b12c82)

1\. Resource Owner는 Client를 통해 로그인을 한 후 Resource Server로부터 code를 받습니다.

2\. Client는 이 code로 Authorization Server에 `로그인 post` 요청을 합니다.

3\. Authorization Server는 client ID, client secrets, code를 이용하여 Resource Server에 access Token을 요청합니다.

4\. Authorization Server는 발급 받은 access Token을 header에 담아서 Resource Server에 user Info를 요청합니다.

5\. Authorization Server는 자체 생성한 access Token, refresh Token과 Resource Server로 부터 받은 user Info를 Client에 넘겨줍니다.

6\. Client는 이를 기기에 저장한 후 access Token을 header에 담아서 api를 요청하는 데에 사용합니다.

<br>
<br>

## Github OAuth로 로그아웃도 구현해봅시다!

### 로그아웃은 언제 일어나나요?

<br>

**refresh Token이 만료됐을 때**

1\. Client는 localStorage를 clear합니다.

2\. Authorization Server는 자동으로 DB에 있는 refresh Token을 삭제합니다.

<br>

**로그아웃을 눌렀을 때**

1\. Client는 localStorage를 clear한 후 `로그아웃 post`를 요청합니다.

2\. Authorization Server는 로그아웃 post를 요청 받으면 DB에 있는 refresh Token을 삭제합니다.

<br>
<br>

### 로그아웃 클릭

1\. Client는 localStorage를 clear한 후 `로그아웃 post`를 요청합니다.

2\. Authorization Server는 로그아웃 post를 요청 받으면 DB에 있는 refresh Token을 삭제합니다.

![10](https://github.com/user-attachments/assets/d4a4ed60-714a-4dd2-b80f-f66dbeb74aa1)

<br>

**로그아웃 코드**

```tsx
import { useNavigate } from "react-router-dom";

const LogoutPage = () => {
  const navigate = useNavigate();

  const handleLogoutClick = async () => {
    try {
      await postLogout(); // 로그아웃 요청
      localStorage.clear(); // 로그아웃 후 로컬 스토리지 클리어
    } catch (error) {
      alert(error.message || "로그아웃 중 오류가 발생했습니다.");
    }
  };

  return (
    <>
      <button onClick={handleLogoutClick}>로그아웃</button>
    </>
  );
};

export default LogoutPage;
```

<br>
<br>

### 마치며

이번 글에서는 OAuth의 주요 개념부터 Github OAuth 로그인과 로그아웃 구현까지, 실제로 애플리케이션에 적용할 수 있는 예시를 통해 알아보았습니다.

OAuth는 소셜 로그인, API 호출, 인증된 자원 접근 등 다양한 곳에서 유용하게 쓰일 수 있습니다. 이제 여러분도 이 가이드를 기반으로 자신의 애플리케이션에서 OAuth 로그인과 토큰 기반 인증을 안전하게 구현해보세요!
