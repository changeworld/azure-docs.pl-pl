---
title: Integracja z platformą tożsamości firmy Microsoft | Azure
description: Zapoznaj się z najlepszymi rozwiązaniami i typowymi szczegółowymi informacjami na temat integracji z platformą tożsamości firmy Microsoft (v 2.0).
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
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 33128cbece3b217778182b3831b02e2f3f654f3b
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853214"
---
# <a name="microsoft-identity-platform-integration-checklist"></a>Lista kontrolna integracji z platformą Microsoft Identity

Lista kontrolna integracji z platformą Microsoft Identity została zaprojektowana w celu zapewnienia wysokiej jakości i bezpiecznej integracji. Wyróżnia ona najlepsze rozwiązania i często występujące szczegółowe informacje podczas integrowania z platformą tożsamości firmy Microsoft, co pozwala na regularne zapoznanie się z listą w celu zapewnienia jakości i bezpieczeństwa integracji aplikacji z platformą tożsamości. Lista kontrolna nie ma na celu przejrzenia całej aplikacji. Zawartość listy kontrolnej może ulec zmianie w miarę wprowadzania ulepszeń na platformie.

Jeśli dopiero zaczynasz, zapoznaj się z [dokumentacją](index.yml) , aby dowiedzieć się więcej na temat podstawowych informacji dotyczących uwierzytelniania, scenariuszy aplikacji na platformie tożsamości firmy Microsoft i innych.

## <a name="testing-your-integration"></a>Testowanie integracji

