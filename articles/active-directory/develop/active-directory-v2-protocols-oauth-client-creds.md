---
title: Umożliwia dostęp do zasobów bezpiecznego bez interakcji z użytkownikiem v2.0 usługi Azure AD | Dokumentacja firmy Microsoft
description: Tworzenie aplikacji sieci web przy użyciu usługi Azure AD implementacji protokołu uwierzytelniania OAuth 2.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 9b7cfbd7-f89f-4e33-aff2-414edd584b07
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: celested
ms.reviewer: dastrock
ms.custom: aaddev
ms.openlocfilehash: 892cf2cfc93c86b9196000d94f976407156ec248
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34594282"
---
# <a name="azure-active-directory-v20-and-the-oauth-20-client-credentials-flow"></a>V2.0 usługi Azure Active Directory i że przepływ poświadczeń klienta OAuth 2.0
Można użyć [przyznania poświadczeń klienta OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.4) określona w specyfikacji RFC 6749, nazywane również *bokami dwa OAuth*, aby uzyskać dostęp do zasobów hostowanych w sieci web przy użyciu tożsamości aplikacji. Ten typ grant często służy do interakcji do serwera, które muszą zostać uruchomione w tle, bez natychmiastowego interakcji z użytkownikiem. Często aplikacje tego typu są określane jako *demony* lub *kont usługi*.

> [!NOTE]
> Punktu końcowego v2.0 nie obsługuje wszystkich scenariuszy Azure Active Directory i funkcji. Aby ustalić, czy należy używać punktu końcowego v2.0, przeczytaj o [ograniczenia v2.0](active-directory-v2-limitations.md).
>
>

W bardziej typowego *bokami trzech OAuth*, aplikacja kliencka udzielono uprawnień do uzyskania dostępu do zasobu w imieniu określonego użytkownika. Uprawnienia delegowane przez użytkownika do aplikacji, zazwyczaj podczas [zgody](active-directory-v2-scopes.md) procesu. Jednak przepływu poświadczeń klienta, uprawnienia są przyznawane bezpośrednio do aplikacji. Użytkownik przedstawia aplikacji wymusza token do zasobu, zasobów, które aplikacja ma autoryzacji do wykonania akcji, a nie ma autoryzacji.

## <a name="protocol-diagram"></a>Diagram protokołu
Przepływ poświadczeń klienta całego wygląda podobnie do następnego diagramu. Opisano poszczególne kroki w dalszej części tego artykułu.

![Przepływ poświadczeń klienta](../../media/active-directory-v2-flows/convergence_scenarios_client_creds.png)

## <a name="get-direct-authorization"></a>Uzyskiwanie bezpośredniego autoryzacji
Aplikacja odbiera zwykle bezpośredniego autoryzacji dostępu do zasobu w jeden z dwóch sposobów: przez listy kontroli dostępu (ACL) do danego zasobu, albo przez przypisanie uprawnień aplikacji w usłudze Azure Active Directory (Azure AD). Te dwie metody są najbardziej rozpowszechnione w usłudze Azure AD, i zaleca ich dla klientów i zasobów, które wykonują klienta przepływ poświadczeń. Zasób można jednak zezwolić klientom w inny sposób. Każdy serwer zasobów można wybrać metodę najbardziej przydatny dla swojej aplikacji.

### <a name="access-control-lists"></a>Listy kontroli dostępu
Dostawca zasobów może wymuszać wyboru autoryzacji na podstawie listy identyfikatorów aplikacji wie i nieograniczony dostęp do określonego poziomu. Gdy zasób otrzymała token od punktu końcowego v2.0, można zdekodować token i wyodrębnić identyfikator aplikacji klienta z `appid` i `iss` oświadczeń. Następnie porównuje aplikacji z listy kontroli dostępu, który przechowuje. Szczegółowości ACL i metody mogą się znacznie różnić między zasobami.

