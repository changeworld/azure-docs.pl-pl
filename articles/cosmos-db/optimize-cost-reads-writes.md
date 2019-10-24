---
title: Optymalizacja kosztów odczytu i zapisu w Azure Cosmos DB
description: W tym artykule wyjaśniono, jak zmniejszyć koszty Azure Cosmos DB podczas wykonywania operacji odczytu i zapisu danych.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 934853b80c6e6377923df4c2b5cce7b7d7d57d7c
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754927"
---
# <a name="optimize-reads-and-writes-cost-in-azure-cosmos-db"></a>Optymalizuj operacje odczytu i zapisu w Azure Cosmos DB

W tym artykule opisano, jak koszt wymagany do odczytu i zapisu danych z Azure Cosmos DB jest obliczany. Operacje odczytu obejmują operacje pobrania dla elementów i operacje zapisu obejmują Wstawianie, zastępowanie, usuwanie i upsert elementów.  

## <a name="cost-of-reads-and-writes"></a>Koszt odczytu i zapisu

Azure Cosmos DB gwarantuje przewidywalną wydajność w zakresie przepływności i opóźnienia przy użyciu modelu przepływności aprowizacji. Obsługiwana przepływność jest reprezentowana w zakresie [jednostek żądań](request-units.md) na sekundę lub RU/s. Jednostka żądania (RU) to logiczne Abstrakcja zasobów obliczeniowych, takich jak procesor CPU, pamięć, we/wy itp., które są wymagane do wykonania żądania. Zainicjowana przepływność (jednostek ru) jest oddzielona i przeznaczona dla kontenera lub bazy danych, aby zapewnić przewidywalną przepływność i opóźnienia. Elastyczna przepływność umożliwia Azure Cosmos DB w celu zapewnienia przewidywalnej i spójnej wydajności, gwarantowanych małych opóźnień i wysokiej dostępności w dowolnej skali. Jednostki żądań reprezentują znormalizowaną walutę, która upraszcza powód, ile zasobów potrzebuje aplikacja. 

Nie trzeba myśleć o rozróżnieniu jednostek żądań między operacjami odczytu i zapisu. Ujednolicony model waluty jednostek żądania tworzy efektywność w celu zapewnienia niezmienionego użycia tej samej przepustowości dla operacji odczytu i zapisu. W poniższej tabeli przedstawiono koszty odczytu i zapisu w zakresie RU/s dla elementów, które mają rozmiar 1 KB i 100 KB.

|**Rozmiar elementu**  |**Koszt jednego odczytu** |**Koszt jednego zapisu**|
|---------|---------|---------|
|1 KB |1 RU |5 jednostek ru |
|100 KB |10 jednostek RU |50 jednostek ru |

Odczytywanie elementu o rozmiarze 1 KB jest kosztem jednego RU. Pisanie elementu z 1 KB kosztami pięciu jednostek ru. Koszty odczytu i zapisu są stosowane w przypadku korzystania z domyślnego [poziomu spójności](consistency-levels.md)sesji.  Zagadnienia dotyczące jednostek ru obejmują: rozmiar elementu, liczba właściwości, spójność danych, indeksowane właściwości, indeksowanie i wzorce zapytań.

## <a name="normalized-cost-for-1-million-reads-and-writes"></a>Znormalizowany koszt dla operacji odczytu i zapisu 1 000 000

Inicjowanie obsługi 1 000 RU/s jest przeliczane do 3 600 000 RU/godz. koszt $0,08 jest naliczany przez godzinę (w Stanach Zjednoczonych i Europie). W przypadku elementu o pojemności 1 KB można wykonać 3 600 000 odczytów lub 720 000 zapisów (wartość ta jest obliczana jako `3.6 million RU / 5`) za godzinę przy użyciu tej alokowanej przepływności. Po przeniesieniu do miliona odczytów i zapisów koszt będzie wynosił $0,022 do 1 000 000 operacji odczytu (ta wartość jest obliczana jako: $0,08/3,6 mln) i $0,111 for 1 000 000 (ta wartość jest obliczana jako: $0,08/0,72 mln).

## <a name="number-of-regions-and-the-request-units-cost"></a>Liczba regionów i koszt jednostki żądania

Koszt zapisu jest stałą niezależnie od liczby regionów skojarzonych z kontem usługi Azure Cosmos. Innymi słowy, zapis o wartości 1 KB będzie kosztował pięć jednostek ru niezależnie od liczby regionów skojarzonych z tym kontem. Istnieje nieprosta ilość zasobów poświęcanych na replikowanie, akceptowanie i przetwarzanie ruchu związanego z replikacją w każdym regionie. Aby uzyskać szczegółowe informacje na temat optymalizacji kosztów w ramach wieloregionu, zobacz [Optymalizacja kosztu wieloregionowych kont Cosmos](optimize-cost-regions.md) .

## <a name="optimize-the-cost-of-writes-and-reads"></a>Optymalizowanie kosztów operacji zapisu i odczytu

Podczas wykonywania operacji zapisu należy zapewnić wystarczającą pojemność, aby obsługiwać liczbę operacji zapisu na sekundę. Można zwiększyć przepływność przy użyciu zestawu SDK, portalu, interfejsu wiersza polecenia przed wykonaniem operacji zapisu, a następnie zmniejszyć przepływność po zakończeniu zapisywania. Przepustowość dla okresu zapisu to minimalna przepływność wymagana dla danych oraz przepływność wymagana do wstawiania obciążeń przy założeniu, że żadne inne obciążenia nie są uruchomione. 

Jeśli używasz innych obciążeń współbieżnie, na przykład zapytania/odczytu/aktualizacji/usuwania, należy dodać dodatkowe jednostki żądania wymagane dla tych operacji. Jeśli operacje zapisu są ograniczone proporcjonalnie, można dostosować zasady ponawiania/wycofywania przy użyciu zestawów SDK Azure Cosmos DB. Na przykład można zwiększyć obciążenie do momentu, aż mała częstotliwość żądań uzyska wartość rate-Limited. W przypadku wystąpienia limitu współczynnika aplikacja kliencka powinna wycofać się z żądań ograniczenia szybkości dla określonego interwału ponowień. Przed ponowną próbą zapisu należy mieć minimalny odstęp czasu między ponownymi próbami. Obsługa zasad ponownych prób jest uwzględniana w zestawach SDK programu SQL .NET, Java, Node. js i Python oraz we wszystkich obsługiwanych wersjach zestawów SDK platformy .NET Core. 

Można również zbiorczo wstawiać dane do Azure Cosmos DB lub kopiować dane ze wszystkich obsługiwanych źródłowych magazynów danych do Azure Cosmos DB przy użyciu [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). Azure Data Factory natywnie integruje się z Azure Cosmos DB zbiorczej interfejsu API w celu zapewnienia najlepszej wydajności podczas zapisywania danych.

## <a name="next-steps"></a>Następne kroki

Następnie możesz dowiedzieć się więcej o optymalizacji kosztów w Azure Cosmos DB z następującymi artykułami:

* Dowiedz się więcej [na temat optymalizacji na potrzeby programowania i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [zrozumieniu Azure Cosmos DB rachunku](understand-your-bill.md)
* Dowiedz się więcej na temat [optymalizowania kosztu przepływności](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizowaniu kosztów magazynu](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizowaniu kosztów zapytań](optimize-cost-queries.md)
* Dowiedz się więcej [na temat optymalizowania kosztów kont usługi Azure Cosmos w wielu regionach](optimize-cost-regions.md)
