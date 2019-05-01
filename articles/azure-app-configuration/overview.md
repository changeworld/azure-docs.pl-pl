---
title: Co to jest Konfiguracja aplikacji platformy Azure? | Microsoft Docs
description: Omówienie usługi Azure App Configuration.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: f2019dd5a810a9e9099fd9f9e171fd5af21d1dc5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64715068"
---
# <a name="what-is-azure-app-configuration"></a>Co to jest Konfiguracja aplikacji platformy Azure?

Konfiguracja aplikacji platformy Azure udostępnia usługę umożliwia centralne zarządzanie ustawieniami aplikacji. Nowoczesnych programów, szczególnie programy działające w chmurze, zwykle mają wiele składników, które są rozmieszczone w rodzaju. Posiadanie ustawień konfiguracji w ramach tych składników może powodować występowanie błędów podczas wdrażania aplikacji, których diagnozowanie będzie bardzo skomplikowane. Używać konfiguracji aplikacji do przechowywania wszystkich ustawień dla aplikacji i zabezpieczanie ich dostęp w jednym miejscu.

Konfiguracja aplikacji jest bezpłatna podczas korzystania z wersji zapoznawczej. Jeśli chcesz spróbować go [zarejestrować](https://aka.ms/azconfig/register) skorzystania z wersji zapoznawczej.

## <a name="why-use-app-configuration"></a>Dlaczego warto używać konfiguracji aplikacji?

Aplikacje oparte na chmurze są często uruchamiane na wielu maszynach wirtualnych lub kontenerach w wielu regionach i korzystają z wielu usług zewnętrznych. Tworzenie aplikacji rozproszonych, które jest niezawodne i skalowalne jest trudne. 

Różne metody programowania pomogą przeciwdziałania zwiększa złożoność tworzenia aplikacji. Na przykład aplikacji 12-składnikowych opisano wiele dobrze przetestowanych wzorce architektury i poznasz najlepsze rozwiązania dotyczące użycia z aplikacjami w chmurze. Jednym z kluczowych zaleceń z tego przewodnika jest oddzielenia konfiguracji od kodu. W takim przypadku ustawienia konfiguracji aplikacji należy przechowywane na zewnątrz do jego pliku wykonywalnego i odczytywane z źródła zewnętrznego lub jego środowiska uruchomieniowego.

Podczas każdej aplikacji można skorzystać z konfiguracji aplikacji, poniższe przykłady są typy aplikacji, które korzystają z wykorzystać jego możliwości:

* Mikrousługi oparty na usłudze Azure Kubernetes Service, Azure Service Fabric lub innych konteneryzowanych aplikacji wdrożonych w przynajmniej jednej lokalizacji geograficznych
* Bez użycia serwera aplikacji, które obejmują usługi Azure Functions lub inne aplikacje oparte na zdarzeniach bezstanowe obliczeń
* Potok ciągłego wdrażania

Usługa App Configuration zapewnia następujące korzyści:

* W pełni zarządzana usługa, która można skonfigurować w ciągu kilku minut
* Mapowania i elastyczne reprezentacje klucza
* Znakowanie z etykietami
* W momencie powtarzania ustawień
* Porównanie dwóch zestawów konfiguracje w wymiarach niestandardowy
* Zwiększone zabezpieczenia za pomocą tożsamości zarządzanych platformy Azure
* Szyfrowanie kompletne dane, magazynowane i przesyłane
* Natywna integracja za pomocą popularnych platform

Konfiguracja aplikacji uzupełnia [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/), która umożliwia przechowywanie kluczy tajnych aplikacji. Konfiguracja aplikacji sprawia, że łatwiej zaimplementować w następujących scenariuszach:

* Scentralizowane zarządzanie i stopień rozproszenia danych hierarchicznych konfiguracji dla różnych środowisk i różnych lokalizacji geograficznych
* Zmiany konfiguracji dynamicznej bez konieczności ponownego wdrażania lub ponownego uruchomienia aplikacji
* Funkcja zarządzania

## <a name="use-app-configuration"></a>Używać konfiguracji aplikacji

Najprostszym sposobem dodania magazynu konfiguracji aplikacji do aplikacji jest użycie biblioteki klienta udostępnianej przez firmę Microsoft. Na podstawie języka programowania i framework, dostępne są następujące metody najlepsze.

| Język programowania i platforma | Jak nawiązać połączenie |
|---|---|
| .NET core i ASP.NET Core | Dostawca konfiguracji aplikacji dla platformy .NET Core |
| .NET i programu ASP.NET | Konstruktor konfiguracji aplikacji dla platformy .NET |
| Java Spring | Konfiguracja klienta aplikacji Spring Cloud |
| Inne | Konfiguracja aplikacji interfejsu API REST |

## <a name="next-steps"></a>Kolejne kroki

* [Przewodnik Szybki Start platformy ASP.NET Core](./quickstart-aspnet-core-app.md)
* [Przewodnik Szybki Start platformy .NET core](./quickstart-dotnet-core-app.md)
* [Przewodnik Szybki Start .NET framework](./quickstart-dotnet-app.md)
* [Przewodnik Szybki Start Java Spring](./quickstart-java-spring-app.md)
* [Szybki Start platformy Azure — funkcja](./quickstart-azure-function-csharp.md)
