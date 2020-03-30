---
title: Przepływ kodu autoryzacji — usługa Azure Active Directory B2C | Dokumenty firmy Microsoft
description: Dowiedz się, jak tworzyć aplikacje sieci Web przy użyciu protokołu uwierzytelniania Usługi Azure AD B2C i OpenID Connect.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 8248ca0abb1d633786b09b894bcd6b1089ab2d8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260893"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>Przepływ kodu autoryzacji OAuth 2.0 w usłudze Azure Active Directory B2C

Można użyć opowu 2.0 autoryzacji kodu udzielić w aplikacjach zainstalowanych na urządzeniu, aby uzyskać dostęp do chronionych zasobów, takich jak interfejsy API sieci web. Korzystając z implementacji usługi Azure Active Directory B2C (Azure AD B2C) o treści OAuth 2.0, można dodać zadania rejestracji, logowania i innych zadań zarządzania tożsamościami do aplikacji mobilnych i klasycznych. Ten artykuł jest niezależny od języka. W tym artykule opisujemy sposób wysyłania i odbierania wiadomości HTTP bez korzystania z bibliotek typu open source.

Przepływ kodu autoryzacji OAuth 2.0 jest opisany w [sekcji 4.1 specyfikacji OAuth 2.0](https://tools.ietf.org/html/rfc6749). Można go używać do uwierzytelniania i autoryzacji w większości [typów aplikacji,](application-types.md)w tym aplikacji sieci web i natywnie zainstalowanych aplikacji. Przepływ kodu autoryzacyjnego OAuth 2.0 służy do bezpiecznego uzyskiwania tokenów dostępu i odświeżania tokenów dla aplikacji, które mogą służyć do uzyskiwania dostępu do zasobów zabezpieczonych przez [serwer autoryzacji](protocols-overview.md).  Token odświeżania umożliwia klientowi uzyskanie nowych tokenów dostępu (i odświeżania) po wygaśnięciu tokenu dostępu, zazwyczaj po godzinie.

W tym artykule koncentruje się na **klientów publicznych** OAuth 2.0 przepływ kodu autoryzacji. Klient publiczny to każda aplikacja kliencka, któremu nie można ufać, aby bezpiecznie zachować integralność tajnego hasła. Obejmuje to aplikacje mobilne, aplikacje klasyczne i zasadniczo dowolną aplikację, która działa na urządzeniu i musi uzyskać tokeny dostępu.

> [!NOTE]
> Aby dodać zarządzanie tożsamościami do aplikacji sieci web przy użyciu usługi Azure AD B2C, użyj [OpenID Connect](openid-connect.md) zamiast OAuth 2.0.

Usługa Azure AD B2C rozszerza standardowe przepływy OAuth 2.0, aby wykonać więcej niż zwykłe uwierzytelnianie i autoryzacja. Wprowadza przepływ [użytkownika](user-flow-overview.md). Dzięki przepływom użytkowników można użyć funkcji OAuth 2.0, aby dodać środowisko użytkownika do aplikacji, takie jak rejestracja, logowanie i zarządzanie profilami. Dostawcy tożsamości korzystający z protokołu OAuth 2.0 to [amazon,](identity-provider-amazon.md) [azure active directory,](identity-provider-azure-ad-single-tenant.md) [Facebook,](identity-provider-facebook.md) [GitHub](identity-provider-github.md), [Google](identity-provider-google.md)i [LinkedIn.](identity-provider-linkedin.md)

Aby wypróbować żądania HTTP w tym artykule:

1. Zamień `{tenant}` na nazwę dzierżawy usługi Azure AD B2C.
1. Zamień `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` identyfikator aplikacji zarejestrowanej wcześniej w dzierżawie usługi Azure AD B2C.
1. Zamień `{policy}` na przykład `b2c_1_sign_in`nazwę zasady utworzonej w dzierżawie.

## <a name="1-get-an-authorization-code"></a>1. Uzyskaj kod autoryzacyjny
Przepływ kodu autoryzacji rozpoczyna się od klienta `/authorize` kierującego użytkownika do punktu końcowego. Jest to interaktywna część przepływu, gdzie użytkownik podejmuje działania. W tym żądaniu klient wskazuje `scope` w parametrze uprawnienia, które musi uzyskać od użytkownika. Następujące trzy przykłady (z podziałami wierszy dla czytelności) każdy użyć innego przepływu użytkownika.


```HTTP
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
```


| Parametr | Wymagana? | Opis |
| --- | --- | --- |
|{dzierżawca}| Wymagany | Nazwa dzierżawy usługi Azure AD B2C|
| {zasady} | Wymagany | Przepływ użytkownika do uruchomienia. Określ nazwę przepływu użytkownika utworzonego w dzierżawie usługi Azure AD B2C. Na `b2c_1_sign_in`przykład: `b2c_1_sign_up`, `b2c_1_edit_profile`, lub . |
| client_id |Wymagany |Identyfikator aplikacji przypisany do aplikacji w [witrynie Azure portal](https://portal.azure.com). |
| response_type |Wymagany |Typ odpowiedzi, który `code` musi zawierać dla przepływu kodu autoryzacji. |
| redirect_uri |Wymagany |Identyfikator URI przekierowania aplikacji, w którym odpowiedzi na uwierzytelnianie są wysyłane i odbierane przez aplikację. Musi dokładnie odpowiadać jednemu z identyfikatorów URI przekierowania, które zostały zarejestrowane w portalu, z tą różnicą, że musi być zakodowany w adresie URL. |
| scope |Wymagany |Oddzielona spacja lista zakresów. Wartość pojedynczego zakresu wskazuje usługi Azure Active Directory (Azure AD) zarówno z wymaganych uprawnień. Przy użyciu identyfikatora klienta, jak zakres wskazuje, że aplikacja potrzebuje tokenu dostępu, który może być używany względem własnej usługi lub interfejsu API sieci web, reprezentowane przez ten sam identyfikator klienta.  Zakres `offline_access` wskazuje, że aplikacja potrzebuje tokenu odświeżania dla długotrwałego dostępu do zasobów. Można również użyć `openid` zakresu, aby zażądać tokenu identyfikatora z usługi Azure AD B2C. |
| response_mode |Zalecane |Metoda, której używasz do wysyłania wynikowego kodu autoryzacji z powrotem do aplikacji. Może to `query` `form_post`być `fragment`, , lub . |
| state |Zalecane |Wartość zawarta w żądaniu, która może być ciągiem dowolnej zawartości, której chcesz użyć. Zazwyczaj używana jest losowo wygenerowana unikatowa wartość, aby zapobiec atakom fałszerstwa żądań między witrynami. Stan jest również używany do kodowania informacji o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelnienia. Na przykład strona, na którą był włączony użytkownik lub przepływ użytkownika, który był wykonywany. |
| Wierszu |Optional (Opcjonalność) |Typ interakcji użytkownika, który jest wymagany. Obecnie jedyną prawidłową `login`wartością jest , która wymusza na użytkowniku wprowadzenie poświadczeń w tym żądaniu. Logowanie jednokrotne nie zostanie zastosowane. |

W tym momencie użytkownik jest proszony o ukończenie przepływu pracy przepływu użytkownika. Może to obejmować wprowadzenie nazwy użytkownika i hasła, zalogowanie się przy użyciu tożsamości społecznościowej, zarejestrowanie się w katalogu lub dowolną inną liczbę kroków. Akcje użytkownika zależą od sposobu definiowania przepływu użytkownika.

Po zakończeniu przepływu użytkownika usługa Azure AD zwraca odpowiedź do aplikacji po `redirect_uri`wartości używanej dla . Używa metody określonej w `response_mode` parametrze. Odpowiedź jest dokładnie taka sama dla każdego scenariusza akcji użytkownika, niezależnie od przepływu użytkownika, który został wykonany.

Pomyślna odpowiedź, `response_mode=query` która używa wygląda następująco:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parametr | Opis |
| --- | --- |
| kod |Kod autoryzacji, o który prosiła aplikacja. Aplikacja może użyć kodu autoryzacji, aby zażądać tokenu dostępu dla zasobu docelowego. Kody autoryzacyjne są bardzo krótkotrwałe. Zazwyczaj wygasają po około 10 minutach. |
| state |Zobacz pełny opis w tabeli w powyższej sekcji. Jeśli `state` parametr jest uwzględniony w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, `state` czy wartości w żądaniu i odpowiedzi są identyczne. |

Odpowiedzi na błędy mogą być również wysyłane do identyfikatora URI przekierowania, dzięki czemu aplikacja może obsługiwać je odpowiednio:

```HTTP
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który służy do klasyfikowania typów błędów, które występują. Można również użyć ciągu, aby reagować na błędy. |
| error_description |Określony komunikat o błędzie, który może pomóc w zidentyfikowaniu głównej przyczyny błędu uwierzytelniania. |
| state |Zobacz pełny opis w powyższej tabeli. Jeśli `state` parametr jest uwzględniony w żądaniu, ta sama wartość powinna pojawić się w odpowiedzi. Aplikacja powinna sprawdzić, `state` czy wartości w żądaniu i odpowiedzi są identyczne. |

## <a name="2-get-a-token"></a>2. Zdobądź żeton
Po nabyciu kodu autoryzacji można zrealizować `code` token dla zamierzonego zasobu, wysyłając `/token` żądanie POST do punktu końcowego. W usłudze Azure AD B2C można [zażądać tokenów dostępu dla innych interfejsów API](access-tokens.md#request-a-token) w zwykły sposób, określając ich zakresy w żądaniu.

Możesz również zażądać tokenu dostępu dla własnego interfejsu API sieci Web zaplecza aplikacji zgodnie z konwencją używania identyfikatora klienta aplikacji jako żądanego zakresu (co spowoduje token dostępu o tym identyfikatorze klienta jako "odbiorcy"):

```HTTP
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parametr | Wymagana? | Opis |
| --- | --- | --- |
|{dzierżawca}| Wymagany | Nazwa dzierżawy usługi Azure AD B2C|
|{zasady}| Wymagany| Przepływ użytkownika, który został użyty do uzyskania kodu autoryzacji. W tym żądaniu nie można użyć innego przepływu użytkownika. |
| client_id |Wymagany |Identyfikator aplikacji przypisany do aplikacji w [witrynie Azure portal](https://portal.azure.com).|
| client_secret | Tak, w aplikacjach sieci Web | Klucz tajny aplikacji, który został wygenerowany w [witrynie Azure portal](https://portal.azure.com/). Wpisy tajne klienta są używane w tym przepływie dla scenariuszy aplikacji sieci Web, w których klient może bezpiecznie przechowywać klucz tajny klienta. W scenariuszach aplikacji natywnej (klienta publicznego) wpisy tajne klienta nie mogą być bezpiecznie przechowywane i dlatego nie są używane w tym wywołaniu. Jeśli używasz klucza tajnego klienta, zmień go okresowo. |
| grant_type |Wymagany |Rodzaj dotacji. Dla przepływu kodu autoryzacji, typ `authorization_code`dotacji musi być . |
| scope |Zalecane |Oddzielona spacja lista zakresów. Wartość pojedynczego zakresu wskazuje usługi Azure AD zarówno uprawnienia, które są wymagane. Przy użyciu identyfikatora klienta, jak zakres wskazuje, że aplikacja potrzebuje tokenu dostępu, który może być używany względem własnej usługi lub interfejsu API sieci web, reprezentowane przez ten sam identyfikator klienta.  Zakres `offline_access` wskazuje, że aplikacja potrzebuje tokenu odświeżania dla długotrwałego dostępu do zasobów.  Można również użyć `openid` zakresu, aby zażądać tokenu identyfikatora z usługi Azure AD B2C. |
| kod |Wymagany |Kod autoryzacji, który został nabyty w pierwszym etapie przepływu. |
| redirect_uri |Wymagany |Przekierowanie identyfikatora URI aplikacji, w którym odebrano kod autoryzacji. |

Pomyślna odpowiedź tokenu wygląda następująco:

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parametr | Opis |
| --- | --- |
| not_before |Czas, w którym token jest uważany za prawidłowy, w czasie epoki. |
| token_type |Wartość typu tokenu. Jedynym typem, który obsługuje usługi Azure AD jest Bearer. |
| access_token |Wymagany token JSON Web Token (JWT). |
| scope |Zakresy, dla których token jest prawidłowy. Można również użyć zakresów do pamięci podręcznej tokenów do późniejszego użycia. |
| expires_in |Czas, przez który token jest prawidłowy (w sekundach). |
| refresh_token |Token odświeżania OAuth 2.0. Aplikacja może użyć tego tokenu, aby uzyskać dodatkowe tokeny po wygaśnięciu bieżącego tokenu. Tokeny odświeżania są długotrwałe. Można ich używać do zachowania dostępu do zasobów przez dłuższy czas. Aby uzyskać więcej informacji, zobacz [odwołanie do tokenu usługi Azure AD B2C](tokens-overview.md). |

Odpowiedzi na błędy wyglądają następująco:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, który służy do klasyfikowania typów błędów, które występują. Można również użyć ciągu, aby reagować na błędy. |
| error_description |Określony komunikat o błędzie, który może pomóc w zidentyfikowaniu głównej przyczyny błędu uwierzytelniania. |

## <a name="3-use-the-token"></a>3. Użyj tokenu
Teraz, gdy pomyślnie uzyskałeś token dostępu, możesz użyć tokenu w żądaniach do interfejsów `Authorization` API sieci web zaplecza, dołączając go do nagłówka:

```HTTP
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Odśwież token
Tokeny dostępu i tokeny identyfikatorów są krótkotrwałe. Po ich wygaśnięciu należy je odświeżyć, aby kontynuować dostęp do zasobów. Aby to zrobić, prześlij kolejne żądanie POST do punktu końcowego. `/token` Tym razem, `refresh_token` podaj `code`zamiast:

```HTTP
POST https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1

Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parametr | Wymagana? | Opis |
| --- | --- | --- |
|{dzierżawca}| Wymagany | Nazwa dzierżawy usługi Azure AD B2C|
|{zasady} |Wymagany |Przepływ użytkownika, który został użyty do uzyskania oryginalnego tokenu odświeżania. W tym żądaniu nie można użyć innego przepływu użytkownika. |
| client_id |Wymagany |Identyfikator aplikacji przypisany do aplikacji w [witrynie Azure portal](https://portal.azure.com). |
| client_secret | Tak, w aplikacjach sieci Web | Klucz tajny aplikacji, który został wygenerowany w [witrynie Azure portal](https://portal.azure.com/). Wpisy tajne klienta są używane w tym przepływie dla scenariuszy aplikacji sieci Web, w których klient może bezpiecznie przechowywać klucz tajny klienta. W scenariuszach aplikacji natywnej (klienta publicznego) wpisy tajne klienta nie mogą być bezpiecznie przechowywane i dlatego nie są używane w tym wywołaniu. Jeśli używasz klucza tajnego klienta, zmień go okresowo. |
| grant_type |Wymagany |Rodzaj dotacji. Dla tej części przepływu kodu autoryzacji, `refresh_token`typ dotacji musi być . |
| scope |Zalecane |Oddzielona spacja lista zakresów. Wartość pojedynczego zakresu wskazuje usługi Azure AD zarówno uprawnienia, które są wymagane. Przy użyciu identyfikatora klienta, jak zakres wskazuje, że aplikacja potrzebuje tokenu dostępu, który może być używany względem własnej usługi lub interfejsu API sieci web, reprezentowane przez ten sam identyfikator klienta.  Zakres `offline_access` wskazuje, że aplikacja będzie potrzebować tokenu odświeżania dla długotrwałego dostępu do zasobów.  Można również użyć `openid` zakresu, aby zażądać tokenu identyfikatora z usługi Azure AD B2C. |
| redirect_uri |Optional (Opcjonalność) |Przekierowanie identyfikatora URI aplikacji, w którym odebrano kod autoryzacji. |
| refresh_token |Wymagany |Oryginalny token odświeżania, który został nabyty w drugiej części przepływu. |

Pomyślna odpowiedź tokenu wygląda następująco:

```JSON
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```
| Parametr | Opis |
| --- | --- |
| not_before |Czas, w którym token jest uważany za prawidłowy, w czasie epoki. |
| token_type |Wartość typu tokenu. Jedynym typem, który obsługuje usługi Azure AD jest Bearer. |
| access_token |Podpisany JWT, o który prosiłeś. |
| scope |Zakresy, dla których token jest prawidłowy. Można również użyć zakresów do pamięci podręcznej tokenów do późniejszego użycia. |
| expires_in |Czas, przez który token jest prawidłowy (w sekundach). |
| refresh_token |Token odświeżania OAuth 2.0. Aplikacja może użyć tego tokenu, aby uzyskać dodatkowe tokeny po wygaśnięciu bieżącego tokenu. Tokeny odświeżania są długotrwałe i mogą służyć do zachowania dostępu do zasobów przez dłuższy czas. Aby uzyskać więcej informacji, zobacz [odwołanie do tokenu usługi Azure AD B2C](tokens-overview.md). |

Odpowiedzi na błędy wyglądają następująco:

```JSON
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parametr | Opis |
| --- | --- |
| error |Ciąg kodu błędu, którego można użyć do klasyfikowania typów błędów, które występują. Można również użyć ciągu, aby reagować na błędy. |
| error_description |Określony komunikat o błędzie, który może pomóc w zidentyfikowaniu głównej przyczyny błędu uwierzytelniania. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Korzystanie z własnego katalogu usługi Azure AD B2C
Aby wypróbować te żądania samodzielnie, wykonaj następujące kroki. Zastąp przykładowe wartości, których używamy w tym artykule, własnymi wartościami.

1. [Utwórz katalog usługi Azure AD B2C](tutorial-create-tenant.md). Użyj nazwy katalogu w żądaniach.
2. [Utwórz aplikację w](tutorial-register-applications.md) celu uzyskania identyfikatora aplikacji i identyfikatora URI przekierowania. Dołącz klienta macierzystego w aplikacji.
3. [Utwórz przepływy użytkownika,](user-flow-overview.md) aby uzyskać nazwy przepływu użytkownika.
