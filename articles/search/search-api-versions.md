---
title: Zarządzanie wersjami interfejsu API dla platformy .NET i REST
titleSuffix: Azure Cognitive Search
description: Zasady wersji dla interfejsów API REST usługi Azure Cognitive Search i biblioteki klienta w pliku .NET SDK.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 178f56354120bf7a65c51f1c9cf54e34bd011d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137292"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Wersje interfejsu API w usłudze Azure Cognitive Search

Usługa Azure Cognitive Search regularnie wprowadza aktualizacje funkcji. Czasami, ale nie zawsze, te aktualizacje wymagają nowej wersji interfejsu API, aby zachować zgodność wsteczną. Publikowanie nowej wersji pozwala kontrolować, kiedy i jak zintegrować aktualizacje usługi wyszukiwania w kodzie.

Z reguły zespół usługi Azure Cognitive Search publikuje nowe wersje tylko wtedy, gdy jest to konieczne, ponieważ może to wymagać pewnego wysiłku, aby uaktualnić kod, aby użyć nowej wersji interfejsu API. Nowa wersja jest potrzebna tylko wtedy, gdy niektóre aspekty interfejsu API uległy zmianie w sposób, który przerywa zgodność wsteczną. Takie zmiany mogą się zdarzyć z powodu poprawek do istniejących funkcji lub z powodu nowych funkcji, które zmieniają istniejący obszar powierzchni interfejsu API.

