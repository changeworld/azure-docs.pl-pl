---
title: Dostępność i wydajność kompromisy dla różnych poziomów spójności w usłudze Azure Cosmos DB
description: Dostępność i wydajność kompromisy dla różnych poziomów spójności w usłudze Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 52f08511d16e216ced7e3d1de11eae960cdbaeb8
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2019
ms.locfileid: "54041870"
---
# <a name="availability-and-performance-tradeoffs-for-various-consistency-levels-in-azure-cosmos-db"></a>Dostępność i wydajność kompromisy dla różnych poziomów spójności w usłudze Azure Cosmos DB

Rozproszonych baz danych, które zależą od replikacji dla wysokiej dostępności i małym opóźnieniu musi kompromisy. Skutków ubocznych należą do zakresu od spójności odczytu, a dostępność, opóźnienia i przepływności. 

Wyjaśnienie pojęcia spójności danych jako liczne opcje zbliża się do usługi Azure Cosmos DB. Ta metoda obejmuje więcej opcji niż dwoma skrajnymi poziomami spójności silną i ostateczną. Możesz wybrać z pięciu dobrze zdefiniowanych modeli na spektrum spójności. Od najsilniejszej do najsłabszej, modele są:

- Silna 
- Powiązana nieaktualność 
- Sesja 
- Spójny prefiks 
- Ostateczna 

Każdy model zawiera wpływ na dostępność i wydajność i jest wspierana przez kompleksowe umowy SLA.

## <a name="consistency-levels-and-latency"></a>Poziomy spójności i opóźnienia

- Opóźnienie odczytu dla wszystkich poziomów spójności zawsze może być mniejsza niż 10 milisekund w 99. percentylu. To opóźnienie odczytu są objęte umową SLA. Średnia, opóźnienie w 50. percentyl odczytu jest zwykle 2 milisekund lub mniej. Azure konta usługi Cosmos span kilku regionach, które są skonfigurowane przy użyciu silnej spójności są wyjątkiem od tej gwarancji.

-  Opóźnienie zapisu dla pozostałych poziomów spójności zawsze może być mniejsza niż 10 milisekund w 99. percentylu. To opóźnienie zapisu są objęte umową SLA. Opóźnienie zapisu średnia, na 50. percentyl jest zazwyczaj 5 milisekund lub mniej.

Niektóre konta usługi Azure Cosmos może być kilku regionach skonfigurowane za pomocą silnej spójności. W tym przypadku opóźnienie zapisu może być mniejsza niż dwa razy czas obustronnej konwersji (RTT) plus 10 milisekund w 99. percentylu. RTT między dowolnymi dwoma regionami położony jest skojarzony z Twoim kontem usługi Azure Cosmos. Jest równa RTT między dowolnymi dwoma regionami położony skojarzonych z Twoim kontem usługi Azure Cosmos. Ta opcja jest obecnie w wersji zapoznawczej. 

Dokładny czas oczekiwania RTT jest funkcja odległości szybkość świateł i topologię sieci platformy Azure. Sieci platformy Azure nie zapewnia żadnych opóźnień umowy SLA dla RTT między wszystkie dwóch regionach platformy Azure. Konta usługi Azure Cosmos opóźnienia w replikacji są wyświetlane w witrynie Azure portal. Za pomocą witryny Azure portal do monitorowania opóźnienia w replikacji między różnych regionów, które są skojarzone z Twoim kontem.

## <a name="consistency-levels-and-throughput"></a>Poziomy spójności i przepływności

- Sesja, spójny prefiks i poziomy spójności ostatecznej taką samą liczbę jednostek żądania, podaj około dwa razy to przepływność odczytu w porównaniu z silną i powiązana nieaktualność.

- Dla danego typu operacji zapisu, takich jak insert, replace, upsert i usuwania przepływność zapisu dla jednostek żądania jest taka sama dla wszystkich poziomów spójności.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat dystrybucji globalnej i wpływ na ogólną spójność w systemach rozproszonych. Zobacz następujące artykuły:

* [Wady i zalety spójności w nowoczesnych rozproszoną bazę danych, projektowanie systemów](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Wysoka dostępność](high-availability.md)
* [Azure Cosmos DB w umowie SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
