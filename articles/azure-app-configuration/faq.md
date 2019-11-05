---
title: Konfiguracja aplikacji platformy Azure — często zadawane pytania | Microsoft Docs
description: Często zadawane pytania dotyczące konfiguracji aplikacji platformy Azure
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: dbfb6a1c4c53b1bd255560e688d3dc0cf3835a3a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469639"
---
# <a name="azure-app-configuration-faq"></a>Konfiguracja aplikacji platformy Azure — często zadawane pytania

W tym artykule opisano często zadawane pytania dotyczące konfiguracji aplikacji platformy Azure.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Czym różni się usługa App Configuration od usługi Azure Key Vault?

Konfiguracja aplikacji została zaprojektowana dla różnych przypadków użycia: ułatwia deweloperom zarządzanie ustawieniami aplikacji i dostępnością funkcji kontroli. Celem jest uproszczenie wielu zadań związanych z pracą ze złożonymi danymi konfiguracyjnymi.

Konfiguracja aplikacji obsługuje:

- Hierarchiczne przestrzenie nazw
- etykietowania
- Obszerne zapytania
- Pobieranie wsadowe
- Wyspecjalizowane operacje zarządzania
- Interfejs użytkownika zarządzania funkcjami

Konfiguracja aplikacji jest uzupełniana do Key Vault, a dwa z nich powinny być używane obok większości wdrożeń aplikacji.

## <a name="should-i-store-secrets-in-app-configuration"></a>Czy należy przechowywać wpisy tajne w konfiguracji aplikacji?

Mimo że konfiguracja aplikacji zapewnia zabezpieczenia z ograniczeniami, Key Vault jest nadal najlepszym miejscem do przechowywania wpisów tajnych aplikacji. Key Vault zapewnia szyfrowanie na poziomie sprzętu, szczegółowe zasady dostępu i operacje zarządzania, takie jak rotacja certyfikatów.

## <a name="does-app-configuration-encrypt-my-data"></a>Czy konfiguracja aplikacji szyfruje moje dane?

Tak. Konfiguracja aplikacji szyfruje wszystkie przechowywane wartości klucza i szyfruje komunikację sieciową. Nazwy kluczy są używane jako indeksy do pobierania danych konfiguracji i nie są szyfrowane.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Czy istnieją jakieś ograniczenia rozmiaru kluczy i wartości przechowywanych w konfiguracji aplikacji?

Istnieje limit 10 KB dla pojedynczego elementu klucz-wartość.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Jak należy przechowywać konfiguracje dla wielu środowisk (testów, przemieszczania, produkcji itp.)?

Obecnie można kontrolować, kto ma dostęp do konfiguracji aplikacji na poziomie magazynu. Użyj oddzielnego magazynu dla każdego środowiska, które wymaga innych uprawnień. Takie podejście zapewnia najlepszą izolację zabezpieczeń.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Jakie są zalecane metody korzystania z konfiguracji aplikacji?

Zobacz [najlepsze rozwiązania](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Ile jest koszt konfiguracji aplikacji?

Usługa jest bezpłatna do użycia w publicznej wersji zapoznawczej.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Jak mogę otrzymywać anonse dotyczące nowych wersji i innych informacji związanych z konfiguracją aplikacji?

Subskrybowanie [repozytorium anonsów usługi GitHub](https://github.com/Azure/AppConfiguration-Announcements).

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Jak zgłosić problem lub przekazać sugestię?

Możesz skontaktować się bezpośrednio z usługą [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Następne kroki

* [Informacje o konfiguracji aplikacji platformy Azure](./overview.md)
