---
title: Azure Active Directory w wersji 2.0 zakresy, uprawnienia i zgody | Dokumentacja firmy Microsoft
description: Opis autoryzacji w punktu końcowego v2.0 usługi Azure AD, w tym zakresy, uprawnienia i zgody.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
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
ms.openlocfilehash: f001751c9401b88d9bfaf35444882d3d5ccbfef3
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157340"
---
# <a name="scopes-permissions-and-consent-in-the-azure-active-directory-v20-endpoint"></a>Zakresy, uprawnienia i zgody w punkcie końcowym v2.0 usługi Azure Active Directory
Aplikacje do zintegrowania z usługą Azure Active Directory (Azure AD) wykonaj modelu autoryzacji, która zapewnia użytkownikom kontrolę nad jak aplikacja może uzyskiwać dostępu do danych. Implementacja v2.0 modelu autoryzacji została zaktualizowana i zmienia sposób aplikacji musi współdziałać z usługą Azure AD. W tym artykule opisano podstawowe pojęcia tego modelu autoryzacji, w tym zakresy, uprawnienia i zgody.

> [!NOTE]
> Punktu końcowego v2.0 nie obsługuje wszystkich scenariuszy Azure Active Directory i funkcji. Aby ustalić, czy należy używać punktu końcowego v2.0, przeczytaj o [ograniczenia v2.0](active-directory-v2-limitations.md).
>
>

## <a name="scopes-and-permissions"></a>Zakresy i uprawnień
Azure AD implementuje [OAuth 2.0](active-directory-v2-protocols.md) protokołu autoryzacji. OAuth 2.0 jest to metoda, za pomocą którego aplikacji innych firm mają dostęp do zasobów sieci web hostowanych w imieniu użytkownika. Dowolnego zasobu hostowanych w sieci web, która integruje się z usługą Azure AD ma identyfikator zasobu, lub *aplikacji identyfikator URI*. Na przykład zasobów hostowanych w sieci web firmy Microsoft są między innymi:

* Office 365 Unified poczty interfejsu API: `https://outlook.office.com`
* W przypadku interfejsu API programu Graph usługi Azure AD: `https://graph.windows.net`
* Program Microsoft Graph: `https://graph.microsoft.com`

