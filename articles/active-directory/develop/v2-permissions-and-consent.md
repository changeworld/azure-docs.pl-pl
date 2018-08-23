---
title: Usługa Azure Active Directory w wersji 2.0 zakresy, uprawnienia i zgody | Dokumentacja firmy Microsoft
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
ms.date: 08/21/2018
ms.author: celested
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.openlocfilehash: 6d3847f547646ae7c62f98b4cee716af5c6ba5e9
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2018
ms.locfileid: "42056158"
---
# <a name="scopes-permissions-and-consent-in-the-azure-active-directory-v20-endpoint"></a>Zakresy, uprawnienia i zgody w punkcie końcowym usługi Azure Active Directory w wersji 2.0
Aplikacje, które integrują się z usługą Azure Active Directory (Azure AD) wykonaj modelu autoryzacji, która zapewnia użytkownikom kontrolę nad jak aplikacja może uzyskiwać dostęp do swoich danych. Zaktualizowano v2.0 stosowania modelu autoryzacji i zmiany, jak aplikacja może mieć interakcji z usługą Azure AD. W tym artykule opisano podstawowe pojęcia tego modelu autoryzacji, w tym zakresy, uprawnienia i zgody.

> [!NOTE]
> Nie obsługuje punktu końcowego v2.0, wszystkie scenariusze usługi Azure Active Directory i funkcje. Aby ustalić, czy należy używać punktu końcowego v2.0, przeczytaj temat [ograniczenia v2.0](active-directory-v2-limitations.md).
>
>

## <a name="scopes-and-permissions"></a>Zakresy i uprawnienia
Usługa Azure AD implementuje [OAuth 2.0](active-directory-v2-protocols.md) Protokół autoryzacji. OAuth 2.0 to metoda, za pomocą którego aplikacja innej firmy ma dostęp do zasobów hostowanych w sieci web w imieniu użytkownika. Dowolny zasób hostowanych w sieci web, która integruje się z usługą Azure AD jest identyfikatorem zasobu lub *identyfikator URI Identyfikatora aplikacji*. Na przykład zasobów hostowanych w sieci web firmy Microsoft między innymi:

* Pakiet Office 365 ujednoliconego interfejsu API w wiadomości E-mail: `https://outlook.office.com`
* Interfejs API programu Graph usługi Azure AD: `https://graph.windows.net`
* Program Microsoft Graph: `https://graph.microsoft.com`