Użyj poniższej listy kontrolnej, aby upewnić się, że aplikacja jest efektywnie zintegrowana z [platformą tożsamości firmy Microsoft](https://docs.microsoft.com/azure/active-directory/develop/).

### <a name="basics"></a>Ustawienia podstawowe

|   |   |
|---|---|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Przeczytaj i zapoznaj się z [zasadami platformy firmy Microsoft](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409). Upewnij się, że aplikacja jest zgodna z warunkami, które zostały zaprojektowane w celu ochrony użytkowników i platformy. |

### <a name="ownership"></a>Wyniku

|   |   |
|---|---|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Upewnij się, że informacje skojarzone z kontem używanym do rejestrowania aplikacji i zarządzania nimi są aktualne. |

### <a name="branding"></a>Znakowanie

|   |   |
|---|---|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Przestrzeganie [wytycznych dotyczących znakowania aplikacji](howto-add-branding-in-azure-ad-apps.md). |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Podaj opisową nazwę i logo dla swojej aplikacji. Te informacje są wyświetlane w monicie o zgodę aplikacji. Upewnij się, że Twoja nazwa i logo są reprezentatywne dla Twojej firmy/produktu, aby użytkownicy mogli podejmować świadome decyzje. Upewnij się, że nie są naruszane żadne znaki towarowe. |

### <a name="privacy"></a>Ochrona prywatności

|   |   |
|---|---|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Podaj linki do warunków użytkowania i zasad zachowania poufności informacji aplikacji. |

### <a name="security"></a>Bezpieczeństwo

|   |   |
|---|---|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Zachować własność wszystkich identyfikatorów URI przekierowania i zachować Aktualności rekordów DNS. Nie używaj symboli wieloznacznych (*) w identyfikatorach URI. W przypadku usługi Web Apps upewnij się, że wszystkie identyfikatory URI są bezpieczne i szyfrowane (na przykład przy użyciu schematów https). W przypadku klientów publicznych Użyj identyfikatorów URI przekierowania specyficznych dla platformy, jeśli są odpowiednie (głównie dla systemów iOS i Android). W przeciwnym razie Użyj identyfikatorów URI przekierowania z dużą ilością losowości, aby zapobiec kolizjom podczas wywoływania z powrotem do aplikacji. Jeśli aplikacja jest używana przez izolowanego agenta sieci Web, można użyć https://login.microsoftonline.com/nativeclient programu. Regularnie Przeglądaj i przycinaj wszystkie nieużywane lub niepotrzebne identyfikatory URI przekierowań. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Jeśli Twoja aplikacja jest zarejestrowana w katalogu, Zminimalizuj i ręcznie Monitoruj listę właścicieli rejestracji aplikacji. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Nie włączaj obsługi niejawnego [przepływu OAuth2](v2-oauth2-implicit-grant-flow.md) , chyba że jest to wymagane jawnie. Zapoznaj się z prawidłowym scenariuszem. [](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant) |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Nie używaj [przepływu poświadczeń hasła właściciela zasobu (ROPC)](v2-oauth-ropc.md), który bezpośrednio obsługuje hasła użytkowników. Ten przepływ wymaga wysokiego stopnia zaufania i zagrożeń użytkownika i powinien być używany tylko wtedy, gdy inne, bezpieczniejsze, nie można używać przepływów. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Ochrona poświadczeń aplikacji i zarządzanie nimi. Użyj [poświadczeń certyfikatu](active-directory-certificate-credentials.md), a nie poświadczeń hasła (kluczy tajnych klienta). Jeśli musisz użyć poświadczenia hasła, nie ustawiaj go ręcznie. Nie należy przechowywać poświadczeń w kodzie ani w konfiguracji, a nigdy nie zezwalać na ich obsługę przez człowieka. Jeśli to możliwe, użyj [zarządzanych tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) lub [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) do przechowywania i regularnego rotacji Twoich poświadczeń. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Upewnij się, że aplikacja żąda najniższych uprawnień. Tylko Poproś o uprawnienia wymagane przez aplikację i tylko wtedy, gdy są one potrzebne. Zapoznaj się z różnymi [rodzajami uprawnień](v1-permissions-and-consent.md#types-of-permissions). W razie potrzeby używaj uprawnień aplikacji; Użyj uprawnień delegowanych, jeśli jest to możliwe. Aby uzyskać pełną listę uprawnień Microsoft Graph, zobacz to [odwołanie do uprawnień](https://docs.microsoft.com/graph/permissions-reference). |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | W przypadku zabezpieczania interfejsu API przy użyciu platformy tożsamości firmy Microsoft należy uważnie zastanowić się nad uprawnieniami, które powinien ujawnić. Weź pod uwagę, jaki jest poziom szczegółowości rozwiązania i jakie uprawnienia wymagają zgody administratora. Przed podjęciem decyzji o autoryzacji Sprawdź, czy w tokenach przychodzących są oczekiwane uprawnienia. |

### <a name="implementation"></a>Implementacja

|   |   |
|---|---|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Używaj nowoczesnych rozwiązań uwierzytelniania (OAuth 2,0, [OpenID Connect Connect](v2-protocols-oidc.md)) do bezpiecznego logowania użytkowników. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Nie Wdrażaj samodzielnie protokołów — Użyj [bibliotek uwierzytelniania obsługiwanych przez firmę Microsoft](reference-v2-libraries.md) (MSAL, oprogramowanie pośredniczące serwera). Upewnij się, że używasz najnowszej wersji biblioteki uwierzytelniania zintegrowanej z programem. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Jeśli dane wymagane przez aplikację są dostępne za pośrednictwem [Microsoft Graph](https://developer.microsoft.com/graph), zażądaj uprawnień dla tych danych przy użyciu punktu końcowego Microsoft Graph, a nie do poszczególnych interfejsów API. |

### <a name="end-user-experience"></a>Środowisko użytkownika końcowego

|   |   |
|---|---|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | [Zapoznaj się z](application-consent-experience.md) tematem zgody i skonfiguruj fragmenty monitu o zgodę aplikacji, aby użytkownicy końcowi i administratorzy mieli wystarczającą ilość informacji, aby określić, czy ufają aplikacji. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Zminimalizuj, ile razy użytkownik musi wprowadzić poświadczenia logowania podczas korzystania z aplikacji, próbując przeprowadzić uwierzytelnianie dyskretne (dyskretny token) przed interaktywnymi przepływami. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Nie używaj "Prompt = zgody" przy każdym logowaniu. Należy używać monitu = wyrażanie zgody, jeśli określono, że należy zażądać zgody na dodatkowe uprawnienia (na przykład jeśli zmieniono wymagane uprawnienia aplikacji). |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Jeśli ma to zastosowanie, Wzbogacaj aplikację o dane użytkownika. Aby to zrobić, użyj [interfejsu API Microsoft Graph](https://developer.microsoft.com/graph) . Narzędzie [Eksplorator grafów](https://developer.microsoft.com/graph/graph-explorer) ułatwiające rozpoczęcie pracy. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Zarejestruj pełny zestaw uprawnień wymaganych przez aplikację, aby administratorzy mogli łatwo udzielić zgody na dostęp do swojej dzierżawy. Używaj [przyrostowej zgody](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) w czasie wykonywania, aby ułatwić użytkownikom zrozumienie, Dlaczego aplikacja żąda uprawnień, które mogą mieć problemy lub mylić użytkowników, gdy są żądane podczas pierwszego uruchomienia. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Zaimplementuj [czyste środowisko logowania](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut)jednokrotnego. Jest to ochrona prywatności i wymagania dotyczące zabezpieczeń oraz zapewnia dobre środowisko użytkownika. |

### <a name="testing"></a>Testowanie

|   |   |
|---|---|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Przetestuj [zasady dostępu warunkowego](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) , które mogą mieć wpływ na możliwość korzystania z aplikacji przez użytkowników. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Przetestuj swoją aplikację ze wszystkimi możliwymi kontami, które planujesz obsługiwać (na przykład konta służbowe, osobiste konta Microsoft, konta podrzędne i suwerenne konta). |

## <a name="additional-resources"></a>Dodatkowe zasoby

Uzyskaj szczegółowe informacje na temat wersji 2.0:

* [Microsoft Identity platform (wersja 2.0 — omówienie)](v2-overview.md)
* [Informacje o protokołach Microsoft Identity platform](active-directory-v2-protocols.md)
* [Dokumentacja tokenów dostępu](access-tokens.md)
* [Dokumentacja tokenów identyfikatorów](id-tokens.md)
* [Dokumentacja bibliotek uwierzytelniania](reference-v2-libraries.md)
* [Uprawnienia i zgoda na platformie tożsamości firmy Microsoft](v2-permissions-and-consent.md)
* [Interfejs API programu Microsoft Graph](https://developer.microsoft.com/graph)
