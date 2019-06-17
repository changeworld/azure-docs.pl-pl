---
title: Optymalizowanie kosztów odczytuje i zapisuje w usłudze Azure Cosmos DB
description: W tym artykule wyjaśniono, wyjaśnia, jak obniżyć koszty usługi Azure Cosmos DB, podczas przeprowadzania odczytu i zapisu na danych.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: 13ce5ee8b0e2a5d9cc84ea1a408ebba152b46050
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65967408"
---
# <a name="optimize-reads-and-writes-cost-in-azure-cosmos-db"></a>Optymalizacja operacji odczytu i zapisu kosztów w usłudze Azure Cosmos DB

W tym artykule opisano, jak jest obliczana koszt wymaganych do odczytu i zapisu danych z usługi Azure Cosmos DB. Operacje odczytu operacji get na elementach a operacje zapisu insert, replace, delete i upsert elementów.  

## <a name="cost-of-reads-and-writes"></a>Koszt odczyty i zapisy

Usługa Azure Cosmos DB gwarantuje przewidywalną wydajność pod względem przepustowości i opóźnienia, przy użyciu modelu aprowizowanej przepływności. Aprowizowaną przepływność jest reprezentowana w [jednostek żądań](request-units.md) na sekundę lub jednostek RU/s. Jednostki żądań (RU) jest logiczną abstrakcji za pośrednictwem zasobów obliczeniowych, takich jak procesor CPU, pamięć, we/wy, itp., które są wymagane do wykonania żądania. Aprowizowana przepływność (ru) jest zrezygnować z i do kontenera lub bazy danych w celu zapewnienia przewidywalnej przepływności i opóźnień w wersji dedykowanej. Aprowizowana przepływność umożliwia usłudze Azure Cosmos DB zapewniają przewidywalne i spójną wydajność gwarantowane małe opóźnienia i wysoką dostępność w dowolnej skali. Jednostki żądania reprezentują znormalizowane Waluta, która upraszcza logikę, o ile zasobów, aplikacja musi. 

Nie trzeba myśleć o rozróżnienie tych jednostek żądania między operacji odczytu i zapisu. Model ujednoliconego waluty jednostek żądania tworzy efektywność zamiennie używać tego samego przepływność dla odczytów i zapisów. W poniższej tabeli przedstawiono koszt odczyty i zapisuje pod względem jednostek RU/s dla elementów, które są w rozmiarze 1 KB do 100 KB.

|**Rozmiar elementu**  |**Koszt odczytu jeden** |**Koszt jednego zapisu**|
|---------|---------|---------|
|1 KB |1 JEDNOSTKA ŻĄDANIA |5 jednostek ru |
|100 KB |10 jednostek RU |50 (RUS) |

Odczytywanie elementu, który jest 1 KB RU jeden koszty rozmiar. Zapisywanie elementu, który wynosi 1 KB koszty pięć jednostek RU. Koszt odczytu i zapisu są stosowane w przypadku, gdy za pomocą sesji domyślnej [poziomu spójności](consistency-levels.md).  Zagadnienia dotyczące, wokół RUs obejmują: element, rozmiar, liczbę właściwości, spójności danych, właściwości indeksowanych, indeksowanie i wykonuje zapytania wzorców.

## <a name="normalized-cost-for-1-million-reads-and-writes"></a>Znormalizowana koszt 1 miliona operacji odczytu i zapisu

Inicjowanie obsługi administracyjnej 1000 jednostek RU/s przekłada się na 3.6 mln jednostek RU/godz., a koszt 0,08 USD za godzinę (w Stanach Zjednoczonych i Europie). Dla elementu o wielkości 1 KB wykonywać 3.6 mln odczytów i zapisów 0,72 milionów, (Ta wartość jest obliczana jako: `3.6 million RU / 5`) na godzinę za pomocą tego aprowizowanej przepływności. Znormalizowane do miliona operacji odczytu i zapisu, opłata wyniesie $0,022 do 1 mln odczytów (Ta wartość jest obliczana jako: $0.08/3.6 milionów) i 0.111 $ dla 1 mln zapisów (Ta wartość jest obliczana jako: $0.08/0.72 mln).

## <a name="number-of-regions-and-the-request-units-cost"></a>Liczba regionów i jednostek żądania, koszty

Koszt zapisu jest stałe niezależnie od tego, przez liczbę regionów skojarzonych z kontem usługi Azure Cosmos. Innymi słowy zapis 1 KB koszt pięć jednostek żądania, które są niezależne od przez liczbę regionów skojarzonych z kontem. Jest trywialny ilość zasobów w replikacji, akceptowania i przetwarzania ruch związany z replikacją w każdym regionie. Aby uzyskać szczegółowe informacje o wielu regionów, w optymalizacji kosztów, zobacz [optymalizacji kosztów multiregionalne konta usługi Cosmos](optimize-cost-regions.md) artykułu.

## <a name="optimize-the-cost-of-writes-and-reads"></a>Optymalizuj koszt zapisu i odczytu

Podczas wykonywania operacji zapisu, należy udostępnić możliwości wystarczające do obsługi liczbę operacji zapisu, potrzebne na sekundę. Możesz zwiększyć przepływność aprowizowana za pomocą zestawu SDK, portalu, interfejsu wiersza polecenia przed wykonaniem zapisu i następnie zmniejszenie przepływności po ukończeniu zapisy. Przepływność dla okresu zapisu jest minimalna przepływność, które są wymagane dla danego danych oraz przepływności wymaganej insert obciążenia przy założeniu, że żadne inne obciążenia jest uruchomiony. 

Jeśli są uruchomione inne obciążenia jednocześnie, na przykład, zapytanie/odczyt/aktualizowanie/usuwanie dodawaj dodatkowych jednostek żądań zbyt wymagane dla tych operacji. Jeśli operacje zapisu są ograniczone szybkość, można dostosować zasady ponawiania/wycofywania, za pomocą zestawów SDK usługi Azure Cosmos DB. Można na przykład zwiększenie obciążenia do momentu mała liczba żądań, które pobiera współczynnik ograniczone. W sytuacji limit szybkości aplikacja kliencka powinna możesz się wycofać na szybkości żądania interwał ponawiania określony. Przed ponowieniem próby zapisu, należy za pomocą minimalnej ilości odstęp czasu między kolejnymi próbami. Obsługa zasad ponawiania prób są objęte SQL .NET, Java, Node.js i Python SDK oraz wszystkie obsługiwane wersje zestawów .NET Core SDK. 

Możesz również zbiorczo wstawiania danych do usługi Azure Cosmos DB lub skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do usługi Azure Cosmos DB przy użyciu [usługi Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). Usługa Azure Data Factory natywnie integruje się z usługi Azure Cosmos DB zbiorcze interfejs API, aby zapewnia najlepszą wydajność, podczas zapisywania danych.

## <a name="next-steps"></a>Kolejne kroki

Następnie możesz przejść do Dowiedz się więcej o optymalizacji kosztów w usłudze Azure Cosmos DB z następujących artykułów:

* Dowiedz się więcej o [Optymalizacja na potrzeby programowania i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [informacje o rachunku Azure Cosmos DB](understand-your-bill.md)
* Dowiedz się więcej o [optymalizacji kosztów przepustowości](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizacji kosztów magazynowania](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizacji kosztów zapytań](optimize-cost-queries.md)
* Dowiedz się więcej o [optymalizacji kosztów kont usługi Azure Cosmos w wielu regionach](optimize-cost-regions.md)
