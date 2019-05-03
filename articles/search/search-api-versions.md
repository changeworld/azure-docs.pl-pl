---
title: Zarządzanie wersjami interfejsów API zestawu SDK platformy .NET i interfejsów API REST — usługa Azure Search
description: Zasady dotyczące wersji interfejsów API REST usługi Azure Search i biblioteki klienta w zestawie SDK platformy .NET.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.openlocfilehash: f361417f25579b0ca605b33bafa4a581f68b1798
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024404"
---
# <a name="api-versions-in-azure-search"></a>Wersje interfejsu API w usłudze Azure Search
Usługa Azure Search regularnie zbiera i wydaje aktualizacje funkcji. Czasami, ale nie zawsze te aktualizacje wymagają nowej wersji interfejsu API w celu zachowania zgodności z poprzednimi wersjami. Publikowanie nowej wersji pozwala na kontrolowanie, kiedy i jak integrować aktualizacje usługi wyszukiwania w kodzie.

Zgodnie z zasadą zespołu usługi Azure Search publikuje nowe wersje tylko wtedy, gdy jest to konieczne, ponieważ może pociągać za sobą pewne starań, aby uaktualnić swój kod, aby używać nowej wersji interfejsu API. Nowa wersja jest potrzebny tylko wtedy, gdy niektóre aspekty interfejs API został zmieniony w sposób, który przerywa zgodności z poprzednimi wersjami. Takie zmiany, może się zdarzyć z powodu poprawek dla istniejących funkcji lub ze względu na nowe funkcje, które zmieniają istniejącego obszaru powierzchni interfejsu API.

Ta sama zasada dotyczy aktualizacji zestawu SDK. Zestaw SDK usługi Azure Search jest zgodna [wersji semantycznej](https://semver.org/) reguł, które oznacza, że jej wersja ma trzy części: głównych, niewielkie i numer (na przykład 1.1.0) kompilacji. Główne nową wersję zestawu SDK jest zwalniany tylko w przypadku zmiany naruszające zgodności z poprzednimi wersjami. Aktualizacje funkcji bez podziału powoduje zwiększenie wersji pomocniczej i poprawki błędów zwiększy tylko wersja kompilacji.

> [!NOTE]
> Wystąpienia usługi Azure Search obsługuje kilka wersji interfejsu API REST, w tym najnowsze. Można użyć wersji, gdy nie jest już najnowsze, ale zaleca się przeprowadzenie migracji kodu, aby użyć najnowszej wersji. Korzystając z interfejsu API REST, należy określić wersję interfejsu API w każdym żądaniu za pomocą parametru api-version. Korzystając z zestawu .NET SDK, wersję zestawu SDK, w przypadku używania określa odpowiednią wersję interfejsu API REST. Jeśli używasz starszy zestaw SDK, można nadal uruchomić ten kod bez konieczności wprowadzania zmian, nawet wtedy, gdy usługa zostanie uaktualniony do nowszej wersji interfejsu API do obsługi.

## <a name="snapshot-of-current-versions"></a>Migawki bieżących wersji
Poniżej to migawki bieżących wersji wszystkich interfejsów programowania do usługi Azure Search.


| Interfejsy | Najnowszą wersją | Stan |
| --- | --- | --- |
| [Zestaw SDK platformy .NET](https://aka.ms/search-sdk) |9.0 |Ogólnie dostępne, wydane maja 2019 r. Zawartość jest nadal w trakcie tworzenia.  |
| [Zestaw SDK platformy .NET w wersji zapoznawczej](https://aka.ms/search-sdk-preview) |8.0.0-Preview |Podgląd wydane kwietnia 2019 r. Zawartość jest nadal w trakcie tworzenia.|
| [Interfejs API REST usługi](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |Ogólnie dostępne |
| [Usługa REST API 2019-05-06-Preview](search-api-preview.md) |2019-05-06-preview |Wersja zapoznawcza |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |3.0 |Ogólnie dostępne |
| [Interfejs API REST zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Ogólnie dostępne |

Interfejsy API REST, w tym `api-version` jest wymagany przy każdym wywołaniu. Za pomocą `api-version` ułatwia pod kątem określonej wersji, takich jak interfejs API w wersji zapoznawczej. W poniższym przykładzie pokazano sposób, w jaki `api-version` określono parametr:

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> Mimo że każde żądanie ma `api-version`, zalecane jest użycie tej samej wersji dla wszystkich żądań interfejsu API. Jest to szczególnie istotne w przypadku wprowadzenia nowych wersjach interfejsu API, atrybuty lub operacje, które nie są rozpoznawane przez poprzednie wersje. Mieszanie wersji interfejsu API może mieć niezamierzone konsekwencje i należy ich unikać.
>
> Interfejs API REST usługi i interfejsu API REST zarządzania są kontrolę wersji niezależnie od siebie nawzajem. Wszelkie podobieństwa numerów wersji jest zupełnie przypadkowe.

Ogólnie dostępna (lub GA) interfejsy API mogą być używane w środowisku produkcyjnym i jest zależna od umów dotyczących poziomu usług platformy Azure. Wersji zapoznawczych mają funkcji eksperymentalnych, które nie zawsze są migrowane do wersji Ogólnodostępnej. **Zdecydowanie zaleca się unikać korzystania z wersji zapoznawczej interfejsów API w aplikacjach produkcyjnych.**

## <a name="about-preview-and-generally-available-versions"></a>Informacje dotyczące wersji Preview i jest ogólnie dostępna
Usługa Azure Search zawsze wstępnie zwalnia eksperymentalne funkcje za pośrednictwem interfejsu API REST po pierwsze, następnie za pośrednictwem wstępnej wersji zestawu SDK platformy .NET.

Funkcje w wersji zapoznawczej są dostępne do testowania i eksperymentowania, z celem zbierania opinii na projektowania i implementacji. Z tego powodu funkcje w wersji zapoznawczej można zmienić wraz z upływem czasu, być może w sposób, który przerwania wstecznej zgodności. Jest to w przeciwieństwie do funkcji w wersji Ogólnodostępnej, które są stabilne i raczej nie ulegnie zmianie z wyjątkiem drobnych poprawek zgodne z poprzednimi wersjami i ulepszeń. Ponadto funkcje w wersji zapoznawczej nie zawsze należy ją do wersji Ogólnodostępnej.

Z tego względu zalecamy względem pisania kodu produkcyjnego, który przyjmuje zależność wersji zapoznawczych. Jeśli używasz starszej wersji (wersja zapoznawcza), zalecamy przeprowadzić migrację do ogólnie dostępnej wersji (GA).

Dla zestawu SDK platformy .NET: Wskazówki dotyczące migracji kod znajduje się w temacie [uaktualnianie zestawu SDK .NET](search-dotnet-sdk-migration.md).

Ogólnie dostępne oznacza, że usługa Azure Search jest teraz w ramach umowy dotyczącej poziomu usług (SLA). Umowy SLA można znaleźć w [umowy dotyczące poziomu usług Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
