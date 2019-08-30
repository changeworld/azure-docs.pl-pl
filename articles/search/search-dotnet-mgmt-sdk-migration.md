---
title: Uaktualnianie do Azure Search .NET Management SDK w wersji 2 — Azure Search
description: Uaktualnij do Azure Search .NET Management SDK wersja 2 z poprzednich wersji. Dowiedz się, co nowego i jakie zmiany w kodzie są wymagane.
author: brjohnstmsft
manager: nitinme
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: 2a59cff7f5313b0ac5a060d698950a4c82160f67
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182252"
---
# <a name="upgrading-to-the-azure-search-net-management-sdk-version"></a>Uaktualnianie do Azure Search wersji zestawu SDK programu .NET Management 

> [!Important]
> Ta zawartość jest nadal w fazie tworzenia. Wersja 3,0 zestawu .NET SDK programu Azure Search Management jest dostępna w pakiecie NuGet. Pracujemy nad aktualizacją tego przewodnika migracji, aby wyjaśnić, jak przeprowadzić uaktualnienie do nowej wersji. 
>

W przypadku korzystania z wersji 1.0.2 lub starszej [Azure Search .NET Management SDK](https://aka.ms/search-mgmt-sdk)ten artykuł pomoże Ci uaktualnić aplikację do korzystania z wersji 2.

Wersja 2 programu Azure Search .NET Management SDK zawiera pewne zmiany z wcześniejszych wersji. Są one w większości drobnych, więc zmiana kodu powinna wymagać tylko minimalnego nakładu pracy. Zobacz [procedurę uaktualniania](#UpgradeSteps) , aby uzyskać instrukcje dotyczące zmiany kodu w celu użycia nowej wersji zestawu SDK.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>Co nowego w wersji 2
Wersja 2 zestawu SDK zarządzania programu Azure Search .NET jest przeznaczona dla tej samej ogólnie dostępnej wersji interfejsu API REST zarządzania Azure Search, jak w poprzednich wersjach zestawu SDK, w odniesieniu do 2015-08-19. Zmiany w zestawie SDK są ścisłymi zmianami po stronie klienta, aby poprawić użyteczność samego zestawu SDK. Są to następujące zmiany:

* `Services.CreateOrUpdate`i jego wersje asynchroniczne teraz automatycznie sonduje Inicjowanie obsługi `SearchService` i nie zwracają do momentu ukończenia aprowizacji usługi. Dzięki temu możesz samodzielnie napisać ten kod sondowania.
* Jeśli nadal chcesz przeprowadzić sondowanie aprowizacji usługi ręcznie, możesz użyć nowej `Services.BeginCreateOrUpdate` metody lub jednej z jej wersji asynchronicznych.
* Do zestawu `Services.Update` SDK dodano nowe metody i jego wersje asynchroniczne. Metody te używają poprawki HTTP do obsługi przyrostowej aktualizacji usługi. Można na przykład skalować usługę przez przekazanie `SearchService` wystąpienia do tych metod, które zawierają tylko żądane `partitionCount` i `replicaCount` właściwości. Stary sposób wywoływania `Services.Get`, modyfikowania zwracanych `SearchService`i przekazywania go do `Services.CreateOrUpdate` jest nadal obsługiwany, ale nie jest już potrzebny. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Kroki do uaktualnienia
Najpierw zaktualizuj odwołanie do `Microsoft.Azure.Management.Search` programu NuGet przy użyciu konsoli Menedżera pakietów NuGet lub kliknij prawym przyciskiem myszy odwołania do projektu i wybierz polecenie "Zarządzaj pakietami NuGet..." w programie Visual Studio.

Po pobraniu przez program NuGet nowych pakietów i ich zależności, należy ponownie skompilować projekt. W zależności od tego, jak kod jest strukturalny, może zostać pomyślnie ponownie odbudowany. Jeśli tak, wszystko jest gotowe!

Jeśli kompilacja nie powiedzie się, może to oznaczać, że wdrożono niektóre interfejsy zestawu SDK (na przykład na potrzeby testowania jednostek), które uległy zmianie. Aby rozwiązać ten problem, należy zaimplementować nowe metody, takie jak `BeginCreateOrUpdateWithHttpMessagesAsync`.

Po naprawieniu błędów kompilacji możesz wprowadzić zmiany w aplikacji, aby korzystać z nowych funkcji. Nowe funkcje zestawu SDK są szczegółowo opisane w temacie [co nowego w wersji 2](#WhatsNew).

## <a name="conclusion"></a>Wniosek
Poznamy Twoją opinię na temat zestawu SDK. Jeśli wystąpią problemy, skontaktuj się z nami, aby uzyskać pomoc dotyczącą [Azure Search witrynie MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Jeśli znajdziesz błąd, możesz zgłosić problem w [repozytorium GitHub zestawu SDK platformy Azure](https://github.com/Azure/azure-sdk-for-net/issues). Upewnij się, że tytuł problemu jest prefiksem "[Azure Search]".

Dziękujemy za korzystanie z Azure Search!
