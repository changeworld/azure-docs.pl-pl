---
title: Struktury danych usługi mobilności w usłudze Azure Maps| Mapy platformy Microsoft Azure
description: W tym artykule dowiesz się o typowych polach i strukturach danych zwracanych za pośrednictwem usług microsoft azure maps mobilności.
author: philmea
ms.author: philmea
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 30696c5dcb3353ea468aa78dbc107dae4d292edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334443"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Struktury danych w usłudze mobilności usługi Azure Maps

W tym artykule przedstawiono pojęcie Metro Area w [usłudze Azure Maps Mobility Service.](https://aka.ms/AzureMapsMobilityService) Omówimy niektóre typowe pola, które są zwracane, gdy ta usługa jest wyszukiwana dla przystanków transportu publicznego i linii. Zalecamy przeczytanie tego artykułu przed opracowaniem za pomocą interfejsów API usługi mobilności.

## <a name="metro-area"></a>Metro

Dane usługi mobilności są pogrupowane według obsługiwanych obszarów metra. Obszary metra nie są zgodne z granicami miasta. Obszar metra może zawierać wiele miast, gęsto zaludnione miasto i okoliczne miasta. W rzeczywistości kraj/region może być jednym obszarem metra. 

Jest `metroID` to identyfikator obszaru metra, który może służyć do wywołania [Get Metro Area Info API](https://aka.ms/AzureMapsMobilityMetroAreaInfo). Użyj interfejsu API "Pobierz metro" usługi Azure Maps, aby zażądać typów tranzytu, agencji tranzytowych, aktywnych alertów i dodatkowych szczegółów dla wybranej stacji metra. Można również zamówić obsługiwane obszary metra i metroIDs. Identyfikatory obszaru metra mogą ulec zmianie.

**metroID:** 522 **Nazwa:** Seattle-Tacoma-Bellevue

![Seattle-metro-area](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Zatrzymaj identyfikatory

Zatrzymania przesyłania mogą być określane przez dwa typy identyfikatorów, identyfikator specyfikacji ogólnego [kanału informacyjnego (GFTS)](https://gtfs.org/) i identyfikator zatrzymania usługi Azure Maps. Identyfikator GFTS jest określany jako stopKey i identyfikator zatrzymania usługi Azure Maps jest określany jako identyfikator stopID. Często odwołując się do przystanków przesyłania, zachęcamy do korzystania z identyfikatora zatrzymania usługi Azure Maps. stopID jest bardziej stabilny i może pozostać taki sam, o ile istnieje fizyczne zatrzymanie. Identyfikator zatrzymania GTFS jest aktualizowany częściej. Na przykład identyfikator zatrzymania GTFS można zaktualizować na żądanie dostawcy GTFS lub po wydaniu nowej wersji GTFS. Chociaż fizyczne zatrzymanie nie miało zmian, identyfikator zatrzymania GTFS może ulec zmianie.

Aby rozpocząć, możesz poprosić o pobliskie przystanki tranzytowe za pomocą [interfejsu Get Nearby Transit API](https://aka.ms/AzureMapsMobilityNearbyTransit).

## <a name="line-groups-and-lines"></a>Grupy wierszy i linie

Usługa mobilności używa równoległego modelu danych dla linii i grup linii. Model ten jest używany do lepszego radzenia sobie ze zmianami odziedziczonymi po trasach [GTFS](https://gtfs.org/) i danych o podróżach.


### <a name="line-groups"></a>Grupy wierszy

Grupa wierszy jest encją, która grupuje wszystkie wiersze, które są logicznie częścią tej samej grupy. Zazwyczaj grupa wierszy zawiera dwa wiersze, jeden od punktu A do B, a drugi powracający z punktu B do A. Obie linie należałyby do tej samej agencji transportu publicznego i miałyby ten sam numer linii. Mogą jednak wystąpić przypadki, w których grupa wierszy ma więcej niż dwie linie lub tylko jedną linię w obrębie.


### <a name="lines"></a>Linie

Jak wspomniano powyżej, każda grupa wierszy składa się z zestawu wierszy. Każda grupa wierszy składa się z dwóch linii, a każda linia opisuje kierunek.  Istnieją jednak przypadki, w których więcej wierszy tworzą grupę wierszy. Na przykład istnieje linia, która czasami objazdy przez pewną dzielnicę, a czasami nie. W obu przypadkach działa pod tym samym numerem wiersza. Również grupa wierszy może składać się z pojedynczego wiersza. Linia kołowa z jednym kierunkiem jest grupą lingową z jedną linią.

Aby rozpocząć, można zażądać grup wierszy przy użyciu [interfejsu API Pobierz linię tranzytową](https://aka.ms/AzureMapsMobilityTransitLine).


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak żądać danych tranzytowych za pomocą usługi mobilności:

> [!div class="nextstepaction"]
> [Jak zażądać danych tranzytowych](how-to-request-transit-data.md)

Dowiedz się, jak żądać danych w czasie rzeczywistym za pomocą usługi mobilności:

> [!div class="nextstepaction"]
> [Jak żądać danych w czasie rzeczywistym](how-to-request-real-time-data.md)

Zapoznaj się z dokumentacją interfejsu API usługi mobilności usługi Azure Maps

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API usługi mobilności](https://aka.ms/AzureMapsMobilityService)
