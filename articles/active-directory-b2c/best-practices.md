---
title: Najważniejsze wskazówki dotyczące usługi Azure AD B2C
titleSuffix: Azure AD B2C
description: Zalecenia i najlepsze rozwiązania, które należy wziąć pod uwagę podczas pracy z usługą Azure Active Directory B2C (Azure AD B2C).
services: active-directory-b2c
author: vigunase
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: a76852a6e3cc5ffcdfcac62ce29fe47c97af3df1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136163"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Zalecenia i najlepsze rozwiązania dotyczące usługi Azure Active Directory B2C

Poniższe najlepsze rozwiązania i zalecenia obejmują niektóre z podstawowych aspektów integracji usługi Azure Active Directory (Azure AD) B2C z istniejącymi lub nowymi środowiskami aplikacji.

## <a name="fundamentals"></a>Podstawy

|  |  |
|--|--|
| Wybieranie przepływów użytkowników w większości scenariuszy | Struktura środowiska tożsamości usługi Azure AD B2C jest podstawową siłą usługi. Zasady w pełni opisują środowisko tożsamości, takie jak rejestracja, logowanie się lub edytowanie profilu. Aby ułatwić konfigurowanie najbardziej typowych zadań tożsamości, portal usługi Azure AD B2C zawiera wstępnie zdefiniowane, konfigurowalne zasady o nazwie przepływy użytkowników. Dzięki przepływom użytkownika możesz tworzyć wspaniałe wrażenia użytkownika w ciągu kilku minut, za pomocą zaledwie kilku kliknięć. [Dowiedz się, kiedy używać przepływów użytkowników a zasad niestandardowych](custom-policy-overview.md#comparing-user-flows-and-custom-policies).|
| Rejestracje aplikacji | Każda aplikacja (web, native) i interfejs API, który jest zabezpieczony musi być zarejestrowany w usłudze Azure AD B2C. Jeśli aplikacja ma zarówno sieci web i natywnej wersji systemu iOS i Android, można zarejestrować je jako jedną aplikację w usłudze Azure AD B2C z tym samym identyfikatorem klienta. Dowiedz się, jak [zarejestrować aplikacje OIDC, SAML, web i natywne.](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=applications) Dowiedz się więcej o [typach aplikacji, które mogą być używane w usłudze Azure AD B2C.](https://docs.microsoft.com/azure/active-directory-b2c/application-types) |
| Przechodzenie do miesięcznych aktywnych użytkowników | Usługa Azure AD B2C została przeniesiona z miesięcznych aktywnych uwierzytelniania do miesięcznych aktywnych użytkowników (MAU) rozliczeń. Większość klientów znajdzie ten model opłacalne. [Dowiedz się więcej o miesięcznych płatnościach dla aktywnych użytkowników](https://azure.microsoft.com/updates/mau-billing/). |

## <a name="planning-and-design"></a>Planowanie i projektowanie

Zdefiniuj architekturę aplikacji i usług, bieżące systemy zapasów i zaplanuj migrację na usługę Azure AD B2C.

|  |  |
|--|--|
| Architekt kompleksowego rozwiązania | Uwzględnij wszystkie zależności aplikacji podczas planowania integracji usługi Azure AD B2C. Należy wziąć pod uwagę wszystkie usługi i produkty, które są obecnie w środowisku lub które mogą wymagać dodania do rozwiązania, na przykład usługi Azure Functions, systemy zarządzania relacjami z klientami (CRM), brama usługi Azure API Management i usługi magazynu. Weź pod uwagę bezpieczeństwo i skalowalność wszystkich usług. |
| Dokumentowanie doświadczeń użytkowników | Szczegółowo wszystkie podróże użytkowników, które klienci mogą wystąpić w aplikacji. Uwzględnij każdy ekran i wszelkie przepływy rozgałęziania, które mogą wystąpić podczas interakcji z aspektami tożsamości i profilu aplikacji. Uwzględnij użyteczność, dostępność i lokalizację w planowaniu. |
| Wybierz odpowiedni protokół uwierzytelniania |  Aby zapoznać się z podziałem różnych scenariuszy aplikacji i ich zalecanych przepływów uwierzytelniania, zobacz [Scenariusze i obsługiwane przepływy uwierzytelniania](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows). |
| Pilotaż kompleksowego środowiska weryfikacji koncepcji (POC) | Zacznij od naszych [przykładów kodu firmy Microsoft](code-samples.md) i [przykładów społeczności.](https://github.com/azure-ad-b2c/samples) |
| Tworzenie planu migracji |Planowanie z wyprzedzeniem może sprawić, że migracja przebiegnie płynniej. Dowiedz się więcej o [migracji użytkowników](user-migration.md).|
| Użyteczność a bezpieczeństwo | Rozwiązanie musi zapewniać właściwą równowagę między użytecznością aplikacji a dopuszczalnym poziomem ryzyka w organizacji. |
| Przenoszenie lokalnych zależności do chmury | Aby zapewnić odporne rozwiązanie, należy rozważyć przeniesienie istniejących zależności aplikacji do chmury. |
| Migrowanie istniejących aplikacji w celu b2clogin.com | Wycofanie login.microsoftonline.com weszło w życie dla wszystkich dzierżaw usługi Azure AD B2C w dniu 04 grudnia 2020 r. [Dowiedz się więcej](b2clogin.md). |

## <a name="implementation"></a>Wdrażanie

Na etapie wdrażania należy wziąć pod uwagę następujące zalecenia.

|  |  |
|--|--|
| Edytowanie zasad niestandardowych za pomocą rozszerzenia usługi Azure AD B2C dla programu Visual Studio Code | Pobierz kod programu Visual Studio i to rozszerzenie utworzone przez społeczność [z portalu Visual Studio Code Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c). Chociaż nie jest to oficjalny produkt firmy Microsoft, rozszerzenie usługi Azure AD B2C dla programu Visual Studio Code zawiera kilka funkcji, które ułatwiają pracę z zasadami niestandardowymi. |
| Dowiedz się, jak rozwiązywać problemy z usługą Azure AD B2C | Dowiedz się, jak [rozwiązywać problemy z zasadami niestandardowymi](https://docs.microsoft.com/azure/active-directory-b2c/troubleshoot-custom-policies?tabs=applications) podczas opracowywania. Dowiedz się, jak wygląda normalny przepływ uwierzytelniania i użyj narzędzi do wykrywania anomalii i błędów. Na przykład użyj [usługi Application Insights](troubleshoot-with-application-insights.md) do przeglądania dzienników danych wyjściowych podróży użytkowników. |
| Wykorzystaj naszą bibliotekę sprawdzonych wzorców zasad niestandardowych | Znajdź przykłady kilku [ulepszonych procesów](https://github.com/azure-ad-b2c/samples) pozyskiwania użytkowników tożsamości i dostępu klientów usługi Azure AD B2C (CIAM). |


## <a name="testing"></a>Testowanie

Testowanie i automatyzacja implementacji usługi Azure AD B2C.

|  |  |
|--|--|
| Uwzględnianie globalnego ruchu | Użyj źródeł ruchu z różnych adresów globalnych, aby przetestować wymagania dotyczące wydajności i lokalizacji. Upewnij się, że wszystkie htmls, CSS i zależności mogą spełnić Twoje potrzeby w zakresie wydajności. |
| Testowanie funkcjonalne i interfejsu użytkownika | Przetestuj przepływy użytkownika end-to-end. Dodaj syntetycznych testów co kilka minut za pomocą Selenu, VS Web Test, itp. |
| Testowanie pióra | Przed rozpoczęciem pracy z rozwiązaniem należy wykonać ćwiczenia testów penetracyjnych, aby sprawdzić, czy wszystkie składniki są bezpieczne, w tym zależności innych firm. Sprawdź, czy zabezpieczenia interfejsów API zostały zabezpieczone za pomocą tokenów dostępu i użycia odpowiedniego protokołu uwierzytelniania dla scenariusza aplikacji. Dowiedz się więcej o [testach penetracyjnych](https://docs.microsoft.com/azure/security/fundamentals/pen-testing) i [ujednoliconych regułach testowania penetracji w chmurze firmy Microsoft.](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) |
| Testowanie A/B | Lot nowe funkcje z małym, losowy zestaw użytkowników przed wprowadzeniem do całej populacji. Po włączeniu języka JavaScript w usłudze Azure AD B2C można zintegrować się z narzędziami testowymi A/B, takimi jak Optymalizator, Przejrzystość i inne. |
| Testowanie obciążeniowe | Usługa Azure AD B2C można skalować, ale aplikacja może skalować tylko wtedy, gdy wszystkie jego zależności można skalować. Sprawdź obciążenia interfejsów API i cdn. |
| Ograniczanie przepływności |  Usługa Azure AD B2C ogranicza ruch, jeśli zbyt wiele żądań są wysyłane z tego samego źródła w krótkim okresie czasu. Użyj kilku źródeł ruchu podczas testowania `AADB2C90229` obciążenia i obsługiwać kod błędu bezpiecznie w aplikacjach. |
| Automatyzacja | Użyj potoków ciągłej integracji i dostarczania (CI/CD), aby zautomatyzować testowanie i wdrażanie, na przykład [azure devops](deploy-custom-policies-devops.md). |

## <a name="operations"></a>Operacje

Zarządzanie środowiskiem usługi Azure AD B2C.

|  |  |
|--|--|
| Tworzenie wielu środowisk | Aby ułatwić operacje i wdrożenie, należy utworzyć oddzielne środowiska do programowania, testowania, przedprodukcji i produkcji. Tworzenie dzierżaw usługi Azure AD B2C dla każdego. |
| Używanie kontroli wersji dla zasad niestandardowych | Należy rozważyć użycie usługi GitHub, usługi Azure Repos lub innego systemu kontroli wersji w chmurze dla zasad niestandardowych usługi Azure AD B2C. |
| Za pomocą interfejsu API programu Microsoft Graph można zautomatyzować zarządzanie dzierżawcami usługi B2C | Interfejsy API programu Microsoft Graph:<br/>Zarządzanie [platformą środowiska tożsamości](https://docs.microsoft.com/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta) (zasady niestandardowe)<br/>[Klucze](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset?view=graph-rest-beta)<br/>[User Flows (Przepływy użytkowników)](https://docs.microsoft.com/graph/api/resources/identityuserflow?view=graph-rest-beta) |
| Integracja z platformą Azure DevOps | [Potok ciągłej integracji/ciągłego wdrażania](deploy-custom-policies-devops.md) sprawia, że przenoszenie kodu między różnymi środowiskami jest łatwe i zapewnia gotowość do produkcji przez cały czas.   |
| Integracja z usługą Azure Monitor | [Zdarzenia dziennika inspekcji](view-audit-logs.md) są zachowywane tylko przez siedem dni. [Integracja z usługą Azure Monitor](azure-monitor.md) w celu zachowania dzienników do długotrwałego użytku lub integracja z narzędziami do zarządzania informacjami i zdarzeniami (SIEM) innych firm w celu uzyskania szczegółowych informacji o środowisku. |
| Konfigurowanie aktywnego alertów i monitorowania | [Śledzenie zachowania użytkowników](active-directory-b2c-custom-guide-eventlogger-appins.md) w usłudze Azure AD B2C przy użyciu usługi Application Insights. |


## <a name="support-and-status-updates"></a>Aktualizacje pomocy technicznej i stanu

Bądź na bieżąco ze stanem usługi i znajdź opcje pomocy technicznej.

|  |  |
|--|--|
| [Aktualizacje usług](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  Bądź na bieżąco z aktualizacjami i ogłoszeniami dotyczącymi usług Azure AD B2C. |
| [Pomoc techniczna firmy Microsoft](support-options.md) | Złóż żądanie pomocy technicznej dotyczące problemów technicznych usługi Azure AD B2C. Pomoc techniczna w zakresie rozliczeń i zarządzania subskrypcjami jest świadczona bezpłatnie. |
| [Stan platformy Azure](https://status.azure.com/status) | Wyświetl bieżący stan kondycji wszystkich usług platformy Azure. |