To samo dotyczy dla wszystkich zasobów innych firm, które zostały zintegrowane z usługą Azure AD. Żadnego z tych zasobów również definiować zestaw uprawnień, które mogą służyć do dzielenia funkcjonalność tego zasobu na mniejsze fragmenty. Na przykład [Microsoft Graph](https://graph.microsoft.io) ma określone uprawnienia do wykonywania następujących zadań, między innymi:

* Przeczytaj kalendarza użytkownika
* Zapis do kalendarza użytkownika
* Wysyłaj pocztę jako użytkownik

Zdefiniowanie tych typów uprawnień, zasób ma precyzyjną kontrolę nad jego danych i jak dane są widoczne. Aplikacji innych firm mogą żądać tych uprawnień przez użytkownika aplikacji. Użytkownik aplikacji musi zatwierdzić uprawnienia, zanim aplikacja może działać w imieniu użytkownika. Według segmentu zasobu funkcje na mniejsze zestawy uprawnień, aplikacje innych firm mogą być tworzone na żądanie tylko określone uprawnienia, które są niezbędne do wykonywania ich funkcji. Użytkownicy aplikacji mogą poznać, dokładnie tak jak aplikacja będzie używać swoich danych i może być większa pewność, że aplikacja nie zachowuje się ze złośliwymi działaniami.

W usłudze Azure AD i OAuth, tego rodzaju uprawnienia są nazywane *zakresy*. One również są nazywane *oAuth2Permissions*. Zakres jest reprezentowana w usłudze Azure AD jako wartość ciągu. W ramach przykładu Microsoft Graph, jest wartość zakresu dla każdego uprawnienia:

* Przeczytaj kalendarza użytkownika za pomocą `Calendars.Read`
* Zapis do kalendarza użytkownika za pomocą `Calendars.ReadWrite`
* Wysyłania wiadomości e-mail jako użytkownika przy użyciu przez `Mail.Send`

Aplikacji mogą żądać tych uprawnień, określając zakresów w żądaniach wysyłanych do punktu końcowego v2.0.

## <a name="openid-connect-scopes"></a>Zakresy OpenID Connect
V2.0 wdrażania protokołu OpenID Connect ma kilka dobrze zdefiniowany zakresy, które nie mają zastosowanie do określonego zasobu: `openid`, `email`, `profile`, i `offline_access`.

### <a name="openid"></a>openid
Jeśli aplikacja przeprowadza logowania za pomocą [OpenID Connect](active-directory-v2-protocols.md), należy go zażądać `openid` zakresu. `openid` Zakresu wyświetlane na stronie zgoda konta pracy jako uprawnienie "Logowanie się w" oraz na osobiste strony zgody konto Microsoft jako uprawnienie "Wyświetl swój profil i łączenie z aplikacjami i usługami za pomocą konta Microsoft". Z tego uprawnienia, aplikacja może odbierać Unikatowy identyfikator dla użytkownika w postaci `sub` oświadczeń. Udostępnia również aplikacji uprawnień dostępu do punktu końcowego informacje o użytkowniku. `openid` Zakresu pozwala na token punktu końcowego v2.0 uzyskać tokeny Identyfikatora, które mogą być używane do zabezpieczenia połączenia HTTP między poszczególnymi składnikami aplikacji.

### <a name="email"></a>wyślij wiadomość e-mail
`email` Zakresu, może być używany z `openid` zakresu i innych. Daje dostęp do aplikacji do użytkownika podstawowego adresu e-mail w postaci `email` oświadczeń. `email` Oświadczenia jest uwzględniona w tokenu, tylko wtedy, gdy adres e-mail jest skojarzony z konta użytkownika, które nie zawsze jest wielkość liter. Gdy jest używana funkcja `email` zakresu, aplikacja powinna być przygotowana do obsługi przypadek, w którym `email` oświadczenia nie istnieje w tokenie.

### <a name="profile"></a>profil
`profile` Zakresu, może być używany z `openid` zakresu i innych. Daje dostęp do aplikacji znacznej ilości informacji o użytkowniku. Informacje, które mogą uzyskać dostęp obejmuje, ale nie jest ograniczona do użytkownika imię, nazwisko preferowanych nazwy użytkownika i identyfikator obiektu. Aby uzyskać pełną listę dostępnych w parametrze id_tokens oświadczeń profilu określonego użytkownika, zobacz [v2.0 tokeny odwołanie](active-directory-v2-tokens.md).

### <a name="offlineaccess"></a>offline_access
[ `offline_access` Zakres](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) pozwala aplikacji dostęp do zasobów w imieniu użytkownika przez dłuższy czas. Na stronie pracy konta zgody ten zakres jest wyświetlany jako uprawnień "W każdej chwili uzyskać dostęp do danych". Na osobiste strony zgody konta Microsoft jest widoczny jako uprawnień "W każdej chwili dostęp do informacji". Jeśli użytkownik zaakceptuje `offline_access` zakresu, aplikacja może odbierać tokeny odświeżania z punktu końcowego tokenu w wersji 2.0. Tokeny odświeżania są długotrwałe. Aplikację można uzyskać nowe tokeny dostępu, zgodnie z tych starszych wygaśnie.

Jeśli aplikacja nie żąda `offline_access` zakresu, nie będziesz otrzymywać tokenów odświeżania. Oznacza to, że gdy zrealizować kod autoryzacji w [przepływu kodu autoryzacji protokołu OAuth 2.0](active-directory-v2-protocols.md), otrzymasz tylko token dostępu z `/token` punktu końcowego. Token dostępu jest ważny przez krótki czas. Token dostępu zwykle wygaśnie w ciągu godziny. AT, że punkt, Twoja aplikacja powinna przekieruje użytkownika z powrotem do `/authorize` punktu końcowego, aby uzyskać nowy kod autoryzacji. Podczas tego przekierowania, w zależności od typu aplikacji użytkownik może być konieczne ponowne wprowadzenie poświadczeń lub ponownie wyrażenia zgody na uprawnienia.

Aby uzyskać więcej informacji o sposobie pobrania i używania tokenów odświeżania, zobacz [referencyjne protokołu v2.0](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Żąda zgody użytkownika
W [OpenID Connect i OAuth 2.0](active-directory-v2-protocols.md) żądania autoryzacji, aplikację można zażądać uprawnień, należy za pomocą `scope` parametr zapytania. Na przykład po zalogowaniu użytkownika do aplikacji, aplikacja wysyła żądanie, takich jak w poniższym przykładzie (podziałami wierszy dodane dla czytelności):

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

`scope` Parametr jest rozdzielonej spacjami listy zakresy, których żąda aplikacji. Każdego zakresu jest wskazane przez dołączenie identyfikatora zasobu (aplikacji identyfikator URI) wartość zakresu. W przykładzie żądania aplikacji wymaga zgody na odczytywanie kalendarza użytkownika i wysyłania wiadomości e-mail w imieniu użytkownika.

Gdy użytkownik wprowadzi poświadczeń, punktu końcowego v2.0 sprawdza pasującego rekordu *zgody użytkownika*. Jeśli użytkownik zgodził się nie do dowolnego zasobu w przeszłości, punktu końcowego v2.0 pyta użytkownika w celu przyznania żądanych uprawnień.

![Zgody konto robocze](../../media/active-directory-v2-flows/work_account_consent.png)

Gdy użytkownik zatwierdza uprawnienia, zgody zostaje zarejestrowana, dzięki czemu użytkownik nie musi ponownie wyrazić zgodę na kolejne konta logowania.

## <a name="requesting-consent-for-an-entire-tenant"></a>Żąda zgody dla całego dzierżawcy
Często gdy organizacja zakupi licencji lub subskrypcji dla aplikacji, organizacja chce pełni udostępniania aplikacji dla swoich pracowników. W ramach tego procesu administrator może udzielić zgody dla aplikacji, aby działać w imieniu dowolnego pracownika. Jeśli Administrator przydziela zgody na cały dzierżawy, pracownicy organizacji nie będą widzieć strony zgody dla aplikacji.

Aby poprosić o zgodę dla wszystkich użytkowników w dzierżawie, punktu końcowego zgody administratora może używać aplikacja.

## <a name="admin-restricted-scopes"></a>Ograniczonym zakresów
Można podać niektóre wysokiego poziomu uprawnień w ekosystemie Microsoft *ograniczonym*. Przykłady tego rodzaju zakresy obejmują następujące uprawnienia:

* Odczytuj dane katalogu organizacji za pomocą `Directory.Read`
* Zapis danych katalogu w organizacji za pomocą `Directory.ReadWrite`
* Przeczytaj przy użyciu grup zabezpieczeń w katalogu organizacji `Groups.Read.All`

Mimo że użytkownika klienta może umożliwić aplikacji dostęp do danych tego rodzaju, użytkowników w organizacji są ograniczone z udzielanie dostępu do tego samego zestawu poufne dane firmy. Jeśli aplikacja żąda dostępu do jednego z tych uprawnień z organizacji użytkownika, użytkownik otrzymuje komunikat o błędzie z informacją, że nie masz uprawnień do wyrażenia zgody na uprawnienia aplikacji.

Jeśli aplikacja wymaga dostępu do ograniczonym zakresy dla organizacji, należy zażądać ich bezpośrednio z administratorem firmy, również przy użyciu punktu końcowego zgody administratora, opisane w dalszej części.

Administrator przydziela te uprawnienia za pośrednictwem punktu końcowego zgody administratora, będzie udzielany zgody dla wszystkich użytkowników w dzierżawie.

## <a name="using-the-admin-consent-endpoint"></a>Przy użyciu punktu końcowego zgody administratora
Jeśli wykonujesz te kroki, aplikację można zbierać uprawnienia dla wszystkich użytkowników w dzierżawie, łącznie z ograniczonym zakresów. Aby zobaczyć przykładowy kod, który implementuje kroki, zobacz [próbki ograniczonym zakresy](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Żądanie uprawnienia w portalu rejestracji aplikacji
1. Przejdź do aplikacji w [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), lub [Utwórz aplikację](active-directory-v2-app-registration.md) Jeśli jeszcze.
2. Zlokalizuj **Microsoft Graph uprawnienia** sekcji, a następnie Dodaj uprawnienia wymagane przez aplikację.
3. Upewnij się, że **zapisać** rejestracji aplikacji.

### <a name="recommended-sign-the-user-in-to-your-app"></a>Zalecane: Zalogować użytkownika do aplikacji
Zazwyczaj podczas kompilowania aplikacji korzystającej z punktu końcowego zgody administratora, aplikacja musi strony lub widok, w którym administrator może zatwierdzić uprawnienia aplikacji. Ta strona może być część przepływu rejestracji aplikacji, część ustawień aplikacji lub może być dedykowany przepływu "connect". W wielu przypadkach warto dla aplikacji pokazać to "Połącz" widoku tylko wtedy, gdy użytkownik jest zalogowany za pomocą służbowego konta Microsoft.

Po zalogowaniu użytkownika do aplikacji, należy zidentyfikować organizacji, do którego należy administrator, przed monitem o zatwierdzenie wystarczających uprawnień. Chociaż nie niezbędne, ułatwia tworzenie bardziej intuicyjne środowisko dla użytkowników w organizacji. Aby zalogować się użytkownik, wykonaj naszych [samouczki protocol w wersji 2.0](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Zażądaj uprawnień od administratora katalogu
Gdy wszystko jest gotowe zażądać uprawnień od administratora Twojej organizacji, można przekierować użytkownika do v2.0 *punktu końcowego zgody administratora*.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parametr | Warunek | Opis |
| --- | --- | --- |
| dzierżawa |Wymagane |Dzierżawca katalogu, który chcesz zażądać uprawnień. Może być podany w formacie przyjaznej nazwy lub identyfikatora GUID lub objęty przywoływana za pomocą "typowe" jak pokazano w przykładzie. |
| client_id |Wymagane |Identyfikator aplikacji, która [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) przypisany do aplikacji. |
| redirect_uri |Wymagane |Identyfikator URI przekierowania w miejscu odpowiedź do wysłania dla aplikacji do obsługi. Dokładnie musi odpowiadać jeden przekierowania URI, który został zarejestrowany w portalu rejestracji aplikacji. |
| state |Zalecane |Wartość zawarte w żądaniu zwracana w odpowiedzi tokenu. Można go ciągiem żadnej zawartości, który ma. Umożliwia kodowanie informacje o stanie użytkownika w aplikacji przed wystąpieniem żądania uwierzytelniania, takich jak strony lub widoku, które znajdowały się w stan. |

W tym momencie usługi Azure AD wymaga administrator dzierżawy może zalogować się do wykonania żądania. Administrator musi zatwierdzić wszystkie uprawnienia, które żądanej aplikacji w portalu rejestracji aplikacji.

#### <a name="successful-response"></a>Odpowiedź oznaczająca Powodzenie
Jeśli administrator zatwierdza uprawnień dla aplikacji, odpowiedź oznaczająca Powodzenie wygląda następująco:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametr | Opis |
| --- | --- | --- |
| dzierżawa |Dzierżawy katalogu, który uprawnień aplikacji żądał, w formacie GUID. |
| state |Wartość zawarte w żądaniu, które również zostaną zwrócone w odpowiedzi tokenu. Można go ciągiem żadnej zawartości, który ma. Stan jest używany do kodowania informacje o stanie użytkownika w aplikacji, przed wystąpieniem żądania uwierzytelniania, takich jak strony lub widok, które były na. |
| admin_consent |Zostanie ustawiona do **true**. |

#### <a name="error-response"></a>Odpowiedzi na błąd
Jeśli administrator nie zatwierdzenia uprawnień dla aplikacji, nie powiodło się odpowiedzi wygląda następująco:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametr | Opis |
| --- | --- | --- |
| error |Ciąg kodu błędu, który może służyć do klasyfikowania typy błędów występujących i może służyć do reagowania na błędy. |
| error_description |Komunikat o błędzie, które mogą ułatwić dewelopera Określ przyczynę błędu. |

Po otrzymaniu pomyślnej odpowiedzi z punktu końcowego zgody administratora, aplikację zyskały zażądała uprawnienia. Następnie może żądać tokenu dla żądanego zasobu.

## <a name="using-permissions"></a>Korzystając z uprawnień
Po użytkownik wyraża zgodę na uprawnień dla aplikacji, aplikacji, mogą uzyskiwać tokeny dostępu, które reprezentują aplikacji uprawnień dostępu do zasobu w niektórych pojemności. Token dostępu można używać tylko do jednego zasobu, ale zakodowanych w tokenie dostępu jest uprawnienie każdej aplikacji ma przyznane dla tego zasobu. Uzyskanie tokenu dostępu, aplikacji ułatwia żądania tokenu punktu końcowego v2.0, jak to:

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

W żądaniach HTTP do zasobu, można użyć tokenu dostępu. Wskazuje on, niezawodnie do zasobu czy aplikacja ma odpowiednie uprawnienia do wykonywania określonych zadań. 

Aby uzyskać więcej informacji na temat protokołu OAuth 2.0 oraz pobieranie tokenów dostępu, zobacz [odwołania protokół punktu końcowego v2.0](active-directory-v2-protocols.md).
