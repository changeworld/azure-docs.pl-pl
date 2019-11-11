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
ms.openlocfilehash: 8d286cbab33a1fb6a2d2a2cb70caed11b21af735
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904090"
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

Można utworzyć wartości konfiguracji aplikacji, które odwołują się do wpisów tajnych przechowywanych w Key Vault. Aby uzyskać więcej informacji, zobacz [Korzystanie z odwołań Key Vault w aplikacji ASP.NET Core](./use-key-vault-references-dotnet-core.md).

## <a name="does-app-configuration-encrypt-my-data"></a>Czy konfiguracja aplikacji szyfruje moje dane?

Tak. Konfiguracja aplikacji szyfruje wszystkie przechowywane wartości klucza i szyfruje komunikację sieciową. Nazwy kluczy są używane jako indeksy do pobierania danych konfiguracji i nie są szyfrowane.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>Czym różni się konfiguracja aplikacji od ustawień Azure App Service?

Azure App Service umożliwia zdefiniowanie ustawień aplikacji dla każdego wystąpienia App Service. Te ustawienia są przesyłane jako zmienne środowiskowe do kodu aplikacji. W razie potrzeby można skojarzyć ustawienie z określonym miejscem wdrożenia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie ustawień aplikacji](/azure/app-service/configure-common#configure-app-settings).

W przeciwieństwie do konfiguracji aplikacji platformy Azure można definiować ustawienia, które mogą być współużytkowane przez wiele aplikacji, w tym aplikacje działające w App Service. Te ustawienia są dostępne w kodzie aplikacji za pośrednictwem dostawców konfiguracji dla platform .NET i Java za pomocą zestawu Azure SDK lub bezpośrednio za pośrednictwem interfejsów API REST.

Można również importować i eksportować ustawienia między App Service i konfiguracji aplikacji. Dzięki temu można szybko skonfigurować nowy magazyn konfiguracji aplikacji na podstawie istniejących ustawień App Service lub łatwo udostępnić konfigurację istniejącej aplikacji, która opiera się na App Service ustawieniach.

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
