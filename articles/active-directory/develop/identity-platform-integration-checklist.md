---
title: Najważniejsze wskazówki dotyczące platformy tożsamości firmy Microsoft | Azure
description: Dowiedz się więcej o najlepszych rozwiązaniach, zaleceniach i typowych niedopatrzeniach podczas integracji z platformą tożsamości firmy Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/11/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 56975cebbfe4f6dd6452c850c338d431faea27bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050494"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Najważniejsze wskazówki i zalecenia dotyczące platformy tożsamości firmy Microsoft

W tym artykule przedstawiono najlepsze rozwiązania, zalecenia i typowe niedopatrzenia podczas integracji z platformą tożsamości firmy Microsoft.  Ta lista kontrolna poprowadzi Cię do wysokiej jakości i bezpiecznej integracji. Regularnie przeglądaj tę listę, aby upewnić się, że zachowasz jakość i bezpieczeństwo integracji aplikacji z platformą tożsamości. Lista kontrolna nie jest przeznaczona do przeglądania całej aplikacji. Zawartość listy kontrolnej może ulec zmianie w miarę wprowadzania ulepszeń na platformie.

Jeśli dopiero zaczynasz, zapoznaj się z [dokumentacją platformy tożsamości firmy Microsoft,](index.yml) aby dowiedzieć się więcej o podstawach uwierzytelniania, scenariuszach aplikacji na platformie tożsamości firmy Microsoft i nie tylko.