Jest typowych przypadkach użycia na potrzeby kontroli Uruchom testy dla aplikacji sieci web lub interfejs API sieci Web. Interfejs API sieci Web może udzielić tylko podzbioru pełnych uprawnień do określonego klienta. Aby uruchomić testy na trasie interfejsu API, należy utworzyć klienta testu, który uzyskuje tokenów z punktem końcowym v2.0, a następnie wysyła je do interfejsu API. Interfejs API następnie sprawdza listy ACL dla Identyfikatora aplikacji klient testowy dla pełnego dostępu do całej funkcji interfejsu API. Jeśli używasz tego rodzaju listy ACL, pamiętaj zweryfikować nie tylko wywołującego `appid` wartość. Również sprawdzić, czy `iss` wartość tokenu jest zaufany.

Ten typ autoryzacji jest typowe dla demonów i kont usług, które wymagają dostępu do danych należących do użytkowników odbiorców, którzy mają osobistego konta Microsoft. Dla danych należących do organizacji firma Microsoft zaleca, możesz uzyskać wymagane zgody za pośrednictwem uprawnienia aplikacji.

### <a name="application-permissions"></a>Uprawnienia aplikacji
Zamiast przy użyciu list kontroli dostępu, możesz użyć interfejsów API do udostępnienia zestaw uprawnień aplikacji. Uprawnienia aplikacji uzyskuje do aplikacji przez administratora organizacji i może służyć tylko dostęp do danych należących do organizacji i jej pracowników. Na przykład program Microsoft Graph udostępnia kilka aplikacji uprawnienia do wykonaj następujące czynności:

* Odczytuj pocztę we wszystkich skrzynkach pocztowych
* Odczytuj i zapisuj wiadomości e-mail we wszystkich skrzynkach pocztowych
* Wysyłaj wiadomości e-mail jako dowolny użytkownik
* Odczytaj dane katalogu