Ta sama reguła ma zastosowanie do aktualizacji SDK. Zestaw SDK usługi Azure Cognitive Search jest zgodny z regułami [przechowywania wersji semantycznych,](https://semver.org/) co oznacza, że jego wersja ma trzy części: numer główny, pomocniczy i numer kompilacji (na przykład 1.1.0). Nowa wersja główna pakietu SDK jest wydana tylko dla zmian, które przerywają zgodność wsteczną. Nieulegające aktualizacje funkcji zwiększy wersję pomocniczą, a poprawki błędów tylko zwiększą wersję kompilacji.

> [!NOTE]
> Wystąpienie usługi Azure Cognitive Search obsługuje kilka wersji interfejsu API REST, w tym najnowszą. Możesz nadal używać wersji, gdy nie jest już najnowsza, ale zaleca się migrację kodu w celu użycia najnowszej wersji. Podczas korzystania z interfejsu API REST, należy określić wersję interfejsu API w każdym żądaniu za pomocą parametru wersji interfejsu api. Podczas korzystania z .NET SDK, wersja SDK używasz określa odpowiednią wersję interfejsu API REST. Jeśli używasz starszego sdk, można kontynuować uruchamianie tego kodu bez zmian, nawet jeśli usługa jest uaktualniany do obsługi nowszej wersji interfejsu API.

## <a name="snapshot-of-current-versions"></a>Migawka bieżących wersji
Poniżej znajduje się migawka bieżących wersji wszystkich interfejsów programowania do usługi Azure Cognitive Search.


| Interfejsy | Najnowsza wersja główna | Stan |
| --- | --- | --- |
| [Zestaw SDK platformy .NET](https://aka.ms/search-sdk) |9.0 |Ogólnie dostępne, wydany w maju 2019 |
| [Podgląd pliku SDK platformy .NET](https://aka.ms/search-sdk-preview) |8.0-podgląd |Wersja zapoznawcza, wydana w kwietniu 2019 r. |
| [Interfejs API REST usługi](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |Ogólnie dostępne |
| [Interfejs API REST usługi 2019-05-06-Preview](search-api-preview.md) |2019-05-06-Podgląd |Wersja zapoznawcza |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |3.0 |Ogólnie dostępne |
| [Interfejs API REST zarządzania](https://docs.microsoft.com/rest/api/searchmanagement/) |2020-03-13|Ogólnie dostępne |

Dla interfejsów API REST, `api-version` w tym na każde wywołanie jest wymagane. Korzystanie `api-version` ułatwia kierowanie określonej wersji, takiej jak interfejs API w wersji zapoznawczej. Poniższy przykład ilustruje sposób określania parametru: `api-version`

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> Mimo że każde `api-version`żądanie ma , zaleca się użycie tej samej wersji dla wszystkich żądań interfejsu API. Jest to szczególnie ważne, gdy nowe wersje interfejsu API wprowadzają atrybuty lub operacje, które nie są rozpoznawane przez poprzednie wersje. Mieszanie wersji interfejsu API może mieć niezamierzone konsekwencje i należy ich unikać.
>
> Interfejs API REST usługi i interfejs api REST zarządzania są wersjona niezależnie od siebie. Wszelkie podobieństwa w numerach wersji są przypadkowe.

Ogólnie dostępne interfejsy API (lub GA) mogą być używane w produkcji i podlegają umowom dotyczących poziomu usług platformy Azure. Wersje w wersji zapoznawczej mają funkcje eksperymentalne, które nie zawsze są migrowane do wersji GA. **Zdecydowanie zaleca się unikanie używania interfejsów API w wersji zapoznawczej w aplikacjach produkcyjnych.**

## <a name="update-to-the-latest-version-of-the-rest-api-by-october-15-2020"></a>Aktualizacja do najnowszej wersji interfejsu API REST do 15 października 2020 r.
Następujące wersje interfejsu API rest usługi Azure Cognitive Search zostaną wycofane i nie będą już obsługiwane od 15 października 2020 r.: **2014-07-31-Preview**, **2014-10-20-Preview**, **2015-02-28-Preview**i **2015-02-28**. Ponadto wersje interfejsu SDK usługi Azure Cognitive Search .NET starsze niż **3.0.0-rc** również zostaną wycofane, ponieważ są one przeznaczone dla jednej z tych wersji interfejsu API REST. Po tej dacie aplikacje korzystające z dowolnego przestarzałego interfejsu API REST lub wersji SDK nie będą już działać i muszą zostać uaktualnione. Podobnie jak w przypadku każdej zmiany tego typu, dajemy 12-miesięczne powiadomienie, więc masz wystarczająco dużo czasu, aby dostosować.  Aby kontynuować korzystanie z usługi Azure Cognitive Search, do 15 października 2020 r. należy przeprowadzić migrację istniejącego kodu przeznaczonego dla [interfejsu API REST](search-api-migration.md) do interfejsu API REST w wersji [2019-05-06](https://docs.microsoft.com/rest/api/searchservice/) lub nowszej lub zestawu SDK .NET do [wersji 3.0](search-dotnet-sdk-migration.md) lub nowszej.  Jeśli masz jakieś pytania dotyczące aktualizacji do najnowszej azuresearch_contact@microsoft.com wersji, wyślij pocztę do 15 maja 2020 r., aby upewnić się, że masz wystarczająco dużo czasu na aktualizację kodu.

## <a name="about-preview-and-generally-available-versions"></a>Informacje o wersji zapoznawczej i ogólnie dostępnych wersjach
Usługa Azure Cognitive Search zawsze wstępnie udostępnia funkcje eksperymentalne za pośrednictwem interfejsu API REST, a następnie za pośrednictwem wersji wstępnej sdk .NET.

Funkcje w wersji zapoznawczej są dostępne do testowania i eksperymentowania, w celu zebrania opinii na temat projektowania i implementacji funkcji. Z tego powodu funkcje podglądu mogą zmieniać się wraz z czasem, prawdopodobnie w sposób, który przerywa zgodność wsteczną. Jest to w przeciwieństwie do funkcji w wersji GA, które są stabilne i mało prawdopodobne, aby zmienić z wyjątkiem małych wstecznie kompatybilnych poprawek i ulepszeń. Ponadto funkcje podglądu nie zawsze sprawiają, że w wersji GA.

Z tych powodów zaleca się pisanie kodu produkcyjnego, który przyjmuje zależność od wersji w wersji zapoznawczej. Jeśli używasz starszej wersji w wersji zapoznawczej, zalecamy migrację do ogólnie dostępnej wersji (GA).

W przypadku pliku .NET SDK: Wskazówki dotyczące migracji kodu można znaleźć w [aktualizacji sdk .NET](search-dotnet-sdk-migration-version-9.md).

Ogólna dostępność oznacza, że usługa Azure Cognitive Search jest teraz w ramach umowy dotyczącej poziomu usług (SLA). The SLA can be found at [Azure Cognitive Search Service Level Agreements](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
