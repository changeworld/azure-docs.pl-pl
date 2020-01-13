---
title: Struktury danych usługi mobilności w Azure Maps | Mapy Microsoft Azure
description: Ten artykuł zawiera informacje o typowych polach i strukturach danych zwracanych przez Microsoft Azure Maps usługi mobilności.
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 213910ee2439fa958b9f1d4926883eb8e066ba41
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910718"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Struktury danych w Azure Maps usłudze mobilności

W tym artykule wprowadzono koncepcję obszaru Metro w [Azure Maps usłudze mobilności](https://aka.ms/AzureMapsMobilityService) i niektórych wspólnych pól zwracanych za pośrednictwem usług, gdy jest wykonywane zapytanie o zatrzymanie i wiersze tranzytu publicznego. Zalecamy przeprowadzenie tego artykułu przed rozpoczęciem korzystania z interfejsów API usługi mobilności. Omawiamy typowe pola poniżej.

## <a name="metro-area"></a>Obszar Metro

Dane usługi mobilności są podzielone na obsługiwane obszary Metro. Obszary Metro nie są zgodne z granicami miejscowości, obszar Metro może zawierać wiele miast, na przykład gęsto wypełnione miasto i jego otaczające miasta. a kraj/region może być jednym obszarem Metro. 

`metroID` to identyfikator obszaru Metro, którego można użyć do wywołania [interfejsu API informacji o powierzchni Metro](https://aka.ms/AzureMapsMobilityMetroAreaInfo) w celu żądania obsługiwanych typów tranzytu i dodatkowych szczegółów dotyczących obszaru Metro, takich jak agencje tranzytowe i aktywne alerty. Możesz użyć Azure Maps uzyskać interfejsu API Metro, aby zażądać obsługiwanych obszarów Metro i metroIDs. Identyfikatory obszaru Metro mogą ulec zmianie.

**metroID:** 522 **Nazwa:** Seattle-Tacoma-Bellevue

![Seattle — obszar Metro](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Zatrzymywanie identyfikatorów

Zatrzymywanie tranzytu może być określane przez dwa typy identyfikatorów, identyfikator [GFTS](https://gtfs.org/) (, zwany jako stopKey) i identyfikator zatrzymania Azure Maps (określany jako stopid). W przypadku odwołujących się do zatrzymań w czasie sugerowane jest użycie Azure Mapsego identyfikatora zatrzymania, ponieważ ten identyfikator jest bardziej stabilny i nie będzie prawdopodobnie zmieniany, o ile istnieje fizyczne zatrzymanie. Identyfikator zatrzymania GTFS jest często aktualizowany częściej, na przykład w przypadku, gdy dostawca GTFS musi zmienić go lub wydano nową wersję GTFS, mimo że fizyczne zatrzymanie nie miało żadnych zmian.

Aby rozpocząć, można zażądać zatrzymywania tranzytu w pobliżu za pomocą [interfejsu API pobierania pobliżu tranzytu](https://aka.ms/AzureMapsMobilityNearbyTransit).

## <a name="line-groups-and-lines"></a>Wiersze i grupy wierszy

Usługa mobilności używa równoległego modelu danych dla linii i grup liniowych, aby lepiej zajmować się zmianami dziedziczonymi z modelu danych [GTFS](https://gtfs.org/) Routes i TRIPS.


### <a name="line-groups"></a>Grupy wierszy

Grupa wierszy jest jednostką, która grupuje ze sobą wszystkie wiersze, które są logicznie częścią tej samej grupy. Zwykle grupa wierszy będzie zawierać dwa wiersze, jedno z punktu A do B, a drugie zwraca od punktu B do A, zarówno należące do tej samej publicznej agencji transportowej, jak i ma ten sam numer wiersza. Mogą jednak wystąpić sytuacje, w których grupa linii ma więcej niż dwa wiersze lub tylko jeden wiersz.


### <a name="lines"></a>Linie

Jak opisano powyżej, każda grupa wierszy składa się z zestawu wierszy. Często każdy wiersz opisuje kierunek, a każda grupa linii składa się z dwóch wierszy. Istnieją jednak przypadki, w których więcej wierszy składa się z grupy wierszy, na przykład, że jest to linia, która czasami przeprowadzi Cię przez niektóre otoczenie i czasami nie działa, i jest obsługiwana w obu przypadkach pod tym samym numerem wiersza, a istnieją inne przypadki, w których linia g rupuj składa się z pojedynczego wiersza, na przykład cyklicznej linii z pojedynczym kierunkiem.

Aby rozpocząć, możesz zażądać grup wierszy przy użyciu [interfejsu API uzyskiwania linii tranzytowej](https://aka.ms/AzureMapsMobilityTransitLine) , a następnie przejść do szczegółów wierszy.


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak żądać danych tranzytowych przy użyciu usługi mobilności:

> [!div class="nextstepaction"]
> [Jak żądać danych tranzytowych](how-to-request-transit-data.md)

Informacje na temat żądania danych w czasie rzeczywistym przy użyciu usługi mobilności:

> [!div class="nextstepaction"]
> [Jak żądać danych w czasie rzeczywistym](how-to-request-real-time-data.md)

Poznaj dokumentację interfejsu API usługi mobilności Azure Maps

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API usługi mobilności](https://aka.ms/AzureMapsMobilityService)
