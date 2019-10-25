---
title: Zarządzanie wersjami interfejsu API dla zestawu .NET SDK i interfejsów API REST
titleSuffix: Azure Cognitive Search
description: Zasady wersji dla interfejsów API REST platformy Azure Wyszukiwanie poznawcze i biblioteki klienta w zestawie SDK platformy .NET.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4a976655d0f634c7026f008d64516a629947e90c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793056"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Wersje interfejsu API na platformie Azure Wyszukiwanie poznawcze

Usługa Azure Wyszukiwanie poznawcze regularnie przedstawia aktualizacje funkcji. Czasami, ale nie zawsze, te aktualizacje wymagają nowej wersji interfejsu API w celu zachowania zgodności z poprzednimi wersjami. Opublikowanie nowej wersji pozwala określić, kiedy i w jaki sposób integrują aktualizacje usługi Search w kodzie.

Zgodnie z regułą zespół usługi Azure Wyszukiwanie poznawcze publikuje nowe wersje tylko wtedy, gdy jest to konieczne, ponieważ może to wymagać pewnego wysiłku, aby uaktualnić kod w celu użycia nowej wersji interfejsu API. Nowa wersja jest wymagana tylko wtedy, gdy jakiś aspekt interfejsu API zmienił się w taki sposób, który powoduje przerwanie zgodności z poprzednimi wersjami. Takie zmiany mogą wystąpić z powodu poprawek do istniejących funkcji lub z powodu nowych funkcji, które zmieniają istniejący obszar powierzchni interfejsu API.

Ta sama reguła dotyczy aktualizacji zestawu SDK. Zestaw SDK platformy Azure Wyszukiwanie poznawcze jest zgodny z regułami [wersji semantycznej](https://semver.org/) , co oznacza, że jej wersja ma trzy części: główna, pomocnicza i numer kompilacji (na przykład 1.1.0). Nowa główna wersja zestawu SDK jest wydawana tylko dla zmian, które Przerwij zgodność z poprzednią wersją. Niekrytyczne aktualizacje funkcji spowodują zwiększenie wersji pomocniczej, a poprawki błędów spowodują zwiększenie wersji kompilacji.

> [!NOTE]
> Wystąpienie usługi Azure Wyszukiwanie poznawcze obsługuje kilka wersji interfejsu API REST, w tym najnowsze. Możesz nadal korzystać z wersji, gdy nie jest już Najnowsza, ale zalecamy przeprowadzenie migracji kodu w celu użycia najnowszej wersji. W przypadku korzystania z interfejsu API REST należy określić wersję interfejsu API w każdym żądaniu za pośrednictwem parametru API-Version. Podczas korzystania z zestawu SDK platformy .NET wersja zestawu SDK określa odpowiednią wersję interfejsu API REST. Jeśli używasz starszego zestawu SDK, możesz nadal uruchamiać ten kod bez zmian, nawet jeśli usługa zostanie uaktualniona w celu obsługi nowszej wersji interfejsu API.

## <a name="snapshot-of-current-versions"></a>Migawka bieżących wersji
Poniżej znajduje się migawka bieżących wersji wszystkich interfejsów programowania do Wyszukiwanie poznawcze platformy Azure.


| Interfejsów | Najnowsza wersja główna | Stan |
| --- | --- | --- |
| [Zestaw SDK platformy .NET](https://aka.ms/search-sdk) |9.0 |Ogólnie dostępne, wydane mogą 2019 |
| [.NET SDK — wersja zapoznawcza](https://aka.ms/search-sdk-preview) |8,0 — wersja zapoznawcza |Wersja zapoznawcza, wydana kwiecień 2019 |
| [Interfejs API REST usługi](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |Ogólnie dostępne |
| [Interfejs API REST usługi 2019-05-06 — wersja zapoznawcza](search-api-preview.md) |2019-05-06 — wersja zapoznawcza |Wersja zapoznawcza |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |3.0 |Ogólnie dostępne |
| [Interfejs API REST zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Ogólnie dostępne |

W przypadku interfejsów API REST, w tym `api-version` na każdym wywołaniu jest wymagane. Używanie `api-version` ułatwia kierowanie określonych wersji, takich jak interfejs API w wersji zapoznawczej. Poniższy przykład ilustruje sposób określenia parametru `api-version`:

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> Chociaż każde żądanie ma `api-version`, zalecamy użycie tej samej wersji dla wszystkich żądań interfejsu API. Jest to szczególnie prawdziwe, gdy nowe wersje interfejsu API wprowadzają atrybuty lub operacje, które nie są rozpoznawane przez poprzednie wersje. Mieszanie wersji interfejsu API może mieć niezamierzone konsekwencje i powinno być nieuniknione.
>
> Interfejsy API REST usługi i interfejs API REST zarządzania są niezależne od siebie. Wszelkie podobieństwa w numerach wersji to zdarzenie.

Ogólnie dostępne interfejsy API mogą być używane w środowisku produkcyjnym i podlegają umowom dotyczącym poziomu usług platformy Azure. Wersje zapoznawcze mają funkcje eksperymentalne, które nie są zawsze migrowane do wersji GA. **Zdecydowanie zaleca się unikanie korzystania z interfejsów API w wersji zapoznawczej w aplikacjach produkcyjnych.**

## <a name="about-preview-and-generally-available-versions"></a>Informacje o wersji zapoznawczej i ogólnie dostępne wersje
Usługa Azure Wyszukiwanie poznawcze zawsze wstępnie zwalnia funkcje eksperymentalne za pomocą interfejsu API REST, a następnie za pomocą wersji wstępnej zestawu .NET SDK.

Funkcje w wersji zapoznawczej są dostępne do testowania i eksperymentowania, a celem zebrania opinii na temat projektowania i implementacji funkcji. Z tego powodu funkcje wersji zapoznawczej mogą ulec zmianie z upływem czasu, prawdopodobnie w sposób powodujący przerwanie zgodności z poprzednimi wersjami. Jest to w przeciwieństwie do funkcji w wersji GA, które są stabilne i mało prawdopodobne, aby można je było zmienić z wyjątkiem małych poprawek i ulepszeń zgodnych z poprzednimi wersjami. Ponadto funkcje wersji zapoznawczej nie zawsze sprawiają, że są one w wersji GA.

Z tego powodu zalecamy zapisanie kodu produkcyjnego, który jest zależny od wersji w wersji zapoznawczej. Jeśli używasz starszej wersji zapoznawczej, zalecamy przeprowadzenie migracji do ogólnie dostępnej wersji.

Dla zestawu .NET SDK: wskazówki dotyczące migracji kodu można znaleźć w temacie [upgrade SDK platformy .NET](search-dotnet-sdk-migration-version-9.md).

Ogólna dostępność oznacza, że usługa Azure Wyszukiwanie poznawcze jest teraz objęta umową dotyczącą poziomu usług (SLA). Umowę SLA można znaleźć na stronie [umowy dotyczące poziomu usług platformy Azure wyszukiwanie poznawcze](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
