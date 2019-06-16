---
title: Struktury danych usługi mobilności w usługi Azure Maps | Dokumentacja firmy Microsoft
description: Struktury danych usługi mobilności Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 29e8a9d7555ca836b6266879f3b3c1e32ffd3980
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735559"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Struktury danych usługi mobilności Azure Maps

Ten artykuł wprowadza pojęcia obszar Metro [usługę mobilności usługi Azure Maps](https://aka.ms/AzureMapsMobilityService) i niektóre typowe pola zwrócone za pośrednictwem usług, gdy zostanie zapytany publicznych działało zatrzymuje linii. Firma Microsoft zaleca, przeprowadzając w tym artykule, przed rozpoczęciem za pomocą interfejsów API usługi mobilności. Omówimy następujące typowe poniższe pola.

## <a name="metro-area"></a>Miasto

Dane usługi mobilności jest dzielony na obsługiwanych obszarów metro. Obszary Metro nie wykonuj granice miasta, metro obszar może zawierać wielu miast, na przykład zagęszczenie miasta i jego otaczającego miast; i kraju/regionu może być jednym obszarze metro. 

`metroID` Jest Identyfikatorem metro obszar, który może służyć do wywołania [uzyskać API informacje o obszarze Metro](https://aka.ms/AzureMapsMobilityMetroAreaInfo) typów przesyłania żądania obsługiwane oraz dodatkowe szczegóły dla metro obszaru, takiego jak agencje przesyłania i aktywne alerty. Mapy Pobierz Metro interfejsu API usługi Azure służy do żądania obsługiwanych obszarów metro i metroIDs. Miasto identyfikatory mogą ulec zmianie.

**metroID:** 522 **nazwy:** Seattle-Tacoma-Bellevue

![Seattle-metro-area](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Zatrzymaj identyfikatorów

Zatrzymanie przesyłania mogą być przywoływane przez dwa typy identyfikatorów, [ogólne przesyłania strumieniowego źródła specyfikacji (GFTS)](https://gtfs.org/) identyfikator (nazywane stopKey) i usługi Azure Maps przestać identyfikator (nazywane stopId). Przy odwoływaniu się do zatrzymuje wraz z upływem czasu, zalecane jest skorzystaj z usługi Azure Maps stop, ponieważ ten identyfikator jest bardziej stabilny i prawdopodobnie nie zmienia tak długo, jak istnieje fizycznych stop. Identyfikator stop GTFS jest aktualizowana częściej, na przykład w przypadku, gdy dostawca GTFS trzeba je zmienić lub wydaniu nowej wersji GTFS, chociaż fizyczne stop nastąpiła zmiana nie.

Aby rozpocząć, możesz poprosić o pobliskich przesyłania zatrzymuje się przy użyciu [uzyskać pobliskich API przesyłania](https://aka.ms/AzureMapsMobilityNearbyTransit).

## <a name="line-groups-and-lines"></a>Grupy wierszy i wiersze

Usługę mobilności przy użyciu modelu danych równoległych wierszy i członkowie jego grupy wierszy, aby lepiej poradzić sobie ze zmianami [GTFS](https://gtfs.org/) trasy i przesłania modelu danych.


### <a name="line-groups"></a>Grupy wierszy

Grupa wiersza jest jednostką, grupowanie wszystkie wiersze, które są logicznie częścią tej samej grupy. Zazwyczaj grupy wiersz będzie zawierać dwa wiersze, co będzie od A do punktu B, inne zwracanie z punktu B do A, zarówno należące do tej samej agencji transportem publicznym i posiadających ten sam numer wiersza. Jednak może być przypadki, w których grupa wiersz zawiera więcej niż dwóch wierszy lub w jednym wierszu w nim.


### <a name="lines"></a>wiersze

Jak wspomniano powyżej, każdy wiersz grupa składa się z zestawu wierszy. Często każdy wiersz w tym artykule opisano kierunku i dla każdego wiersza grupa składa się z dwóch wierszy. Jednakże istnieją przypadki, w więcej wierszy, które zawiera grupę linii, na przykład jest wiersza, które czasami przekierowuje za pośrednictwem niektórych otoczenia i czasami nie i jest świadczona w obu przypadkach, w ramach tego samego numeru wiersza, a innych sytuacjach, w których linię g Grupa składa się z jednym wierszu, na przykład cykliczne wiersz w jednym kierunku.

Aby rozpocząć, możesz poprosić grupy wierszy przy użyciu [Rozpoczynanie przesyłania wiersza API](https://aka.ms/AzureMapsMobilityTransitLine) i nowszych do przechodzenia do szczegółów wierszy.


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak utworzyć żądanie przesyłane dane przy użyciu usługi mobilności:

> [!div class="nextstepaction"]
> [Jak utworzyć żądanie przesyłane dane](how-to-request-transit-data.md)

Dowiedz się, jak żądania danych w czasie rzeczywistym przy użyciu usługi mobilności:

> [!div class="nextstepaction"]
> [Jak utworzyć żądanie danych w czasie rzeczywistym](how-to-request-real-time-data.md)

Zapoznaj się z dokumentacją interfejsu API usługi Azure Maps Mobility Service

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API usługi mobilności](https://aka.ms/AzureMapsMobilityService)
