---
title: Ograniczenia punktu końcowego v2.0 w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Lista ograniczenia i ograniczenia dotyczące punktu końcowego v2.0 usługi Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a99289c0-e6ce-410c-94f6-c279387b4f66
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: celested
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.openlocfilehash: edf0b52e5889fe8fa875de65fcaa8c2a22df1a7f
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590753"
---
# <a name="should-i-use-the-v20-endpoint"></a>Należy używać punktu końcowego v2.0?

W przypadku tworzenia aplikacji, które integrują się z usługą Azure Active Directory (Azure AD), musisz zdecydować, czy v2.0 punktu końcowego i protokołów uwierzytelniania własnych potrzeb. Oryginalny punktu końcowego usługi Azure AD jest nadal w pełni obsługiwane, a pod pewnymi względami jest więcej wyposażonym niż w wersji 2.0. Jednak punktu końcowego v2.0 [wprowadzono znaczące korzyści związane](azure-ad-endpoint-comparison.md) dla deweloperów.

Oto uproszczony zalecenia dla deweloperów, w tym momencie:

* Jeśli w Twojej aplikacji, musi obsługiwać osobistych kont Microsoft, użyj punktu końcowego v2.0. Jednak zanim to zrobisz, pamiętaj, że znasz ograniczenia omówionych w tym artykule.
* Jeśli aplikacja wymaga tylko do obsługi prac firmy Microsoft i kont służbowych, nie należy używać punktu końcowego v2.0. Zamiast tego należy odwoływać się do [przewodnik dewelopera usługi Azure AD](azure-ad-developers-guide.md).

Aby wyeliminować ograniczenia wymienione w tym miejscu, tak aby tylko nigdy nie będą musieli używać punktu końcowego v2.0 ewoluuje wraz z punktem końcowym v2.0. W międzyczasie Użyj w tym artykule, aby określić, czy punktem końcowym v2.0 jest odpowiedni dla Ciebie. Firma Microsoft nalicza się do aktualizacji w tym artykule, aby odzwierciedlić bieżący stan punktu końcowego v2.0. Sprawdź ponownie oceń wymagań względem możliwości w wersji 2.0.

Jeśli masz istniejącą aplikację usługi Azure AD, która nie korzysta z punktu końcowego v2.0, nie ma potrzeby od samego początku. W przyszłości firma Microsoft zapewnia łatwy sposób, możesz użyć istniejącej aplikacji usługi Azure AD z punktem końcowym v2.0.

## <a name="restrictions-on-app-types"></a>Ograniczenia dotyczące typów aplikacji

Obecnie następujące typy aplikacji nie są obsługiwane przez punkt końcowy w wersji 2.0. Opis typów obsługiwanych aplikacji, zobacz [typy aplikacji dla punktu końcowego v2.0 usługi Azure Active Directory](v2-app-types.md).

### <a name="standalone-web-apis"></a>Interfejsy API sieci Web autonomiczny