Dotyczy to także wszystkie zasoby innych firm, które mają zintegrowany z usługą Azure AD. Dowolnego z następujących zasobów również zdefiniować zestaw uprawnień, które mogą służyć do dzielenia funkcji tego zasobu na mniejsze części. Na przykład [programu Microsoft Graph](https://graph.microsoft.io) zdefiniował uprawnień do wykonywania następujących zadań, między innymi:

* Przeczytaj kalendarza użytkownika
* Zapis do kalendarza użytkownika
* Wysyłaj pocztę jako użytkownik

Definiując te rodzaje uprawnień, zasób ma precyzyjną kontrolę nad jego danych oraz uwidaczniania danych. Aplikacji innych firm mogą żądać tych uprawnień przez użytkownika aplikacji. Użytkownik aplikacji musi zatwierdzić uprawnienia, zanim aplikacja może działać w imieniu użytkownika. Według segmentu zasobu funkcje na mniejsze zestawy uprawnień, aplikacje innych firm mogą być wbudowane do żądania tylko określone uprawnienia, które są im niezbędne do wykonywania ich funkcji. Użytkownicy aplikacji mogą wiedzieć, dokładnie tak jak aplikacja będzie używać swoich danych i może być większa pewność, że aplikacja nie zachowuje się ze złośliwymi działaniami.

W usłudze Azure AD i OAuth, tego rodzaju uprawnienia są nazywane *zakresy*. Mogą również czasami są nazywane *oAuth2Permissions*. Zakres jest reprezentowana w usłudze Azure AD jako wartość ciągu. Wartość zakresu dla każdego uprawnienia, kontynuując przykład programu Microsoft Graph, jest:

* Odczytaj kalendarza użytkownika za pomocą `Calendars.Read`
* Zapisać przy użyciu kalendarza użytkownika `Calendars.ReadWrite`
* Wysyłaj pocztę jako użytkownika za pomocą przez `Mail.Send`

Aplikację można zażądać te uprawnienia, określając zakresy w żądaniach wysyłanych do punktu końcowego v2.0.

## <a name="openid-connect-scopes"></a>Zakresy uwierzytelniania OpenID Connect
V2.0 wdrażania protokołu OpenID Connect zawiera kilka zakresów dobrze zdefiniowanych, które nie są stosowane do określonego zasobu: `openid`, `email`, `profile`, i `offline_access`.

### <a name="openid"></a>openid
Jeśli aplikacja wykonuje logowania za pomocą [OpenID Connect](active-directory-v2-protocols.md), należy go zażądać `openid` zakresu. `openid` Zakresu przedstawiono na stronie zgoda konta pracy jako uprawnienie "Logowanie się w", a na osobiste strona zgody użytkownika konta Microsoft jako uprawnienie "Wyświetl swój profil i nawiązać połączenie z aplikacjami i usługami korzystającymi z Twojego konta Microsoft". Posiadając to uprawnienie, aplikacja może odbierać Unikatowy identyfikator dla użytkownika w formie `sub` oświadczenia. Daje ona również dostęp do aplikacji do punktu końcowego informacji o użytkowniku. `openid` Zakres może służyć w punkcie końcowym tokenów w wersji 2.0 można uzyskać Identyfikatora tokenów, które mogą być używane do zabezpieczania połączeń HTTP między poszczególnymi składnikami aplikacji.

### <a name="email"></a>e-mail
`email` Zakresu, może być używany z `openid` zakresu i innych. Daje ona dostęp do aplikacji, aby do adres podstawowy adres e-mail użytkownika w postaci `email` oświadczenia. `email` Oświadczenia znajduje się w tokenie, tylko wtedy, gdy jest skojarzony z konta użytkownika, które nie zawsze jest to adres e-mail. Jeśli użyto `email` zakresu, aplikacja powinna być przygotowana do obsługi przypadek, w którym `email` oświadczenia nie istnieje w tokenie.

### <a name="profile"></a>Profil
`profile` Zakresu, może być używany z `openid` zakresu i innych. Daje ona dostęp do aplikacji do znacznej ilości informacji o użytkowniku. Informacje, które mogą uzyskać dostęp, obejmują, ale nie jest ograniczony do użytkownika imię, nazwisko, preferowany nazwy użytkownika i identyfikatora obiektu. Aby uzyskać pełną listę dostępnych w parametrze id_tokens oświadczeń profilu określonego użytkownika, zobacz [v2.0 tokenów odwołanie](v2-id-and-access-tokens.md).

### <a name="offlineaccess"></a>offline_access
[ `offline_access` Zakres](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) pozwala aplikacji dostęp do zasobów w imieniu użytkownika przez dłuższy czas. Na stronie zgoda konta pracy ten zakres jest wyświetlana jako uprawnienie "Dostęp do danych w dowolnym momencie". Na osobiste stronie zgody konta Microsoft będzie ono wyświetlane jako uprawnienie "Dostęp do informacji w dowolnym momencie". Gdy użytkownik akceptuje `offline_access` zakresu, aplikacja może odbierać tokeny odświeżania z punktu końcowego tokenów w wersji 2.0. Długotrwałe są tokeny odświeżania. Aplikację można uzyskać nowych tokenów dostępu, jak wygaśnięciu starszych.

Jeśli aplikacja nie żąda `offline_access` zakresu, nie będzie ona otrzymywać tokenów odświeżania. Oznacza to, że po wprowadzeniu kodu autoryzacji w [przepływ kodu autoryzacji OAuth 2.0](active-directory-v2-protocols.md), otrzymasz tylko tokenu dostępu z `/token` punktu końcowego. Token dostępu jest ważny przez krótki czas. Token dostępu jest zazwyczaj wygasa w ciągu jednej godziny. AT, że punkt, Twoja aplikacja powinna przekieruje użytkownika z powrotem do `/authorize` punktu końcowego, aby uzyskać nowy kod autoryzacji. Podczas tego przekierowania, w zależności od typu aplikacji użytkownik może być konieczne ponowne wprowadzenie poświadczeń lub ponownie zgodę na uprawnienia.

Aby uzyskać więcej informacji o tym, jak pobrać i używać tokenów odświeżania, zobacz [referencyjne protokołu v2.0](active-directory-v2-protocols.md).

## <a name="accessing-v10-resources"></a>Uzyskiwanie dostępu do zasobów w wersji 1.0
aplikacje w wersji 2.0 może żądać tokenów i zgody dla aplikacji w wersji 1.0 (np. interfejsu API usługi Power BI `https://analysis.windows.net/powerbi/api` interfejsu API programu Sharepoint lub `https://{tenant}.sharepoint.com`).  Aby to zrobić, możesz odwoływać się do ciągu identyfikatora URI i zakresu aplikacji w `scope` parametru.  Na przykład `scope=https://analysis.windows.net/powerbi/api/Dataset.Read.All` będzie żądania usługi Power BI `View all Datasets` uprawnienia dla aplikacji. 

Aby zażądać wiele uprawnień, należy dołączyć całego identyfikatora URI ze spacją lub `+`, np. `scope=https://analysis.windows.net/powerbi/api/Dataset.Read.All+https://analysis.windows.net/powerbi/api/Report.Read.All`.  Żąda zarówno `View all Datasets` i `View all Reports` uprawnienia.  Należy pamiętać, że podobnie jak w przypadku wszystkich zakresów w usłudze Azure AD i uprawnienia aplikacji można wprowadzać tylko żądania do jednego zasobu naraz — więc żądania `scope=https://analysis.windows.net/powerbi/api/Dataset.Read.All+https://api.skypeforbusiness.com/Conversations.Initiate`, który żąda zarówno usługi Power BI `View all Datasets` uprawnień i Skype dla firm `Initiate conversations` uprawnienia zostanie odrzucone z powodu żądania uprawnień na dwóch różnych zasobów.  

### <a name="v10-resources-and-tenancy"></a>zasoby w wersji 1.0 i dzierżawy
Protokoły usługi Azure AD w wersji 1.0 i 2.0 używają `{tenant}` parametru osadzone w identyfikatorze URI (`https://login.microsoftonline.com/{tenant}/oauth2/`).  Dostęp do zasobów organizacji w wersji 1.0 za pomocą punktu końcowego v2.0 `common` i `consumers` dzierżawcy nie można użyć, ponieważ te zasoby są dostępne jedynie w usłudze organizacji (Azure AD) konta.  Dlatego podczas uzyskiwania dostępu do tych zasobów, a tylko identyfikator GUID dzierżawy lub `organizations` mogą być używane jako `{tenant}` parametru.  

Jeśli aplikacja podejmie próbę dostępu do zasobów organizacji w wersji 1.0 przy użyciu niepoprawne dzierżawy, będzie zwracany błąd podobny do przedstawionego poniżej. 

`AADSTS90124: Resource 'https://analysis.windows.net/powerbi/api' (Microsoft.Azure.AnalysisServices) is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.`


## <a name="requesting-individual-user-consent"></a>Żądanie zgody użytkownika
W [OpenID Connect i OAuth 2.0](active-directory-v2-protocols.md) autoryzacji żądania, może zażądać uprawnień potrzebuje za pomocą, aplikację `scope` parametr zapytania. Na przykład gdy użytkownik loguje się do aplikacji, aplikacja wysyła żądanie, takie jak w poniższym przykładzie (z podziały wierszy dodane w celu uzyskania czytelności):

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

`scope` Parametr jest rozdzielonej spacjami listy zakresów, których żąda aplikacja. Każdego zakresu jest wskazywany przez dołączenie wartości zakresu do identyfikatora zasobu (identyfikator URI Identyfikatora aplikacji). W tym przykładzie żądania aplikacji wymaga uprawnień do odczytu użytkownika kalendarza, a następnie wysyłaj pocztę jako użytkownik.

Gdy użytkownik wprowadzi swoje poświadczenia, punktu końcowego v2.0 sprawdza, czy pasującego rekordu w *zgody użytkownika*. Jeśli użytkownik nie wyraził zgodę na dowolne żądane uprawnienia w przeszłości, punktu końcowego v2.0 z monitem o żądane uprawnienia.

![Praca zgodę konta](./media/v2-permissions-and-consent/work_account_consent.png)

Po użytkownik zatwierdza uprawnienie, zgoda jest rejestrowana tak, aby użytkownik nie musi ponownie zgody na kolejne logowania.

## <a name="requesting-consent-for-an-entire-tenant"></a>Żądanie zgody na całej dzierżawie
Często gdy organizacja nabywa licencji lub subskrypcji dla aplikacji, organizacja chce pełną aprowizację aplikacji dla swoich pracowników. W ramach tego procesu administrator może nadać zgodę, aplikacja może działać w imieniu dowolnego pracownika. Jeśli Administrator przydziela zgody na całej dzierżawie, pracownicy organizacji nie będzie widoczna strona zgody użytkownika dla aplikacji.

W celu zażądania zgody dla wszystkich użytkowników w dzierżawie, Twoja aplikacja może używać punktu końcowego zgody administratora.

## <a name="admin-restricted-scopes"></a>Ograniczonym zakresów
Można ustawić niektóre wysokiego poziomu uprawnień w ekosystemie Microsoft *ograniczonym*. Przykłady te rodzaje zakresów następujące uprawnienia:

* Odczyt danych katalogu w organizacji za pomocą `Directory.Read`
* Zapis danych w katalogu organizacji za pomocą `Directory.ReadWrite`
* Odczytaj grup zabezpieczeń w katalogu organizacji za pomocą `Groups.Read.All`

Mimo, że użytkownik klienta może udzielić aplikacji dostępu do tego rodzaju danych, użytkownicy w organizacji są ograniczeni przed udzieleniem im dostępu do tego samego zestawu poufnym danym firmy. Jeśli aplikacja żąda dostępu do jednego z tych uprawnień przez użytkownika w organizacji, użytkownik otrzymuje komunikat o błędzie stwierdzający, że nie masz uprawnień do wyrażenia zgody na uprawnienia dotyczące Twojej aplikacji.

Jeśli aplikacja wymaga dostępu do ograniczonym zakresy dla organizacji, zażądaj je bezpośrednio z poziomu administratora firmy, również przy użyciu punktu końcowego zgody administratora, opisane w dalszej części.

Jeśli administrator przydziela te uprawnienia za pośrednictwem punktu końcowego zgody administratora, zgody jest udzielany dla wszystkich użytkowników w dzierżawie.

## <a name="using-the-admin-consent-endpoint"></a>Przy użyciu punktu końcowego zgody administratora
Jeśli wykonujesz te kroki, aplikację można zbierać uprawnień dla wszystkich użytkowników w dzierżawie, łącznie z ograniczonym zakresów. Aby zobaczyć przykładowy kod, który implementuje kroków, zobacz [przykładowe ograniczonym zakresy](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Żądanie uprawnień w portalu rejestracji aplikacji
1. Przejdź do aplikacji w [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), lub [tworzenie aplikacji](quickstart-v2-register-an-app.md) Jeśli jeszcze go.
2. Znajdź **Microsoft Graph uprawnienia** sekcji, a następnie Dodaj uprawnienia, których wymaga aplikacja.
3. Upewnij się, że **Zapisz** rejestracji aplikacji.

### <a name="recommended-sign-the-user-in-to-your-app"></a>Zalecane: Zalogować użytkownika do aplikacji
Zazwyczaj podczas kompilowania aplikacji korzystającej z punktu końcowego zgody administratora, aplikacja musi strony lub widok, w którym administrator może zatwierdzić uprawnienia aplikacji. Ta strona może być część przepływu rejestracji aplikacji, część ustawień aplikacji lub może być dedykowany przepływ "Połącz". W wielu przypadkach dobrym pomysłem dla aplikacji wyświetlić to "łączenie" Wyświetl tylko wtedy, gdy użytkownik zalogował się przy użyciu służbowego konta Microsoft.

Po zalogowaniu użytkownika do aplikacji, można zidentyfikować organizacji, do której należy administrator, przed monitem o zatwierdzenia niezbędnych uprawnień. Chociaż nie niezbędne, pomoże Ci utworzyć bardziej intuicyjne środowisko dla użytkowników w organizacji. Aby utworzyć użytkownika, postępuj zgodnie z naszym [samouczki protocol w wersji 2.0](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Żądanie uprawnień od administratora katalogu
Gdy wszystko będzie gotowe zażądać uprawnień z administratorem Twojej organizacji, można przekierować użytkownika do v2.0 *punktu końcowego zgody administratora*.

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
| dzierżawa |Wymagane |Dzierżawy katalogu, który chcesz zażądać uprawnień z. Mogą być podane w formacie przyjaznej nazwy lub identyfikatora GUID lub objęty przywoływane z "typowych", jak pokazano w przykładzie. |
| client_id |Wymagane |Identyfikator aplikacji [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) przypisany do aplikacji. |
| redirect_uri |Wymagane |Identyfikator URI, którego odpowiedź do wysłania dla aplikacji do obsługi przekierowania. Dokładnie musi odpowiadać jednej z przekierowania URI, który został zarejestrowany w portalu rejestracji aplikacji. |
| state |Zalecane |Wartość uwzględnione w żądaniu, które również zostaną zwrócone w odpowiedzi tokenu. Może być ciągiem żadnej zawartości, którą chcesz. Umożliwia kodowanie informacje o stanie użytkownika w aplikacji, zanim żądanie uwierzytelniania wystąpił, takich jak strony lub widoku, które znajdowały się w stan. |

W tym momencie usługa Azure AD wymaga tylko administrator dzierżawy może zalogować się do realizacji żądania. Administrator jest monitowany o zatwierdzenie wszystkich uprawnień, które żądanej aplikacji w portalu rejestracji aplikacji.

#### <a name="successful-response"></a>Odpowiedź oznaczająca Powodzenie
Jeśli administrator zatwierdza uprawnień dla aplikacji, odpowiedź oznaczająca Powodzenie wygląda następująco:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametr | Opis |
| --- | --- | --- |
| dzierżawa |Dzierżawy katalogu, który uprawnień aplikacji żądał, w formacie identyfikatora GUID. |
| state |Wartość uwzględnione w żądaniu, które również zostaną zwrócone w odpowiedzi tokenu. Może być ciągiem żadnej zawartości, którą chcesz. Stan jest używany do kodowania informacje o stanie użytkownika w aplikacji, zanim żądanie uwierzytelniania wystąpił, takich jak strony lub widoku, które znajdowały się w. |
| admin_consent |Zostanie ustawiony na **true**. |

#### <a name="error-response"></a>Odpowiedzi na błąd
Jeśli administrator nie zatwierdzanie uprawnień aplikacji, odpowiedzi o niepowodzeniu wygląda następująco:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametr | Opis |
| --- | --- | --- |
| error |Ciągu kodu błędu, który może służyć do klasyfikowania typy błędów, które występują i może służyć do reagowania na błędy. |
| error_description |Określony komunikat o błędzie ułatwiający Deweloper odkryć ich główną przyczynę błędu. |

Po otrzymaniu pomyślnej odpowiedzi z punktu końcowego zgody administratora, aplikację zdobyła go wymagane uprawnienia. Następnie możesz zażądać token dla żądanego zasobu.

## <a name="using-permissions"></a>Przy użyciu uprawnień
Po użytkownik wyraża zgodę na uprawnienia dla swojej aplikacji, aplikacji można uzyskać tokenów dostępu, które reprezentują aplikacji uprawnień dostępu do zasobu w niektórych pojemności. Token dostępu może służyć tylko do jednego zasobu zakodowanych w tokenie dostępu jest jednak wszystkie uprawnienia, która aplikacja ma przyznane dla tego zasobu. Uzyskanie tokenu dostępu, aplikacja zgłosić wniosek aby token punktu końcowego v2.0, następująco:

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

W żądaniach HTTP do zasobu, można użyć wynikowy token dostępu. Niezawodne wskazuje na zasób czy aplikacja ma odpowiednie uprawnienia do wykonywania określonych zadań. 

Aby uzyskać więcej informacji na temat protokołu OAuth 2.0 i uzyskiwanie tokenów dostępu, zobacz [referencyjne protokołu punktu końcowego v2.0](active-directory-v2-protocols.md).
