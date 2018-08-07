---
title: Co to jest inne w przypadku punktu końcowego v2.0 usługi Azure AD? | Microsoft Docs
description: Porównanie oryginalnej usługi Azure AD i punktów końcowych w wersji 2.0.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5060da46-b091-4e25-9fa8-af4ae4359b6c
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: celested
ms.reviewer: elisol, jmprieur, hirsin
ms.custom: aaddev
ms.openlocfilehash: 0e344f6e9dfee3793320dc9cb79e3231c2eeda87
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581592"
---
# <a name="whats-different-about-the-v20-endpoint"></a>Jakie są różnice dotyczące punktu końcowego v2.0?

Jeśli znasz usługi Azure Active Directory (Azure AD) lub aplikacje zostały zintegrowane z usługą Azure AD w przeszłości, istnieją pewne różnice w punktu końcowego v2.0, który nie może oczekiwać. W tym artykule wywołuje różnice zrozumienie.

> [!NOTE]
> Nie wszystkie scenariusze usługi Azure AD i funkcje są obsługiwane przez punkt końcowy w wersji 2.0. Aby ustalić, należy użyć punktu końcowego v2.0, przeczytaj temat [ograniczenia v2.0](active-directory-v2-limitations.md).
>

## <a name="microsoft-accounts-and-azure-ad-accounts"></a>Konta Microsoft i kontami usługi Azure AD

Punktu końcowego v2.0 umożliwia programistom pisanie aplikacji, które akceptują Zaloguj się za pomocą kont Accounts firmy Microsoft i usługi Azure AD przy użyciu uwierzytelniania pojedynczego punktu końcowego. Daje możliwość napisać własną aplikację całkowicie konto, niezależnie od, co oznacza, że aplikacja może być zakresu typu konta, które użytkownik zaloguje się przy użyciu. Użytkownik skracać czas reakcji aplikacji o rodzaju konta używane w określonej sesji, ale nie masz.

