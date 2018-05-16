---
title: Wskazówki dla deweloperów na potrzeby dostępu warunkowego w usłudze Azure Active Directory
description: Wskazówki dla deweloperów i scenariuszy dostępu warunkowego dla usługi Azure AD
services: active-directory
keywords: ''
author: CelesteDG
manager: mtillman
editor: PatAltimore
ms.author: celested
ms.reviewer: dadobali
ms.date: 07/19/2017
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.openlocfilehash: 922af0d860528902bc1eb36d72476852c67fcadf
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Wskazówki dla deweloperów na potrzeby dostępu warunkowego w usłudze Azure Active Directory

Azure Active Directory (Azure AD) oferuje kilka sposobów zabezpieczania aplikacji i usługi ochrony. Jedną z tych funkcji unikatowy jest dostępu warunkowego. Dostęp warunkowy umożliwia deweloperom i klienci korporacyjni ochrony usług w wiele różnych sposobów, na przykład:

* Uwierzytelnianie wieloskładnikowe
* Stosowanie Intune tylko zarejestrowane urządzenia dostępu do określonych usług
* Ograniczenie lokalizacji użytkownika i adres IP z zakresów

Aby uzyskać więcej informacji na pełne możliwości dostępu warunkowego, zobacz [dostępu warunkowego w usłudze Azure Active Directory](../active-directory-conditional-access-azure-portal.md). 

W tym artykule możemy skupić się na jakie dostępu warunkowego oznacza, że Deweloperzy tworzący aplikacje dla usługi Azure AD. Zakłada się znajomość [pojedynczego](active-directory-integrating-applications.md) i [wielodostępne](active-directory-devhowto-multi-tenant-overview.md) aplikacji i [typowe wzorce uwierzytelniania](active-directory-authentication-scenarios.md).

Firma Microsoft będzie Poznaj wpływ uzyskiwania dostępu do zasobów, które nie mają kontrolę nad mających zasady dostępu warunkowego są stosowane. Ponadto Poznaj możemy efekty dostępu warunkowego w imieniu-przepływ "w", aplikacje sieci web podczas uzyskiwania dostępu do programu Microsoft Graph i wywoływania interfejsów API.

## <a name="how-does-conditional-access-impact-an-app"></a>Jak dostęp warunkowy wpływ aplikacji?

### <a name="app-types-impacted"></a>Typy aplikacji, w pełni funkcjonalne

W typowych przypadkach dostępu warunkowego nie powoduje zmiany zachowania aplikacji lub wymaga wszelkie zmiany od dewelopera. Tylko w niektórych przypadkach, gdy aplikacja pośrednio lub w trybie dyskretnym zażąda token dla usługi, aplikacja wymaga zmian kodu do obsługi dostępu warunkowego "wyzwania". Może być tak proste, jak wykonywanie interakcyjne żądania logowania. 

W szczególności następujące scenariusze wymagają kodu do obsługi dostępu warunkowego "wyzwania": 

* Uzyskiwanie dostępu do programu Microsoft Graph aplikacji
* Wykonywanie przepływu w imieniu — z aplikacji
* Uzyskiwanie dostępu do wielu usług/zasoby aplikacji
* Aplikacje jednej strony przy użyciu ADAL.js
* Wywoływanie zasobu aplikacji sieci Web

Dostęp warunkowy zasad można zastosować do aplikacji, ale mogą być również stosowane do interfejsu API sieci web uzyskuje dostęp do aplikacji. Aby dowiedzieć się więcej o sposobie konfigurowania zasad dostępu warunkowego, zobacz [wprowadzenie do korzystania z usługi Azure Active Directory dostępu warunkowego](../active-directory-conditional-access-azure-portal-get-started.md).

W zależności od scenariusza klient enterprise można zastosować i usuwać zasady dostępu warunkowego w dowolnym momencie. Aplikację, aby kontynuować działanie po zastosowaniu nowych zasad, należy zaimplementować Obsługa "test". Poniższe przykłady przedstawiają żądania obsługi. 

### <a name="conditional-access-examples"></a>Przykłady dostępu warunkowego

W niektórych scenariuszach wymagane zmiany kodu do obsługi dostępu warunkowego innych prac, ponieważ jest. Poniżej przedstawiono kilka scenariuszy, w celu uwierzytelniania wieloskładnikowego, zapewniająca dotyczą różnica przy użyciu dostępu warunkowego.

