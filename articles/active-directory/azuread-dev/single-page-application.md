---
title: Aplikacje jednostronicowe w usłudze Azure Active Directory
description: W tym artykule opisano, jakie są aplikacje jednostronicowe (SPA) i podstawy dotyczące przepływu protokołu, rejestracji i wygaśnięcia tokenu dla tego typu aplikacji.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: adf3c5b5cd40a9ea3f07ba9c92cfc4544ca60f1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154750"
---
# <a name="single-page-applications"></a>Aplikacje jednostronicowe

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Aplikacje jednostronicowe (SPA) są zazwyczaj skonstruowane jako warstwa prezentacji JavaScript (fronton), która działa w przeglądarce, a interfejs API sieci Web, który działa na serwerze i implementuje logikę biznesową aplikacji. Aby dowiedzieć się więcej na temat niejawnego udzielania autoryzacji i pomóc zdecydować, czy jest odpowiedni dla scenariusza aplikacji, zobacz [Opis niejawnego przepływu dotacji OAuth2 w usłudze Azure Active Directory.](v1-oauth2-implicit-grant-flow.md)

W tym scenariuszu, gdy użytkownik się zaloguje, fronton JavaScript używa [biblioteki uwierzytelniania usługi Active Directory dla języka JavaScript (ADAL). JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) i niejawne udzielenie autoryzacji w celu uzyskania tokenu identyfikatora (id_token) z usługi Azure AD. Token jest buforowany, a klient dołącza go do żądania jako token nośnika podczas nawiązywać wywołania do zaplecza interfejsu API sieci Web, który jest zabezpieczony przy użyciu oprogramowania pośredniczącego OWIN.

## <a name="diagram"></a>Diagram

![Diagram aplikacji jednostronicowych](./media/authentication-scenarios/single-page-app.png)

## <a name="protocol-flow"></a>Przepływ protokołu

1. Użytkownik przechodzi do aplikacji sieci web.
1. Aplikacja zwraca fronton JavaScript (warstwę prezentacji) do przeglądarki.
1. Użytkownik inicjuje logowanie, na przykład klikając łącze logowania. Przeglądarka wysyła GET do punktu końcowego autoryzacji usługi Azure AD, aby zażądać tokenu identyfikatora. To żądanie zawiera identyfikator aplikacji i adres URL odpowiedzi w parametrach zapytania.
1. Usługa Azure AD sprawdza poprawność adresu URL odpowiedzi względem zarejestrowanego adresu URL odpowiedzi skonfigurowany w witrynie Azure portal.
1. Użytkownik loguje się na stronie logowania.
1. Jeśli uwierzytelnianie zakończy się pomyślnie, usługa Azure AD tworzy token identyfikatora i zwraca go jako fragment adresu URL (#) do adresu URL odpowiedzi aplikacji. W przypadku aplikacji produkcyjnej ten adres URL odpowiedzi powinien być https. Zwrócony token zawiera oświadczenia dotyczące użytkownika i usługi Azure AD, które są wymagane przez aplikację do sprawdzania poprawności tokenu.
1. Kod klienta JavaScript uruchomiony w przeglądarce wyodrębnia token z odpowiedzi do użycia w zabezpieczaniu wywołań interfejsu API sieci web aplikacji zaplecza.
1. Przeglądarka wywołuje interfejs API sieci web aplikacji z tokenem identyfikatora w nagłówku autoryzacji. Usługa uwierzytelniania usługi Azure AD wystawia token identyfikatora, który może służyć jako token na okaziciela, jeśli zasób jest taki sam jak identyfikator klienta (w tym przypadku jest to prawdą, ponieważ internetowy interfejs API jest własną zapleczem aplikacji).

## <a name="code-samples"></a>Przykłady kodu

Zobacz [przykłady kodu dla jednostronicowych scenariuszy aplikacji](sample-v1-code.md#single-page-applications). Pamiętaj, aby często sprawdzać, jak nowe próbki są dodawane często.

## <a name="app-registration"></a>Rejestracja aplikacji

* Pojedyncza dzierżawa — jeśli budujemy aplikację tylko dla twojej organizacji, musi ona być zarejestrowana w katalogu firmy przy użyciu witryny Azure portal.
* Multi-tenant — jeśli budujesz aplikację, która może być używana przez użytkowników spoza organizacji, musi być zarejestrowana w katalogu firmy, ale również musi być zarejestrowana w katalogu każdej organizacji, która będzie korzystać z aplikacji. Aby udostępnić aplikację w ich katalogu, można dołączyć proces rejestracji dla klientów, który umożliwia im wyrażenie zgody na aplikację. Po zarejestrowaniu się w aplikacji zostaną one wyświetlone z okna dialogowego, który pokazuje uprawnienia aplikacji wymaga, a następnie opcja zgody. W zależności od wymaganych uprawnień administrator w innej organizacji może być zobowiązany do wyrażenia zgody. Gdy użytkownik lub administrator wyrazi na to zgodę, aplikacja jest rejestrowana w ich katalogu.

Po zarejestrowaniu aplikacji, musi być skonfigurowany do używania protokołu dotacji niejawnych OAuth 2.0. Domyślnie ten protokół jest wyłączony dla aplikacji. Aby włączyć protokół dotacji niejawnych OAuth2 dla aplikacji, edytuj jego manifest aplikacji z witryny Azure portal i ustaw wartość "oauth2AllowImplicitFlow" na true. Aby uzyskać więcej informacji, zobacz [Manifest aplikacji](../develop/reference-app-manifest.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="token-expiration"></a>Wygaśnięcie tokenu

Korzystanie z ADAL.js pomaga w:

* Odświeżanie wygasłego tokenu
* Żądanie tokenu dostępu do wywoływania zasobu internetowego interfejsu API

Po pomyślnym uwierzytelnieniu usługa Azure AD zapisuje plik cookie w przeglądarce użytkownika w celu ustanowienia sesji. Należy zauważyć, że sesja istnieje między użytkownikiem a usługą Azure AD (nie między użytkownikiem a aplikacją sieci web). Po wygaśnięciu tokenu plik ADAL.js używa tej sesji do dyskretnego uzyskiwania innego tokenu. Plik ADAL.js używa ukrytego elementu iFrame do wysyłania i odbierania żądania przy użyciu protokołu dotacji niejawnej OAuth. Plik ADAL.js może również używać tego samego mechanizmu do dyskretnego uzyskiwania tokenów dostępu dla innych zasobów interfejsu API sieci web, które wywołują aplikacje, o ile te zasoby obsługują współdzielenie zasobów między źródłami (CORS), są zarejestrowane w katalogu użytkownika, a wymagana zgoda została podane przez użytkownika podczas logowania.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [typach i scenariuszach aplikacji](app-types.md)
* Dowiedz się więcej o [podstawach uwierzytelniania](v1-authentication-scenarios.md) usługi Azure AD
