---
title: Wersje interfejsu API usługi Azure Search | Dokumentacja firmy Microsoft
description: Wersja zasady dla interfejsów API usługi Azure Search REST i biblioteki klienckiej zestawu .NET SDK.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.openlocfilehash: 3235813708b3c097e7f66610796f75eafd06aa05
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="api-versions-in-azure-search"></a>Wersje interfejsu API w usłudze Azure Search
Usługa Azure Search regularnie zbiera i wydaje aktualizacje funkcji. Czasami, ale nie zawsze te aktualizacje wymagają firmie Microsoft w celu publikowania nowej wersji interfejsach API w celu zachowania zgodności z poprzednimi wersjami. Opublikowanie nowych wersji umożliwia określenie, kiedy i jak integrować aktualizacje usługi wyszukiwania w kodzie.

Zgodnie z zasadą spróbujemy publikowanie nowych wersji tylko wtedy, gdy jest to konieczne, ponieważ może dotyczyć wysiłku, aby uaktualnić swój kod, aby używać nowej wersji interfejsu API. Nowa wersja firma Microsoft opublikuje tylko, jeśli należy zmienić niektóre aspekty interfejsu API w sposób, który dzieli zgodności z poprzednimi wersjami. Może to nastąpić z powodu poprawki istniejących funkcji, lub z powodu nowe funkcje, które spowodują zmianę istniejących powierzchni interfejsu API.

Firma Microsoft, wykonaj tę samą zasadę aktualizacji zestawu SDK. Zestaw SDK usługi Azure Search następuje [wersjonowania semantycznego](http://semver.org/) reguł, które oznacza, że wersja ma trzy części: główne, pomocnicze i kompilacji (na przykład 1.1.0). Firma Microsoft opublikuje nowej wersji głównej zestawu SDK tylko w przypadku zmiany, które Podziel zgodności z poprzednimi wersjami. W przypadku twardej funkcji aktualizacji możemy powoduje zwiększenie wersji pomocniczej, a dla poprawki możemy tylko zwiększy wersji kompilacji.

> [!NOTE]
> Wystąpienia usługi Azure Search obsługuje różne wersje interfejsu API REST, w tym najnowsze. Możesz użyć wersji, gdy nie jest już najnowszego, ale zaleca się przeprowadzenie migracji swój kod, aby używać najnowszej wersji. Korzystając z interfejsu API REST, należy określić wersję interfejsu API w każde żądanie za pośrednictwem parametru api-version. Podczas korzystania z zestawu .NET SDK, wersja zestawu SDK używasz określa odpowiednią wersję interfejsu API REST. Jeśli używasz starszej zestawu SDK można kontynuować do uruchomienia kodu bez zmian, nawet jeśli usługa nie zostanie uaktualniony do nowszej wersji interfejsu API obsługuje.

## <a name="snapshot-of-current-versions"></a>Migawkę bieżącej wersji.
Poniżej jest migawkę bieżącej wersji wszystkich interfejsów programowania do usługi Azure Search.

| Interfejsy | Najnowszą wersją główną | Stan |
| --- | --- | --- |
| [Zestaw SDK platformy .NET](https://aka.ms/search-sdk) |5.0 |Ogólnie dostępne, wydane 2018 kwietnia |
| [Wersja zapoznawcza zestawu SDK .NET](https://aka.ms/search-sdk-preview) |4.0.1-Preview |Podgląd wydane 2017 maja |
| [Interfejs API REST usługi](https://docs.microsoft.com/rest/api/searchservice/) |2017-11-11 |Ogólnie dostępna |
| [Podgląd interfejsu API REST usługi](search-api-2016-09-01-preview.md) |2016-09-01-preview |Wersja zapoznawcza |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |2.0 |Ogólnie dostępna |
| [Interfejsu API REST zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Ogólnie dostępna |

Dla interfejsów API REST, w tym `api-version` na każde wywołanie jest wymagane. Dzięki temu można łatwo pod kątem określonej wersji, np. interfejsu API w wersji zapoznawczej. Poniższy przykład przedstawia sposób `api-version` określony parametr:

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2017-11-11

> [!NOTE]
> Mimo że każdego żądania ma `api-version`, zalecane jest użycie tej samej wersji dla wszystkich żądań interfejsu API. Jest to szczególnie istotne podczas wprowadzenia nowych wersji interfejsu API atrybuty lub operacje, które nie są rozpoznawane przez poprzednie wersje. Mieszanie wersji interfejsu API może mieć niezamierzone skutki i należy unikać.
>
> Usługi interfejsu API REST i interfejsu API REST zarządzania są numerów wersji niezależnie od siebie nawzajem. Przypadkowe jest wszelkie podobieństwa numerów wersji.

Ogólnie dostępne (lub GA) interfejsy API mogą być używane w środowisku produkcyjnym i podlegają umów dotyczących poziomu usług platformy Azure. Wersji zapoznawczych ma eksperymentalne funkcje, które nie są migrowane zawsze wersją GA. **Zdecydowanie odradzamy przy użyciu podglądu interfejsów API w aplikacji produkcyjnych.**

## <a name="about-preview-and-generally-available-versions"></a>Informacje dotyczące wersji Preview i ogólnie dostępna
Wyszukiwanie Azure zawsze wstępnie zwalnia eksperymentalną za pośrednictwem interfejsu API REST, następnie za pomocą wstępnej wersji zestawu .NET SDK.

Funkcje w wersji zapoznawczej nie ma gwarancji do migracji do wersji GA. Funkcje w wersji GA są traktowane jako stabilny i prawdopodobnie nie można zmienić z wyjątkiem małych poprawki zapewniającej i ulepszenia, funkcje w wersji zapoznawczej są dostępne do testowania i eksperymenty, w celu zbierania opinii na projektowania i implementacji.

Jednak ponieważ funkcje w wersji zapoznawczej mogą ulec zmianie, zaleca się przed pisania kodu produkcyjnego ma zależności w wersji zapoznawczej. Jeśli używasz starszej wersji zapoznawczej firma Microsoft zaleca migracji jest ogólnie dostępna wersja (GA).

Dla zestawu .NET SDK: wskazówki dotyczące migracji kodu można znaleźć w folderze [uaktualnienia zestawu .NET SDK](search-dotnet-sdk-migration.md).

Ogólnie oznacza, że usługi Azure Search ma teraz w obszarze umowę dotyczącą poziomu usług (SLA). Umowa SLA można znaleźć w folderze [umowy dotyczące poziomu usług wyszukiwanie Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
