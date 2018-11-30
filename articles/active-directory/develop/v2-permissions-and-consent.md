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
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: hirsin, jesakowi, justhu
ms.custom: aaddev
ms.openlocfilehash: 5283782188eaebe3997b6de31b087da74cf10486
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/29/2018
ms.locfileid: "52620136"
---
# <a name="permissions-and-consent-in-the-azure-active-directory-v20-endpoint"></a>Uprawnienia i zgody w punkcie końcowym usługi Azure Active Directory w wersji 2.0

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Aplikacje, które integrują się z platformą Microsoft identity postępuj zgodnie z modelu autoryzacji, która zapewnia użytkownikom i administratorom kontrolę nad jak można uzyskać dostępu do danych. Implementacja modelu autoryzacji został zaktualizowany w punkcie końcowym v2.0 i zmiany, jak aplikacja może mieć interakcji z platformą Microsoft identity. W tym artykule opisano podstawowe pojęcia tego modelu autoryzacji, w tym zakresy, uprawnienia i zgody.

> [!NOTE]
> Nie obsługuje punktu końcowego v2.0, wszystkie scenariusze i funkcje. Aby ustalić, czy należy używać punktu końcowego v2.0, przeczytaj temat [ograniczenia v2.0](active-directory-v2-limitations.md).

## <a name="scopes-and-permissions"></a>Zakresy i uprawnienia

Implementuje platforma tożsamości firmy Microsoft [OAuth 2.0](active-directory-v2-protocols.md) Protokół autoryzacji. OAuth 2.0 to metoda, za pomocą którego aplikacja innej firmy ma dostęp do zasobów hostowanych w sieci web w imieniu użytkownika. Dowolny zasób hostowanych w sieci web, która integruje się z platformą Microsoft identity jest identyfikatorem zasobu lub *identyfikator URI Identyfikatora aplikacji*. Na przykład zasobów hostowanych w sieci web firmy Microsoft między innymi:

* Program Microsoft Graph: `https://graph.microsoft.com`
* Interfejs API wiadomości E-mail usługi Office 365: `https://outlook.office.com`
* Usługa Azure AD Graph: `https://graph.windows.net`

> [!NOTE]
> Zdecydowanie zaleca się używać programu Microsoft Graph zamiast programu Graph usługi Azure AD, interfejsu API usługi Office 365 wiadomości E-mail itd.

