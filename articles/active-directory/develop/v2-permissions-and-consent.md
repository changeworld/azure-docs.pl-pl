---
title: Zakresy platforma tożsamości firmy Microsoft, uprawnienia i zgody | Dokumentacja firmy Microsoft
description: Opis autoryzacji końcowy platformy tożsamości firmy Microsoft, w tym zakresy, uprawnienia i zgody.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 032cc0edaa140d82124a7369232cb82bf6c00c10
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702705"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Uprawnienia i zgody w punkcie końcowym platforma tożsamości firmy Microsoft

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Aplikacje, które integrują się z platformą Microsoft identity postępuj zgodnie z modelu autoryzacji, która zapewnia użytkownikom i administratorom kontrolę nad jak można uzyskać dostępu do danych. Implementacja modelu autoryzacji został zaktualizowany w punkcie końcowym platforma tożsamości firmy Microsoft, a zmiany, jak aplikacja może mieć interakcji z platformą Microsoft identity. W tym artykule opisano podstawowe pojęcia tego modelu autoryzacji, w tym zakresy, uprawnienia i zgody.

> [!NOTE]
> Punkt końcowy platforma tożsamości firmy Microsoft nie obsługuje wszystkie scenariusze i funkcje. Aby ustalić, czy należy używać punktu końcowego platformy tożsamości firmy Microsoft, przeczytaj temat [ograniczenia dotyczące programu Microsoft identity platformy](active-directory-v2-limitations.md).

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

Definiując te rodzaje uprawnień, zasób ma precyzyjną kontrolę nad jego danych i jak funkcja interfejsu API jest widoczna. Aplikacja innej firmy mogą żądać tych uprawnień z użytkowników i administratorów, którzy musi zatwierdzić żądanie, zanim aplikacja może uzyskiwać dostęp do danych lub działanie w imieniu użytkownika. Według segmentu zasobu funkcje na mniejsze zestawy uprawnień, aplikacje innych firm mogą być wbudowane do żądania tylko określone uprawnienia, które są im niezbędne do wykonywania ich funkcji. Użytkownicy i Administratorzy mogą wiedzieć, dokładnie dane aplikacji ma dostęp do i może być większa pewność nie zachowuje się ze złośliwymi działaniami. Deweloperzy należy zawsze przestrzegać pojęcie minimalnych uprawnień, pytaniem, czy są dostępne tylko uprawnienia, które są im niezbędne do ich działanie aplikacji.

OAuth w wersji 2.0 tego rodzaju uprawnienia są nazywane *zakresy*. Dlatego są często nazywane *uprawnienia*. Uprawnienie jest reprezentowany w platformie tożsamości firmy Microsoft jako wartość ciągu. Wartość ciągu dla każdego uprawnienia, kontynuując przykład programu Microsoft Graph, jest:

* Odczytaj kalendarza użytkownika za pomocą `Calendars.Read`
* Zapisać przy użyciu kalendarza użytkownika `Calendars.ReadWrite`
* Wysyłaj pocztę jako użytkownika za pomocą przez `Mail.Send`

