---
title: Co to jest różna w punktu końcowego v2.0 usługi Azure AD? | Microsoft Docs
description: Porównanie oryginalnego usługi Azure AD i punkty końcowe w wersji 2.0.
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
ms.openlocfilehash: 1d82f9a8c9ba6bcdf9914337c7ca0eadaccf17a0
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36318478"
---
# <a name="whats-different-about-the-v20-endpoint"></a>Czym się różni punktu końcowego v2.0?

Jeśli zaznajomieni z usługą Azure Active Directory (Azure AD) lub zostały zintegrowane aplikacje z usługą Azure AD w przeszłości, istnieją pewne różnice w punktu końcowego v2.0, który może nie oczekiwać. W tym artykule wywołuje różnic zrozumienie.

> [!NOTE]
> Nie wszystkie usługi Azure AD scenariusze i funkcje są obsługiwane przez punkt końcowy w wersji 2.0. Aby ustalić, czy należy używać punktu końcowego v2.0, przeczytaj o [ograniczenia v2.0](active-directory-v2-limitations.md).
>

## <a name="microsoft-accounts-and-azure-ad-accounts"></a>Konta Microsoft i konta usługi Azure AD

Punktu końcowego v2.0 programiści mogą pisać aplikacje, które akceptują logowanie z konta Microsoft Accounts i usługi Azure AD przy użyciu punktu końcowego uwierzytelniania pojedynczego. Zapewnia możliwość zapisywania aplikacji całkowicie konta niezwiązane z żadnym, co oznacza, że aplikacja może być ignorujących typu konta, które użytkownik loguje się przy użyciu. Można zapewnić pamiętać o rodzaju konto używane w określonej sesji aplikacji, ale nie masz.

