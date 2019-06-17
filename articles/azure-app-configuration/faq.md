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
ms.openlocfilehash: e321c0b473b110597b5b87a6e67666737116daa2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66393348"
---
# <a name="azure-app-configuration-faq"></a>Konfiguracja aplikacji platformy Azure — często zadawane pytania

W tym artykule opisano często zadawane pytania dotyczące konfiguracji aplikacji platformy Azure.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Czym różni się usługa App Configuration od usługi Azure Key Vault?

Konfiguracja aplikacji jest przeznaczony dla różnych zestaw przypadków użycia: ułatwia deweloperom Zarządzanie ustawieniami aplikacji i kontrolować dostępności funkcji. Ma on uprościć wykonywanie wielu zadań Praca z danymi złożonej konfiguracji.

Obsługuje konfigurację aplikacji:

- Hierarchiczne przestrzenie nazw
- Etykietowania
- Rozbudowane zapytania
- Pobieranie usługi Batch
- Operacje zarządzania specjalne
- Interfejs użytkownika funkcji zarządzania

Konfiguracja aplikacji jest wzajemnie dopełniającego się do usługi Key Vault, a dwa powinny być używane równolegle w większości wdrożeń aplikacji.

## <a name="should-i-store-secrets-in-app-configuration"></a>Czy lepiej przechowywać klucze tajne, w konfiguracji aplikacji?

Mimo że zabezpiecza ze wzmocnionymi zabezpieczeniami konfiguracji aplikacji usługi Key Vault jest nadal najlepsze miejsce do przechowywania wpisów tajnych aplikacji. Key Vault oferuje szyfrowanie na poziomie sprzętu, zasady dostępu szczegółowej i operacji zarządzania, takich jak rotacja certyfikatu.

## <a name="does-app-configuration-encrypt-my-data"></a>Czy konfiguracja aplikacji szyfruje Moje dane?

Tak. Konfiguracja aplikacji szyfruje wszystkie wartości klucza, który przechowuje, a szyfrowanie komunikacji sieciowej. Nazwy kluczy są używane jako indeksy do pobierania danych konfiguracji, a nie są szyfrowane.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Jak należy przechowywać konfiguracje dla wielu środowisk (test, tymczasowego, produkcyjnego i tak dalej)

Obecnie można kontrolować, kto ma dostęp do konfiguracji aplikacji na poziomie-store. Użyj oddzielnego magazynu dla każdego środowiska, który wymaga różnych uprawnień. Takie podejście zapewnia najlepsze izolacji zabezpieczeń.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Jakie są zalecane sposoby korzystania z konfiguracji aplikacji?

Zobacz [najlepsze praktyki](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Ile kosztuje konfiguracji aplikacji

Usługa jest dostępna bezpłatnie do użycia w ramach publicznej wersji zapoznawczej.

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Jak zgłosić problem lub podać sugestię?

Możesz docierać do nas bezpośrednio na [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Kolejne kroki

* [O konfiguracji aplikacji platformy Azure](./overview.md)