Aby uzyskać więcej informacji o uprawnieniach aplikacji, przejdź do [Microsoft Graph](https://graph.microsoft.io).

Aby użyć uprawnienia aplikacji w aplikacji, wykonaj czynności, które omówiono w kolejnych sekcjach.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Żądanie uprawnienia w portalu rejestracji aplikacji
1. Przejdź do aplikacji w [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), lub [Utwórz aplikację](active-directory-v2-app-registration.md), jeśli nie jest jeszcze. Należy użyć co najmniej jeden klucz tajny aplikacji podczas tworzenia aplikacji.
2. Zlokalizuj **Microsoft Graph uprawnienia** sekcji, a następnie dodaj **uprawnienia aplikacji** aplikację wymagającej.
3. **Zapisz** rejestracji aplikacji.

#### <a name="recommended-sign-the-user-in-to-your-app"></a>Zalecane: Zalogować użytkownika do aplikacji
Zazwyczaj podczas kompilowania aplikacji korzystającej z aplikacji uprawnienia aplikacji wymaga strony lub widok, w którym administrator zatwierdzi uprawnienia aplikacji. Ta strona może być częścią aplikacji logowania przepływu, część ustawień aplikacji lub może być dedykowany przepływu "connect". W wielu przypadkach warto dla aplikacji pokazać to "Połącz" widoku tylko wtedy, gdy użytkownik jest zalogowany za pomocą służbowego konta Microsoft.

Jeśli logowania się użytkownika do aplikacji, można go zidentyfikować organizacji, do której należy użytkownik przed poprosić użytkownika o zatwierdzenie uprawnienia dostępu do aplikacji. Chociaż nie niezbędne, ułatwia tworzenie bardziej intuicyjne środowisko dla użytkowników. Aby zalogować się użytkownik, wykonaj naszych [samouczki protocol w wersji 2.0](active-directory-v2-protocols.md).

#### <a name="request-the-permissions-from-a-directory-admin"></a>Zażądaj uprawnień od administratora katalogu
Gdy wszystko jest gotowe zażądać uprawnień od administratora Twojej organizacji, można przekierować użytkownika do v2.0 *punktu końcowego zgody administratora*.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the following request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parametr | Warunek | Opis |
| --- | --- | --- |
| dzierżawa |Wymagane |Dzierżawca katalogu, który chcesz zażądać uprawnień. Może to być w formacie przyjaznej nazwy lub identyfikatora GUID. Jeśli nie wiesz, jakiego użytkownik należy do dzierżawy i chcesz umożliwić Zaloguj się przy użyciu dowolnej dzierżawy, użyj `common`. |
| client_id |Wymagane |Identyfikator aplikacji, która [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) przypisany do aplikacji. |
| redirect_uri |Wymagane |Identyfikator URI przekierowania w miejscu odpowiedź do wysłania dla aplikacji do obsługi. Go musi dokładnie odpowiadać jeden przekierowania URI, który został zarejestrowany w portalu, z wyjątkiem tego, że musi być zakodowane w adresie URL, i może mieć segmenty ścieżki dodatkowe. |
| state |Zalecane |Wartość, która znajduje się w żądaniu jest także zwracany w odpowiedzi tokenu. Można go ciąg ma zawartość. Stan jest używany do kodowania informacje o stanie użytkownika w aplikacji, przed wystąpieniem żądania uwierzytelniania, takich jak strony lub widok, które były na. |

W tym momencie usługi Azure AD wymusza, czy administrator dzierżawy może zalogować się do wykonania żądania. Administrator będzie musiał zatwierdzić wszystkie uprawnienia bezpośrednich zastosowań, które żądanej aplikacji w portalu rejestracji aplikacji.

##### <a name="successful-response"></a>Odpowiedź oznaczająca Powodzenie
Jeśli administrator zatwierdza uprawnienia dla aplikacji, odpowiedź oznaczająca Powodzenie wygląda następująco:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametr | Opis |
| --- | --- | --- |
| dzierżawa |Katalogu dzierżawy, którym przyznano uprawnienia, które są wymagane, w formacie GUID aplikacji. |
| state |Wartość, która znajduje się w żądaniu jest także zwracany w odpowiedzi tokenu. Można go ciąg ma zawartość. Stan jest używany do kodowania informacje o stanie użytkownika w aplikacji, przed wystąpieniem żądania uwierzytelniania, takich jak strony lub widok, które były na. |
| admin_consent |Ustaw **true**. |

##### <a name="error-response"></a>Odpowiedzi na błąd
Jeśli administrator nie zatwierdzenia uprawnień dla aplikacji, nie powiodło się odpowiedzi wygląda następująco:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametr | Opis |
| --- | --- | --- |
| error |Ciąg kodu błędu, który służy do klasyfikowania typy błędów, które można wykorzystać do reagowania na błędy. |
| error_description |Komunikat o błędzie, które mogą pomóc Określ przyczynę błędu. |

Po otrzymaniu pomyślnej odpowiedzi z aplikacji inicjowania obsługi administracyjnej punktu końcowego, aplikację zyskały zażądała uprawnienia bezpośredniego stosowania. Teraz można żądać tokenu dla zasobu, który ma.

## <a name="get-a-token"></a>Uzyskaj token
Po zostały nabyte niezbędne autoryzacji dla aplikacji, kontynuuj pobieranie tokenów dostępu do interfejsów API. Aby uzyskać token za pomocą klienta poświadczeń, Wyślij żądanie POST `/token` punktu końcowego v2.0:

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Najpierw przypadek: żądanie tokenu dostępu z wspólny klucz tajny

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Parametr | Warunek | Opis |
| --- | --- | --- |
| dzierżawa |Wymagane | Plany działać, w formacie nazwy domeny lub identyfikator GUID dzierżawy katalogu aplikacji. |
| client_id |Wymagane |Identyfikator aplikacji, która [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) przypisany do aplikacji. |
| scope |Wymagane |Wartość przekazana do `scope` parametr w tym żądaniu powinien być identyfikatora zasobów (URI Identyfikatora aplikacji), należy umieścić z zasobu `.default` sufiks. Na przykład program Microsoft Graph, wartość jest `https://graph.microsoft.com/.default`. Ta wartość informuje punktu końcowego v2.0, że wszystkie bezpośredniego stosowania uprawnień skonfigurowanych dla aplikacji, jego powinien wystawiać token dla skojarzone z zasobem, który ma być używany. |
| client_secret |Wymagane |Klucz tajny aplikacji, generowany dla aplikacji w portalu rejestracji aplikacji. |
| Typ grant_type |Wymagane |Musi być `client_credentials`. |

### <a name="second-case-access-token-request-with-a-certificate"></a>W drugim przypadku: żądanie tokenu dostępu przy użyciu certyfikatu

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| Parametr | Warunek | Opis |
| --- | --- | --- |
| dzierżawa |Wymagane | Plany działać, w formacie nazwy domeny lub identyfikator GUID dzierżawy katalogu aplikacji. |
| client_id |Wymagane |Identyfikator aplikacji, która [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) przypisany do aplikacji. |
| scope |Wymagane |Wartość przekazana do `scope` parametr w tym żądaniu powinien być identyfikatora zasobów (URI Identyfikatora aplikacji), należy umieścić z zasobu `.default` sufiks. Na przykład program Microsoft Graph, wartość jest `https://graph.microsoft.com/.default`. Ta wartość informuje punktu końcowego v2.0, że wszystkie bezpośredniego stosowania uprawnień skonfigurowanych dla aplikacji, jego powinien wystawiać token dla skojarzone z zasobem, który ma być używany. |
| client_assertion_type |wymagane |Wartość musi być `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |wymagane | (JSON Web Token) potwierdzenia, że musisz utworzyć i podpisać przy użyciu certyfikatu został zarejestrowany jako poświadczeń dla aplikacji. Przeczytaj informacje o [certyfikatu poświadczeń](active-directory-certificate-credentials.md) informacje na temat rejestracji certyfikatu i format potwierdzenia.|
| Typ grant_type |Wymagane |Musi być `client_credentials`. |

Należy zauważyć, że parametry są prawie takie same jak w przypadku żądania przez Wspólny klucz tajny, z wyjątkiem tego, że parametr client_secret zostało zastąpione przez dwa parametry: client_assertion_type i client_assertion.

### <a name="successful-response"></a>Odpowiedź oznaczająca Powodzenie
Odpowiedź oznaczająca Powodzenie wygląda następująco:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parametr | Opis |
| --- | --- |
| access_token |Żądany dostęp token. Aplikacja może używać tego tokenu do uwierzytelniania do zabezpieczonych zasobów, takich jak interfejs API sieci Web. |
| token_type |Wskazuje wartość typ tokenu. Jedynym typem, który obsługuje usługę Azure AD jest `bearer`. |
| expires_in |Jak długo token dostępu jest nieprawidłowy (w sekundach). |

### <a name="error-response"></a>Odpowiedzi na błąd
Odpowiedzi na błąd wygląda następująco:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który służy do klasyfikowania typy błędów występujących i reagowanie na błędy. |
| error_description |Komunikat o błędzie, które mogą ułatwić identyfikację przyczyny błędu uwierzytelniania. |
| error_codes |Lista specyficzne dla usługi STS kody błędów, które mogą ułatwić rozwiązanie diagnostyki. |
| sygnatura czasowa |Czas, w którym wystąpił błąd. |
| trace_id |Unikatowy identyfikator dla żądania, które mogą ułatwić rozwiązanie diagnostyki. |
| correlation_id |Unikatowy identyfikator dla żądania, które mogą pomóc w diagnostyce między składnikami. |

## <a name="use-a-token"></a>Użyj tokenu
Teraz, gdy zostały nabyte token, użyj tokenu, aby wysyłać żądania do zasobu. Po wygaśnięciu tokenu, powtórz żądanie `/token` punktu końcowego można uzyskać tokenu dostępu świeże.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro tip: Try the following command! (Replace the token with your own.)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-sample"></a>Przykład kodu
Aby zapoznać się przykładem aplikacji czy implementuje poświadczeń klienta przyznać za pośrednictwem administratora zgody punktu końcowego, zobacz nasze [przykładowy kod demon v2.0](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
