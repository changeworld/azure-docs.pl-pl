---
title: Uwierzytelnianie i autoryzacja
description: Dowiedz się więcej o różnych sposobach uwierzytelniania aplikacji lub usługi w zakotwiczeniu przestrzennym platformy Azure oraz o poziomach kontroli, które mają mieć dostęp do zakotwiczeń przestrzennych platformy Azure.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: df27a77c202951a6c789703f12712e75bd8b5906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656989"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Uwierzytelnianie i autoryzacja zakotwiczenia przestrzenne platformy Azure

W tej sekcji omówimy różne sposoby uwierzytelniania zakotwiczenia przestrzenne platformy Azure z aplikacji lub usługi sieci web oraz sposoby, w jaki można używać kontroli dostępu opartej na rolach w usłudze Azure Directory (Azure AD) do kontrolowania dostępu do kont zakotwiczenia przestrzenne.

## <a name="overview"></a>Omówienie

![Omówienie uwierzytelniania w zakotwiczeniu przestrzennym platformy Azure](./media/spatial-anchors-authentication-overview.png)

Aby uzyskać dostęp do danego konta zakotwiczenia przestrzennego platformy Azure, klienci muszą najpierw uzyskać token dostępu z usługi tokenu zabezpieczającego usługi Azure Mixed Reality (STS). Tokeny uzyskane z STS na żywo przez 24 godziny i zawierają informacje dla usług Zakotwiczenia przestrzenne do podejmowania decyzji autoryzacji na koncie i upewnij się, że tylko autoryzowanych podmiotów zabezpieczeń mogą uzyskać dostęp do tego konta.

Tokeny dostępu można uzyskać w zamian za klucze konta lub tokeny wystawione przez usługę Azure AD.

Klucze konta umożliwiają szybkie rozpoczęcie korzystania z usługi Azure Spatial Anchors; Jednak przed wdrożeniem aplikacji w procesach produkcyjnych zaleca się zaktualizowanie aplikacji w celu użycia uwierzytelniania opartego na usłudze Azure AD.

Tokeny uwierzytelniania usługi Azure AD można uzyskać na dwa sposoby:

- Jeśli budujesz aplikację dla przedsiębiorstw, a twoja firma używa usługi Azure AD jako systemu tożsamości, możesz użyć uwierzytelniania usługi Azure AD opartego na użytkownikach w aplikacji i udzielić dostępu do kont zakotwiczenia przestrzennego przy użyciu istniejących grup zabezpieczeń usługi Azure AD lub bezpośrednio do użytkowników w organizacji.
- W przeciwnym razie zaleca się uzyskanie tokenów usługi Azure AD z usługi sieci web obsługującej aplikację. Korzystanie z usługi sieci web obsługuje jest zalecaną metodą uwierzytelniania dla aplikacji produkcyjnych, ponieważ pozwala uniknąć osadzania poświadczeń dostępu do zakotwiczenia przestrzennego platformy Azure w aplikacji klienckiej.

## <a name="account-keys"></a>Klucze konta

Korzystanie z kluczy kont w celu uzyskania dostępu do konta Azure Spatial Anchors jest najprostszym sposobem rozpoczęcia pracy. Klucze konta znajdziesz w witrynie Azure portal. Przejdź do swojego konta i wybierz kartę "Klucze".