* Tworzenia aplikacji systemu iOS pojedynczej dzierżawy i Zastosuj zasady dostępu warunkowego. Loguje użytkownika i nie żądania dostępu do interfejsu API aplikacji. Gdy użytkownik się zaloguje, zasad jest wywoływana automatycznie i użytkownik powinien przeprowadzać uwierzytelnianie wieloskładnikowe (MFA). 
* Tworzysz aplikację sieci web wielodostępne, który używa programu Microsoft Graph dostęp do programu Exchange, między innymi usługami. Klient przedsiębiorstwa, który przyjmuje tej aplikacji ustawia zasady programu Exchange. W przypadku aplikacji sieci web żądania tokenu Graph MS, aplikacja nie zostanie wąskie do zgodnej z zasadami. Użytkownik końcowy jest zalogowany za pomocą prawidłowych tokenów. Jeśli aplikacja próbuje użyć tego tokenu dla programu Microsoft Graph dostępu do danych programu Exchange, oświadczenia "test", jest zwracana do aplikacji sieci web za pośrednictwem ```WWW-Authenticate``` nagłówka. Aplikacja może następnie używać ```claims``` w nowe żądanie, a użytkownik końcowy otrzyma monit zgodne z warunkami. 
* Tworzysz aplikacji natywnej, która dostępu podrzędne interfejsu API przy użyciu usługi warstwy środkowej. Odbiorcy przedsiębiorstwa w firmie za pomocą tej aplikacji stosuje zasady do podrzędne interfejsu API. Po zalogowaniu użytkownika końcowego aplikacji natywnej żąda dostępu do warstwy środkowej i wysyła ten token. Warstwy środkowej wykonuje imieniu-przepływ "w" umożliwia zażądanie dostępu do interfejsu API niższego rzędu. W tym momencie oświadczenia "test" jest przesyłany do warstwy środkowej. Warstwy środkowej wysyła żądania do natywnej aplikacji, która musi być zgodne z zasadami dostępu warunkowego.

### <a name="complying-with-a-conditional-access-policy"></a>Zgodne z zasadami dostępu warunkowego

Dla kilku topologiach innej aplikacji zasady dostępu warunkowego jest oceniane po ustanowieniu sesji. Jak zasady dostępu warunkowego na poziom szczegółowości, aplikacji i usług, punktu, w którym jest wywoływana silnie zależny od scenariusza, który próbujesz wykonać.

Gdy aplikacja próbuje dostępu do usługi za pomocą zasad dostępu warunkowego, napotkać żądanie dostępu warunkowego. Ten problem został zakodowany w `claims` parametr, który jest dostarczany w odpowiedzi z usługi Azure AD lub programu Microsoft Graph. Oto przykład tego parametru żądania: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Deweloperzy mogą wykonać tego żądania i dołącza je na nowe żądanie do usługi Azure AD. Przekazywanie tego stanu monituje użytkownika końcowego do wykonywania dowolnych akcji, które są niezbędne do zapewnienia zgodności z zasadami dostępu warunkowego. W poniższych scenariuszach opisano szczegóły błędu i jak wyodrębnić parametru. 

## <a name="scenarios"></a>Scenariusze

### <a name="prerequisites"></a>Wymagania wstępne