Na przykład jeśli aplikacja odwołuje się [programu Microsoft Graph](https://graph.microsoft.io), niektóre dodatkowe funkcje i dane będą dostępne dla użytkowników w przedsiębiorstwach, takich jak witryny programu SharePoint lub dane katalogu. Jednak wiele akcji takich jak [czytanie poczty użytkownika](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message), kod można napisać tak, dokładnie tak samo dla kont usługi Azure AD i Accounts Microsoft. 

Integracja aplikacji z aplikacją Accounts Microsoft i kontami usługi Azure AD jest teraz jednego prostego procesu. Pojedynczy zestaw punktów końcowych, jednej biblioteki i rejestracji pojedynczej aplikacji można użyć do uzyskania dostępu do firmowych i komercyjnych rozwiązań. Aby dowiedzieć się więcej na temat punktu końcowego v2.0, zapoznaj się z [Przegląd](active-directory-appmodel-v2-overview.md).

## <a name="new-app-registration-portal"></a>Nowy portal rejestracji aplikacji

Aby zarejestrować aplikację, która współdziała z punktem końcowym v2.0, należy użyć portalu rejestracji aplikacji firmy Microsoft: [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). To jest portal, gdzie można uzyskać identyfikator aplikacji, dostosuj wygląd strony logowania dla swojej aplikacji i nie tylko. To wszystko, czego potrzebujesz, aby uzyskać dostęp do portalu konta Microsoft obsługiwane — osobistych lub służbowe konto.

## <a name="one-app-id-for-all-platforms"></a>Identyfikator aplikacji dla wszystkich platform

Jeśli używano usługi Azure AD, prawdopodobnie zarejestrowanego w kilku różnych aplikacji dla pojedynczego projektu. Na przykład jeśli utworzono witrynę sieci Web i aplikacji systemu iOS trzeba było zarejestrować je oddzielnie, za pomocą dwóch różnych identyfikatorów aplikacji. Portal rejestracji aplikacji usługi Azure AD zmuszony do takiego rozróżnienia podczas rejestracji:

![Rejestrowanie aplikacji starego interfejsu użytkownika](./media/azure-ad-endpoint-comparison/old_app_registration.PNG)

Podobnie jeśli masz witrynę sieci Web i zaplecza internetowego interfejsu api może zarejestrowano każdego jako osobna aplikacja w usłudze Azure AD. Lub jeśli masz aplikację dla systemu iOS i Android aplikacji również może być zarejestrowany dwóch różnych aplikacji. Zarejestrowanie każdego składnika aplikacji doprowadziło do niektórych nieoczekiwane zachowania dla deweloperów i klientów:

* Każdy składnik pojawił się jako oddzielna aplikacja w dzierżawie usługi Azure AD każdego klienta.
* Administrator dzierżawy próbował stosowanie zasad do zarządzania dostępem do lub usuwanie aplikacji, zostałyby zrobić dla każdego składnika aplikacji.
* Gdy klienci wyraża zgodę na aplikację, każdy składnik pojawią się na ekranie wyrażania zgody jako odrębne aplikacji.

Z punktem końcowym v2.0 można teraz rejestrowanie wszystkich składników projektu jako rejestracja jednej aplikacji i używanie pojedynczego identyfikatora aplikacji dla całego projektu. Można dodać kilka "platforms" do każdego projektu, a następnie podaj odpowiednie dane dla wszystkich platform, które możesz dodać. Oczywiście można utworzyć dowolną liczbę aplikacji, w zależności od wymagań, ale dla większości przypadków tylko jeden identyfikator aplikacji powinno być konieczne.

Naszym celem jest, to będzie prowadzić do bardziej uproszczone zarządzanie aplikacjami i Środowisko deweloperskie i tworzenia bardziej skonsolidowanego widoku pojedynczego projektu, który może działać na.

## <a name="scopes-not-resources"></a>Zakresy, nie zasobów

W usłudze Azure AD, aplikacja może zachowywać się jak **zasobów**, lub odbiorcy tokenów. Zasób można zdefiniować wiele **zakresy** lub **oAuth2Permissions** siebie, dzięki czemu klienta aplikacji, aby żądać tokenów do zasobu na potrzeby określone zakresy. Należy wziąć pod uwagę interfejsu API programu Graph usługi Azure AD, na przykład zasób:

* Identyfikator zasobu lub `AppID URI`: `https://graph.windows.net/`
* Zakresy, lub `OAuth2Permissions`: `Directory.Read`, `Directory.Write`itp. 

Wszystko to prawdziwe dla punktu końcowego v2.0. Aplikacja, nadal może zachowywać się jak zasób, Definiowanie zakresów i być identyfikowany przez identyfikator URI. Aplikacje klienckie nadal może zażądać dostępu do tych zakresów. Jednak zmienił się sposób, że klient żąda te uprawnienia. W przeszłości autoryzacji OAuth 2.0 żądania do usługi Azure AD może być sprawdzono, takich jak:

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

gdzie **zasobów** parametru wskazane zasobu, który aplikacja kliencka żąda zezwolenia na. Usługa Azure AD obliczane uprawnień wymaganych przez aplikację na podstawie konfiguracji statycznej w witrynie Azure portal i w związku z tym wystawionych tokenów. Teraz tego samego protokołu OAuth 2.0 zezwalają na żądanie wygląda:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

gdzie **zakres** parametr wskazuje zasobu, który i uprawnień aplikacja żąda autoryzacji dla. Żądany zasób jest nadal znajdują się na żądanie — po prostu ujęty w każdej wartości parametru zakresu. Za pomocą parametru zakresu w ten sposób umożliwia punktem końcowym v2.0 za bardziej zgodny ze specyfikacją protokołu OAuth 2.0 i lepiej wyrównane typowe rozwiązania w branży. Umożliwia aplikacji przeprowadzenie [przyrostowe zgody](#incremental-and-dynamic-consent), który jest opisany w następnej sekcji.

## <a name="incremental-and-dynamic-consent"></a>Przyrostowe i dynamiczne zgody

Aplikacje zarejestrowane w usłudze Azure AD, które wcześniej jest potrzebne do określenia ich wymaganych uprawnień OAuth 2.0 w witrynie Azure portal w czasie tworzenia aplikacji:

![Uprawnienia rejestracji interfejsu użytkownika](./media/azure-ad-endpoint-comparison/app_reg_permissions.PNG)

Uprawnienia aplikacji zostały skonfigurowane **statycznie**. Dozwolone konfiguracji aplikacji, które istnieją w witrynie Azure portal, i przechowywane kod nieuprzywilejowany i proste, przedstawia kilka problemów dla deweloperów:

* Aplikację musieli wiedzieć, wszystkie uprawnienia, które nigdy nie będą potrzebne w czasie tworzenia aplikacji. Dodawanie uprawnień wraz z upływem czasu było trudne procesu.
* Aplikację musieli wiedzieć, zasoby, które nigdy nie dostęp do wcześniej. Trudno było je tworzyć aplikacje, które można uzyskać dostępu dowolną liczbę zasobów.
* Aplikacja było zażądać uprawnień, które nigdy nie będą potrzebne po użytkownik pierwsze logowanie. W niektórych przypadkach, które doprowadziło to do długą listę uprawnień, które nie zaleca się użytkownikom końcowym zatwierdzanie aplikacji dostęp do początkowego logowania.

Z punktem końcowym v2.0, można określić uprawnienia wymagane przez aplikację **dynamicznie**, w czasie wykonywania, podczas normalnego użytkowania Twojej aplikacji. Aby to zrobić, można określić zakresy, Twoja aplikacja wymaga w dowolnym danym momencie w czasie, umieszczając je w `scope` parametru żądanie autoryzacji:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

Powyżej żądań uprawnień aplikacji w celu odczytania danych katalogu użytkownika usługi Azure AD, jak również zapisywać dane do swojego katalogu. Jeśli użytkownik wyraził zgodę na te uprawnienia w przeszłości dla tej konkretnej aplikacji, wprowadź swoje poświadczenia i logowanie do aplikacji. Jeśli użytkownik nie wyraził zgody na dowolne z tych uprawnień, punktu końcowego v2.0 będzie monitować użytkownika o zgodę na te uprawnienia. Aby dowiedzieć się więcej, użytkownik może Czytaj [uprawnienia, wyrażania zgody i zakresy](v2-permissions-and-consent.md).

Zezwolenie aplikacji, aby zażądać uprawnień dynamicznie za pomocą `scope` parametru daje pełną kontrolę nad środowiskiem użytkownika. Jeśli chcesz, istnieje możliwość frontload swoją zgodę środowisko i poproś o wszystkie uprawnienia w jednym żądaniu początkowej autoryzacji. Jeśli aplikacja wymaga dużej liczby uprawnień, można też uzyskanie te uprawnienia od użytkownika przyrostowo, zgodnie z ich próbują użyć niektórych funkcji aplikacji wraz z upływem czasu.

## <a name="well-known-scopes"></a>Dobrze znane zakresów

### <a name="offline-access"></a>Dostęp w trybie offline

Aplikacje korzystające z punktem końcowym v2.0 może wymagać korzystanie z nowego uprawnienia dobrze znanych dla aplikacji — `offline_access` zakresu. Wszystkie aplikacje, należy zażądać tego uprawnienia, gdy potrzebują dostępu do zasobów w imieniu użytkownika przez dłuższy okres czasu, nawet wtedy, gdy użytkownik może nie być aktywnie przy użyciu aplikacji. `offline_access` Użytkownikowi w oknach dialogowych wyrażania zgody, jak będzie wyglądać zakres, "Dostęp do danych w trybie offline", który użytkownik musi wyrazić zgodę na. Żądanie `offline_access` uprawnień spowoduje włączenie aplikacji sieci web do odbierania refresh_tokens OAuth 2.0 z punktem końcowym v2.0. Refresh_tokens są długotrwałe i może zostać wymienione na nowe access_tokens OAuth 2.0 przez dłuższy czas dostępu. 

Jeśli aplikacja nie żąda `offline_access` zakresu, nie będzie ona otrzymywać refresh_tokens. Oznacza to, że po wprowadzeniu authorization_code w przepływ kodu autoryzacji OAuth 2.0, subskrybent otrzyma tylko ponownie ' access_token ' z `/token` punktu końcowego. Ten access_token pozostanie ważny przez krótki okres czasu (zazwyczaj jedna godzina), ale wygaśnie po pewnym czasie. W tym punktu w czasie Twojej aplikacji będą musieli przekieruje użytkownika z powrotem do `/authorize` punktu końcowego, aby pobrać nowy authorization_code. Podczas tego przekierowania użytkownik może lub nie trzeba ponownie wprowadzić swoje poświadczenia lub ponownie wyrazić zgodę na uprawnienia, w zależności od typu aplikacji.

Aby dowiedzieć się więcej na temat protokołu OAuth 2.0, refresh_tokens i access_tokens, zapoznaj się z [referencyjne protokołu v2.0](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, profilu i wiadomości e-mail

W przeszłości najbardziej podstawowa przepływ protokołu OpenID Connect logowania w usłudze Azure AD będzie dostarczali mnóstwa informacji o użytkowniku w wynikowej id_token. Oświadczenia w id_token mogą obejmować nazwy, preferowany nazwy użytkownika, adres e-mail, identyfikator obiektu i więcej.

Informacje, `openid` zakresu daje aplikacji dostęp do jest teraz ograniczone. `openid` Zakresu zezwala tylko Twojej aplikacji logują użytkownika i odbieranie identyfikator specyficzny dla aplikacji dla użytkownika. Jeśli chcesz uzyskać danych osobowych użytkownika w aplikacji, aplikację należy zażądania dodatkowych uprawnień przez użytkownika. Dwa nowe zakresy — `email` i `profile` zakresy — umożliwi zażądania dodatkowych uprawnień.

`email` Zakresu umożliwia aplikacji dostęp do adres podstawowy adres e-mail użytkownika za pośrednictwem `email` oświadczenia w id_token. 

`profile` Zakresu daje aplikacji dostęp do wszystkich innych podstawowe informacje o użytkowniku — ich nazwy, preferowany nazwy użytkownika, identyfikator obiektu i tak dalej.

Dzięki temu kod aplikacji w sposób minimalny ujawnienie — można tylko prosisz użytkownika o zestawie informacji, że wymaganych przez aplikację, aby to zrobić. Aby uzyskać więcej informacji na temat tych zakresów, zobacz [dokumentacja zakresu v2.0](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Oświadczenia tokenu

Oświadczenia w tokeny wystawione przez punkt końcowy v2.0, nie będzie taka sama jak tokeny wystawione przez ogólnie dostępne punkty końcowe usługi Azure AD. Migracja do nowej usługi aplikacji nie powinna przyjęto założenie, że danego oświadczenia będzie istnieć w id_tokens lub access_tokens. Aby dowiedzieć się o określonych oświadczeń, które są emitowane w tokenach w wersji 2.0, zobacz [odwołania do tokenu v2.0](v2-id-and-access-tokens.md).

## <a name="limitations"></a>Ograniczenia

Istnieje kilka ograniczeń pod uwagę podczas korzystania z punktem w wersji 2.0. Aby dowiedzieć się, jeśli dowolny z tych ograniczeń dotyczą konkretnego scenariusza, zobacz [doc ograniczenia v2.0](active-directory-v2-limitations.md).
