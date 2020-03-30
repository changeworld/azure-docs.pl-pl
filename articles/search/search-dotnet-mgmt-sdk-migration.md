---
title: Uaktualnienie do zestawu SDK zarządzania usługą Azure Search .NET w wersji 2
titleSuffix: Azure Cognitive Search
description: Uaktualnij do zestawu SDK zarządzania usługi Azure Search .NET w wersji 2 z poprzednich wersji. Dowiedz się, co nowego i jakie zmiany kodu są wymagane.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b18e9688141ee64eb7dfcb82ce58db198e324b5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847535"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>Uaktualnianie wersji sdk zarządzania usługą Azure Search .NET

> [!Important]
> Ta zawartość jest nadal w budowie. Wersja 3.0 sdk .NET usługi Azure Search Management jest dostępna w programie NuGet. Pracujemy nad aktualizacją tego przewodnika migracji, aby wyjaśnić, jak uaktualnić do nowej wersji. 
>

Jeśli używasz wersji 1.0.2 lub starszej z [usługi Azure Search .NET Management SDK,](https://aka.ms/search-mgmt-sdk)ten artykuł pomoże Ci uaktualnić aplikację do korzystania z wersji 2.

Wersja 2 zestawu SDK zarządzania usługi Azure Search .NET zawiera pewne zmiany z wcześniejszych wersji. Są one w większości niewielkie, więc zmiana kodu powinna wymagać tylko minimalnego wysiłku. Zobacz [Kroki uaktualnienia, aby uzyskać](#UpgradeSteps) instrukcje dotyczące zmiany kodu, aby użyć nowej wersji SDK.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>Co nowego w wersji 2
Wersja 2 zestawu SDK zarządzania usługi Azure Search .NET jest przeznaczona dla tej samej ogólnie dostępnej wersji interfejsu API REST usługi Azure Search Management, co poprzednie wersje zestawu SDK, w szczególności 2015-08-19. Zmiany w SDK są ściśle po stronie klienta zmiany w celu poprawy użyteczności samego SDK. Zmiany te są następujące:

* `Services.CreateOrUpdate`i jego wersje asynchroniczne teraz automatycznie `SearchService` sondowania inicjowania obsługi administracyjnej i nie zwracaj, dopóki nie zostanie zakończona inicjowania obsługi administracyjnej usługi. Dzięki temu nie musisz samodzielnie pisać takiego kodu sondowania.
* Jeśli nadal chcesz ręcznie sondować aprowizacji usługi, `Services.BeginCreateOrUpdate` można użyć nowej metody lub jednej z jej wersji asynchronicznych.
* Nowe `Services.Update` metody i jego wersje asynchroniczne zostały dodane do SDK. Metody te używają protokołu HTTP PATCH do obsługi przyrostowego aktualizowania usługi. Na przykład można teraz skalować usługę, przekazując wystąpienie `SearchService` do `partitionCount` `replicaCount` tych metod, które zawiera tylko żądane i właściwości. Stary sposób wywoływania `Services.Get`, modyfikowanie zwróconego `SearchService` `Services.CreateOrUpdate` i przekazywanie go do jest nadal obsługiwany, ale nie jest już konieczne. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki uaktualnienia
Najpierw zaktualizuj `Microsoft.Azure.Management.Search` odwołanie NuGet do użycia konsoli Menedżera pakietów NuGet lub klikając prawym przyciskiem myszy odwołania do projektu i wybierając opcję "Zarządzaj pakietami NuGet..." w programie Visual Studio.

Po pobraniu nowych pakietów i ich zależności, odbuduj projekt. W zależności od struktury kodu może pomyślnie odbudować. Jeśli tak, jesteś gotowy do pracy!

Jeśli kompilacja nie powiedzie się, może to być spowodowane zaimplementowano niektóre interfejsy SDK (na przykład na potrzeby testowania jednostkowego), które uległy zmianie. Aby rozwiązać ten problem, musisz zaimplementować nowe metody, takie jak `BeginCreateOrUpdateWithHttpMessagesAsync`.

Po usunięciu błędów kompilacji można wprowadzić zmiany w aplikacji, aby korzystać z nowych funkcji, jeśli chcesz. Nowe funkcje w SDK są szczegółowo opisane [w Co nowego w wersji 2](#WhatsNew).

## <a name="next-steps"></a>Następne kroki
Czekamy na Wasze opinie na temat SDK. Jeśli napotkasz problemy, opublikuj swoje pytania do [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest). Jeśli znajdziesz błąd, możesz zgłosić problem w [repozytorium GitHub .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Pamiętaj, aby oznaczyć tytuł problemu "[szukaj]".
