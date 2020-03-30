---
title: Wskazówki dla deweloperów usługi Azure AD dotyczącym dostępu warunkowego
description: Wskazówki dla deweloperów i scenariusze dostępu warunkowego usługi Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 02/28/2019
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ROBOTS: NOINDEX
ms.openlocfilehash: 92acb1a475fbd41bfb7351d73c61db866ce2bbc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154937"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Wskazówki dla deweloperów dotyczące dostępu warunkowego usługi Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Funkcja dostępu warunkowego w usłudze Azure Active Directory (Azure AD) oferuje jeden z kilku sposobów, za pomocą których można zabezpieczyć aplikację i chronić usługę. Dostęp warunkowy umożliwia deweloperom i klientom korporacyjnym ochronę usług na wiele sposobów, w tym:

* Uwierzytelnianie wieloskładnikowe
* Zezwalanie tylko zarejestrowanym urządzeniom usługi Intune na dostęp do określonych usług
* Ograniczanie lokalizacji użytkowników i zakresów adresów IP

Aby uzyskać więcej informacji na temat pełnych możliwości dostępu warunkowego, zobacz [Dostęp warunkowy w usłudze Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

W przypadku deweloperów tworzących aplikacje dla usługi Azure AD w tym artykule pokazano, jak można użyć dostępu warunkowego, a także dowiedzieć się o wpływie uzyskiwania dostępu do zasobów, nad którymi nie masz kontroli, nad którymi mogą być stosowane zasady dostępu warunkowego. W tym artykule omówiono również implikacje dostępu warunkowego w imieniu przepływu, aplikacje sieci web, uzyskiwanie dostępu do programu Microsoft Graph i wywoływanie interfejsów API.

Zakłada się znajomość aplikacji [jedno- i wielodostępnych](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) oraz [typowych wzorców uwierzytelniania.](v1-authentication-scenarios.md)

## <a name="how-does-conditional-access-impact-an-app"></a>Jak dostęp warunkowy wpływa na aplikację?

### <a name="app-types-impacted"></a>Wpływ na typy aplikacji

W większości przypadków dostęp warunkowy nie zmienia zachowania aplikacji ani nie wymaga żadnych zmian od dewelopera.Tylko w niektórych przypadkach, gdy aplikacja pośrednio lub dyskretnie żąda tokenu dla usługi, aplikacja wymaga zmian kodu do obsługi "wyzwania" dostępu warunkowego.Może to być tak proste, jak wykonanie interaktywnego żądania logowania.

W szczególności następujące scenariusze wymagają kodu do obsługi "wyzwań" dostępu warunkowego:

* Aplikacje wykonujące przepływ w imieniu
* Aplikacje uzyskujące dostęp do wielu usług/zasobów
* Aplikacje jednostronicowe korzystające z pliku ADAL.js
* Aplikacje sieci Web wywołujące zasób

Zasady dostępu warunkowego można zastosować do aplikacji, ale można również zastosować do internetowego interfejsu API, do który uzyskuje dostęp aplikacja. Aby dowiedzieć się więcej o konfigurowaniu zasad dostępu warunkowego, zobacz [Szybki start: Wymaganie usługi MFA dla określonych aplikacji z dostępem warunkowym usługi Azure Active Directory](../conditional-access/app-based-mfa.md).

W zależności od scenariusza klient przedsiębiorstwa może zastosować i usunąć zasady dostępu warunkowego w dowolnym momencie. Aby aplikacja nadal działać po zastosowaniu nowych zasad, należy zaimplementować obsługę "wyzwanie". Poniższe przykłady ilustrują obsługę wyzwań.

### <a name="conditional-access-examples"></a>Przykłady dostępu warunkowego

Niektóre scenariusze wymagają zmian kodu do obsługi dostępu warunkowego, podczas gdy inne działają tak, jak jest. Oto kilka scenariuszy przy użyciu dostępu warunkowego do uwierzytelniania wieloskładnikowego, który daje pewien wgląd w różnicę.

* Budujesz aplikację dla systemu iOS z jedną dzierżawą i zastosuj zasady dostępu warunkowego. Aplikacja loguje się do użytkownika i nie żąda dostępu do interfejsu API. Gdy użytkownik się zaloguje, zasady są wywoływane automatycznie, a użytkownik musi wykonać uwierzytelnianie wieloskładnikowe (MFA). 
* Budujesz aplikację macierzystą, która używa usługi warstwy środkowej, aby uzyskać dostęp do interfejsu API podrzędnego. Klient przedsiębiorstwa w firmie korzystającej z tej aplikacji stosuje zasady do interfejsu API podrzędnego. Gdy użytkownik końcowy loguje się, natywna aplikacja żąda dostępu do warstwy środkowej i wysyła token. Warstwa środkowa wykonuje w imieniu przepływu, aby zażądać dostępu do interfejsu API podrzędnego. W tym momencie roszczenia "wyzwanie" jest przedstawiony do warstwy środkowej. Warstwa środkowa wysyła wyzwanie z powrotem do aplikacji macierzystej, która musi być zgodna z zasadami dostępu warunkowego.

#### <a name="microsoft-graph"></a>Microsoft Graph

Program Microsoft Graph ma szczególne uwagi podczas tworzenia aplikacji w środowiskach dostępu warunkowego. Ogólnie rzecz biorąc mechanika dostępu warunkowego zachowują się tak samo, ale zasady, które użytkownicy zobaczą będą oparte na danych źródłowych aplikacji żąda z wykresu. 

W szczególności wszystkie zakresy programu Microsoft Graph reprezentują niektóre zestawy danych, które mogą indywidualnie mieć stosowane zasady. Ponieważ zasady dostępu warunkowego są przypisywane określonych zestawów danych, usługa Azure AD będzie wymuszać zasady dostępu warunkowego na podstawie danych za wykres — zamiast samego wykresu.

Jeśli na przykład aplikacja zażąda następujących zakresów programu Microsoft Graph,

```
scopes="Bookings.Read.All Mail.Read"
```

Aplikacja może oczekiwać, że użytkownicy będą spełniać wszystkie zasady ustawione na Bookings and Exchange. Niektóre zakresy mogą być mapowane na wiele zestawów danych, jeśli udziela dostępu. 

### <a name="complying-with-a-conditional-access-policy"></a>Przestrzeganie zasad dostępu warunkowego

W przypadku kilku różnych topologii aplikacji zasady dostępu warunkowego są oceniane po ustanowieniu sesji. Ponieważ zasady dostępu warunkowego działa na szczegółowości aplikacji i usług, punkt, w którym jest wywoływana zależy w dużej mierze od scenariusza, który próbujesz wykonać.

Gdy aplikacja próbuje uzyskać dostęp do usługi z zasadami dostępu warunkowego, może wystąpić wyzwanie dostępu warunkowego. To wyzwanie jest zakodowane w parametrze, `claims` który jest w odpowiedzi z usługi Azure AD. Oto przykład tego parametru wyzwania: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Deweloperzy mogą podjąć to wyzwanie i dołączyć je do nowego żądania do usługi Azure AD. Przekazywanie tego stanu monituje użytkownika końcowego o wykonanie wszelkich działań niezbędnych do zapewnienia zgodności z zasadami dostępu warunkowego. W następujących scenariuszach szczegóły błędu i jak wyodrębnić parametr są wyjaśnione.

## <a name="scenarios"></a>Scenariusze

### <a name="prerequisites"></a>Wymagania wstępne

Dostęp warunkowy usługi Azure AD to funkcja uwzględniona w [usłudze Azure AD Premium.](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) Więcej informacji na temat wymagań licencyjnych można uzyskać w [raporcie użytkowania nielicencjonowanego](../active-directory-conditional-access-unlicensed-usage-report.md). Deweloperzy mogą dołączyć do [usługi Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx), która obejmuje bezpłatną subskrypcję pakietu Enterprise Mobility Suite, który obejmuje usługę Azure AD Premium.

### <a name="considerations-for-specific-scenarios"></a>Zagadnienia dotyczące określonych scenariuszy

Następujące informacje mają zastosowanie tylko w tych scenariuszach dostępu warunkowego:

* Aplikacje wykonujące przepływ w imieniu
* Aplikacje uzyskujące dostęp do wielu usług/zasobów
* Aplikacje jednostronicowe korzystające z pliku ADAL.js

W poniższych sekcjach omówiono typowe scenariusze, które są bardziej złożone. Podstawową zasadą działania jest zasady dostępu warunkowego są oceniane w czasie, gdy token jest wymagany dla usługi, która ma zastosowane zasady dostępu warunkowego.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Scenariusz: aplikacja wykonująca przepływ w imieniu

W tym scenariuszu przechodzimy przez przypadek, w którym natywna aplikacja wywołuje usługę sieci web/interfejs API. Z kolei ta usługa wykonuje przepływ "w imieniu" do wywołania usługi podrzędnej. W naszym przypadku zastosowaliśmy nasze zasady dostępu warunkowego do usługi podrzędnej (interfejs API sieci Web 2) i używamy aplikacji natywnej, a nie aplikacji serwera/demona. 

![Aplikacja wykonująca diagram przepływu w imieniu](./media/conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

Początkowe żądanie tokenu dla interfejsu API sieci Web 1 nie monituje użytkownika końcowego o uwierzytelnianie wieloskładnikowe, ponieważ interfejs API sieci Web 1 nie zawsze może trafić do interfejsu API podrzędnego. Gdy interfejs API sieci Web 1 próbuje zażądać tokenu w imieniu użytkownika dla interfejsu API sieci Web 2, żądanie kończy się niepowodzeniem, ponieważ użytkownik nie zalogował się za pomocą uwierzytelniania wieloskładnikowego.

Usługa Azure AD zwraca odpowiedź HTTP z interesującymi danymi:

> [!NOTE]
> W tym przypadku jest to opis błędu uwierzytelniania wieloskładnikowego, ale istnieje szeroki zakres możliwości dotyczących dostępu warunkowego. `interaction_required`

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

W interfejsie API sieci Web `error=interaction_required`1 możemy `claims` złapać błąd i odesłać wyzwanie do aplikacji klasycznej. W tym momencie aplikacja na `acquireToken()` pulpicie można `claims`wykonać nowe wywołanie i dołączyć wyzwanie jako parametr dodatkowego ciągu zapytania. To nowe żądanie wymaga od użytkownika wykonania uwierzytelniania wieloskładnikowego, a następnie wysłania tego nowego tokenu z powrotem do interfejsu API sieci Web 1 i ukończenia przepływu w imieniu.

Aby wypróbować ten scenariusz, zobacz nasz [przykładowy kod .NET](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Pokazuje, jak przekazać wyzwanie oświadczeń z powrotem z interfejsu API sieci Web 1 do aplikacji macierzystej i skonstruować nowe żądanie wewnątrz aplikacji klienckiej.

## <a name="scenario-app-accessing-multiple-services"></a>Scenariusz: dostęp do aplikacji z wieloma usługami

W tym scenariuszu przechodzimy przez przypadek, w którym aplikacja sieci web uzyskuje dostęp do dwóch usług, z których jedna ma przypisane zasady dostępu warunkowego. W zależności od logiki aplikacji może istnieć ścieżka, w której aplikacja nie wymaga dostępu do obu usług sieci web. W tym scenariuszu kolejność, w której żądasz tokenu odgrywa ważną rolę w środowiska użytkownika końcowego.

Załóżmy, że mamy usługę sieci web A i B, a usługa internetowa B ma nasze zasady dostępu warunkowego stosowane. Podczas gdy początkowe interaktywne żądanie akcesoryczne wymaga zgody dla obu usług, zasady dostępu warunkowego nie jest wymagane we wszystkich przypadkach. Jeśli aplikacja żąda tokenu dla usługi sieci web B, zasady są wywoływane, a kolejne żądania dla usługi sieci web A również zakończy się powodzeniem w następujący sposób.

![Dostęp aplikacji do diagramu przepływu wielu usług](./media/conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Alternatywnie jeśli aplikacja początkowo żąda tokenu dla usługi sieci web A, użytkownik końcowy nie wywołuje zasad dostępu warunkowego. Dzięki temu deweloper aplikacji do kontrolowania środowiska użytkownika końcowego i nie wymuszać zasady dostępu warunkowego do wywołania we wszystkich przypadkach. Trudne jest to, jeśli aplikacja następnie żąda tokenu dla usługi sieci web B. W tym momencie użytkownik końcowy musi przestrzegać zasad dostępu warunkowego. Gdy aplikacja próbuje `acquireToken`, może wygenerować następujący błąd (zilustrowany na poniższym diagramie):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![Aplikacja uzyskująca dostęp do wielu usług żądających nowego tokenu](./media/conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Jeśli aplikacja korzysta z biblioteki ADAL, niepowodzenie uzyskania tokenu jest zawsze ponowione interaktywnie. W przypadku wystąpienia tego żądania interaktywnego użytkownik końcowy ma możliwość wykonania dostępu warunkowego. Jest to prawdą, chyba `AcquireTokenSilentAsync` `PromptBehavior.Never` że żądanie jest lub w ```AcquireToken``` którym to przypadku aplikacja musi wykonać interaktywne żądanie, aby dać użytkownikowi końcowemu możliwość przestrzegania zasad.

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Scenariusz: aplikacja jednostronicowa (SPA) przy użyciu pliku ADAL.js

W tym scenariuszu możemy przejść przez przypadek, gdy mamy aplikacji jednostronicowej (SPA), za pomocą ADAL.js do wywołania interfejsu API sieci web chroniony dostęp warunkowy. Jest to prosta architektura, ale ma pewne niuanse, które należy wziąć pod uwagę podczas opracowywania wokół dostępu warunkowego.

W pliku ADAL.js istnieje kilka funkcji, `login()`które `acquireToken(...)` `acquireTokenPopup(…)`uzyskują `acquireTokenRedirect(…)`tokeny: , , i .

* `login()`uzyskuje token identyfikatora za pośrednictwem interaktywnego żądania logowania, ale nie uzyskuje tokenów dostępu dla żadnej usługi (w tym chronionego interfejsu API sieci web dostępu warunkowego).
* `acquireToken(…)`następnie można użyć do cichego uzyskania tokenu dostępu, co oznacza, że nie pokazuje interfejsu użytkownika w żadnych okolicznościach.
* `acquireTokenPopup(…)`i `acquireTokenRedirect(…)` są używane do interaktywnego żądania tokenu dla zasobu, co oznacza, że zawsze pokazują logowania w interfejsie użytkownika.

Gdy aplikacja potrzebuje tokenu dostępu do wywołania `acquireToken(…)`interfejsu API sieci Web, próbuje . Jeśli sesja tokenu wygasła lub musimy przestrzegać zasad dostępu warunkowego, funkcja *acquireToken* kończy `acquireTokenPopup()` `acquireTokenRedirect()`się niepowodzeniem, a aplikacja używa lub .

![Jednostronicowa aplikacja przy użyciu diagramu przepływu ADAL](./media/conditional-access-dev-guide/spa-using-adal-scenario.png)

Przejdźmy przez przykład z naszego scenariusza dostępu warunkowego. Użytkownik końcowy właśnie wylądował na stronie i nie ma sesji. Wykonujemy `login()` wywołanie, otrzymujemy token identyfikatora bez uwierzytelniania wieloskładnikowego. Następnie użytkownik uderza przycisk, który wymaga aplikacji do żądania danych z internetowego interfejsu API. Aplikacja próbuje wykonać `acquireToken()` wywołanie, ale kończy się niepowodzeniem, ponieważ użytkownik nie wykonał jeszcze uwierzytelniania wieloskładnikowego i musi być zgodny z zasadami dostępu warunkowego.

Usługa Azure AD odsyła następującą odpowiedź HTTP:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Nasza aplikacja musi `error=interaction_required`złapać . Aplikacja może następnie użyć `acquireTokenPopup()` `acquireTokenRedirect()` jednego lub na tym samym zasobie. Użytkownik jest zmuszony do uwierzytelniania wieloskładnikowego. Po zakończeniu uwierzytelniania wieloskładnikowego użytkownik otrzymuje nowy token dostępu dla żądanego zasobu.

Aby wypróbować ten scenariusz, zobacz nasz [przykład kodu JS SPA w imieniu.](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) Ten przykładowy kod używa zasad dostępu warunkowego i interfejsu API sieci web, który został zarejestrowany wcześniej w JS SPA, aby zademonstrować ten scenariusz. Pokazuje, jak prawidłowo obsługiwać wyzwanie oświadczeń i uzyskać token dostępu, który może służyć do interfejsu API sieci Web. Alternatywnie, wyewidencjonuj [ogólny przykład kodu Angular.js,](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) aby uzyskać wskazówki dotyczące angular SPA

## <a name="see-also"></a>Zobacz też

* Aby dowiedzieć się więcej o możliwościach, zobacz [Dostęp warunkowy w usłudze Azure Active Directory](../active-directory-conditional-access-azure-portal.md).
* Aby uzyskać więcej przykładów kodu usługi Azure AD, zobacz [Repozytorium GitHub przykładów kodu.](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory)
* Aby uzyskać więcej informacji na temat zestawów SDK ADAL i uzyskać dostęp do dokumentacji referencyjnej, zobacz [przewodnik po bibliotece](active-directory-authentication-libraries.md).
* Aby dowiedzieć się więcej o scenariuszach z wieloma dzierżawami, zobacz [Jak zalogować użytkowników przy użyciu wzorca wielodostępnika](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).
