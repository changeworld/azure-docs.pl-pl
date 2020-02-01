---
title: Co to jest konfiguracja aplikacji platformy Azure?
description: Omówienie usługi Azure App Configuration.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: overview
ms.date: 02/24/2019
ms.openlocfilehash: 22ebfc53405149358b0a2a03cd256a78db5d1910
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899334"
---
# <a name="what-is-azure-app-configuration"></a>Co to jest konfiguracja aplikacji platformy Azure?

Konfiguracja aplikacji platformy Azure umożliwia centralne zarządzanie ustawieniami aplikacji i flagami funkcji. Nowoczesne programy, szczególnie programy działające w chmurze, zwykle mają wiele składników, które są rozpowszechniane. Posiadanie ustawień konfiguracji w ramach tych składników może powodować występowanie błędów podczas wdrażania aplikacji, których diagnozowanie będzie bardzo skomplikowane. Użyj konfiguracji aplikacji, aby przechowywać wszystkie ustawienia aplikacji i zabezpieczyć ich dostęp w jednym miejscu.

Konfiguracja aplikacji jest obecnie w publicznej wersji zapoznawczej. Korzystanie z niego jest bezpłatne w okresie zapoznawczym. Możesz utworzyć konto w [Azure Portal](https://portal.azure.com).

## <a name="why-use-app-configuration"></a>Dlaczego warto używać konfiguracji aplikacji?

Aplikacje oparte na chmurze są często uruchamiane na wielu maszynach wirtualnych lub kontenerach w wielu regionach i korzystają z wielu usług zewnętrznych. Tworzenie aplikacji rozproszonej, która jest niezawodna i skalowalna, jest wyzwaniem.

Różne metody programowania pomagają deweloperom w coraz większym stopniu złożoności tworzenia aplikacji. Na przykład [aplikacja 12-czynnikowa](https://12factor.net/) opisuje wiele dobrze przetestowanych wzorców architektonicznych i najlepszych rozwiązań do użycia z aplikacjami w chmurze. Jednym z kluczowych zaleceń z tego przewodnika jest oddzielenia konfiguracji od kodu. W takim przypadku ustawienia konfiguracji aplikacji powinny być przechowywane zewnętrznie do jego pliku wykonywalnego i odczytywane ze środowiska uruchomieniowego lub zewnętrznego źródła.

Podczas gdy dowolna aplikacja może korzystać z konfiguracji aplikacji, następujące przykłady są typami aplikacji, które korzystają z jej użycia:

* Mikrousługi oparte na usłudze Azure Kubernetes Service, platformie Azure Service Fabric lub innych aplikacjach kontenerowych wdrożonych w jednym lub większej liczbie lokalizacje geograficzne
* Aplikacje bezserwerowe, które obejmują Azure Functions lub inne bezstanowe aplikacje do obliczeń sterowane zdarzeniami
* Potok ciągłego wdrażania

Usługa App Configuration zapewnia następujące korzyści:

* W pełni zarządzana usługa, którą można skonfigurować w kilka minut
* Elastyczne reprezentacje i mapowania kluczy
* Tagowanie z etykietami
* Odtwarzanie ustawień do punktu w czasie
* Dedykowany interfejs użytkownika do zarządzania flagami funkcji
* Porównanie dwóch zestawów konfiguracji w wymiarach zdefiniowanych przez użytkownika
* Ulepszone zabezpieczenia za pomocą tożsamości zarządzanych przez platformę Azure
* Ukończ szyfrowanie danych w czasie spoczynku lub w tranzycie
* Natywna integracja z popularnymi strukturami

Konfiguracja aplikacji uzupełnia [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), które są używane do przechowywania wpisów tajnych aplikacji. Konfiguracja aplikacji ułatwia implementowanie następujących scenariuszy:

* Scentralizowanie zarządzania i dystrybucji hierarchicznych danych konfiguracyjnych dla różnych środowisk i lokalizacje geograficzne
* Dynamicznie Zmieniaj ustawienia aplikacji bez konieczności ponownego wdrażania lub ponownej instalacji aplikacji
* Dostępność funkcji kontroli w czasie rzeczywistym

## <a name="use-app-configuration"></a>Korzystanie z konfiguracji aplikacji

Najprostszym sposobem dodawania magazynu konfiguracji aplikacji do aplikacji jest użycie biblioteki klienckiej udostępnianej przez firmę Microsoft. Na podstawie języka programowania i struktury są dostępne następujące najlepsze metody.

| Język programowania i platforma | Jak nawiązać połączenie |
|---|---|
| .NET Core i ASP.NET Core | Dostawca konfiguracji aplikacji dla platformy .NET Core |
| .NET Framework i ASP.NET | Konstruktor konfiguracji aplikacji dla platformy .NET |
| Java Spring | Klient konfiguracji aplikacji dla chmury wiosennej |
| Inne | Interfejs API REST konfiguracji aplikacji |

## <a name="next-steps"></a>Następne kroki

* [ASP.NET Core — Szybki Start](./quickstart-aspnet-core-app.md)
* [.NET Core — Szybki Start](./quickstart-dotnet-core-app.md)
* [.NET Framework — Szybki Start](./quickstart-dotnet-app.md)
* [Azure Functions — Szybki Start](./quickstart-azure-functions-csharp.md)
* [Środowisko Java — Szybki Start](./quickstart-java-spring-app.md)
* [ASP.NET Core flagi funkcji — Szybki Start](./quickstart-feature-flag-aspnet-core.md)
* [Flaga funkcji rozruchu sprężynowego — Szybki Start](./quickstart-feature-flag-spring-boot.md)
