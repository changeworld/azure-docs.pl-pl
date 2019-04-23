---
title: Wskazówki dla deweloperów na potrzeby dostępu warunkowego usługi Azure Active Directory
description: Wskazówki dla deweloperów i scenariuszy dostępu warunkowego usługi Azure AD
services: active-directory
keywords: ''
author: CelesteDG
manager: mtillman
ms.author: celested
ms.reviewer: dadobali
ms.date: 02/28/2019
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3346f7a5af2a22cb7b7ece312fc367a874095668
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001052"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Wskazówki dla deweloperów na potrzeby dostępu warunkowego usługi Azure Active Directory

Funkcja dostępu warunkowego w usłudze Azure Active Directory (Azure AD) oferuje kilka sposobów, które służy do zabezpieczania aplikacji i ochrony usługi. Dostęp warunkowy umożliwia deweloperom i firmom, chronią usługi w różnych sposobów, w tym:

* Uwierzytelnianie wieloskładnikowe
* Zezwalanie tylko usług Intune zarejestrowanych urządzeń na dostęp do określonych usług
* Ograniczenie lokalizacji użytkownika i adres IP zakresów

Aby uzyskać więcej informacji na temat pełnego zestawu dostępu warunkowego, zobacz [dostępu warunkowego w usłudze Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

Dla deweloperów, tworzenie aplikacji dla usługi Azure AD w tym artykule przedstawiono sposób korzystania z dostępu warunkowego i zdobędziesz także wiedzę na temat uzyskiwania dostępu do zasobów, które nie mają wpływu że kontrolę nad tym, który może być stosowane zasady dostępu warunkowego. Artykuł opisuje także wpływ dostępu warunkowego w przepływie w imieniu użytkownika z aplikacji sieci web, uzyskiwanie dostępu do programu Microsoft Graph i wywoływanie interfejsów API.

Znajomości [pojedynczego](quickstart-v1-integrate-apps-with-azure-ad.md) i [wielodostępnych](howto-convert-app-to-be-multi-tenant.md) aplikacji i [typowych wzorców uwierzytelniania](authentication-scenarios.md) zakłada, że.

## <a name="how-does-conditional-access-impact-an-app"></a>Jaki dostęp warunkowy wpływ na aplikację?

### <a name="app-types-impacted"></a>Typy aplikacji wpływ

W typowych przypadkach dostępu warunkowego nie powoduje zmiany zachowania aplikacji lub wymaga zmiany od dewelopera. W przypadku aplikacji, pośrednio lub w trybie cichym żąda tokenu dla usługi tylko w niektórych przypadkach aplikacja wymaga zmiany kodu do obsługi dostępu warunkowego "wyzwania". Może to być proste i polega na wykonywanie interaktywne żądanie logowania.

W szczególności następujące scenariusze wymagają kod służący do obsługi dostępu warunkowego "wyzwania":

* Wykonywanie przepływu w imieniu użytkownika z aplikacji
* Uzyskiwanie dostępu do wielu usług/zasobów aplikacji
* Aplikacje jednej strony, przy użyciu ADAL.js
* Usługa Web Apps wywołanie zasobu

Dostęp warunkowy, zasady mogą być stosowane do aplikacji, ale także mogą być stosowane do interfejsu API sieci web uzyskuje dostęp do aplikacji. Aby dowiedzieć się więcej o sposobie konfigurowania zasad dostępu warunkowego, zobacz [Szybki Start: Wymagać uwierzytelniania Wieloskładnikowego dla określonych aplikacji przy użyciu dostępu warunkowego usługi Azure Active Directory](../conditional-access/app-based-mfa.md).

W zależności od scenariusza Klient firmowy zastosować i usuwania zasad dostępu warunkowego w dowolnym momencie. Dla twojej aplikacji kontynuować działanie po zastosowaniu nowych zasad, należy zaimplementować obchodzenia "żądania". Poniższe przykłady ilustrują żądania obsługi.

### <a name="conditional-access-examples"></a>Przykłady dostępu warunkowego

Niektóre scenariusze wymagają zmiany kodu do obsługi dostępu warunkowego, natomiast inne działał. Poniżej przedstawiono kilka scenariuszy przy użyciu dostępu warunkowego w celu uwierzytelniania wieloskładnikowego, który umożliwia pewne wgląd w różnicy.

* Tworzysz aplikację dla systemu iOS z jedną dzierżawą i Zastosuj zasady dostępu warunkowego. Aplikacja loguje się użytkownik i nie poprosić o dostęp do interfejsu API. Gdy użytkownik się zaloguje, zasady jest wywoływana automatycznie, a użytkownik musi wykonywać uwierzytelnianie wieloskładnikowe (MFA). 
* Tworzysz natywnych aplikacji, która korzysta z usługi warstwy środkowej dostępu do podrzędnego interfejsu API. Klient firmowy w firmie korzystania z tej aplikacji stosuje zasady podrzędne interfejsu API. Po zalogowaniu się użytkownika końcowego aplikacji natywnej żąda dostępu do warstwy środkowej i wysyła ten token. Warstwa środkowa wykonuje w imieniu z przepływu, aby zażądać dostępu do interfejsu API niższego rzędu. W tym momencie oświadczenia "żądanie" są prezentowane w warstwie środkowej. Warstwy środkowej wysyła żądania do natywnej aplikacji, która musi być zgodna z zasadami dostępu warunkowego.

#### <a name="microsoft-graph"></a>Microsoft Graph

Program Microsoft Graph ma specjalne zagadnienia dotyczące tworzenia aplikacji w środowiskach dostępu warunkowego. Ogólnie rzecz biorąc sposobu działania dostępu warunkowego, działa tak samo, ale zasady, które użytkownicy zobaczą będzie zależeć od danych źródłowych, których aplikacja żąda z wykresu. 

W szczególności wszystkie zakresy w programie Microsoft Graph reprezentują niektóre zestawu danych, który indywidualnie może mieć stosowane zasady. Ponieważ zasady dostępu warunkowego są przypisane określone zestawy danych, usługi Azure AD będzie wymuszania zasad dostępu warunkowego w oparciu o dane źródłowe wykresu — zamiast samego wykresu.

Na przykład, jeśli aplikacja żąda następujące zakresy programu Microsoft Graph

```
scopes="Bookings.Read.All Mail.Read"
```

Aplikację można oczekiwać, że użytkownicy spełnienia wszystkich zasad zabezpieczeń ustawionych na Bookings i Exchange. Niektóre zakresy są mapowane do wielu zestawów danych, jeśli on przyznanie dostępu. 

### <a name="complying-with-a-conditional-access-policy"></a>Zgodne z zasadami dostępu warunkowego

Dla kilku topologiach innej aplikacji zasady dostępu warunkowego jest oceniany po ustanowieniu sesji. Jak działa zasad dostępu warunkowego na poziom szczegółowości, aplikacji i usług, punkt, w którym jest wywoływana zależy od intensywnie scenariusza, który próbujesz osiągnąć.

Gdy aplikacja próbuje uzyskać dostęp do usługi za pomocą zasad dostępu warunkowego, napotkać żądania dostępu warunkowego. Ten problem został zakodowany w `claims` parametr, który jest dostarczany w odpowiedzi z usługi Azure AD. Oto przykład tego parametru żądania: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Deweloperzy mogą zająć temu wyzwaniu i dołącz go na nowe żądanie do usługi Azure AD. Przekazanie tego stanu monituje użytkownika końcowego do wykonywania dowolnych akcji, które są niezbędne do zapewnienia zgodności z zasadami dostępu warunkowego. W następujących scenariuszach opisano szczegóły błędu i sposób wyodrębniania parametru.

## <a name="scenarios"></a>Scenariusze

### <a name="prerequisites"></a>Wymagania wstępne

Dostęp warunkowy usługi Azure AD jest to funkcja dostępna w [usługi Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). Dowiedz się więcej na temat licencjonowania wymagań [raport użycia nielicencjonowane](../active-directory-conditional-access-unlicensed-usage-report.md). Deweloperzy mogą dołączyć do [sieci Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx), co obejmuje bezpłatna subskrypcja pakietu Enterprise Mobility Suite, która obejmuje usługi Azure AD Premium.

### <a name="considerations-for-specific-scenarios"></a>Zagadnienia dotyczące konkretnych scenariuszy

Poniższe informacje dotyczą tylko w tych scenariuszy dostępu warunkowego:

* Wykonywanie przepływu w imieniu użytkownika z aplikacji
* Uzyskiwanie dostępu do wielu usług/zasobów aplikacji
* Aplikacje jednej strony, przy użyciu ADAL.js

W poniższych sekcjach omówiono typowe scenariusze, które są bardziej złożone. Podstawowa zasada działania jest dostęp warunkowy, których zasady są oceniane w czasie których żądany jest token dla usługi, która ma zastosowane zasady dostępu warunkowego.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Scenariusz: Wykonywanie przepływu w imieniu użytkownika z aplikacji

W tym scenariuszu, w jaki sposób za pośrednictwem przypadek, w którym aplikacja natywna wywołania API/usługi sieci web. Z kolei ta usługa ma przepływ "w imieniu z" do wywołania usługi podrzędne. W naszym przypadku możemy zostały zastosowane nasze zasady dostępu warunkowego w usłudze podrzędnego (Web API 2) i korzysta z aplikacji natywnej, a nie aplikacji demona/na serwerze. 

![Diagram przepływu w imieniu z wykonywania aplikacji](./media/conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

Żądania tokenu początkowego dla sieci Web API 1 nie monituje użytkownika końcowego do uwierzytelniania wieloskładnikowego, zgodnie z 1 interfejsu API sieci Web nie zawsze trafień podrzędnego interfejsu API. Po 1 interfejsu API sieci Web podejmie próbę wysłania żądania tokenu w imieniu z użytkownika w sieci Web API 2, żądanie kończy się niepowodzeniem, ponieważ użytkownik nie zalogował się przy użyciu usługi Multi-Factor authentication.

Usługa Azure AD zwraca odpowiedź HTTP z niektórych interesujące dane:

> [!NOTE]
> W tym wystąpieniu jest opis błędu uwierzytelniania wieloskładnikowego, ale istnieje szereg `interaction_required` możliwe odnoszących się do dostępu warunkowego.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

W sieci Web API 1, zostanie przechwycony błąd `error=interaction_required`i odsyłania `claims` żądania do aplikacji klasycznej. W tym momencie można wprowadzać nową aplikację klasyczną `acquireToken()` wywoływanie obiektów blob i uzupełnialnych `claims`wyzwaniem, jako parametr ciągu zapytania dodatkowych. To nowe żądanie wymaga od użytkownika czy uwierzytelnianie wieloskładnikowe, a następnie wysłanie tego nowego tokenu do 1 interfejsu API sieci Web i ukończenie przepływu w imieniu z.

Aby wypróbować ten scenariusz, zobacz nasze [przykładowy kod .NET](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Pokazuje sposób przekazywać żądania oświadczeń powrót po awarii z 1 interfejsu API sieci Web do natywnej aplikacji i utworzyć nowe żądanie w aplikacji klienckiej.

## <a name="scenario-app-accessing-multiple-services"></a>Scenariusz: Uzyskiwanie dostępu do wielu usług aplikacji

W tym scenariuszu, w jaki sposób za pośrednictwem przypadek, w którym aplikacja sieci web uzyskuje dostęp do dwóch usług w jednym z nich ma przypisane zasady dostępu warunkowego. W zależności od logika aplikacji może istnieć ścieżka aplikacji nie wymaga dostępu do obu usług sieci web. W tym scenariuszu kolejność, w której żądania tokenu odgrywa ważną rolę w środowisku użytkownika końcowego.

Załóżmy, że mamy usługi sieci web, A i B, a usługi sieci web B ma nasze zasady dostępu warunkowego, zastosowane. Gdy żądanie początkowe uwierzytelnianie interakcyjne wymaga zgody dla obu usług, zasady dostępu warunkowego nie jest wymagany we wszystkich przypadkach. Jeśli aplikacja żąda tokenu usługi sieci web B, jest wywoływana przez zasady, a kolejne żądania usługi sieci web, A także zakończy się pomyślnie w następujący sposób.

![Uzyskiwanie dostępu do wielu usług diagramu przepływu aplikacji](./media/conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Alternatywnie, jeśli aplikacja żąda tokenu początkowo, a usługi sieci web, użytkownik końcowy nie jest wywoływany zasad dostępu warunkowego. Dzięki temu deweloper aplikacji do kontroli użytkownika końcowego środowisko i wymuszania zasad dostępu warunkowego do wywołania we wszystkich przypadkach. Trudne wielkość liter jest, jeśli aplikacja następnie żąda tokenu usługi sieci web B. W tym momencie użytkownik końcowy musi zachować zgodności z zasadami dostępu warunkowego. Gdy aplikacja próbuje `acquireToken`, to mogą pojawić się następujący błąd (zilustrowane na poniższym diagramie):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![Uzyskiwanie dostępu do wielu usług, żądanie nowego tokenu aplikacji](./media/conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Jeśli aplikacja używa biblioteki ADAL, awarii w celu uzyskania tokenu zawsze zostanie ponowiony interaktywnie. W przypadku wystąpienia tego interaktywnego żądania użytkownika końcowego ma możliwość wykonania przy użyciu dostępu warunkowego. Dotyczy to żądanie jest `AcquireTokenSilentAsync` lub `PromptBehavior.Never` w takim przypadku aplikacja musi wykonać interaktywną ```AcquireToken``` żądania, aby dać użytkownikowi możliwość zgodna z zasadami.

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Scenariusz: Aplikacja jednostronicowa (SPA) przy użyciu ADAL.js

W tym scenariuszu przeanalizujemy przypadku gdy otrzymamy aplikacji jednostronicowej (SPA) przy użyciu ADAL.js wywołać warunkowego dostępu do chronionego internetowego interfejsu API. To jest architektura proste, ale ma niektóre różnice, które należy wziąć pod uwagę, opracowując wokół dostępu warunkowego.

W ADAL.js, istnieje kilka funkcji, które uzyskiwania tokenów: `login()`, `acquireToken(...)`, `acquireTokenPopup(…)`, i `acquireTokenRedirect(…)`.

* `login()` uzyskuje identyfikator tokenu za pośrednictwem interakcyjnego żądanie logowania, ale nie uzyskiwanie tokenów dostępu usługi (łącznie z dostępu warunkowego chronionego internetowego interfejsu API).
* `acquireToken(…)` następnie można dyskretnie uzyskania tokenu dostępu, co oznacza, że nie uwzględnia interfejsu użytkownika w dowolnej sytuacji.
* `acquireTokenPopup(…)` i `acquireTokenRedirect(…)` są zarówno używany na potrzeby interakcyjnego żądania tokenu do zasobu oznacza zawsze pokazuj interfejs użytkownika logowania.

Gdy aplikacja potrzebuje tokenu dostępu do wywoływania interfejsu API sieci Web, próbuje `acquireToken(…)`. Jeśli token sesja wygasła lub musimy jest zgodne z zasadami dostępu warunkowego, a następnie *acquireToken* funkcja kończy się niepowodzeniem, a ta aplikacja używa `acquireTokenPopup()` lub `acquireTokenRedirect()`.

![Aplikacja jednostronicowa, za pomocą biblioteki ADAL diagramu przepływu](./media/conditional-access-dev-guide/spa-using-adal-scenario.png)

Przejdźmy teraz przez przykład z naszym scenariuszu dostępu warunkowego. Użytkownik końcowy po prostu otwarta w lokacji i nie ma sesji. Wykonamy `login()` wywołanie, Uzyskaj identyfikator tokenu z pominięciem usługi Multi-Factor authentication. Następnie użytkownik naciśnie przycisk, który wymaga aplikacji, aby dane żądania z internetowego interfejsu API. Aplikacja próbuje wykonać `acquireToken()` wywołanie ale nie powiedzie się, ponieważ użytkownik nie wykonał jeszcze uwierzytelnianie wieloskładnikowe i musi być zgodne z zasadami dostępu warunkowego.

Usługa Azure AD wysyła z powrotem następującą odpowiedź HTTP:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Nasza aplikacja musi zostać przechwycony `error=interaction_required`. Aplikacja następnie użyć `acquireTokenPopup()` lub `acquireTokenRedirect()` na ten sam zasób. Użytkownik jest zmuszony do usługi Multi-Factor authentication. Po ukończeniu uwierzytelniania wieloskładnikowego użytkownik aplikacji wystawiono tokenu dostępu od nowa dla żądanego zasobu.

Aby wypróbować ten scenariusz, zobacz nasze [przykładowy kod w imieniu użytkownika z aplikacji JEDNOSTRONICOWEJ JS](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Ten przykładowy kod korzysta z zasad dostępu warunkowego i internetowy interfejs API zarejestrowany wcześniej z aplikacji JEDNOSTRONICOWEJ JS, aby zademonstrować tego scenariusza. Widoczny jest sposób poprawnie obsługiwać żądania oświadczeń i uzyskania tokenu dostępu, który może służyć do interfejsu API sieci Web. Alternatywnie wyewidencjonowania ogólne [przykładowy kod Angular.js](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) wskazówki dotyczące platformy Angular SPA

## <a name="see-also"></a>Zobacz także

* Aby dowiedzieć się więcej o możliwościach, zobacz [dostępu warunkowego w usłudze Azure Active Directory](../active-directory-conditional-access-azure-portal.md).
* Aby uzyskać przykłady kodu w usłudze Azure AD, zobacz [z przykładów kodu w repozytorium GitHub](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory).
* Aby uzyskać więcej informacji dotyczących dostępu dokumentację referencyjną i ADAL zestawu SDK, zobacz [instrukcją biblioteki](active-directory-authentication-libraries.md).
* Aby dowiedzieć się więcej na temat scenariuszy z wieloma dzierżawami, zobacz [jak logować użytkowników za pomocą wzorca wielodostępnych](howto-convert-app-to-be-multi-tenant.md).
