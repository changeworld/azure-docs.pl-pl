---
title: Dostępność i wydajność kompromisy dla różnych poziomów spójności w usłudze Azure Cosmos DB | Dokumentacja firmy Microsoft
description: Dostępność i wydajność kompromisy dla różnych poziomów spójności w usłudze Azure Cosmos DB.
keywords: spójność, wydajność, usługi azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.openlocfilehash: 0e4105d6f56a8eb45a83e970c85319cf25041781
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2018
ms.locfileid: "51514778"
---
# <a name="availability-and-performance-tradeoffs-for-various-consistency-levels-in-azure-cosmos-db"></a>Dostępność i wydajność kompromisy dla różnych poziomów spójności w usłudze Azure Cosmos DB

Rozproszonych baz danych, opierając się na replikację, wysoką dostępność, małych opóźnień, czy oba rodzaje, wprowadzić podstawowe zależnościami między spójności odczytu, a dostępność, opóźnienia i przepływności. Wyjaśnienie pojęcia spójności danych jako liczne opcje zamiast dwoma skrajnymi poziomami spójnością silną i ostateczną zbliża się do usługi Azure Cosmos DB. Usługa cosmos DB umożliwia deweloperom do wyboru spośród pięciu dobrze zdefiniowanych modeli spójności spektrum spójności (najsilniejszej do najsłabszej) — **silne**, **powiązana nieaktualność**, **sesji** , **spójny prefiks**, i **ostatecznej**. Każda z pięcioma modelami spójności zapewnia wpływ na dostępność i wydajność i jest wspierana przez kompleksowe umowy SLA.

## <a name="consistency-levels-and-latency"></a>Poziomy spójności i opóźnienia

- **Opóźnienie odczytu** dla wszystkich poziomów spójności jest zawsze musi być mniejsza niż 10 milisekund w 99. percentylu i są objęte umową SLA. Średni (w 50. percentyl) odczytu, czas oczekiwania jest zwykle 2 milisekund lub mniej.

- Z wyjątkiem konta usługi Cosmos, span kilku regionach, które są skonfigurowane przy użyciu silnej spójności **opóźnienie zapisu** pozostałe sprawdzania spójności poziomy zawsze może być mniejsza niż 10 milisekund w 99th percentyl. To opóźnienie zapisu są objęte umową SLA. Opóźnienie zapisu średni (w 50. percentyl) jest zazwyczaj 5 MS lub mniej.

- Dla konta Cosmos, które mają kilku regionach skonfigurowano wysoki poziom spójności (obecnie dostępna w wersji zapoznawczej) **opóźnienie zapisu** musi być mniejsza niż < (2 * dwustronnej konwersji czasu/RTT) + 10 milisekund w 99. percentylu. RTT między dowolnymi dwoma regionami położony skojarzonych z Twoim kontem Cosmos. Dokładny czas oczekiwania RTT jest funkcją szybkość programu światła odległości i dokładnie topologię sieci platformy Azure. Sieci platformy Azure nie zapewnia żadnych opóźnień umowy SLA dla RTT między wszystkie dwóch regionach platformy Azure. Opóźnienia w replikacji bazy danych cosmos są wyświetlane w witrynie Azure portal dla Twojego konta usługi Cosmos, dzięki czemu można monitorować opóźnienia w replikacji między różnych regionów skojarzonych z Twoim kontem Cosmos.

## <a name="consistency-levels-and-throughput"></a>Poziomy spójności i przepływności

- Na tę samą liczbę jednostek żądania sesja, spójny prefiks i poziomy spójności ostatecznej zapewnienia około 2 X odczytu przepływność w porównaniu z silną i powiązana nieaktualność.

- Dla danego typu operacji zapisu, takich jak insert, replace, upsert, usuwanie, itp. przepływność zapisu dla jednostek żądania jest taka sama dla wszystkich poziomów spójności.

## <a name="next-steps"></a>Kolejne kroki

Następnie możesz dowiedzieć się więcej o globalnej dystrybucji i wpływ na ogólną spójność w systemach rozproszonych za pomocą następujących artykułach:

* [Wady i zalety spójności w nowoczesnych rozproszoną bazę danych, projektowanie systemów](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Wysoka dostępność](high-availability.md)
* [Cosmos DB w umowie SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
