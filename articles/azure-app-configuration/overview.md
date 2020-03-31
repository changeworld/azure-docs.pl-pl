---
title: Co to jest usługa Azure App Configuration?
description: Omówienie usługi Azure App Configuration.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: overview
ms.date: 02/19/2020
ms.openlocfilehash: 1f1cec68813d33e7fa19a414a30adfc9a41df91f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77523479"
---
# <a name="what-is-azure-app-configuration"></a>Co to jest usługa Azure App Configuration?

Usługa Azure App Configuration zapewnia usługę centralnego zarządzania ustawieniami aplikacji i flagami funkcji. Nowoczesne programy, zwłaszcza programy działające w chmurze, zazwyczaj mają wiele składników, które są rozproszone w przyrodzie. Posiadanie ustawień konfiguracji w ramach tych składników może powodować występowanie błędów podczas wdrażania aplikacji, których diagnozowanie będzie bardzo skomplikowane. Użyj konfiguracji aplikacji, aby zapisać wszystkie ustawienia aplikacji i zabezpieczyć ich dostęp w jednym miejscu.

## <a name="why-use-app-configuration"></a>Dlaczego warto korzystać z konfiguracji aplikacji?

Aplikacje oparte na chmurze są często uruchamiane na wielu maszynach wirtualnych lub kontenerach w wielu regionach i korzystają z wielu usług zewnętrznych. Tworzenie niezawodnej i skalowalnej aplikacji w rozproszonym środowisku stanowi poważne wyzwanie.

Różne metody programowania pomagają deweloperom radzić sobie z rosnącą złożonością tworzenia aplikacji. Na przykład [aplikacja Twelve-Factor](https://12factor.net/) opisuje wiele dobrze przetestowanych wzorców architektury i najlepszych rozwiązań do użycia z aplikacjami w chmurze. Jednym z kluczowych zaleceń z tego przewodnika jest oddzielenia konfiguracji od kodu. Ustawienia konfiguracji aplikacji powinny być przechowywane na zewnątrz do jej pliku wykonywalnego i odczytywane z jego środowiska wykonawczego lub źródła zewnętrznego.

Podczas gdy każda aplikacja może korzystać z konfiguracji aplikacji, poniższe przykłady są typy aplikacji, które korzystają z korzystania z niego:

* Mikrousługi oparte na usłudze Azure Kubernetes, sieci szkieletowej usługi Azure lub innych aplikacjach konteneryzowanych wdrożonych w co najmniej jednej lokalizacji geograficznej
* Aplikacje bezserwerowe, które obejmują usługi Azure Functions lub inne aplikacje obliczeniowe oparte na zdarzeniach bezstanowe
* Potok ciągłego wdrażania

Usługa App Configuration zapewnia następujące korzyści:

* W pełni zarządzana usługa, którą można skonfigurować w ciągu kilku minut
* Elastyczne reprezentacje kluczy i mapowania
* Oznaczanie etykietami
* Powtórka ustawień w czasie
* Dedykowany interfejs użytkownika do zarządzania flagami funkcji
* Porównanie dwóch zestawów konfiguracji w wymiarach zdefiniowanych na zamówienie
* Zwiększone bezpieczeństwo dzięki tożsamościom zarządzanym przez platformę Azure
* Szyfrowanie poufnych informacji w spoczynku i podczas przesyłania
* Natywna integracja z popularnymi strukturami

Konfiguracja aplikacji uzupełnia [usługę Azure Key Vault](https://azure.microsoft.com/services/key-vault/), która służy do przechowywania wpisów tajnych aplikacji. Konfiguracja aplikacji ułatwia implementowanie następujących scenariuszy:

* Centralizacja zarządzania i dystrybucji hierarchicznych danych konfiguracyjnych dla różnych środowisk i regionów geograficznych
* Dynamiczna zmiana ustawień aplikacji bez konieczności ponownego rozmieszczenia lub ponownego uruchomienia aplikacji
* Sterowanie dostępnością funkcji w czasie rzeczywistym

## <a name="use-app-configuration"></a>Korzystanie z konfiguracji aplikacji

Najprostszym sposobem dodania magazynu konfiguracji aplikacji do aplikacji jest biblioteka klienta dostarczona przez firmę Microsoft. Dostępne są następujące metody łączenia się z aplikacją, w zależności od wybranego języka i struktury

| Język programowania i platforma | Jak nawiązać połączenie |
|---|---|
| .NET Core i ASP.NET Core | Dostawca konfiguracji aplikacji dla platformy .NET Core |
| .NET Framework i ASP.NET | Konstruktor konfiguracji aplikacji dla platformy .NET |
| Java Spring | Klient konfiguracji aplikacji dla spring cloud |
| Inne | Interfejs API REST konfiguracji aplikacji |

## <a name="next-steps"></a>Następne kroki

* [Szybki start ASP.NET Core](./quickstart-aspnet-core-app.md)
* [Szybki start .NET Core](./quickstart-dotnet-core-app.md)
* [Szybki start programu .NET Framework](./quickstart-dotnet-app.md)
* [Szybki start usługi Azure Functions](./quickstart-azure-functions-csharp.md)
* [Szybki start Java Spring](./quickstart-java-spring-app.md)
* [ASP.NET rdzeń flaga funkcji Szybki start](./quickstart-feature-flag-aspnet-core.md)
* [Skrócona flaga funkcji Wiosna Rozruch](./quickstart-feature-flag-spring-boot.md)
