---
title: Uaktualnianie do usługi Azure Search .NET Management SDK w wersji 2 | Dokumentacja firmy Microsoft
description: Uaktualnianie do usługi Azure Search .NET Management SDK w wersji 2
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.openlocfilehash: a6b6c01f0cc811abca90139e4c26c27b7bd7119f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="upgrading-to-the-azure-search-net-management-sdk-version-2"></a>Uaktualnianie do usługi Azure Search .NET Management SDK w wersji 2
Jeśli używasz wersji 1.0.2 lub starszy z [zestawu SDK usługi Azure Search .NET zarządzania](https://aka.ms/search-mgmt-sdk), ten artykuł pomoże Ci uaktualnienie aplikacji w wersji 2.

Zestaw SDK zarządzania usługi Azure Search .NET w wersji 2 zawiera pewne zmiany z wcześniejszych wersji. Są to przede wszystkim niewielkie, tak więc zmiana kodu powinny wymagać tylko minimalnym wysiłku. Zobacz [kroki niezbędne do uaktualnienia](#UpgradeSteps) instrukcje dotyczące sposobu zmiany kodu do nowej wersji zestawu SDK.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>Nowości w wersji 2
Zestaw SDK zarządzania usługi Azure Search .NET w wersji 2 dotyczy tego samego ogólnie dostępna wersja interfejsu API REST usługi Azure Search zarządzania jako poprzednie wersje zestawu SDK, w szczególności 2015-08-19. Zmiany do zestawu SDK są zmiany wyłącznie po stronie klienta w celu polepszenia użyteczność zestawu SDK dla samej siebie. Następujące zmiany:

* `Services.CreateOrUpdate` i jego wersje asynchroniczne teraz sondują udostępnianie `SearchService` i nie mają przed zakończeniem inicjowania obsługi usługi. To pozwala uniknąć konieczności pisania kodu takie sondowania samodzielnie.
* Jeśli nadal chcesz przeprowadzać sondowanie udostępniania ręcznie usługi, można użyć nowej `Services.BeginCreateOrUpdate` metody lub jeden z jego wersje asynchroniczne.
* Nowych metod `Services.Update` oraz jego wersje asynchroniczne zostały dodane do zestawu SDK. Te metody umożliwiają HTTP PATCH obsługuje aktualizacji przyrostowych usługi. Na przykład teraz można skalować usługi przez przekazanie `SearchService` wystąpienia tych metod, które zawiera tylko żądaną `partitionCount` i `replicaCount` właściwości. Stary sposób wywołania metody `Services.Get`, modyfikując zwróconego `SearchService`i nastąpiło przejście do `Services.CreateOrUpdate` nadal jest obsługiwany, ale nie jest już konieczne. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki niezbędne do uaktualnienia
Najpierw należy zaktualizować odwołania programu NuGet dla `Microsoft.Azure.Management.Search` przy użyciu konsoli Menedżera pakietów NuGet lub przez kliknięcie prawym przyciskiem myszy odwołania projektu i wybierając "Zarządzaj NuGet pakietów..." w programie Visual Studio.

Po NuGet pobrała nowe pakiety oraz ich zależności, ponownie skompiluj projekt. W zależności od struktury kodu jego może odbudować pomyślnie. Jeśli tak, wszystko jest gotowe!

Jeżeli kompilacji nie powiedzie się, być może został zaimplementowano część interfejsów zestawu SDK (na przykład do celów testowania jednostek), które zostały zmienione. Aby rozwiązać ten problem, konieczne będzie wdrożenie nowych metod, takich jak `BeginCreateOrUpdateWithHttpMessagesAsync`.

Gdy problem został rozwiązany błędy kompilacji, można wprowadzić zmiany do aplikacji, aby móc korzystać z nowych funkcji, jeśli chcesz. Nowe funkcje w zestawie SDK wyszczególnione w [nowości w wersji 2](#WhatsNew).

## <a name="conclusion"></a>Podsumowanie
Chętnie poznamy Twoją opinię na temat zestawu SDK. Jeśli wystąpią problemy, możesz poprosić nas o pomoc na [forum usługi Azure Search w witrynie MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Jeśli napotkasz błąd, można pliku problemu w [repozytorium GitHub zestawu SDK .NET usługi Azure](https://github.com/Azure/azure-sdk-for-net/issues). Upewnij się, że tytuł problemu "[wyszukiwanie Azure]" z prefiksu.

Dziękujemy za skorzystanie z usługi Azure Search!
