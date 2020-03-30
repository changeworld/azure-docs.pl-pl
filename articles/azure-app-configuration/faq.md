---
title: Często zadawane pytania dotyczące konfiguracji aplikacji platformy Azure
description: Często zadawane pytania dotyczące konfiguracji aplikacji platformy Azure
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 25187fd055f40e8b32d840ead2a9c54882446b88
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348786"
---
# <a name="azure-app-configuration-faq"></a>Często zadawane pytania dotyczące konfiguracji aplikacji platformy Azure

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące konfiguracji aplikacji platformy Azure.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Czym różni się konfiguracja aplikacji od usługi Azure Key Vault?

Konfiguracja aplikacji pomaga deweloperom zarządzać ustawieniami aplikacji i kontrolować dostępność funkcji. Jego celem jest uproszczenie wielu zadań związanych z pracą ze złożonymi danymi konfiguracyjnymi.

Konfiguracja aplikacji obsługuje:

- Hierarchiczne przestrzenie nazw
- Etykietowania
- Obszerne zapytania
- Pobieranie partii
- Wyspecjalizowane operacje zarządzania
- Interfejs użytkownika zarządzania funkcjami

Konfiguracja aplikacji uzupełnia usługi Key Vault, a dwa powinny być używane obok siebie w większości wdrożeń aplikacji.

## <a name="should-i-store-secrets-in-app-configuration"></a>Czy należy przechowywać wpisy tajne w konfiguracji aplikacji?

Chociaż konfiguracja aplikacji zapewnia wzmocnione zabezpieczenia, usługa Key Vault jest nadal najlepszym miejscem do przechowywania wpisów tajnych aplikacji. Usługa Key Vault zapewnia szyfrowanie na poziomie sprzętu, szczegółowe zasady dostępu i operacje zarządzania, takie jak rotacja certyfikatów.

Można utworzyć wartości konfiguracji aplikacji, które odwołują się do wpisów tajnych przechowywanych w magazynie kluczy. Aby uzyskać więcej informacji, zobacz [Używanie odwołań do magazynu kluczy w aplikacji ASP.NET Core](./use-key-vault-references-dotnet-core.md).

## <a name="does-app-configuration-encrypt-my-data"></a>Czy konfiguracja aplikacji szyfruje moje dane?

Tak. Konfiguracja aplikacji szyfruje wszystkie wartości kluczy, które posiada, i szyfruje komunikację sieciową. Nazwy kluczy i etykiety są używane jako indeksy do pobierania danych konfiguracji i nie są szyfrowane.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>Czym różni się konfiguracja aplikacji od ustawień usługi Azure App Service?