Aplikacja żądania najczęściej te uprawnienia, określając zakresy w żądaniach wysyłanych do firmy Microsoft platformy tożsamości punktu końcowego autoryzacji. Jednak pewne uprawnienia na wysokim poziomie uprawnień można tylko udzielone za zgodą administratora i żądane/przyznać za pomocą [punktu końcowego zgody administratora](v2-permissions-and-consent.md#admin-restricted-permissions). Czytaj dalej, aby dowiedzieć się więcej.

## <a name="permission-types"></a>Typy uprawnień

Platforma tożsamości firmy Microsoft obsługuje dwa typy uprawnień: **delegowane uprawnienia** i **uprawnienia aplikacji**.

* **Delegowane uprawnienia** są używane przez aplikacje, które mają zalogowanego użytkownika istnieje. Dla tych aplikacji użytkownik lub administrator wyraża zgodę na uprawnienia żądań aplikacji i aplikacji to uprawnienia delegowane do działania jako zalogowanego użytkownika, gdy wykonywanie wywołań do zasobu docelowego. Niektóre uprawnienia delegowane mogą wyrażono zgodę przez użytkowników innych niż administracyjne, ale niektóre uprawnienia wyższych uprawnieniach wymagają [zgody administratora](v2-permissions-and-consent.md#admin-restricted-permissions). Aby dowiedzieć się, który administrator ról mogą wyrazić zgodę na delegowane uprawnienia, zobacz [uprawnienia roli administratora w usłudze Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

* **Uprawnienia aplikacji** są używane przez aplikacje z systemem bez zalogowanego użytkownika istnieje; na przykład aplikacji, Uruchom jako usługi w tle lub demonów.  Uprawnienia aplikacji może składać się wyłącznie [wyraził zgodę administrator](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

_Czynne uprawnienia_ uprawnień, mających aplikacji podczas wysyłania żądań do zasobu docelowego. Należy zrozumieć różnicę między delegowane uprawnienia aplikacji, które otrzymuje aplikacji i jej czynne uprawnienia, podczas wykonywania wywołań do zasobu docelowego.

- Dla uprawnienia delegowane _czynne uprawnienia_ Twojej aplikacji będą najniższych uprawnieniach przecięcie delegowane uprawnienia aplikacji udzielono (za pośrednictwem zgodę) i uprawnieniami aktualnie zalogowanego użytkownika. Aplikacja nigdy nie może mieć większych uprawnień niż zalogowany użytkownik. Uprawnienia zalogowanego użytkownika mogą być określone w organizacji na podstawie zasad lub członkostwa w co najmniej jednej roli administratora. Aby dowiedzieć się, który administrator ról mogą wyrazić zgodę na delegowane uprawnienia, zobacz [uprawnienia roli administratora w usłudze Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

   Załóżmy na przykład aplikacji zostały przyznane _User.ReadWrite.All_ delegowane uprawnienia. Uprawnienie to przyznaje aplikacji nominalne uprawnienia do odczytu i aktualizowania profilu każdego użytkownika w organizacji. Jeśli zalogowany użytkownik jest administratorem globalnym, aplikacja będzie mogła zaktualizować profil każdego użytkownika w organizacji. Jednak jeśli zalogowany użytkownik nie ma rolę administratora, aplikacja będzie można zaktualizować tylko profilem zalogowanego użytkownika. Użytkownik nie będzie mógł zaktualizować profili innych użytkowników w organizacji, ponieważ użytkownik mający uprawnienia do działania w imieniu innych osób nie ma odpowiednich uprawnień.
  
- Uprawnienia aplikacji _czynne uprawnienia_ aplikacji będzie pełna poziom uprawnień też dorozumianych przez uprawnienia. Na przykład aplikację, która ma _User.ReadWrite.All_ uprawnień aplikacji może też zaktualizować profil każdego użytkownika w organizacji. 

## <a name="openid-connect-scopes"></a>Zakresy uwierzytelniania OpenID Connect

OpenID Connect implementacja platforma tożsamości firmy Microsoft ma kilka zakresów dobrze zdefiniowanych, które nie są stosowane do określonego zasobu: `openid`, `email`, `profile`, i `offline_access`. `address` i `phone` OpenID Connect zakresy nie są obsługiwane.

### <a name="openid"></a>openid

Jeśli aplikacja wykonuje logowania za pomocą [OpenID Connect](active-directory-v2-protocols.md), należy go zażądać `openid` zakresu. `openid` Zakresu przedstawiono na stronie zgoda konta pracy jako uprawnienie "Logowanie się w", a na osobiste strona zgody użytkownika konta Microsoft jako uprawnienie "Wyświetl swój profil i nawiązać połączenie z aplikacjami i usługami korzystającymi z Twojego konta Microsoft". Posiadając to uprawnienie, aplikacja może odbierać Unikatowy identyfikator dla użytkownika w formie `sub` oświadczenia. Daje ona również dostęp do aplikacji do punktu końcowego informacji o użytkowniku. `openid` Zakres może służyć w punkcie końcowym z tokenu platformy Microsoft tożsamości można uzyskać Identyfikatora tokenów, które mogą być używane przez aplikację uwierzytelniania.

### <a name="email"></a>email

`email` Zakresu, może być używany z `openid` zakresu i innych. Daje ona dostęp do aplikacji, aby do adres podstawowy adres e-mail użytkownika w postaci `email` oświadczenia. `email` Tylko wtedy, gdy jest to adres e-mail jest skojarzony z konta użytkownika, który nie jest zawsze oświadczenia znajduje się w tokenie. Jeśli użyto `email` zakresu, aplikacja powinna być przygotowana do obsługi przypadek, w którym `email` oświadczenia nie istnieje w tokenie.

### <a name="profile"></a>profile

`profile` Zakresu, może być używany z `openid` zakresu i innych. Daje ona dostęp do aplikacji do znacznej ilości informacji o użytkowniku. Informacje, które mogą uzyskać dostęp, który zawiera, ale nie ogranicza się do użytkownika imię, nazwisko, preferowany nazwy użytkownika i identyfikatora obiektu. Aby uzyskać pełną listę dostępnych w parametrze id_tokens oświadczeń profilu określonego użytkownika, zobacz [ `id_tokens` odwołania](id-tokens.md).

### <a name="offlineaccess"></a>offline_access

[ `offline_access` Zakres](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) pozwala aplikacji dostęp do zasobów w imieniu użytkownika przez dłuższy czas. Na stronie zgoda ten zakres jest wyświetlana jako uprawnienie "Zachować dostęp do danych, które mają zapewniony dostęp do". Gdy użytkownik akceptuje `offline_access` zakresu, aplikacja może odbierać tokeny odświeżania z punktu końcowego z tokenu platformy Microsoft identity. Długotrwałe są tokeny odświeżania. Aplikację można uzyskać nowych tokenów dostępu, jak wygaśnięciu starszych.

Jeśli aplikacja nie jawne żądanie `offline_access` zakresu, nie będzie ona otrzymywać tokenów odświeżania. Oznacza to, że po wprowadzeniu kodu autoryzacji w [przepływ kodu autoryzacji OAuth 2.0](active-directory-v2-protocols.md), otrzymasz tylko tokenu dostępu z `/token` punktu końcowego. Token dostępu jest ważny przez krótki czas. Token dostępu jest zazwyczaj wygasa w ciągu jednej godziny. AT, że punkt, Twoja aplikacja powinna przekieruje użytkownika z powrotem do `/authorize` punktu końcowego, aby uzyskać nowy kod autoryzacji. Podczas tego przekierowania, w zależności od typu aplikacji użytkownik może być konieczne ponowne wprowadzenie poświadczeń lub ponownie zgodę na uprawnienia. Gdy `offline_access` zakres jest automatycznie zleconych przez serwer, klient musi nadal żądania w celu odbierania tokenów odświeżania.

Aby uzyskać więcej informacji o tym, jak pobrać i używać tokenów odświeżania, zobacz [referencyjne protokołu platforma tożsamości firmy Microsoft](active-directory-v2-protocols.md).

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

Gdy użytkownik wprowadzi swoje poświadczenia, punkt końcowy platforma tożsamości firmy Microsoft sprawdza, czy pasującego rekordu w *zgody użytkownika*. Jeśli użytkownik nie wyraził zgody na dowolne żądane uprawnienia w przeszłości, ani administrator wyraził zgody na te uprawnienia w imieniu całej organizacji, Microsoft platformy tożsamości z punktu końcowego z monitem o żądane uprawnienia.

> [!NOTE]
> W tej chwili `offline_access` ("zachować dostęp do danych mają zapewniony dostęp do") i `user.read` ("zalogowania Cię i wczytania Twojego profilu) uprawnienia są automatycznie umieszczane w początkowej wyrażania zgody dla aplikacji.  Te uprawnienia są zazwyczaj wymagane dla aplikacji odpowiednie funkcje — `offline_access` zapewnia dostęp do aplikacji, można odświeżyć tokenów krytyczne dla natywnych i aplikacji sieci web podczas `user.read` daje dostęp do `sub` oświadczeń, umożliwiając klienta lub aplikacji, aby poprawnie zidentyfikować użytkownika, za pośrednictwem czasu i dostęp do informacji o użytkowniku podstawowe.  

![Zrzut ekranu pokazujący zgodę konta pracy](./media/v2-permissions-and-consent/work_account_consent.png)

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

Jeśli aplikacja żąda uprawnień aplikacji, a administrator daje te uprawnienia przez administratora zgody punktu końcowego, tym przydziałem nie jest wykonywane w imieniu dowolnego określonego użytkownika. Zamiast tego aplikacja kliencka są udzielane uprawnienia *bezpośrednio*. Tego rodzaju uprawnienia są używane tylko przez usługi demona oraz innych nieinterakcyjnych aplikacji, które są uruchamiane w tle.

## <a name="using-the-admin-consent-endpoint"></a>Przy użyciu punktu końcowego zgody administratora

Jeśli Administrator firmy korzysta z aplikacji, zostanie skierowany do punktu końcowego autoryzacji platforma tożsamości usługi Microsoft wykryje rolę użytkownika i poproś o tym, czy chce zgody w imieniu całej dzierżawie uprawnień, których żądasz. Istnieje jednak również punkt końcowy zgody dedykowanej administracyjnej, których można użyć, jeśli chcesz aktywnie żądania, że administrator udziela uprawnień w imieniu całej dzierżawie. Za pomocą tego punktu końcowego jest również do żądania uprawnień aplikacji, (co nie można żądać przy użyciu punktu końcowego autoryzacji).

Jeśli wykonujesz te kroki, aplikacja może zażądać uprawnień dla wszystkich użytkowników w dzierżawie, łącznie z ograniczonym zakresów. Jest to operacja wysokim poziomie uprawnień, powinno mieć miejsce tylko, jeśli to konieczne w przypadku danego scenariusza.

Aby zobaczyć przykładowy kod, który implementuje kroków, zobacz [przykładowe ograniczonym zakresy](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Żądanie uprawnień w portalu rejestracji aplikacji

Zgoda administratora nie akceptuje parametr zakresu, więc żądanej wszelkie uprawnienia muszą być statycznie zdefiniowane w rejestracji aplikacji. Ogólnie rzecz biorąc jest najlepszym rozwiązaniem, aby upewnić się, że uprawnienia zadeklarowany jako statyczny dla danej aplikacji jest nadzbiorem uprawnienia, że będzie on dynamicznie/przyrostowo żąda.

#### <a name="to-configure-the-list-of-statically-requested-permissions-for-an-application"></a>Aby skonfigurować listę statycznie żądanych uprawnień dla aplikacji

1. Przejdź do aplikacji w [rejestracje aplikacji z witryny Azure portal](https://go.microsoft.com/fwlink/?linkid=2083908) środowiskiem lub [tworzenie aplikacji](quickstart-register-app.md) Jeśli jeszcze go.
2. Znajdź **uprawnienia do interfejsu API** sekcji, a w ramach uprawnień interfejsu API kliknij przycisk Dodaj uprawnienia.
3. Wybierz **programu Microsoft Graph** z listy dostępnych interfejsów API, a następnie Dodaj uprawnienia, których wymaga aplikacja.
3. **Zapisz** rejestracji aplikacji.

### <a name="recommended-sign-the-user-into-your-app"></a>Zalecane: Zaloguj się użytkownika do aplikacji

Zazwyczaj podczas kompilowania aplikacji korzystającej z punktu końcowego zgody administratora, aplikacja musi strony lub widok, w którym administrator może zatwierdzić uprawnienia aplikacji. Ta strona może być część przepływu rejestracji aplikacji, część ustawień aplikacji lub może być dedykowany przepływ "Połącz". W wielu przypadkach dobrym pomysłem dla aplikacji wyświetlić to "łączenie" Wyświetl tylko wtedy, gdy użytkownik zalogował się przy użyciu służbowego konta Microsoft.

Po zalogowaniu się użytkownika do aplikacji, można zidentyfikować organizacji, do której należy administrator, przed monitem o zatwierdzenia niezbędnych uprawnień. Chociaż nie niezbędne, pomoże Ci utworzyć bardziej intuicyjne środowisko dla użytkowników w organizacji. Aby się zalogować użytkownika, postępuj zgodnie z naszym [samouczki protokołu platforma tożsamości firmy Microsoft](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Żądanie uprawnień od administratora katalogu

Gdy wszystko będzie gotowe zażądać uprawnień z administratorem Twojej organizacji, można przekierować użytkownika do platformą Microsoft identity *punktu końcowego zgody administratora*.

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
| `tenant` | Wymagane | Dzierżawy katalogu, który chcesz zażądać uprawnień z. Mogą być podane w formacie przyjaznej nazwy lub identyfikatora GUID lub objęty do którego odwołuje się `common` jak pokazano w przykładzie. |
| `client_id` | Wymagane | **Identyfikator aplikacji (klienta)** , [rejestracje aplikacji z witryny Azure portal](https://go.microsoft.com/fwlink/?linkid=2083908) środowisko przypisany do aplikacji. |
| `redirect_uri` | Wymagane |Identyfikator URI, którego odpowiedź do wysłania dla aplikacji do obsługi przekierowania. Dokładnie musi odpowiadać jednej z przekierowania URI, który został zarejestrowany w portalu rejestracji aplikacji. |
| `state` | Zalecane | Wartość uwzględnione w żądaniu, które również zostaną zwrócone w odpowiedzi tokenu. Może być ciągiem żadnej zawartości, którą chcesz. Umożliwia kodowanie informacje o stanie użytkownika w aplikacji, zanim żądanie uwierzytelniania wystąpił, takich jak strony lub widoku, które znajdowały się w stan. |

W tym momencie usługa Azure AD wymaga tylko administrator dzierżawy może zalogować się do realizacji żądania. Administrator jest monitowany o zatwierdzenie wszystkich uprawnień, które żądanej aplikacji w portalu rejestracji aplikacji.

#### <a name="successful-response"></a>Odpowiedź oznaczająca Powodzenie

Jeśli administrator zatwierdza uprawnień dla aplikacji, odpowiedź oznaczająca Powodzenie wygląda następująco:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parametr | Opis |
| --- | --- |
| `tenant` | Dzierżawy katalogu, który uprawnień aplikacji żądał, w formacie identyfikatora GUID. |
| `state` | Wartość uwzględnione w żądaniu, które również zostaną zwrócone w odpowiedzi tokenu. Może być ciągiem żadnej zawartości, którą chcesz. Stan jest używany do kodowania informacje o stanie użytkownika w aplikacji, zanim żądanie uwierzytelniania wystąpił, takich jak strony lub widoku, które znajdowały się w. |
| `admin_consent` | Zostanie ustawiony na `True`. |

#### <a name="error-response"></a>Odpowiedzi na błąd

Jeśli administrator nie zatwierdzanie uprawnień aplikacji, odpowiedzi o niepowodzeniu wygląda następująco:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parametr | Opis |
| --- | --- |
| `error` | Ciągu kodu błędu, który może służyć do klasyfikowania typy błędów, które występują i może służyć do reagowania na błędy. |
| `error_description` | Określony komunikat o błędzie ułatwiający Deweloper odkryć ich główną przyczynę błędu. |

Po otrzymaniu pomyślnej odpowiedzi z punktu końcowego zgody administratora, aplikację zdobyła go wymagane uprawnienia. Następnie możesz zażądać token dla żądanego zasobu.

## <a name="using-permissions"></a>Przy użyciu uprawnień

Po użytkownik wyraża zgodę na uprawnienia dla swojej aplikacji, aplikacji można uzyskać tokenów dostępu, które reprezentują aplikacji uprawnień dostępu do zasobu w niektórych pojemności. Token dostępu może służyć tylko do jednego zasobu zakodowanych w tokenie dostępu jest jednak wszystkie uprawnienia, która aplikacja ma przyznane dla tego zasobu. Uzyskanie tokenu dostępu, aplikację zgłosić wniosek do firmy Microsoft tożsamości platformy punktu końcowego tokenu, następująco:

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

Aby uzyskać więcej informacji na temat protokołu OAuth 2.0 i uzyskiwanie tokenów dostępu, zobacz [odwołania do programu Microsoft identity platformy punktu końcowego protokołu](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>Zakres /.default

Możesz użyć `/.default` zakresu, aby ułatwić migrowanie aplikacji z punktu końcowego w wersji 1.0 do endpoint platforma tożsamości firmy Microsoft. To jest wbudowanym zakresem dla każdej aplikacji, która odwołuje się do statyczną listę uprawnień skonfigurowanych w rejestracji aplikacji. A `scope` wartość `https://graph.microsoft.com/.default` funkcjonalnie jest taka sama jak punkty końcowe v1.0 `resource=https://graph.microsoft.com` — to znaczy, żąda tokenu z zakresami na programie Microsoft Graph, która aplikacja została zarejestrowana dla w witrynie Azure portal.

Zakres /.default mogą być używane w dowolnym przepływ OAuth 2.0, ale jest to konieczne w [przepływu w imieniu z](v2-oauth2-on-behalf-of-flow.md) i [przepływ poświadczeń klienta](v2-oauth2-client-creds-grant-flow.md).  

> [!NOTE]
> Klienci nie można łączyć statyczne (`/.default`) i dynamiczny zgody w pojedynczym żądaniu. W efekcie `scope=https://graph.microsoft.com/.default+mail.read` spowoduje błąd z powodu kombinacji typów zakresu.

### <a name="default-and-consent"></a>/.default i zgody

`/.default` Zakres wyzwala zachowanie punktu końcowego v1.0 `prompt=consent` także. Wymaga zgody na wszystkie uprawnienia zarejestrowane przez aplikację, niezależnie od tego zasobu. Jeśli dołączone jako część żądania, `/.default` zakres zwraca token, który zawiera zakresy dla żądanego zasobu.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default po użytkownik już wyraził zgody

Ponieważ `/.default` jest funkcjonalnie identyczny `resource`-zachowanie v1.0 skoncentrowane na punkt końcowy zapewnia z nią zgody zachowanie punktu końcowego w wersji 1.0. To znaczy `/.default` wyzwala monit o wyrażenie zgody tylko, jeśli przyznano uprawnienie nie między klientem i zasobu przez użytkownika. Jeśli istnieje taki zgody, następnie token zostanie zwrócony zawierający wszystkie zakresy przyznane przez użytkownika dla tego zasobu. Jednakże, jeśli brak uprawnienia zostały przyznane, lub `prompt=consent` parametr został podany, monit o wyrażenie zgody będą wyświetlane dla wszystkich zakresów zarejestrowane przez aplikację klienta.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Przykład 1: Użytkownik lub Administrator dzierżawy ma przyznane uprawnienia

Użytkownik (lub administrator dzierżawy) ma uprawnień, klienta programu Microsoft Graph `mail.read` i `user.read`. Jeśli klient wysyła żądanie do `scope=https://graph.microsoft.com/.default`, nie monit o wyrażenie zgody zostanie wyświetlona niezależnie od tego, zawartość aplikacji klienckich zarejestrowanych uprawnień dla programu Microsoft Graph. Token do zwrócenia zawierające zakresy `mail.read` i `user.read`.

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Przykład 2: Użytkownik nie zostało przyznane uprawnienia między klientem a zasobu

Nie zgody użytkownika istnieje między klientem i programu Microsoft Graph. Klient został zarejestrowany dla `user.read` i `contacts.read` uprawnień, a także zakresu usługi Azure Key Vault `https://vault.azure.net/user_impersonation`. Gdy klient żąda tokenu do `scope=https://graph.microsoft.com/.default`, użytkownik zostanie wyświetlony ekran wyrażania zgody dla `user.read`, `contacts.read`oraz usługi Key Vault `user_impersonation` zakresów. Token zwracany będzie mieć po prostu z `user.read` i `contacts.read` zakresów w nim.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>Przykład 3: Użytkownik wyraził zgodę i klient żąda dodatkowe zakresy

Użytkownik już wyraził zgodę na `mail.read` dla klienta. Klient został zarejestrowany dla `contacts.read` zakresu jego rejestracji. Gdy klient wysyła żądania tokenu przy użyciu `scope=https://graph.microsoft.com/.default` i żądania za pośrednictwem `prompt=consent`, a następnie użytkownik będzie widział tylko ekran wyrażania zgody dla i wszystkie uprawnienia zarejestrowane przez aplikację. `contacts.read` będzie on wyświetlany na ekranie wyrażania zgody, ale `mail.read` nie będzie. Token zwracany będzie dla programu Microsoft Graph i będzie zawierać `mail.read` i `contacts.read`.

### <a name="using-the-default-scope-with-the-client"></a>Przy użyciu zakresu /.default za pomocą klienta programu

Szczególny przypadek `/.default` zakres istnieje, gdy klient zażąda własnego `/.default` zakresu. Poniższy przykład pokazuje, w tym scenariuszu.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Daje ekran wyrażania zgody dla wszystkich zarejestrowanych uprawnień (jeśli dotyczy oparty na powyższych opisów zgody i `/.default`), następnie zwraca id_token zamiast tokenu dostępu.  To zachowanie dla niektórych starszych klientów, przenoszenie z biblioteki ADAL do biblioteki MSAL istnieje i nie powinny być używane przez nowych klientów, przeznaczone dla punktu końcowego platformy tożsamości firmy Microsoft.  

## <a name="troubleshooting-permissions-and-consent"></a>Rozwiązywanie problemów z uprawnienia i zgody

Jeśli użytkownik lub użytkownicy twojej aplikacji jest wyświetlany nieoczekiwane błędy podczas wyrażania zgody, zobacz ten artykuł zawiera kroki rozwiązywania problemów: [Nieoczekiwany błąd podczas wyrażania zgody aplikacji](../manage-apps/application-sign-in-unexpected-user-consent-error.md).
