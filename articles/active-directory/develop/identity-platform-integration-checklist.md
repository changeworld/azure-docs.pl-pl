---
title: Integracja z platformą Microsoft identity | Azure
description: Dowiedz się więcej o najlepszych rozwiązań i wspólne przeoczeń podczas integracji z platformą tożsamości Microsoft (w wersji 2.0).
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev
ms.openlocfilehash: 0d44e5d9f0d1ed893a16c318e3cfa0ee1bfc123b
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65823348"
---
# <a name="microsoft-identity-platform-integration-checklist"></a>Lista kontrolna dotycząca programu Microsoft tożsamości platformy integracji

Lista kontrolna dotycząca programu Microsoft identity platformy integracji jest przeznaczona do wskazówki dotyczące przejścia do integracji wysokiej jakości i bezpieczne. Zawiera opis najlepszych rozwiązań typowych przeoczeń podczas integracji z platformą tożsamości Microsoft więc zapoznać się z listą w regularnych odstępach czasu, aby upewnić się, że obsługa jakość i bezpieczeństwo aplikacji integracji z platformą tożsamości. Lista kontrolna nie jest przeznaczone do przeglądania całej aplikacji. Zawartość listy kontrolnej mogą ulec zmianie w miarę wprowadzania ulepszeń do korzystania z platformy.

Jeśli po prostu rozpoczynasz pracę, zapoznaj się z [dokumentacji](index.yml) Aby dowiedzieć się więcej na temat podstawowe informacje dotyczące uwierzytelniania, scenariuszy aplikacji na platformie tożsamości firmy Microsoft i innych.

## <a name="testing-your-integration"></a>Testowanie integracji usługi