Azure AD dostęp warunkowy jest to funkcja dostępna w [Azure AD Premium](../active-directory-whatis.md#choose-an-edition). Dowiedz się więcej na temat wymagań dotyczących licencjonowania [raport użycia licencji](../active-directory-conditional-access-unlicensed-usage-report.md). Deweloperzy mogą dołączyć [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx), która obejmuje bezpłatna subskrypcja pakietu Enterprise Mobility Suite, która obejmuje usługi Azure AD Premium.

### <a name="considerations-for-specific-scenarios"></a>Zagadnienia dotyczące konkretnych scenariuszy

Poniższe informacje dotyczą tylko w tych scenariuszach dostępu warunkowego:

* Uzyskiwanie dostępu do programu Microsoft Graph aplikacji
* Wykonywanie przepływu w imieniu — z aplikacji
* Uzyskiwanie dostępu do wielu usług/zasoby aplikacji
* Aplikacje jednej strony przy użyciu ADAL.js

W poniższych sekcjach będzie uzyskujemy do typowe scenariusze, w którym są bardziej złożone. Podstawowe zasada działania jest dostępu warunkowego, które zasady są oceniane w czasie żądanej tokenu usługi, który ma zasady dostępu warunkowego, chyba że jest uzyskiwany za pomocą programu Microsoft Graph.

## <a name="scenario-app-accessing-microsoft-graph"></a>Scenariusz: Aplikacja uzyskiwanie dostępu do programu Microsoft Graph

W tym scenariuszu firma Microsoft przeprowadzenie przypadku, gdy dostęp żądań aplikacji sieci web do programu Microsoft Graph. W takim przypadku zasad dostępu warunkowego może zostać przypisana do programu SharePoint, Exchange lub innych usług, który jest dostępny jako obciążenia za pomocą programu Microsoft Graph. W tym przykładzie załóżmy, że istnieje zasady dostępu warunkowego w usłudze Sharepoint Online.

![Uzyskiwanie dostępu do programu Microsoft Graph diagram przepływu aplikacji](media/active-directory-conditional-access-developer/app-accessing-microsoft-graph-scenario.png)

Aplikację po raz pierwszy żąda autoryzacji do programu Microsoft Graph, które wymaga dostępu do podrzędne obciążenia bez dostępu warunkowego. Żądanie zakończy się pomyślnie bez wywoływania dowolne zasady i aplikacja odbiera tokeny dla programu Microsoft Graph. W tym momencie aplikacja może używać tokenu dostępu w żądaniu elementu nośnego dla żądanego punktu końcowego. Teraz aplikacja musi uzyskać dostępu do usługi Sharepoint Online punktu końcowego programu Microsoft Graph, na przykład: `https://graph.microsoft.com/v1.0/me/mySite`

Aplikacja jest już prawidłowy token dla programu Microsoft Graph, który może wykonywać nowe żądanie bez wystawieniu nowy token. To żądanie nie powiedzie się i żądanie oświadczeń jest wystawione przez program Microsoft Graph w formie HTTP 403 — Dostęp zabroniony z ```WWW-Authenticate``` żądania.

Oto przykład odpowiedzi: 

```
HTTP 403; Forbidden 
error=insufficient_claims
www-authenticate="Bearer realm="", authorization_uri="https://login.windows.net/common/oauth2/authorize", client_id="<GUID>", error=insufficient_claims, claims={"access_token":{"polids":{"essential":true,"values":["<GUID>"]}}}"
```

Żądanie oświadczeń znajduje się wewnątrz ```WWW-Authenticate``` nagłówek, który można przeanalizować można wyodrębnić parametru oświadczeń dla następnego żądania. Po zostanie ono dodane nowe żądanie, usługi Azure AD traktował ocena zasad dostępu warunkowego, podczas logowania użytkownika, a aplikacja jest obecnie niezgodne z zasadami dostępu warunkowego. Powtarzanie żądania do punktu końcowego usługi Sharepoint Online zakończy się pomyślnie.

```WWW-Authenticate``` Nagłówek ma unikatowy struktury i nie jest prosta do analizy w celu wyodrębnienia wartości. Oto krótkie metodę pomocy.

```csharp
        /// <summary>
        /// This method extracts the claims value from the 403 error response from MS Graph. 
        /// </summary>
        /// <param name="wwwAuthHeader"></param>
        /// <returns>Value of the claims entry. This should be considered an opaque string. 
        /// Returns null if the wwwAuthheader does not contain the claims value. </returns>
        private String extractClaims(String wwwAuthHeader)
        {
            String ClaimsKey = "claims=";
            String ClaimsSubstring = "";
            if (wwwAuthHeader.Contains(ClaimsKey))
            {
                int Index = wwwAuthHeader.IndexOf(ClaimsKey);
                ClaimsSubstring = wwwAuthHeader.Substring(Index, wwwAuthHeader.Length - Index);
                string ClaimsChallenge;
                if (Regex.Match(ClaimsSubstring, @"}$").Success)
                {
                    ClaimsChallenge = ClaimsSubstring.Split('=')[1];
                }
                else
                {
                    ClaimsChallenge = ClaimsSubstring.Substring(0, ClaimsSubstring.IndexOf("},") + 1);
                }
                return ClaimsChallenge;
            }
            return null; 
        }
```

Aby uzyskać przykłady kodu, które przedstawiają sposób obsługi żądania oświadczeń, zobacz [przykładowy kod w imieniu — z](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) dla platformy .NET ADAL.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Scenariusza: Wykonywanie przepływu w imieniu — z aplikacji

W tym scenariuszu firma Microsoft Przeprowadź przez przypadek, w którym aplikacji natywnej wywołania API/usługi sieci web. Z kolei, ta usługa ma [przepływ "w imieniu — z"](active-directory-authentication-scenarios.md#application-types-and-scenarios) do wywołania usługi podrzędne. W tym przypadku firma Microsoft zastosowane nasze zasady dostępu warunkowego z usługą podrzędne (Web API 2) i korzysta z aplikacji natywnej, a nie aplikacji serwera/demon. 

![Diagram przepływu w imieniu — z wykonywania aplikacji](media/active-directory-conditional-access-developer/app-performing-on-behalf-of-scenario.png)

Początkowe żądanie tokenu 1 interfejsu API sieci Web nie jest wyświetlany monit użytkownika końcowego w usłudze Multi-Factor authentication, jak 1 interfejsu API sieci Web nie zawsze trafień podrzędne interfejsu API. Po 1 interfejsu API sieci Web próbuje żądania tokenu w imieniu — z użytkownika sieci Web API 2, żądanie kończy się niepowodzeniem, ponieważ użytkownik nie jest zalogowany za pomocą usługi Multi-Factor authentication.

Usługi Azure AD zwraca odpowiedź HTTP z niektórych interesujące dane: 

> [!NOTE]
> W tym wystąpieniu jest opis błędu uwierzytelniania wieloskładnikowego, ale istnieje szereg `interaction_required` możliwe dotyczących dostępu warunkowego. 

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

W naszym 1 interfejsu API sieci Web, możemy catch błąd `error=interaction_required`i wysłanie `claims` żądania do aplikacji klasycznych. W tym momencie pulpitu aplikacja może wykonać nową `acquireToken()` połączenia i Dołącz `claims`żądania jako parametr ciągu zapytania dodatkowa. To nowe żądanie wymaga od użytkownika czy uwierzytelnianie wieloskładnikowe, a następnie wysłać ten nowy token z powrotem do 1 interfejsu API sieci Web i ukończyć przepływu w imieniu z.

Aby wypróbować ten scenariusz, zobacz nasze [przykładowy kod .NET](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Go pokazuje, jak przekazać żądania oświadczeń od 1 do interfejsu API sieci Web do aplikacji natywnej i utworzyć nowe żądanie wewnątrz aplikacji klienckiej. 

## <a name="scenario-app-accessing-multiple-services"></a>Scenariusz: Aplikacja uzyskiwanie dostępu do wielu usług

W tym scenariuszu firma Microsoft Przeprowadź przez przypadek, w którym aplikacja sieci web uzyskuje dostęp do dwóch usług z których jedna ma przypisane zasady dostępu warunkowego. W zależności od logiki aplikacji może istnieć ścieżka aplikacji nie wymaga dostępu do obu usług sieci web. W tym scenariuszu kolejności, w której żądania tokenu odgrywa ważną rolę w środowisko użytkownika końcowego.

Załóżmy, że mamy usługi sieci web A i B, a usługa sieci web B ma nasze zasady dostępu warunkowego zastosowane. Podczas żądania początkowego uwierzytelniania interakcyjne wymaga zgody dla obu usług, zasady dostępu warunkowego nie jest wymagane we wszystkich przypadkach. Jeśli aplikacja prosi token dla usługi sieci web B, zasad jest wywoływany i kolejne żądania usługi sieci web, A także powiedzie się w następujący sposób.

![Uzyskiwanie dostępu do diagramu przepływu wielu usług aplikacji](media/active-directory-conditional-access-developer/app-accessing-multiple-services-scenario.png)

Alternatywnie Jeśli aplikacja zażąda początkowo token A usługi sieci web, użytkownik końcowy nie jest wywoływany zasad dostępu warunkowego. Dzięki temu deweloper aplikacji kontrolować środowisko użytkownika końcowego i wymuszania zasad dostępu warunkowego do wywołania we wszystkich przypadkach. Trudnych zdarza się, jeśli aplikacja prosi następnie token dla usługi sieci web B. W tym momencie użytkownik końcowy musi przestrzegać zasad dostępu warunkowego. Gdy aplikacja próbuje `acquireToken`, może generować następujący błąd (przedstawiony na poniższym diagramie): 

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
``` 

![Dostęp do wielu usług żądania tokenu nowej aplikacji](media/active-directory-conditional-access-developer/app-accessing-multiple-services-new-token.png)

Jeśli aplikacja używa biblioteki ADAL, awarii można uzyskać tokenu jest zawsze ponowione interaktywnie. W przypadku wystąpienia tego żądania interaktywny użytkownik końcowy ma możliwość wykonania dostępu warunkowego. Jest to wartość true, chyba że żądanie jest `AcquireTokenSilentAsync` lub `PromptBehavior.Never` w takim przypadku aplikacja musi wykonać interaktywnej ```AcquireToken``` żądanie daje możliwość zgodnej z zasadami ostatecznego wykorzystania. 

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Scenariusz: Jednej strony aplikacji JEDNOSTRONICOWEJ przy użyciu ADAL.js

W tym scenariuszu firma Microsoft przeprowadzenie sytuacji, gdy będziemy mieć aplikacji jednej strony (SPA), za pomocą ADAL.js do wywołania interfejsu API sieci web warunkowy dostęp do chronionych. To jest prosty architektury, ale ma pewne różnice, które należy uwzględnić podczas opracowywania wokół dostępu warunkowego.

W ADAL.js, istnieje kilka funkcji, które uzyskać tokeny: `login()`, `acquireToken(...)`, `acquireTokenPopup(…)`, i `acquireTokenRedirect(…)`. 

* `login()` uzyskuje identyfikator tokenu za pośrednictwem interaktywnego żądanie logowania, ale nie uzyskanie tokenów dostępu do dowolnej usługi (łącznie z dostępu warunkowego chronione składnika web API). 
* `acquireToken(…)` następnie można dyskretnie uzyskać tokenu dostępu, co oznacza, że nie pokazuje interfejsu użytkownika w żadnym przypadku. 
* `acquireTokenPopup(…)` i `acquireTokenRedirect(…)` są oba używane do interaktywnego żądania tokenu dla zasobu oznacza zawsze pokazuj interfejsu użytkownika logowania.

Gdy aplikacja potrzebuje tokenu dostępu, aby wywołać interfejs API sieci Web, podejmuje `acquireToken(…)`. Jeśli wygasł tokenu sesji lub musimy zgodne z zasadami dostępu warunkowego, a następnie *acquireToken* funkcja kończy się niepowodzeniem i używa aplikacji `acquireTokenPopup()` lub `acquireTokenRedirect()`.

![Przy użyciu biblioteki ADAL diagram przepływu aplikacji jednej strony](media/active-directory-conditional-access-developer/spa-using-adal-scenario.png)

Przejdźmy przykład z naszym scenariuszu dostępu warunkowego. Użytkownik końcowy po prostu wyładowany w lokacji i nie ma sesji. Możemy wykonywać `login()` wywołać, uzyskiwanie Identyfikatora tokenu bez usługi Multi-Factor authentication. Następnie użytkownik naciśnie przycisk, którego wymaga aplikacji, aby dane żądania z interfejsu API sieci web. Aplikacja próbuje przeprowadzić `acquireToken()` ale kończy się niepowodzeniem, ponieważ użytkownik nie wykonał jeszcze uwierzytelnianie wieloskładnikowe i musi być zgodne z zasadami dostępu warunkowego.

Usługi Azure AD odsyła następującą odpowiedź HTTP: 

```
HTTP 400; Bad Request 
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Nasze aplikacja powinna catch `error=interaction_required`. Aplikacja może następnie używać albo `acquireTokenPopup()` lub `acquireTokenRedirect()` na tego samego zasobu. Użytkownik będzie zmuszony do usługi Multi-Factor authentication. Po zakończeniu uwierzytelniania wieloskładnikowego użytkownik aplikacji jest wystawiony token świeże dostępu do żądanego zasobu.

Aby wypróbować ten scenariusz, zobacz nasze [przykładowy kod w imieniu — z JS SPA](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Ten przykładowy kod korzysta z zasad dostępu warunkowego i zarejestrowane wcześniej z SPA JS, aby zademonstrować, w tym scenariuszu interfejsu API sieci web. Widoczny jest sposób poprawnie obsługiwać żądania oświadczeń i uzyskania tokenu dostępu, który może służyć do interfejsu API sieci Web. Alternatywnie wyewidencjonowania ogólne [przykładowy kod Angular.js](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) wskazówki dotyczące SPA dyrektywy Angular


## <a name="see-also"></a>Zobacz także

* Aby dowiedzieć się więcej o możliwościach, zobacz [dostępu warunkowego w usłudze Azure Active Directory](../active-directory-conditional-access-azure-portal.md).
* Aby uzyskać przykłady kodu więcej Azure AD, zobacz [repozytorium Github przykładów kodu](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory). 
* Aby uzyskać więcej informacji na ADAL zestawu SDK i dostęp do dokumentacji, zobacz [przewodnik biblioteki](active-directory-authentication-libraries.md).
* Aby dowiedzieć się więcej o scenariuszach z wieloma dzierżawcami, zobacz [jak do logowania użytkowników przy użyciu wzorca wielodostępne](active-directory-devhowto-multi-tenant-overview.md).
