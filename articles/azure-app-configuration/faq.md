---
title: Konfiguracja aplikacji platformy Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące konfiguracji aplikacji platformy Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 3181609bf34a04de4e31b73429f9bc5fa3fe3408
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411899"
---
# <a name="azure-app-configuration-faq"></a>Konfiguracja aplikacji platformy Azure — często zadawane pytania

Ten artykuł adresy częste pytania dotyczące konfiguracji aplikacji usługi Azure.

## <a name="how-is-app-configuration-different-from-key-vault"></a>Czym różni się konfiguracji aplikacji z usługi Key Vault?

Konfiguracja aplikacji jest przeznaczony dla różnych zestaw przypadków użycia: ułatwia deweloperom Zarządzanie ustawieniami aplikacji i kontrolować dostępności funkcji. Ma on uprościć wykonywanie wielu zadań Praca z danymi złożonej konfiguracji. Obsługuje ona hierarchicznej przestrzeni nazw, etykietowania, zaawansowanych kwerend, pobierania partii i operacje zarządzania wyspecjalizowanych i interfejsów użytkownika. Konfiguracja aplikacji jest wzajemnie dopełniającego się do usługi Key Vault i dwa powinny być używane równolegle w większości wdrożeń aplikacji.

## <a name="should-i-store-secrets-in-app-configuration"></a>Czy lepiej przechowywać klucze tajne, w konfiguracji aplikacji?

Konfiguracja aplikacji zapewnia zabezpieczeń ze wzmocnionymi zabezpieczeniami, jednocześnie usługi Key Vault jest nadal najlepsze miejsce do przechowywania wpisów tajnych aplikacji. Zapewnia szyfrowanie na poziomie sprzętu, zasady dostępu szczegółowej i operacji zarządzania, takich jak rotacja certyfikatu.

## <a name="does-app-configuration-encrypt-my-data"></a>Czy konfiguracja aplikacji szyfruje Moje dane?

Tak. Konfiguracja aplikacji szyfruje wszystkie wartości klucza, jego przechowuje i komunikacja sieciowa. Nazwy są używane jako indeksy do pobierania danych konfiguracji i kluczy nie są szyfrowane.

## <a name="does-app-configuration-support-azure-virtual-network-vnet"></a>Konfiguracja aplikacji obsługuje usługa Azure Virtual Network (VNET)?

Jeszcze nie. Obsługa sieci Wirtualnej jest planowana za usługi ogólnie dostępne.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Jak należy przechowywać konfiguracje dla wielu środowisk (test, tymczasowego, produkcyjnego i tak dalej)

Obecnie można kontrolować, kto ma dostęp do konfiguracji aplikacji na poziomie-store. Należy użyć oddzielnego magazynu dla każdego środowiska, który wymaga różnych uprawnień. Takie podejście zapewnia najlepsze izolacji zabezpieczeń.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Jakie są zalecane sposoby korzystania z konfiguracji aplikacji?

Zobacz [najlepsze praktyki](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Ile kosztuje konfiguracji aplikacji

Usługa jest dostępna bezpłatnie do użycia w ramach publicznej wersji zapoznawczej.

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Jak zgłosić problem lub podać sugestię?

Możesz docierać do nas bezpośrednio na [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Kolejne kroki

* [O konfiguracji aplikacji platformy Azure](./overview.md)