Na przykład jeśli aplikacja wymaga [Microsoft Graph](https://graph.microsoft.io), niektóre dodatkowe funkcje i dane będą dostępne dla użytkowników przedsiębiorstwa, takich jak witryny programu SharePoint lub dane katalogu. Nawet w przypadku wielu akcji takich jak [odczytywanie poczty użytkownika](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message), kodu mogą być zapisywane dokładnie takie same dla konta Microsoft Accounts i usługi Azure AD. 

Integrowanie aplikacji z Accounts Microsoft i kontami usługi Azure AD jest teraz jeden prosty proces. Można użyć jednego zestawu punktów końcowych, jednej biblioteki i rejestracji jednej aplikacji do uzyskiwania dostępu do względem zarówno konsumenta, jak i enterprise. Aby dowiedzieć się więcej na temat punktu końcowego v2.0, zapoznaj się [przeglądu](active-directory-appmodel-v2-overview.md).

## <a name="new-app-registration-portal"></a>Nowy portal rejestracji aplikacji

Aby zarejestrować aplikację, która współdziała z punktem końcowym v2.0, należy użyć portalu rejestracji aplikacji firmy Microsoft: [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). To jest portal, gdzie można uzyskać identyfikator aplikacji, dostosować wygląd strony logowania aplikacji i inne. Wszystkie informacje wymagane do dostępu do portalu to konto Microsoft, jej — osobistych lub pracy/służbowe konto.

## <a name="one-app-id-for-all-platforms"></a>Identyfikator jedną aplikację dla wszystkich platform

Jeśli używasz już usługi Azure AD, prawdopodobnie został zarejestrowany kilka różnych aplikacji dla jednego projektu. Na przykład jeśli utworzono zarówno witrynę sieci Web i aplikacji systemu iOS, trzeba było zarejestrować je oddzielnie, za pomocą dwóch różnych identyfikatorów aplikacji. Portal rejestracji aplikacji usługi Azure AD wymuszone można utworzyć tej różnicy podczas rejestracji:

![Rejestracja aplikacji starego interfejsu użytkownika](../../media/active-directory-v2-flows/old_app_registration.PNG)

Podobnie jeśli masz witrynę sieci Web i zaplecza, interfejs api sieci web może zarejestrowano jako osobnych aplikacji w usłudze Azure AD. Lub jeśli masz aplikację systemu iOS i Android — aplikacja również może być zarejestrowany dwóch różnych aplikacji. Zarejestrowanie każdego składnika aplikacji doprowadziło do niektórych nieoczekiwane zachowania dla deweloperów i klientów:

* Poszczególne składniki znajdowały się jako osobne aplikację w dzierżawie usługi Azure AD każdego klienta.
* Administrator dzierżawy próbowano zastosować zasady do zarządzania dostępem do lub usuwanie aplikacji, zostałyby zrobić dla każdego składnika aplikacji.
* Gdy klienci zgodę na aplikacji, każdy składnik zostanie wyświetlony na ekranie zgodę jako różnych aplikacji.

Z punktem końcowym v2.0 może teraz rejestrować wszystkie składniki projektu jako rejestracja jednej aplikacji i użycie jednego identyfikatora aplikacji dla całego projektu. Można dodać kilka "platformy" do każdego projektu i podaj odpowiednie dane dla każdej platformy, które można dodać. Oczywiście można utworzyć dowolną liczbę aplikacji, w zależności od wymagań, ale w większości przypadków tylko jeden identyfikator aplikacji może być konieczne.

Naszym celem jest, że spowoduje to prowadzić do bardziej uproszczone zarządzanie aplikacjami i środowisko programistyczne i utworzyć więcej skonsolidowanego widoku pojedynczego projektu, który może działać na.

## <a name="scopes-not-resources"></a>Zakresy, nie zasobów

W usłudze Azure AD, aplikacja może zachowywać się jak **zasobów**, lub odbiorcy tokenów. Zasób można zdefiniować wiele **zakresy** lub **oAuth2Permissions** siebie, umożliwiając klienckim aplikacje do żądania tokenów do tego zasobu dla zestawu zakresów. Należy wziąć pod uwagę interfejsu API Azure AD Graph, na przykład zasobu:

* Identyfikator zasobu lub `AppID URI`: `https://graph.windows.net/`
* Zakresy, lub `OAuth2Permissions`: `Directory.Read`, `Directory.Write`itp. 

Wszystko to jest spełniony dla punktu końcowego v2.0. Aplikacja nadal może działać jako zasób, Zdefiniuj zakresy oraz być identyfikowany przez identyfikator URI. Aplikacje klienckie nadal może zażądać dostępu do tych zakresów. Jednak taki sposób, że klient żąda uprawnienia została zmieniona. W przeszłości autoryzacji OAuth 2.0 żądania do usługi Azure AD mogą sprawdzono, takich jak:

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

gdzie **zasobów** parametru wskazane, którego zasobu Aplikacja kliencka żąda autoryzacji dla. Usługi Azure AD obliczana uprawnień wymaganych przez aplikację na podstawie konfiguracji statycznej w portalu Azure i w związku z tym wystawionych tokenów. Teraz tym samym OAuth 2.0 autoryzować żądania prawdopodobnie:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

gdzie **zakres** parametr wskazuje zasobów i uprawnienia aplikacji żąda autoryzacji dla. Żądany zasób jest nadal znajdują się w żądaniu — po prostu ujęty w wartości parametru zakresu. Za pomocą parametru zakresu w ten sposób umożliwia punktu końcowego v2.0 za więcej zgodny ze specyfikacją OAuth 2.0 i bardziej dokładnie wyrównana z typowe rozwiązania z branży. Umożliwia również aplikacji do wykonywania [przyrostowe zgody](#incremental-and-dynamic-consent), który jest opisany w następnej sekcji.

## <a name="incremental-and-dynamic-consent"></a>Zgody przyrostowych i dynamicznych

Aplikacje są zarejestrowane w usłudze Azure AD wcześniej konieczne jest określenie ich wymaganych uprawnień OAuth 2.0 w portalu Azure w czasie tworzenia aplikacji:

![Uprawnienia rejestracji interfejsu użytkownika](../../media/active-directory-v2-flows/app_reg_permissions.PNG)

Uprawnienia aplikacji wymagane są skonfigurowane **statycznie**. Dozwolone konfiguracji aplikacji istnieje w portalu Azure, i przechowywane kod nieuprzywilejowany i proste, przedstawiono kilka problemów dla deweloperów:

* Aplikacja ma wiedzieć, wszystkie uprawnienia, które kiedykolwiek wymagałoby w czasie tworzenia aplikacji. Dodanie uprawnień w czasie było trudne procesu.
* Aplikacja ma wiedzieć, wszystkie zasoby, które kiedykolwiek dostęp do wcześniejsze. Był trudny do tworzenia aplikacji, które można uzyskać dostępu do dowolnej liczby zasobów.
* Aplikację nastąpiło żądanie wszystkie uprawnienia, które kiedykolwiek wymagałoby od jego pierwszego logowania. W niektórych przypadkach prowadzi to do długą listę uprawnień, które nie zaleca się użytkowników końcowych z zatwierdzanie dostępu do aplikacji na początkowej logowania.

Z punktem końcowym v2.0, można określić uprawnienia potrzeb aplikacji **dynamicznie**, w czasie wykonywania, podczas normalnego użytkowania aplikacji. Aby to zrobić, można określić zakresy Twoja aplikacja powinna na dowolnym etapie w czasie przez włączenie ich w `scope` parametr żądania autoryzacji:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

Powyżej uprawnienia żądań aplikacji na odczytywanie danych katalogu użytkownika usługi Azure AD, a także zapisać danych do ich katalogu. Jeśli użytkownik zgodził się tych uprawnień w przeszłości dla tej konkretnej aplikacji, będzie wprowadzić swoje poświadczenia, a zalogować się do aplikacji. Jeśli użytkownik zgodził się nie do dowolnego z tych uprawnień, punktu końcowego v2.0 będzie monitować użytkownika o zgodę na te uprawnienia. Aby dowiedzieć się więcej, można znaleźć na [uprawnień, zgody i zakresy](active-directory-v2-scopes.md).

Możliwość dynamicznie za pomocą aplikacji, aby zażądać uprawnień `scope` parametru zapewnia pełną kontrolę nad środowiskiem użytkownika. Jeśli chcesz, możesz frontload Twojej zgody środowisko i poproś o wszystkie uprawnienia w jednym żądaniu początkowej autoryzacji. Lub jeśli aplikacja wymaga dużej liczby uprawnień, można zebrać te uprawnienia od użytkownika przyrostowo, jak próbują określonych funkcji aplikacji w czasie.

## <a name="well-known-scopes"></a>Dobrze znane zakresów

### <a name="offline-access"></a>Dostęp w trybie offline

Aplikacje przy użyciu punktu końcowego v2.0 może wymagać użycia nowego uprawnienia dobrze znanego dla aplikacji - `offline_access` zakresu. Wszystkie aplikacje, należy poprosić to uprawnienie, jeśli muszą uzyskać dostęp do zasobów w imieniu użytkownika przez dłuższy okres czasu, nawet jeśli użytkownik może nie być aktywnie używających aplikacji. `offline_access` Zakres będzie widoczna dla użytkowników w oknach dialogowych zgodę jako "Dostęp do danych w trybie offline", który użytkownik musi wyrazić zgodę na. Żądanie `offline_access` uprawnień spowoduje włączenie aplikacji sieci web do odbierania refresh_tokens OAuth 2.0 z punktem końcowym v2.0. Refresh_tokens są długotrwałe i może zostać wymienione na nowe access_tokens OAuth 2.0 przez dłuższy czas dostępu. 

Jeśli aplikacja nie żąda `offline_access` zakresu, nie będą otrzymywali refresh_tokens. Oznacza to, że podczas zrealizować authorization_code przepływu kodu autoryzacji protokołu OAuth 2.0, otrzymasz tylko ponownie ' access_token ' z `/token` punktu końcowego. Tego ' access_token ' pozostanie ważny krótkim czasie (zazwyczaj jedna godzina), ale ostatecznie wygaśnie. W tym punktu w czasie, aplikacja będzie musiał przekieruje użytkownika z powrotem do `/authorize` punkt końcowy do pobierania nowych authorization_code. Podczas tego przekierowania użytkownik może lub nie trzeba ponownie wprowadzić swoje poświadczenia lub ponownie wyrażenia zgody na uprawnienia, w zależności od typu aplikacji.

Aby dowiedzieć się więcej na temat protokołu OAuth 2.0, refresh_tokens i access_tokens, zapoznaj się [referencyjne protokołu v2.0](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, profilu i poczta e-mail

W przeszłości najprostsze przepływ protokołu OpenID Connect logowania w usłudze Azure AD zapewni wiele informacji dotyczących użytkownika w żądaniu wynikowy. Oświadczenia w żądaniu mogą obejmować użytkownika nazwę, username preferowany adres e-mail, identyfikator obiektu i więcej.

Informacje który `openid` zakresu daje aplikacji jest teraz dostęp do ograniczone. `openid` Zakresu zezwala tylko aplikację, aby zalogować użytkownika i odbierać identyfikator specyficzny dla aplikacji dla użytkownika. Jeśli chcesz uzyskać danych osobowych dotyczących użytkownika w aplikacji, aplikacji musi żądać dodatkowych uprawnień od użytkownika. Dwa nowe zakresy — `email` i `profile` zakresy — umożliwi zażądania dodatkowych uprawnień.

`email` Zakres umożliwia dostęp aplikacji do użytkownika podstawowego adresu e-mail za pośrednictwem `email` oświadczeń w żądaniu. 

`profile` Zakresu daje aplikacji dostęp do wszystkich innych podstawowe informacje o użytkowniku — ich nazwy, preferowane username identyfikator obiektu i tak dalej.

Dzięki temu można kod aplikacji w sposób minimalnego ujawnienie — tylko poproś użytkownika dla zestawu danych, czy aplikacja wymaga, aby rozpocząć tworzenie. Aby uzyskać więcej informacji o tych zakresów, zobacz [dokumentacja zakresu v2.0](active-directory-v2-scopes.md).

## <a name="token-claims"></a>Oświadczenia tokenu

Oświadczeń z tokenów wystawionych przez punkt końcowy w wersji 2.0 nie będą takie same jak tokeny wystawione przez ogólnie dostępna punktów końcowych usługi Azure AD. Migracja do nowej usługi aplikacji nie powinna założenie, że danego oświadczenia będą istnieć w id_tokens lub access_tokens. Informacje na temat określonych emitowanych w wersji 2.0 tokeny oświadczeń, zobacz [odwołania do tokenu v2.0](active-directory-v2-tokens.md).

## <a name="limitations"></a>Ograniczenia

Istnieje kilka ograniczeń pod uwagę podczas korzystania z punktem w wersji 2.0. Aby dowiedzieć się, jeśli dowolny z tych ograniczeń dotyczy Twojego scenariusza, zobacz [v2.0 ograniczenia doc](active-directory-v2-limitations.md).