Skorzystaj z poniższej listy kontrolnej, aby upewnić się, że aplikacja jest skutecznie zintegrowana z [platformą tożsamości firmy Microsoft.](https://docs.microsoft.com/azure/active-directory/develop/)

## <a name="basics"></a>Podstawy

|   |   |
|---|---|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Przeczytaj i zrozum [zasady platformy Microsoft](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409). Upewnij się, że aplikacja jest zgodna z warunkami opisanymi, ponieważ są one przeznaczone do ochrony użytkowników i platformy. |

## <a name="ownership"></a>Własność

|   |   |
|---|---|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Upewnij się, że informacje skojarzone z kontem używanym do rejestracji aplikacji i zarządzania nimi są aktualne. |

## <a name="branding"></a>Znakowanie

|   |   |
|---|---|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Przestrzegaj [wytycznych dotyczących znakowania aplikacji.](howto-add-branding-in-azure-ad-apps.md) |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Podaj opisową nazwę i logo aplikacji. Te informacje są wyświetlane w [wierszu zgody aplikacji](application-consent-experience.md). Upewnij się, że Twoje imię i logo są reprezentatywne dla Twojej firmy/produktu, aby użytkownicy mogli podejmować świadome decyzje. Upewnij się, że nie naruszasz żadnych znaków towarowych. |

## <a name="privacy"></a>Ochrona prywatności

|   |   |
|---|---|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Podaj linki do warunków korzystania z usługi i zasad zachowania poufności informacji. |

## <a name="security"></a>Zabezpieczenia

|   |   |
|---|---|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Zarządzanie identyfikatorami URI przekierowania: <ul><li>Zachowaj własność wszystkich przekierowanych identyfikatorów URI i aktualizuj rekordy DNS.</li><li>Nie używaj symboli wieloznacznych (*) w URI.</li><li>W przypadku aplikacji internetowych upewnij się, że wszystkie identyfikatory URI są bezpieczne i zaszyfrowane (na przykład przy użyciu schematów https).</li><li>W przypadku klientów publicznych użyj URI przekierowania specyficzne dla platformy, jeśli dotyczy (głównie dla systemów iOS i Android). W przeciwnym razie użyj przekierowania identyfikatorów URI o dużej ilości losowości, aby zapobiec kolizjom podczas odsyłania z powrotem do aplikacji.</li><li>Jeśli aplikacja jest używana z izolowanego agenta internetowego, możesz użyć . `https://login.microsoftonline.com/common/oauth2/nativeclient`</li><li>Regularnie przeglądaj i przycinaj wszystkie nieużywane lub niepotrzebne przekierowanie identyfikatorów URI.</li></ul> |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Jeśli aplikacja jest zarejestrowana w katalogu, zminimalizuj i ręcznie monitoruj listę właścicieli rejestracji aplikacji. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Nie włączaj obsługi dla [przepływu niejawnej dotacji OAuth2,](v2-oauth2-implicit-grant-flow.md) chyba że jawnie wymagane. Dowiedz się więcej o prawidłowym scenariuszu [tutaj](v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant). |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Wyjdą poza nazwę użytkownika/hasło. Nie używaj [przepływu poświadczeń hasła właściciela zasobów (ROPC),](v2-oauth-ropc.md)który bezpośrednio obsługuje hasła użytkowników. Przepływ ten wymaga wysokiego stopnia zaufania i narażenia użytkownika i powinien być używany tylko wtedy, gdy nie można używać innych, bezpieczniejszych przepływów. Ten przepływ jest nadal potrzebne w niektórych scenariuszach (takich jak DevOps), ale uważaj, że przy użyciu go nałoży ograniczenia na aplikację.  Aby uzyskać bardziej nowoczesne podejścia, przeczytaj [przepływy uwierzytelniania i scenariusze aplikacji](authentication-flows-app-scenarios.md).|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Chroń poufne poświadczenia aplikacji i zarządzaj nimi w aplikacjach internetowych, internetowych interfejsach API i aplikacjach demonów. Użyj [poświadczeń certyfikatu,](active-directory-certificate-credentials.md)a nie poświadczeń hasła (wpisów tajnych klienta). Jeśli musisz użyć poświadczenia hasła, nie ustawiaj go ręcznie. Nie przechowuj poświadczeń w kodzie lub konfiguracji i nigdy nie zezwalaj na ich obsługę przez ludzi. Jeśli to możliwe, użyj [tożsamości zarządzanych dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) lub usługi Azure Key [Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) do przechowywania i regularnego obracania poświadczeń. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Upewnij się, że aplikacja żąda uprawnień z najmniejszymi uprawnieniami. Pytaj tylko o uprawnienia, których aplikacja absolutnie potrzebuje i tylko wtedy, gdy ich potrzebujesz. Poznaj różne [typy uprawnień](v2-permissions-and-consent.md#permission-types). W razie potrzeby należy używać uprawnień aplikacji; w miarę możliwości używać uprawnień delegowanych. Aby uzyskać pełną listę uprawnień programu Microsoft Graph, zobacz odwołanie do tych [uprawnień](https://docs.microsoft.com/graph/permissions-reference). |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Jeśli zabezpieczasz interfejs API przy użyciu platformy tożsamości firmy Microsoft, należy dokładnie przemyśleć uprawnienia, które powinny być uwidaczniane. Zastanów się, co jest odpowiednie szczegółowe dla rozwiązania i które uprawnienia wymagają zgody administratora. Sprawdź oczekiwane uprawnienia w tokenach przychodzących przed podjęciem jakichkolwiek decyzji dotyczących autoryzacji. |

## <a name="implementation"></a>Wdrażanie

|   |   |
|---|---|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Użyj nowoczesnych rozwiązań uwierzytelniania (OAuth 2.0, [OpenID Connect](v2-protocols-oidc.md)), aby bezpiecznie logować się do użytkowników. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) |  Nie programuj bezpośrednio przeciwko protokołom, takim jak OAuth 2.0 i Open ID. Zamiast tego skorzystaj z [biblioteki uwierzytelniania firmy Microsoft (MSAL).](msal-overview.md) Biblioteki MSAL bezpiecznie zawijają protokoły zabezpieczeń w łatwej w użyciu bibliotece, a otrzymasz wbudowaną obsługę scenariuszy [dostępu warunkowego,](/azure/active-directory/conditional-access/overview) [logowania jednokrotnego (SSO) całego](/azure/active-directory/manage-apps/what-is-single-sign-on)urządzenia i wbudowanej obsługi buforowania tokenów. Aby uzyskać więcej informacji, zobacz listę obsługiwanych przez firmę Microsoft [bibliotek klienckich](reference-v2-libraries.md#microsoft-supported-client-libraries) i [bibliotek oprogramowania pośredniczącego](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries) oraz listę [zgodnych bibliotek klienckich innych firm](reference-v2-libraries.md#compatible-client-libraries).<br/><br/>Jeśli musisz ręcznie kod dla protokołów uwierzytelniania, należy postępować zgodnie z metodologią, takich jak [Microsoft SDL](https://www.microsoft.com/sdl/default.aspx). Należy zwrócić szczególną uwagę na kwestie bezpieczeństwa w specyfikacjach standardów dla każdego protokołu.|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) |  Migrowanie istniejących aplikacji z [biblioteki uwierzytelniania usługi Azure Active Directory (ADAL)](../azuread-dev/active-directory-authentication-libraries.md) do [biblioteki uwierzytelniania firmy Microsoft](msal-overview.md). MSAL to najnowsze rozwiązanie platformy tożsamości firmy Microsoft i jest preferowane przez ADAL. Jest on dostępny na .NET, JavaScript, Android, iOS, macOS i jest również w publicznej wersji zapoznawczej dla Pythona i Java. Dowiedz się więcej o migracji [aplikacji brokerskich ADAL.NET](msal-net-migration.md), [ADAL.js](msal-compare-msal-js-and-adal-js.md)oraz [ADAL.NET i iOS.](msal-net-migration-ios-broker.md)|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) |  W przypadku aplikacji mobilnych skonfiguruj każdą platformę przy użyciu środowiska rejestracji aplikacji. Aby aplikacja korzystała z usługi Microsoft Authenticator lub Microsoft Company Portal do logowania jednokrotnego, aplikacja musi skonfigurować identyfikator URI przekierowania brokera. Dzięki temu firma Microsoft może przywrócić kontrolę do aplikacji po uwierzytelnieniu. Podczas konfigurowania każdej platformy środowisko rejestracji aplikacji poprowadzi Cię przez proces. Użyj przewodnika Szybki start, aby pobrać przykład roboczy. W systemie iOS używaj brokerów i widoku systemowego, gdy tylko jest to możliwe.|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) |  W aplikacjach sieci Web lub interfejsach API sieci Web zachowaj jedną pamięć podręczną tokenu na konto.  W przypadku aplikacji sieci web pamięć podręczna tokenu powinna być kluczem przez identyfikator konta.  W przypadku interfejsów API sieci web konto powinno być kluczem przez skrót tokenu używanego do wywoływania interfejsu API. MSAL.NET zapewnia serializację niestandardowej pamięci podręcznej tokenów na podplatformach .NET Framework i .NET Core. Ze względów bezpieczeństwa i wydajności naszą rekomendacją jest serializowanie jednej pamięci podręcznej na użytkownika. Aby uzyskać więcej informacji, przeczytaj artykuł o [serializacji pamięci podręcznej tokenów](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application).|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Jeśli dane wymagane przez aplikację są dostępne za pośrednictwem [programu Microsoft Graph,](https://developer.microsoft.com/graph)żądania uprawnień dla tych danych przy użyciu punktu końcowego programu Microsoft Graph, a nie poszczególnych interfejsów API. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) |Nie patrz na wartość tokenu dostępu lub spróbuj przeanalizować go jako klienta.  Mogą zmieniać wartości, formaty, a nawet szyfrować bez ostrzeżenia — zawsze używaj id_token, jeśli klient musi dowiedzieć się czegoś o użytkowniku lub wywołać microsoft graph.  Tylko internetowe interfejsy API powinny analizować tokeny dostępu (ponieważ są to te, które definiują format i ustawiają klucze szyfrowania). |

## <a name="end-user-experience"></a>Środowisko użytkownika końcowego

|   |   |
|---|---|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | [Zapoznaj się z interfejsem zgody](application-consent-experience.md) i skonfiguruj fragmenty monitu o zgodę aplikacji, aby użytkownicy końcowi i administratorzy mieli wystarczającą ilość informacji, aby określić, czy ufają Twojej aplikacji. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Zminimalizuj liczbę razy, kiedy użytkownik musi wprowadzić poświadczenia logowania podczas korzystania z aplikacji, próbując dyskretnego uwierzytelniania (cichego pozyskiwania tokenów) przed przepływami interaktywnymi. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Nie używaj "prompt=consent" dla każdego logowania. Użyj prompt=consent tylko wtedy, gdy stwierdzisz, że musisz poprosić o zgodę na dodatkowe uprawnienia (na przykład, jeśli zmienisz wymagane uprawnienia aplikacji). |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | W stosownych przypadkach wzbogać aplikację o dane użytkownika. Korzystanie z [interfejsu API programu Microsoft Graph](https://developer.microsoft.com/graph) jest łatwym sposobem, aby to zrobić. Narzędzie [Eksplorator wykresu,](https://developer.microsoft.com/graph/graph-explorer) które może pomóc w rozpoczęciu pracy. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Zarejestruj pełny zestaw uprawnień, których wymaga aplikacja, aby administratorzy mogli łatwo udzielić zgody dzierżawie. Użyj [przyrostowej zgody w](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) czasie wykonywania, aby pomóc użytkownikom zrozumieć, dlaczego aplikacja żąda uprawnień, które mogą dotyczyć lub mylić użytkowników, gdy jest to wymagane przy pierwszym uruchomieniu. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Zaimplementuj [czyste środowisko pojedynczego wylogowania](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Jest to wymóg prywatności i bezpieczeństwa i zapewnia dobre wrażenia użytkownika. |

## <a name="testing"></a>Testowanie

|   |   |
|---|---|
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Przetestuj [zasady dostępu warunkowego,](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) które mogą mieć wpływ na zdolność użytkowników do korzystania z aplikacji. |
| ![pole wyboru](./media/active-directory-integration-checklist/checkbox-two.svg) | Przetestuj aplikację przy pomocy wszystkich możliwych kont, które mają być obsługiwane (na przykład kont służbowych, osobistych kont Microsoft, kont podrzędnych i kont suwerennych). |

## <a name="additional-resources"></a>Zasoby dodatkowe

Uzyskaj szczegółowe informacje na temat wersji 2.0:

* [Platforma tożsamości firmy Microsoft (omówienie wersji 2.0)](v2-overview.md)
* [Odwołanie do protokołów platformy tożsamości firmy Microsoft](active-directory-v2-protocols.md)
* [Dokumentacja tokenów dostępu](access-tokens.md)
* [Dokumentacja tokenów identyfikatorów](id-tokens.md)
* [Odwołanie do bibliotek uwierzytelniania](reference-v2-libraries.md)
* [Uprawnienia i zgoda na platformie tożsamości firmy Microsoft](v2-permissions-and-consent.md)
* [Interfejs API programu Microsoft Graph](https://developer.microsoft.com/graph)