![Omówienie uwierzytelniania w zakotwiczeniu przestrzennym platformy Azure](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


Dostępne są dwa klucze, które są jednocześnie ważne dla dostępu do konta Kotwice przestrzenne. Zaleca się regularne aktualizowanie klucza używanego do uzyskiwania dostępu do konta; posiadanie dwóch oddzielnych prawidłowych kluczy umożliwia takie aktualizacje bez przestojów; wystarczy zaktualizować tylko klucz podstawowy i klucz pomocniczy.

SDK ma wbudowaną obsługę uwierzytelniania za pomocą kluczy konta; wystarczy ustawić AccountKey właściwość na cloudSession obiektu.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objc"></a>[ObjC ( ObjC )](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

Gdy to zrobisz, zestaw SDK będzie obsługiwać wymianę klucza konta dla tokenu dostępu i niezbędne buforowanie tokenów dla aplikacji.

> [!WARNING]
> Używanie kluczy konta jest zalecane do szybkiego wejścia na pokład, ale tylko podczas opracowywania/tworzenia prototypów. Zdecydowanie zaleca się, aby nie wysyłać aplikacji do środowiska produkcyjnego przy użyciu klucza konta osadzonego w nim i zamiast tego używać metod uwierzytelniania usługi Azure AD opartych na użytkownikach wymienionych dalej.

## <a name="azure-ad-user-authentication"></a>Uwierzytelnianie użytkowników usługi Azure AD

W przypadku aplikacji przeznaczonych dla użytkowników usługi Azure Active Directory zalecanym podejściem jest użycie tokenu usługi Azure AD dla użytkownika, który można uzyskać za pomocą [biblioteki MSAL.](../../active-directory/develop/msal-overview.md) Należy wykonać kroki wymienione [zarejestrować szybki start aplikacji](../../active-directory/develop/quickstart-register-app.md), które obejmują:

1. Konfiguracja w witrynie Azure portal
    1.  Zarejestruj aplikację w usłudze Azure AD jako **aplikację natywną**. W ramach rejestracji należy określić, czy aplikacja powinna być wielodostępna, czy nie, i podać adresy URL przekierowania dozwolone dla aplikacji.
        1.  Przełączanie do karty **uprawnień interfejsu API**
        2.  Wybierz **pozycję Dodaj uprawnienie**
            1.  Wybierz **dostawcę zasobów rzeczywistości mieszanej** w obszarze **interfejsów API, których używa moja organizacja,** na karcie
            2.  Wybieranie **uprawnień delegowanych**
            3.  Zaznacz pole pod kątem **mixedreality.signin** pod **mixedreality**
            4.  Wybierz **pozycję Dodaj uprawnienia**
        3.  Wybierz **opcję Udzielaj zgody administratora**
    2.  Udziel aplikacji lub użytkownikom dostępu do zasobu:
        1.  Przechodzenie do zasobu zakotwiczenia przestrzenne w witrynie Azure portal
        2.  Przełączanie do karty **Kontrola dostępu (IAM)**
        3.  Trafienie **Dodaj przypisanie roli**
            1.  [Wybierz rolę](#role-based-access-control)
            2.  W polu **Wybierz** wprowadź nazwę użytkowników, grup i/lub aplikacji, do których chcesz przypisać dostęp.
            3.  Wybierz pozycję **Zapisz**.
2. W kodzie:
    1.  Upewnij się, że **używasz identyfikatora aplikacji** i **przekieruj identyfikator Uri** własnej aplikacji usługi Azure AD jako identyfikator **klienta** i parametry **RedirectUri** w usłudze ADAL
    2.  Ustaw informacje o dzierżawie:
        1.  Jeśli aplikacja obsługuje **tylko moją organizację,** zastąp tę wartość **identyfikatorem dzierżawy** lub **nazwą dzierżawy** (na przykład contoso.microsoft.com)
        2.  Jeśli aplikacja obsługuje **konta w dowolnym katalogu organizacyjnym,** zastąp tę wartość **Organizations**
        3.  Jeśli aplikacja obsługuje **wszystkich użytkowników konta Microsoft,** zastąp tę wartość **Common**
    3.  Na żądanie tokenu ustaw **zasób** na "https://sts.mixedreality.azure.com". Ten "zasób" będzie wskazywać usługi Azure AD, że aplikacja żąda tokenu dla usługi Azure Spatial Anchors.

Dzięki temu aplikacja powinna być w stanie uzyskać od msal token usługi Azure AD; można ustawić ten token usługi Azure AD jako **authenticationToken** na obiekcie konfiguracji sesji w chmurze.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objc"></a>[ObjC ( ObjC )](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Uwierzytelnianie usługi Azure AD

Zalecaną opcją wdrażania aplikacji wykorzystujących zakotwiczenia przestrzenne platformy Azure w procesach produkcyjnych jest wykorzystanie usługi wewnętrznej bazy danych, która będzie pośredniczyć w żądaniach uwierzytelniania. Ogólny schemat powinien być opisany na tym diagramie:

![Omówienie uwierzytelniania w zakotwiczeniu przestrzennym platformy Azure](./media/spatial-anchors-aad-authentication.png)

W tym miejscu zakłada się, że aplikacja używa własnego mechanizmu (na przykład: konto Microsoft, PlayFab, Facebook, Identyfikator Google, niestandardowa nazwa użytkownika / hasło itp.) do uwierzytelniania w swojej usłudze zaplecza. Gdy użytkownicy są uwierzytelnione do usługi wewnętrznej bazy danych, że usługa może pobrać token usługi Azure AD, wymienić go na token dostępu dla azure przestrzenne kotwice i zwrócić go z powrotem do aplikacji klienckiej.

Token dostępu usługi Azure AD jest pobierany przy użyciu [biblioteki MSAL](../../active-directory/develop/msal-overview.md). Należy wykonać kroki wymienione [zarejestrować szybki start aplikacji](../../active-directory/develop/quickstart-register-app.md), które obejmują:

1.  Konfiguracja w witrynie Azure portal:
    1.  Zarejestruj aplikację w usłudze Azure AD:
        1.  W witrynie Azure portal przejdź do **usługi Azure Active Directory**i wybierz **rejestracje aplikacji**
        2.  Wybierz **nową rejestrację aplikacji**
        3.  Wprowadź nazwę aplikacji, wybierz **aplikację / INTERFEJS API** sieci Web jako typ aplikacji i wprowadź omów adres URL usługi. Następnie naciśnij pozycję **Utwórz**.
        4.  W tej aplikacji naciśnij pozycję **Ustawienia**, a następnie wybierz kartę **Klawisze.** Wprowadź nazwę klucza, wybierz czas trwania i naciśnij **klawisz Zapisz**. Pamiętaj, aby zapisać wartość klucza, który jest wyświetlany w tym czasie, ponieważ trzeba będzie dołączyć go do kodu usługi sieci web.
    2.  Udziel aplikacji i/lub użytkownikom dostępu do zasobu:
        1.  Przechodzenie do zasobu zakotwiczenia przestrzenne w witrynie Azure portal
        2.  Przełączanie do karty **Kontrola dostępu (IAM)**
        3.  Trafienie **Dodaj przypisanie roli**
        1.  [Wybierz rolę](#role-based-access-control)
        2.  W polu **wyboru** wprowadź nazwę utworzonej aplikacji, do której chcesz przypisać dostęp. Jeśli chcesz, aby użytkownicy aplikacji mieli różne role względem konta Zakotwiczenia przestrzenne, należy zarejestrować wiele aplikacji w usłudze Azure AD i przypisać do każdej z nich osobną rolę. Następnie zaimplementuj logikę autoryzacji, aby użyć odpowiedniej roli dla użytkowników.
    3.  Wybierz pozycję **Zapisz**.
2.  W kodzie (uwaga: możesz użyć przykładu usługi dołączonego do usługi GitHub):
    1.  Upewnij się, że używasz identyfikatora aplikacji, klucza tajnego aplikacji i przekierowywania identyfikatora Uri własnej aplikacji usługi Azure AD jako parametrów identyfikator klienta, klucz tajny i RedirectUri w usłudze ADAL
    2.  Ustaw identyfikator dzierżawy na własny identyfikator dzierżawy AAAzure ADD w parametrze urzędu w adal
    3.  Na żądanie tokenu ustaw **zasób** na "https://sts.mixedreality.azure.com"

Dzięki temu usługa wewnętrznej bazy danych może pobrać token usługi Azure AD. Następnie można wymienić go na token MR, który powróci do klienta. Za pomocą tokenu usługi Azure AD do pobierania tokenu MR odbywa się za pośrednictwem wywołania REST. Oto przykładowe wywołanie:

```
GET https://mrc-auth-prod.trafficmanager.net/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: mrc-auth-prod.trafficmanager.net
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

W przypadku gdy nagłówek Autoryzacja jest sformatowany w następujący sposób:`Bearer <accoundId>:<accountKey>`

Odpowiedź zawiera token MR w postaci zwykłego tekstu.

Token MR jest następnie zwracany do klienta. Aplikacja kliencka można następnie ustawić go jako token dostępu w konfiguracji sesji w chmurze.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objc"></a>[ObjC ( ObjC )](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Aby ułatwić kontrolowanie poziomu dostępu przyznanego aplikacjom, usługom lub użytkownikom usługi Azure AD, utworzono następujące role, aby można je było przypisać zgodnie z potrzebami na kontach zakotwiczenia przestrzennego platformy Azure:

- **Spatial Anchors Właściciel konta:** aplikacje lub użytkownicy, którzy mają tę rolę, mogą tworzyć zakotwiczenia przestrzenne, wysyłać do nich zapytania i usuwać je. Podczas uwierzytelniania na koncie przy użyciu kluczy konta rola **Właściciel konta zakotwiczenia przestrzennego** jest przypisywana do uwierzytelnionego podmiotu.
- **Współtwórca konta zakotwiczenia przestrzennego:** aplikacje lub użytkownicy, którzy pełnią tę rolę, mogą tworzyć zakotwiczenia przestrzenne, wysyłać do nich zapytania, ale nie mogą ich usuwać.
- **Czytnik kont zakotwiczenia przestrzennego:** aplikacje lub użytkownicy, którzy pełnią tę rolę, mogą wyszukiwać tylko zakotwiczenia przestrzenne, ale nie mogą tworzyć nowych, usuwać istniejących ani aktualizować metadanych zakotwiczenia przestrzennego. Jest to zwykle używane dla aplikacji, w których niektórzy użytkownicy kurator środowiska, podczas gdy inni mogą odwołać tylko kotwice wcześniej umieszczone w tym środowisku.

## <a name="next-steps"></a>Następne kroki

Utwórz pierwszą aplikację za pomocą zakotwiczeń przestrzennych platformy Azure.

> [!div class="nextstepaction"]
> [Jedność (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Jedność (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Jedność (Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](../quickstarts/get-started-xamarin-ios.md)