Usługa Azure App Service umożliwia definiowanie ustawień aplikacji dla każdego wystąpienia usługi App Service. Te ustawienia są przekazywane jako zmienne środowiskowe do kodu aplikacji. Można skojarzyć ustawienie z określonym miejscem wdrożenia, jeśli chcesz. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ustawień aplikacji](/azure/app-service/configure-common#configure-app-settings).

Z drugiej strony konfiguracja aplikacji platformy Azure umożliwia definiowanie ustawień, które mogą być współużytkowane przez wiele aplikacji. Obejmuje to aplikacje działające w usłudze App Service, a także inne platformy. Kod aplikacji uzyskuje dostęp do tych ustawień za pośrednictwem dostawców konfiguracji dla platformy .NET i Java za pośrednictwem narzędzia Azure SDK lub bezpośrednio za pośrednictwem interfejsów API REST.

Można również importować i eksportować ustawienia między usługą App Service a konfiguracją aplikacji. Ta funkcja umożliwia szybkie skonfigurowanie nowego magazynu konfiguracji aplikacji na podstawie istniejących ustawień usługi App Service. Konfigurację można również udostępnić istniejącej aplikacji, która opiera się na ustawieniach usługi App Service.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Czy istnieją ograniczenia rozmiaru kluczy i wartości przechowywane w konfiguracji aplikacji?

Istnieje limit 10 KB dla pojedynczego elementu klucza wartości.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Jak przechowywać konfiguracje dla wielu środowisk (test, przemieszczania, produkcji i tak dalej)?

Możesz kontrolować, kto może uzyskać dostęp do konfiguracji aplikacji na poziomie sklepu. Użyj oddzielnego magazynu dla każdego środowiska, które wymaga różnych uprawnień. Takie podejście zapewnia najlepszą izolację zabezpieczeń.

Jeśli nie potrzebujesz izolacji zabezpieczeń między środowiskami, można użyć etykiet do rozróżniania wartości konfiguracji. [Użyj etykiet, aby włączyć różne konfiguracje dla różnych środowisk](./howto-labels-aspnet-core.md) zawiera pełny przykład.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Jakie są zalecane sposoby korzystania z konfiguracji aplikacji?

Zobacz [najlepsze rozwiązania](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Ile kosztuje konfiguracja aplikacji?

Istnieją dwie warstwy cenowe:

- Warstwa Bezpłatna
- Warstwa standardowa.

Jeśli sklep został utworzony przed wprowadzeniem warstwy Standardowa, zostanie on automatycznie przeniesiony do warstwy Bezpłatna po ogólnej dostępności. Możesz uaktualnić do warstwy Standardowej lub pozostać w warstwie Bezpłatna.

Nie można obniżyć sklepu z warstwy Standardowa do warstwy bezpłatna. Można utworzyć nowy magazyn w warstwie Bezpłatna, a następnie zaimportować dane konfiguracji do tego magazynu.

## <a name="which-app-configuration-tier-should-i-use"></a>Której warstwy konfiguracji aplikacji należy używać?

Obie warstwy konfiguracji aplikacji oferują podstawowe funkcje, w tym ustawienia konfiguracji, flagi funkcji, odwołania do usługi Key Vault, podstawowe operacje zarządzania, metryki i dzienniki.

Poniżej przedstawiono zagadnienia dotyczące wyboru warstwy.

- **Zasoby na subskrypcję:** Zasób składa się z magazynu konfiguracji z jedną konfiguracją. Każda subskrypcja jest ograniczona do jednego magazynu konfiguracji w warstwie bezpłatnej. Subskrypcje mogą mieć nieograniczoną liczbę magazynów konfiguracji w warstwie standardowej.
- **Magazyn na zasób:** W warstwie bezpłatnej każdy magazyn konfiguracji jest ograniczony do 10 MB pamięci masowej. W warstwie standardowej każdy magazyn konfiguracji może używać do 1 GB miejsca.
- **Historia kluczy:** Konfiguracja aplikacji przechowuje historię wszystkich zmian wprowadzonych do kluczy. W warstwie bezpłatnej ta historia jest przechowywana przez siedem dni. W warstwie standardowej ta historia jest przechowywana przez 30 dni.
- **Żądania dziennie:** sklepy z bezpłatnymi warstwami są ograniczone do 1000 żądań dziennie. Gdy sklep osiągnie 1000 żądań, zwróci kod stanu HTTP 429 dla wszystkich żądań do północy UTC.

    W przypadku sklepów klasy podstawowej pierwsze 200 000 żądań każdego dnia jest obciążonych opłatą dzienną. Dodatkowe żądania są rozliczane jako nadpłata.

- **Umowa dotyczącą poziomu usług:** warstwa standardowa ma umowę SLA o dostępności 99,9%. Warstwa bezpłatna nie ma umowy SLA.
- **Funkcje zabezpieczeń:** Obie warstwy obejmują podstawowe funkcje zabezpieczeń, w tym szyfrowanie za pomocą kluczy zarządzanych przez firmę Microsoft, uwierzytelnianie za pośrednictwem usługi HMAC lub usługi Azure Active Directory, obsługę RBAC i tożsamość zarządzaną. Warstwa Standard oferuje bardziej zaawansowane funkcje zabezpieczeń, w tym obsługę łącza prywatnego i szyfrowanie za pomocą kluczy zarządzanych przez klienta.
- **Koszt:** sklepy klasy podstawowej mają dzienną opłatę za użycie. Istnieje również opłata za nadpłatę za żądania poza codzienną alokacją. Korzystanie z bezpłatnego sklepu warstwowego nie jest bezpłatne.

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>Czy mogę uaktualnić sklep z warstwy Bezpłatna do warstwy Standardowa? Czy mogę obniżyć wersję sklepu z warstwy Standardowa do warstwy Bezpłatna?

W każdej chwili można uaktualnić warstwę Bezpłatną do warstwy Standardowa.

Nie można obniżyć sklepu z warstwy Standardowa do warstwy bezpłatna. Można utworzyć nowy magazyn w warstwie Bezpłatna, a następnie [zaimportować dane konfiguracyjne do tego magazynu](howto-import-export-data.md).

## <a name="are-there-any-limits-on-the-number-of-requests-made-to-app-configuration"></a>Czy istnieją jakieś ograniczenia dotyczące liczby żądań kierowanych do konfiguracji aplikacji?

Magazyny konfiguracji w warstwie Bezpłatna są ograniczone do 1000 żądań dziennie. Magazyny konfiguracji w warstwie Standardowa mogą wystąpić tymczasowe ograniczanie przepustowości, gdy szybkość żądania przekracza 20 000 żądań na godzinę.

Gdy sklep osiągnie limit, zwróci kod stanu HTTP 429 dla wszystkich żądań złożonych do upływu okresu. Nagłówek `retry-after-ms` w odpowiedzi daje sugerowany czas oczekiwania (w milisekundach) przed ponowieniem próby żądania.

Jeśli aplikacja regularnie występują kod stanu HTTP 429 odpowiedzi, należy rozważyć przeprojektowanie go, aby zmniejszyć liczbę żądań. Aby uzyskać więcej informacji, zobacz [Zmniejszanie żądań kierowanych do konfiguracji aplikacji](./howto-best-practices.md#reduce-requests-made-to-app-configuration)

## <a name="my-application-receives-http-status-code-429-responses-why"></a>Moja aplikacja otrzymuje kod stanu HTTP 429 odpowiedzi. Dlaczego?

W tych okolicznościach otrzymasz odpowiedź z kodem stanu HTTP 429:

* Przekroczenie dziennego limitu żądań dla sklepu w warstwie Bezpłatna.
* Tymczasowe ograniczanie przepustowości z powodu wysokiej stawki żądań dla sklepu w warstwie Standardowa.
* Nadmierne użycie przepustowości.
* Próba utworzenia lub zmodyfikowania klucza po przekroczeniu oferty magazynu.

Sprawdź treść odpowiedzi 429 z konkretnego powodu, dla którego żądanie nie powiodło się.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Jak mogę otrzymywać anonse dotyczące nowych wersji i innych informacji związanych z konfiguracją aplikacji?

Zapisz się do naszego [repozytorium ogłoszeń GitHub](https://github.com/Azure/AppConfiguration-Announcements).

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Jak zgłosić problem lub podać sugestię?

Możesz skontaktować się z nami bezpośrednio na [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Następne kroki

* [Usługa Azure App Configuration — informacje](./overview.md)