Dotyczy to także wszystkie zasoby innych firm, które mają zintegrowany z platformą Microsoft identity. Dowolnego z następujących zasobów również zdefiniować zestaw uprawnień, które mogą służyć do dzielenia funkcji tego zasobu na mniejsze części. Na przykład [programu Microsoft Graph](https://graph.microsoft.com) zdefiniował uprawnień do wykonywania następujących zadań, między innymi:

* Przeczytaj kalendarza użytkownika
* Zapis do kalendarza użytkownika
* Wysyłaj pocztę jako użytkownik

Definiując te rodzaje uprawnień, zasób ma precyzyjną kontrolę nad jego danych i jak funkcja interfejsu API jest widoczna. Aplikacja innej firmy mogą żądać tych uprawnień z użytkowników i administratorów, którzy musi zatwierdzić żądanie, zanim aplikacja może uzyskiwać dostęp do danych lub działanie w imieniu użytkownika. Według segmentu zasobu funkcje na mniejsze zestawy uprawnień, aplikacje innych firm mogą być wbudowane do żądania tylko określone uprawnienia, które są im niezbędne do wykonywania ich funkcji. Użytkownicy i Administratorzy mogą wiedzieć, dokładnie dane aplikacji ma dostęp do i może być większa pewność, że jej nie zachowuje się ze złośliwymi działaniami. Deweloperzy należy zawsze przestrzegać pojęcie minimalnych uprawnień, pytaniem, czy są dostępne tylko uprawnienia, które są im niezbędne do ich działanie aplikacji.

W OAuth, tego rodzaju uprawnienia są nazywane *zakresy*. One również często nazywany *uprawnienia*. Uprawnienie jest reprezentowany w platformie tożsamości firmy Microsoft jako wartość ciągu. Wartość ciągu dla każdego uprawnienia, kontynuując przykład programu Microsoft Graph, jest:

* Odczytaj kalendarza użytkownika za pomocą `Calendars.Read`
* Zapisać przy użyciu kalendarza użytkownika `Calendars.ReadWrite`
* Wysyłaj pocztę jako użytkownika za pomocą przez `Mail.Send`

Aplikacja najczęściej żądania punktu końcowego autoryzacji tych uprawnień, określając zakresy w żądaniach wysyłanych do wersji 2.0. Jednak pewne uprawnienia na wysokim poziomie uprawnień można udzielać tylko za pośrednictwem zgody administratora i ogólnie żądane/udzielone za pomocą [punktu końcowego zgody administratora](v2-permissions-and-consent.md#admin-restricted-scopes). Czytaj dalej, aby dowiedzieć się więcej.

## <a name="permission-types"></a>Typy uprawnień

Platforma tożsamości firmy Microsoft obsługuje dwa typy uprawnień: **delegowane uprawnienia** i **uprawnienia aplikacji**.

* **Delegowane uprawnienia** są używane przez aplikacje, które mają zalogowanego użytkownika istnieje. Dla tych aplikacji użytkownik lub administrator wyraża zgodę na uprawnienia żądań aplikacji i aplikacji to uprawnienia delegowane do działania jako zalogowanego użytkownika, gdy wykonywanie wywołań do zasobu docelowego. Niektóre uprawnienia delegowane mogą wyrażono zgodę przez użytkowników innych niż administracyjne, ale niektóre uprawnienia wyższych uprawnieniach wymagają [zgody administratora](v2-permissions-and-consent.md#admin-restricted-scopes). Aby dowiedzieć się, który administrator ról mogą wyrazić zgodę na delegowane uprawnienia, zobacz [uprawnienia roli administratora w usłudze Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

* **Uprawnienia aplikacji** są używane przez aplikacje z systemem bez zalogowanego użytkownika istnieje; na przykład aplikacji, Uruchom jako usługi w tle lub demonów.  Uprawnienia aplikacji może składać się wyłącznie [wyraził zgodę administrator](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

_Czynne uprawnienia_ uprawnień, mających aplikacji podczas wysyłania żądań do zasobu docelowego. Należy zrozumieć różnicę między delegowane uprawnienia aplikacji, które otrzymuje aplikacji i jej czynne uprawnienia, podczas wykonywania wywołań do zasobu docelowego.

- Dla uprawnienia delegowane _czynne uprawnienia_ Twojej aplikacji będą najniższych uprawnieniach przecięcie delegowane uprawnienia aplikacji udzielono (za pośrednictwem zgodę) i uprawnieniami aktualnie zalogowanego użytkownika. Aplikacja nigdy nie może mieć większych uprawnień niż zalogowany użytkownik. Uprawnienia zalogowanego użytkownika mogą być określone w organizacji na podstawie zasad lub członkostwa w co najmniej jednej roli administratora. Aby dowiedzieć się, który administrator ról mogą wyrazić zgodę na delegowane uprawnienia, zobacz [uprawnienia roli administratora w usłudze Azure AD](../users-groups-roles/directory-assign-admin-roles.md).
  Załóżmy na przykład aplikacji zostały przyznane _User.ReadWrite.All_ delegowane uprawnienia. Uprawnienie to przyznaje aplikacji nominalne uprawnienia do odczytu i aktualizowania profilu każdego użytkownika w organizacji. Jeśli zalogowany użytkownik jest administratorem globalnym, aplikacja będzie mogła zaktualizować profil każdego użytkownika w organizacji. Jednak jeśli zalogowany użytkownik nie ma roli administratora, aplikacja będzie mogła aktualizować tylko profil zalogowanego użytkownika. Użytkownik nie będzie mógł zaktualizować profili innych użytkowników w organizacji, ponieważ użytkownik mający uprawnienia do działania w imieniu innych osób nie ma odpowiednich uprawnień.
  
- Uprawnienia aplikacji _czynne uprawnienia_ aplikacji będzie pełna poziom uprawnień też dorozumianych przez uprawnienia. Na przykład aplikację, która ma _User.ReadWrite.All_ uprawnień aplikacji może też zaktualizować profil każdego użytkownika w organizacji. 

## <a name="openid-connect-scopes"></a>Zakresy uwierzytelniania OpenID Connect

V2.0 wdrażania protokołu OpenID Connect zawiera kilka zakresów dobrze zdefiniowanych, które nie są stosowane do określonego zasobu: `openid`, `email`, `profile`, i `offline_access`.

### <a name="openid"></a>openid

Jeśli aplikacja wykonuje logowania za pomocą [OpenID Connect](active-directory-v2-protocols.md), należy go zażądać `openid` zakresu. `openid` Zakresu przedstawiono na stronie zgoda konta pracy jako uprawnienie "Logowanie się w", a na osobiste strona zgody użytkownika konta Microsoft jako uprawnienie "Wyświetl swój profil i nawiązać połączenie z aplikacjami i usługami korzystającymi z Twojego konta Microsoft". Posiadając to uprawnienie, aplikacja może odbierać Unikatowy identyfikator dla użytkownika w formie `sub` oświadczenia. Daje ona również dostęp do aplikacji do punktu końcowego informacji o użytkowniku. `openid` Zakres może służyć w punkcie końcowym tokenów w wersji 2.0 można uzyskać Identyfikatora tokenów, które mogą być używane do zabezpieczania połączeń HTTP między poszczególnymi składnikami aplikacji.

### <a name="email"></a>e-mail

`email` Zakresu, może być używany z `openid` zakresu i innych. Daje ona dostęp do aplikacji, aby do adres podstawowy adres e-mail użytkownika w postaci `email` oświadczenia. `email` Oświadczenia znajduje się w tokenie, tylko wtedy, gdy jest skojarzony z konta użytkownika, które nie zawsze jest to adres e-mail. Jeśli użyto `email` zakresu, aplikacja powinna być przygotowana do obsługi przypadek, w którym `email` oświadczenia nie istnieje w tokenie.

### <a name="profile"></a>profil

`profile` Zakresu, może być używany z `openid` zakresu i innych. Daje ona dostęp do aplikacji do znacznej ilości informacji o użytkowniku. Informacje, które mogą uzyskać dostęp, obejmują, ale nie jest ograniczony do użytkownika imię, nazwisko, preferowany nazwy użytkownika i identyfikatora obiektu. Aby uzyskać pełną listę dostępnych w parametrze id_tokens oświadczeń profilu określonego użytkownika, zobacz [ `id_tokens` odwołania](id-tokens.md).

### <a name="offlineaccess"></a>offline_access

[ `offline_access` Zakres](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) pozwala aplikacji dostęp do zasobów w imieniu użytkownika przez dłuższy czas. Na stronie zgoda konta pracy ten zakres jest wyświetlana jako uprawnienie "Dostęp do danych w dowolnym momencie". Na osobiste stronie zgody konta Microsoft będzie ono wyświetlane jako uprawnienie "Dostęp do informacji w dowolnym momencie". Gdy użytkownik akceptuje `offline_access` zakresu, aplikacja może odbierać tokeny odświeżania z punktu końcowego tokenów w wersji 2.0. Długotrwałe są tokeny odświeżania. Aplikację można uzyskać nowych tokenów dostępu, jak wygaśnięciu starszych.

Jeśli aplikacja nie żąda `offline_access` zakresu, nie będzie ona otrzymywać tokenów odświeżania. Oznacza to, że po wprowadzeniu kodu autoryzacji w [przepływ kodu autoryzacji OAuth 2.0](active-directory-v2-protocols.md), otrzymasz tylko tokenu dostępu z `/token` punktu końcowego. Token dostępu jest ważny przez krótki czas. Token dostępu jest zazwyczaj wygasa w ciągu jednej godziny. AT, że punkt, Twoja aplikacja powinna przekieruje użytkownika z powrotem do `/authorize` punktu końcowego, aby uzyskać nowy kod autoryzacji. Podczas tego przekierowania, w zależności od typu aplikacji użytkownik może być konieczne ponowne wprowadzenie poświadczeń lub ponownie zgodę na uprawnienia.

Aby uzyskać więcej informacji o tym, jak pobrać i używać tokenów odświeżania, zobacz [referencyjne protokołu v2.0](active-directory-v2-protocols.md).

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

`scope` Parametr jest listę rozdzielonych spacjami delegowane uprawnienia, których żąda aplikacja. Każdego uprawnienia, które jest wskazywany przez dołączenie wartości uprawnienie do identyfikatora zasobu (identyfikator URI Identyfikatora aplikacji). W tym przykładzie żądania aplikacji wymaga uprawnień do odczytu użytkownika kalendarza, a następnie wysyłaj pocztę jako użytkownik.

Gdy użytkownik wprowadzi swoje poświadczenia, punktu końcowego v2.0 sprawdza, czy pasującego rekordu w *zgody użytkownika*. Jeśli użytkownik nie wyraził zgody na dowolne żądane uprawnienia w przeszłości, ani administrator wyraził zgody na te uprawnienia w imieniu całej organizacji, punktu końcowego v2.0 prosi użytkownika o nadanie uprawnień żądany.

![Praca zgodę konta](./media/v2-permissions-and-consent/work_account_consent.png)

Po użytkownik akceptuje żądania o uprawnienia, zgody jest rejestrowany, a użytkownik nie musi ponownie zgody na kolejne logowania do aplikacji.

## <a name="requesting-consent-for-an-entire-tenant"></a>Żądanie zgody na całej dzierżawie

Często gdy organizacja nabywa licencji lub subskrypcji dla aplikacji, organizacja chce aktywnie. Konfigurowanie aplikacji do użytku przez wszystkie elementy członkowskie w organizacji. W ramach tego procesu administrator może nadać zgodę, aplikacja może działać w imieniu dowolnego użytkownika w dzierżawie. Jeśli Administrator przydziela wyrażania zgody dla całej dzierżawy, użytkownicy w organizacji będzie widać strona zgody użytkownika dla aplikacji.

Aby zażądać zgodę na delegowane uprawnienia dla wszystkich użytkowników w dzierżawie, Twoja aplikacja może używać punktu końcowego zgody administratora.

Ponadto aplikacje muszą używać punktu końcowego zgody administratora, aby zażądać uprawnień aplikacji.

## <a name="admin-restricted-permissions"></a>Uprawnienia administratora z ograniczeniami

Można ustawić niektóre wysokiego poziomu uprawnień w ekosystemie Microsoft *ograniczonym*. Oto przykłady tego rodzaju uprawnień:

* Przeczytaj pełne profile wszystkich użytkowników za pomocą `User.Read.All`
* Zapis danych w katalogu organizacji za pomocą `Directory.ReadWrite.All`
* Odczyt wszystkich grup w katalogu organizacji przy użyciu `Groups.Read.All`

Mimo, że użytkownik klienta może udzielić aplikacji dostępu do tego rodzaju danych, użytkownicy w organizacji są ograniczeni przed udzieleniem im dostępu do tego samego zestawu poufnym danym firmy. Jeśli aplikacja żąda dostępu do jednego z tych uprawnień przez użytkownika w organizacji, użytkownik otrzymuje komunikat o błędzie stwierdzający, że nie masz uprawnień do wyrażenia zgody na uprawnienia dotyczące Twojej aplikacji.

Jeśli aplikacja wymaga dostępu do ograniczonym zakresy dla organizacji, zażądaj je bezpośrednio z poziomu administratora firmy, również przy użyciu punktu końcowego zgody administratora, opisane w dalszej części.

Jeśli aplikacja żąda uprawnienia delegowane wysokim poziomie uprawnień, a administrator przyznaje tych uprawnień za pośrednictwem punktu końcowego zgody administratora, zgoda zostanie ustanowione dla wszystkich użytkowników w dzierżawie.

Jeśli aplikacja żąda uprawnień aplikacji, a administrator daje te uprawnienia przez administratora zgody punktu końcowego, tym przydziałem nie odbywa się w imieniu dowolnego określonego użytkownika. Zamiast tego aplikacja kliencka są udzielane uprawnienia *bezpośrednio*. Tego rodzaju uprawnień zazwyczaj są używane tylko przez usługi demona oraz innych nieinterakcyjnych aplikacji, które są uruchamiane w tle.

## <a name="using-the-admin-consent-endpoint"></a>Przy użyciu punktu końcowego zgody administratora

Jeśli Administrator firmy korzysta z aplikacji, zostanie skierowany do punktu końcowego autoryzacji platforma tożsamości usługi Microsoft wykryje rolę użytkownika i poproś o tym, czy chce zgody w imieniu całej dzierżawie uprawnień, których żądasz. Istnieje jednak również punkt końcowy zgody dedykowanej administracyjnej, których można użyć, jeśli chcesz aktywnie żądania, że administrator udziela uprawnień w imieniu całej dzierżawie. Za pomocą tego punktu końcowego jest również do żądania uprawnień aplikacji, (co nie można żądać przy użyciu punktu końcowego autoryzacji).

Jeśli wykonujesz te kroki, aplikacja może zażądać uprawnień dla wszystkich użytkowników w dzierżawie, łącznie z ograniczonym zakresów. Jest to operacja wysokim poziomie uprawnień, powinno mieć miejsce tylko, jeśli to konieczne w przypadku danego scenariusza.

Aby zobaczyć przykładowy kod, który implementuje kroków, zobacz [przykładowe ograniczonym zakresy](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Żądanie uprawnień w portalu rejestracji aplikacji

Zgoda administratora nie akceptuje parametr zakresu, więc żądanej wszelkie uprawnienia muszą być statycznie zdefiniowane w rejestracji aplikacji. Ogólnie rzecz biorąc jest najlepszym rozwiązaniem, aby upewnić się, że uprawnienia zadeklarowany jako statyczny dla danej aplikacji jest nadzbiorem uprawnienia, że będzie on dynamicznie/przyrostowo żąda.

Aby skonfigurować listę statycznie żądanych uprawnień dla aplikacji: 
1. Przejdź do aplikacji w [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), lub [tworzenie aplikacji](quickstart-v2-register-an-app.md) Jeśli jeszcze go.
2. Znajdź **Microsoft Graph uprawnienia** sekcji, a następnie Dodaj uprawnienia, których wymaga aplikacja.
3. **Zapisz** rejestracji aplikacji.

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
| `tenant` | Wymagane | Dzierżawy katalogu, który chcesz zażądać uprawnień z. Mogą być podane w formacie przyjaznej nazwy lub identyfikatora GUID lub objęty przywoływane z "typowych", jak pokazano w przykładzie. |
| `client_id` | Wymagane | Identyfikator aplikacji [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) przypisany do aplikacji. |
| `redirect_uri` | Wymagane |Identyfikator URI, którego odpowiedź do wysłania dla aplikacji do obsługi przekierowania. Dokładnie musi odpowiadać jednej z przekierowania URI, który został zarejestrowany w portalu rejestracji aplikacji. |
| `state` | Zalecane | Wartość uwzględnione w żądaniu, które również zostaną zwrócone w odpowiedzi tokenu. Może być ciągiem żadnej zawartości, którą chcesz. Umożliwia kodowanie informacje o stanie użytkownika w aplikacji, zanim żądanie uwierzytelniania wystąpił, takich jak strony lub widoku, które znajdowały się w stan. |

W tym momencie usługa Azure AD wymaga tylko administrator dzierżawy może zalogować się do realizacji żądania. Administrator jest monitowany o zatwierdzenie wszystkich uprawnień, które żądanej aplikacji w portalu rejestracji aplikacji.

#### <a name="successful-response"></a>Odpowiedź oznaczająca Powodzenie

Jeśli administrator zatwierdza uprawnień dla aplikacji, odpowiedź oznaczająca Powodzenie wygląda następująco:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametr | Opis |
| --- | --- | --- |
| `tenant` | Dzierżawy katalogu, który uprawnień aplikacji żądał, w formacie identyfikatora GUID. |
| `state` | Wartość uwzględnione w żądaniu, które również zostaną zwrócone w odpowiedzi tokenu. Może być ciągiem żadnej zawartości, którą chcesz. Stan jest używany do kodowania informacje o stanie użytkownika w aplikacji, zanim żądanie uwierzytelniania wystąpił, takich jak strony lub widoku, które znajdowały się w. |
| `admin_consent` | Zostanie ustawiony na **true**. |

#### <a name="error-response"></a>Odpowiedzi na błąd

Jeśli administrator nie zatwierdzanie uprawnień aplikacji, odpowiedzi o niepowodzeniu wygląda następująco:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametr | Opis |
| --- | --- | --- |
| `error` |Ciągu kodu błędu, który może służyć do klasyfikowania typy błędów, które występują i może służyć do reagowania na błędy. |
| `error_description` |Określony komunikat o błędzie ułatwiający Deweloper odkryć ich główną przyczynę błędu. |

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

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli użytkownik lub użytkownicy twojej aplikacji jest wyświetlany nieoczekiwane błędy podczas wyrażania zgody, zobacz ten artykuł zawiera kroki rozwiązywania problemów: [nieoczekiwany błąd podczas wyrażania zgody aplikacji](../manage-apps/application-sign-in-unexpected-user-consent-error.md).