Można użyć do punktem końcowym v2.0 [Tworzenie internetowego interfejsu API, który jest zabezpieczony za pomocą protokołu OAuth 2.0](v2-app-types.md#web-apis). Jednak tego interfejsu API sieci Web może odbierać tokeny tylko z aplikacji, która ma ten sam identyfikator aplikacji. Nie można uzyskać dostępu do internetowego interfejsu API z klienta, który ma inny identyfikator aplikacji. Klient nie będzie mógł żądania lub uzyskania uprawnień do internetowego interfejsu API.

Aby zobaczyć, jak utworzyć internetowy interfejs API, który akceptuje tokeny od klienta, który ma ten sam identyfikator aplikacji, zobacz przykłady interfejsu API sieci Web punktu końcowego v2.0 w [wprowadzenie](active-directory-appmodel-v2-overview.md#getting-started) sekcji.

## <a name="restrictions-on-app-registrations"></a>Ograniczenia dotyczące rejestracje aplikacji

Obecnie dla każdej aplikacji, którą chcesz zintegrować z punktem końcowym v2.0, należy utworzyć rejestracji aplikacji w nowym [portalu rejestracji aplikacji Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). Istniejącej usługi Azure AD lub aplikacji konta Microsoft nie są zgodne z punktem końcowym v2.0. Aplikacje, które są zarejestrowane w dowolnym portalu, innych niż portalu rejestracji aplikacji nie są zgodne z punktem końcowym v2.0. W przyszłości planujemy zapewnienie sposób na korzystanie z istniejącej aplikacji jako aplikacji w wersji 2.0. Obecnie nie ma żadnych ścieżki migracji do istniejącej aplikacji do pracy z punktem końcowym v2.0.

Ponadto rejestracje aplikacji, które tworzysz w [portalu rejestracji aplikacji](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) mają następujące zastrzeżenia:

* Tylko dwa wpisy tajne aplikacji są dozwolone na identyfikator aplikacji.
* Rejestracja aplikacji zarejestrowanych przez użytkownika za pomocą osobistego konta Microsoft można wyświetlać i zarządzane tylko przez konto jednego dewelopera. Nie można udostępniać między wielu deweloperów. Jeśli chcesz udostępnić swoją rejestrację aplikacji wśród wielu deweloperów, można utworzyć aplikacji, logując się do portalu rejestracji przy użyciu konta usługi Azure AD.
* Istnieje kilka ograniczeń dotyczących formatu przekierowania URI, który jest dozwolony. Aby uzyskać więcej informacji na temat identyfikatorów URI przekierowań zobacz następną sekcję.

## <a name="restrictions-on-redirect-uris"></a>Ograniczenia dotyczące identyfikatory URI przekierowania

Aplikacje, które są zarejestrowane w portalu rejestracji aplikacji są ograniczone do ograniczonego zestawu wartości identyfikatora URI przekierowania. Przekierowania URI dla aplikacji i usług internetowych musi zaczynać się od systemu `https`, a wszystkie wartości identyfikatora URI przekierowania muszą współużytkować jedną domenę DNS. Na przykład nie można zarejestrować aplikacji sieci web, która zawiera jedną z tych identyfikatorów URI przekierowania:

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

System rejestracji porównuje całą nazwę DNS identyfikatora URI przekierowania istniejącej nazwy DNS przekierowania URI, który chcesz dodać. Żądanie dodania nazwy DNS zakończy się niepowodzeniem, jeśli będzie spełniony jeden z następujących warunków:  

* Cała nazwa DNS nowego identyfikatora URI przekierowania nie jest zgodna z nazwą DNS istniejącego identyfikatora URI przekierowania.
* Cała nazwa DNS nowego identyfikatora URI przekierowania nie jest poddomeną istniejącego identyfikatora URI przekierowania.

Na przykład, jeśli aplikacja ma ten identyfikator URI przekierowania:

`https://login.contoso.com`

Można dodać do niego adres w następujący sposób:

`https://login.contoso.com/new`

W takim przypadku nazwa DNS jest idealnie zgodna. Można też zrobić tak:

`https://new.login.contoso.com`

W takim przypadku przywoływana jest poddomena DNS domeny login.contoso.com. Jeśli chcesz utworzyć aplikację działającą login-east.contoso.com i login-west.contoso.com jako identyfikatorów URI przekierowania, musisz dodać te identyfikatory URI przekierowania w następującej kolejności:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Dwa ostatnie adresy można dodać, ponieważ są poddomenami pierwszego przekierowania URI, contoso.com. To ograniczenie zostanie usunięte w kolejnej wersji.

Należy również zauważyć, może mieć tylko 20 adresów URL odpowiedzi dla określonej aplikacji.

Aby dowiedzieć się, jak zarejestrować aplikację w portalu rejestracji aplikacji, zobacz [jak zarejestrować aplikację za pośrednictwem punktu końcowego v2.0](quickstart-v2-register-an-app.md).

## <a name="restrictions-on-libraries-and-sdks"></a>Ograniczenia dotyczące bibliotek i zestawów SDK

Obecnie Obsługa bibliotek dla punktu końcowego v2.0 jest ograniczona. Jeśli chcesz używać punktu końcowego v2.0 w aplikacji produkcyjnej, masz następujące opcje:

* Jeśli tworzysz aplikację sieci web bezpiecznie służy oprogramowania pośredniczącego Microsoft jest ogólnie dostępna po stronie serwera do wykonywania sprawdzania poprawności logowania i tokenu. Obejmują one oprogramowania pośredniczącego OWIN Open ID Connect platformy ASP.NET i Node.js Passport wtyczki. Aby uzyskać przykłady kodu, które używają oprogramowania pośredniczącego Microsoft, zobacz [wprowadzenie](active-directory-appmodel-v2-overview.md#getting-started) sekcji.
* Jeśli tworzysz aplikację na komputerze lub urządzeniu przenośnym, można użyć jednej z bibliotek uwierzytelniania firmy Microsoft (MSAL) w wersji zapoznawczej. Tych bibliotek znajdują się w obsługiwane w środowisku produkcyjnym wersji zapoznawczej, więc bezpiecznie z nich korzystać w aplikacjach produkcyjnych. Możesz dowiedzieć się więcej o warunkach korzystania z wersji zapoznawczej i dostępnych bibliotek w [dokumentacja bibliotek uwierzytelniania](reference-v2-libraries.md).
* W przypadku platform nie są objęte biblioteki Microsoft można zintegrować z punktem końcowym v2.0 przez bezpośrednie wysyłanie i odbieranie wiadomości protokołu w kodzie aplikacji. Protokoły OpenID Connect i OAuth 2.0 [są udokumentowane](active-directory-v2-protocols.md) ułatwiające wykonanie takiej integracji.
* Na koniec można użyć bibliotek typu open-source Otwórz ID Connect i OAuth do integracji z punktem końcowym v2.0. Protokół v2.0 powinien być zgodny z wielu bibliotek typu open-source protokołu bez znaczące zmiany. Dostępność tych rodzajów bibliotek zależy od języka i platformy. [Open ID Connect](http://openid.net/connect/) i [OAuth 2.0](http://oauth.net/2/) witryn sieci Web utrzymywać listę popularnych implementacji. Aby uzyskać więcej informacji, zobacz [biblioteki Azure Active Directory w wersji 2.0 i uwierzytelniania](reference-v2-libraries.md)i listy, biblioteki klienckie typu open source i przykładów, które zostały przetestowane z punktem końcowym v2.0.

## <a name="restrictions-on-protocols"></a>Ograniczenia dotyczące protokołów

Nie obsługuje punktu końcowego v2.0, SAML lub WS-Federation; obsługuje tylko Open ID Connect i OAuth 2.0. Nie wszystkie funkcje i możliwości protokołów OAuth zostały włączone do punktu końcowego v2.0.

Następujące funkcje protokołu i funkcje są obecnie *nie jest dostępna* w punkcie końcowym v2.0:

* Obecnie `email` oświadczeń jest zwracany tylko jeśli skonfigurowano opcjonalnego roszczenia, a zakres jest zakresem = wiadomość e-mail została określona w żądaniu. Jednak to zachowanie zmieni się zgodnie z punktem końcowym v2.0 jest aktualizowana w celu dalszego są zgodne ze standardami Open ID Connect i OAuth 2.0.
* Informacje o użytkowniku OpenID Connect punkt końcowy nie został zaimplementowany dla punktu końcowego v2.0. Jednak wszystkie dane profilu użytkownika, które potencjalnie otrzyma tego punktu końcowego jest dostępna z programu Microsoft Graph `/me` punktu końcowego.
* Punktu końcowego v2.0 nie obsługuje wystawiającego oświadczeń roli lub grupy w tokeny Identyfikatora.
* [Przydział poświadczeń hasła właściciela zasobów OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.3) nie jest obsługiwana przez punktu końcowego v2.0.

Ponadto punktu końcowego v2.0 nie obsługuje dowolnej formie protokołów SAML lub WS-Federation.

Aby lepiej zrozumieć zakres protokołu funkcje obsługiwane w punkcie końcowym v2.0, zapoznaj się z artykułem naszych [referencyjne protokołu OpenID Connect i OAuth 2.0](active-directory-v2-protocols.md).

## <a name="restrictions-for-work-and-school-accounts"></a>Ograniczenia dotyczące kont służbowych

Jeśli używano Active Directory Authentication Library (ADAL) w aplikacjach Windows, należy wprowadzić jakieś zalet zintegrowanego uwierzytelniania Windows, które używa grant asercji zabezpieczeń Assertion Markup Language (SAML). Z tym przydziałem użytkowników federacyjnych usługi Azure AD mogą dyskretnie uwierzytelnianie dzierżaw za pomocą swojego wystąpienia usługi Active Directory w środowisku lokalnym bez konieczności wprowadzania poświadczeń. Obecnie grant potwierdzenie SAML nie jest obsługiwana w punkcie końcowym v2.0.