Użyj poniższej listy kontrolnej, aby upewnić się, że aplikacja efektywnie jest zintegrowana z usługą [platforma tożsamości usługi Microsoft](https://docs.microsoft.com/azure/active-directory/develop/).

### <a name="basics"></a>Ustawienia podstawowe

|   |   |
|---|---|
| ![Zaznacz pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Dokładnie zapoznaj się [zasady platformy firmy Microsoft](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409). Upewnij się, że aplikacja działa zgodnie z warunki opisane, ponieważ służą one do ochrony użytkowników i platformy. |

### <a name="ownership"></a>Własność

|   |   |
|---|---|
| ![Zaznacz pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Upewnij się, że informacje skojarzone z kontem służące do rejestrowania aplikacji i zarządzanie nimi jest aktualny. |

### <a name="branding"></a>Znakowanie

|   |   |
|---|---|
| ![Zaznacz pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Przestrzegać [znakowania wskazówki dotyczące aplikacji](howto-add-branding-in-azure-ad-apps.md). |
| ![Zaznacz pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Podaj nazwę opisową i logo aplikacji. Te informacje są wyświetlane na monit o wyrażenie zgody aplikacji. Upewnij się, że nazwy i logo są przedstawiciel firmy/product tak, aby użytkownicy mogą wprowadzać świadomych decyzji. Upewnij się, masz wszystkie znaki towarowe bez naruszania. |

### <a name="privacy"></a>Prywatność

|   |   |
|---|---|
| ![Zaznacz pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Zawierają linki do warunków aplikacji usługi i zasady zachowania poufności. |

### <a name="security"></a>Bezpieczeństwo

|   |   |
|---|---|
| ![Zaznacz pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Obsługa własności wszystkie swoje identyfikatory URI przekierowań i Aktualizuj rekordy DNS dla nich. Nie używaj symboli wieloznacznych (*) w swojej identyfikatorów URI. W przypadku aplikacji sieci web upewnij się, wszystkie identyfikatory URI jest bezpieczna i szyfrowana (na przykład przy użyciu protokołu https, schematy). Klientów publicznych identyfikatory URI przekierowania użycia specyficznego dla platformy, jeśli ma to zastosowanie (głównie dla systemów iOS i Android). W przeciwnym razie użyj przekierowania URI z dużej ilości losowości w celu uniknięcia kolizji w przypadku, gdy wywołań zwrotnych do aplikacji. Jeśli aplikacja jest używana z agenta izolowanych sieci web, można użyć https://login.microsoftonline.com/nativeclient. Przejrzyj i Przytnij wszystkie nieużywane i niepotrzebne identyfikatory URI przekierowań w regularnych odstępach czasu. |
| ![Zaznacz pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Jeśli aplikacja zostanie zarejestrowana w katalogu, należy zminimalizować i ręcznie monitorować listy właścicieli rejestracji aplikacji. |
| ![Zaznacz pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Nie włączaj obsługę [niejawne protokołu OAuth2 udzielić przepływ](v2-oauth2-implicit-grant-flow.md) , chyba że wyraźnie wymagane. Dowiedz się więcej o prawidłowe scenariusza [tutaj](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant). |
| ![Zaznacz pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Nie używaj [przepływ poświadczeń hasła właściciela zasobu (ROPC)](v2-oauth-ropc.md), które bezpośrednio obsługuje haseł użytkowników. Ten przepływ wymaga wysokiego stopnia narażenia zaufania i użytkownika i należy używać tylko, gdy nie można użyć innych, bardziej bezpiecznymi przepływów. |
| ![Zaznacz pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Ochrona poświadczeń i zarządzania nimi Twojej aplikacji. Użyj [certyfikatu poświadczeń](active-directory-certificate-credentials.md), nie poświadczeń haseł (wpisy tajne klienta). Jeśli musisz użyć poświadczeń hasła, nie należy ustawiać go ręcznie. Nie przechowuje poświadczeń w kodzie lub konfiguracji i umożliwiających nigdy nie są obsługiwane przez ludzi. Jeśli to możliwe, używaj [zarządzanych tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) lub [usługi Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) do przechowywania i regularnie Obróć swoje poświadczenia. |
| ![Zaznacz pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Upewnij się, że Twoja aplikacja żąda najniższych uprawnień uprawnień. Tylko zadawać pytania dotyczące uprawnień, absolutnie wymaganych przez aplikację i tylko wtedy, gdy ich potrzebujesz. Zrozumienie różnic [typy uprawnień](v1-permissions-and-consent.md#types-of-permissions). Używać tylko uprawnienia aplikacji, w razie potrzeby; Jeśli jest to możliwe, należy użyć uprawnień delegowanych. Aby uzyskać pełną listę uprawnień programu Microsoft Graph, zobacz ten [informacje o uprawnieniach](https://docs.microsoft.com/graph/permissions-reference). |
| ![Zaznacz pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Jeśli zabezpieczasz interfejs API przy użyciu platformy tożsamości firmy Microsoft, należy dokładnie przemyślenie uprawnienia, które powinien ujawniać. Należy wziąć pod uwagę, co to jest odpowiednim poziomie szczegółowości dla rozwiązania i uprawnień, które wymagają zgody administratora. Sprawdź, czy oczekiwany uprawnień w tokenach przychodzących przed podjęciem decyzji o autoryzacji. |

### <a name="implementation"></a>Wdrażanie

|   |   |
|---|---|
| ![Zaznacz pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Korzystanie z rozwiązań nowoczesnego uwierzytelniania (OAuth 2.0 [OpenID Connect](v2-protocols-oidc.md)) do bezpiecznego logowania użytkowników. |
| ![Zaznacz pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Nie implementują protokoły samodzielnie — użyj [bibliotek uwierzytelniania obsługiwane przez firmę Microsoft](reference-v2-libraries.md) (MSAL, oprogramowanie pośredniczące serwera). Upewnij się, że używasz najnowszej wersji biblioteki uwierzytelniania, która została zintegrowana z usługą. |
| ![Zaznacz pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Jeśli danych wymaganych przez aplikację jest dostępna za pośrednictwem [programu Microsoft Graph](https://developer.microsoft.com/graph), zażądać uprawnień dla tych danych przy użyciu punktu końcowego programu Microsoft Graph, a nie poszczególnych interfejsów API. |

### <a name="end-user-experience"></a>Środowisko użytkownika końcowego

|   |   |
|---|---|
| ![Zaznacz pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | [Omówienie środowiska zgody](application-consent-experience.md) i konfigurować elementy monit o wyrażenie zgody aplikacji tak, aby użytkownicy końcowi i Administratorzy mają informacje wystarczające do wyznaczenia, jeśli są zaufane aplikacji. |
| ![Zaznacz pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Ogranicz liczbę razy, użytkownik musi podać poświadczenia logowania podczas korzystania z aplikacji, próbując dyskretnej uwierzytelniania (dyskretnej uzyskanie tokenu) przed interaktywne przepływów. |
| ![Zaznacz pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Nie używaj "Monituj = zgodę" podczas każdego logowania. Użyj tylko wiersza = zgody, nawet jeśli trzeba Pytaj o zgodę na dodatkowe uprawnienia (na przykład, jeśli zmieniono wymagane uprawnienia aplikacji). |
| ![Zaznacz pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Gdy to stosowne, wzbogacanie aplikacji przy użyciu danych użytkownika. Użyj [interfejsu API Microsoft Graph](https://developer.microsoft.com/graph) łatwy sposób, aby to zrobić. [Graph explorer](https://developer.microsoft.com/graph/graph-explorer) narzędzie, które mogą pomóc Ci rozpocząć pracę. |
| ![Zaznacz pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Zarejestruj pełny zestaw uprawnień wymaganych przez aplikację, dzięki czemu administratorzy mogą wyrazić zgody łatwe do swojej dzierżawy. Użyj [przyrostowe zgody](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) w czasie wykonywania, aby ułatwić użytkownikom zrozumienie, dlaczego aplikacja żąda uprawnień, które mogą dotyczyć lub mylić użytkowników zleconą przy pierwszym uruchomieniu. |
| ![Zaznacz pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Implementowanie [wyczyścić pojedyncze środowisko wylogowania](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). To wymaganie dotyczące zabezpieczeń i poufności informacji i sprawia, że środowisko użytkownika. |

### <a name="testing"></a>Testowanie

|   |   |
|---|---|
| ![Zaznacz pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Test dla [zasady dostępu warunkowego](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) mogą wpływać na zdolność użytkowników do korzystania z aplikacji. |
| ![Zaznacz pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Przetestuj aplikację za pomocą wszystkich możliwych kont, które planujesz obsługiwać (na przykład, pracy lub konta służbowe, osobiste konta Microsoft, kont dzieci i kont należących do suwerennej). |

## <a name="additional-resources"></a>Dodatkowe zasoby

Uzyskaj szczegółowe informacje na temat wersji 2.0:

* [Platforma tożsamości firmy Microsoft (w wersji 2.0 — omówienie)](v2-overview.md)
* [Odwołanie protokołów platforma tożsamości firmy Microsoft](active-directory-v2-protocols.md)
* [Dokumentacja tokenów dostępu](access-tokens.md)
* [Dokumentacja tokenów identyfikatorów](id-tokens.md)
* [Dokumentacja biblioteki uwierzytelniania](reference-v2-libraries.md)
* [Uprawnienia i zgody w platformie tożsamości firmy Microsoft](v2-permissions-and-consent.md)
* [Interfejs API programu Microsoft Graph](https://developer.microsoft.com/graph)
