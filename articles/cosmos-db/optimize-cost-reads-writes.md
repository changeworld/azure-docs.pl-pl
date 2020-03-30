---
title: Optymalizacja kosztów odczytów i zapisów w usłudze Azure Cosmos DB
description: W tym artykule wyjaśniono, jak zmniejszyć koszty usługi Azure Cosmos DB podczas wykonywania operacji odczytu i zapisu na danych.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 725876594a7e7c5f3b3a02802f487dc5fdfb64dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535939"
---
# <a name="optimize-reads-and-writes-cost-in-azure-cosmos-db"></a>Optymalizacja kosztów odczytów i zapisów w usłudze Azure Cosmos DB

W tym artykule opisano sposób obliczania kosztów wymaganych do odczytu i zapisu danych z usługi Azure Cosmos DB. Operacje odczytu obejmują operacje get na elementach i operacje zapisu obejmują wstawianie, zamienianie, usuwanie i upsert elementów.  

## <a name="cost-of-reads-and-writes"></a>Koszt odczytów i zapisów

Usługa Azure Cosmos DB gwarantuje przewidywalną wydajność pod względem przepływności i opóźnienia przy użyciu modelu aprowizowanej przepływności. Aprowizowana przepływność jest reprezentowana w kategoriach [jednostek żądań](request-units.md) na sekundę lub jednostek RU/s. Jednostka żądania (RU) jest logiczną abstrakcją zasobów obliczeniowych, takich jak procesor, pamięć, we/wy itp., które są wymagane do wykonania żądania. Aprowizowana przepływność (RUs) jest odłożona i dedykowana do kontenera lub bazy danych, aby zapewnić przewidywalną przepływność i opóźnienie. Aprowizowana przepływność umożliwia usługi Azure Cosmos DB, aby zapewnić przewidywalną i spójną wydajność, gwarantowane małe opóźnienia i wysoką dostępność w dowolnej skali. Jednostki żądań reprezentują znormalizowaną walutę, która upraszcza rozumowanie dotyczące liczby zasobów, których potrzebuje aplikacja. 

Nie musisz myśleć o rozróżnianiu jednostek żądań między odczytami i zapisami. Ujednolicony model waluty jednostek żądań tworzy wydajność, aby zamiennie używać tej samej przepustowości dla odczytów i zapisów. W poniższej tabeli przedstawiono koszt odczytów i zapisów w kategoriach RU/s dla elementów o rozmiarze 1 KB i 100 KB.

|**Rozmiar przedmiotu**  |**Koszt jednego odczytu** |**Koszt jednego zapisu**|
|---------|---------|---------|
|1 KB |1 ru |5 r.r. |
|100 KB |10 jednostek RU |50 r./ |

Odczyt elementu o rozmiarze 1 KB kosztuje jedną ru. Pisanie elementu o masie 1 KB kosztuje pięć procesorów RU. Koszty odczytu i zapisu mają zastosowanie w przypadku korzystania z domyślnego [poziomu spójności](consistency-levels.md)sesji .  Zagadnienia dotyczące obiektów typu rUs obejmują: rozmiar elementu, liczbę właściwości, spójność danych, właściwości indeksowane, indeksowanie i wzorce zapytań.

## <a name="optimize-the-cost-of-writes-and-reads"></a>Optymalizacja kosztów zapisów i odczytów

Podczas wykonywania operacji zapisu, należy aprowizować wystarczającą pojemność do obsługi liczby zapisów wymaganych na sekundę. Można zwiększyć aprowizowaną przepływność przy użyciu SDK, portal, cli przed wykonaniem zapisów, a następnie zmniejszyć przepływność po zakończeniu zapisów. Przepływność dla okresu zapisu jest minimalną przepływnością wymaganą dla danych, a także przepływnością wymaganą do wstawiania obciążenia przy założeniu, że nie są uruchomione żadne inne obciążenia. 

Jeśli używasz innych obciążeń jednocześnie, na przykład query/read/update/delete, należy dodać dodatkowe jednostki żądań wymagane dla tych operacji. Jeśli operacje zapisu są ograniczone szybkością, można dostosować zasady ponawiania/wycofywania przy użyciu zestawów SDK usługi Azure Cosmos DB. Na przykład można zwiększyć obciążenie, dopóki niewielka liczba żądań nie zostanie ograniczona. Jeśli wystąpi limit szybkości, aplikacja kliencka powinna wycofać się z żądań ograniczających szybkość dla określonego interwału ponawiania. Przed ponowną próbą zapisu, powinien mieć minimalną ilość odstępu czasu między ponownych prób. Obsługa zasad ponawiania próby jest uwzględniona w pakietach SDK SQL .NET, Java, Node.js i Python oraz we wszystkich obsługiwanych wersjach pakietów SDK .NET Core. 

Można również zbiorczo wstawiać dane do usługi Azure Cosmos DB lub kopiować dane z dowolnego obsługiwanego magazynu danych źródłowych do usługi Azure Cosmos DB przy użyciu [usługi Azure Data Factory.](../data-factory/connector-azure-cosmos-db.md) Usługa Azure Data Factory natywnie integruje się z masowym interfejsem API usługi Azure Cosmos DB, aby zapewnić najlepszą wydajność podczas pisania danych.

## <a name="next-steps"></a>Następne kroki

Następnie możesz dowiedzieć się więcej o optymalizacji kosztów w usłudze Azure Cosmos DB, aby uzyskać następujące artykuły:

* Dowiedz się więcej o [optymalizacji pod kątem rozwoju i testowania](optimize-dev-test.md)
* Dowiedz się więcej o [opisie rachunku za usługę Azure Cosmos DB](understand-your-bill.md)
* Dowiedz się więcej o [optymalizacji kosztów przepływności](optimize-cost-throughput.md)
* Dowiedz się więcej o [optymalizacji kosztów magazynowania](optimize-cost-storage.md)
* Dowiedz się więcej o [optymalizacji kosztów zapytań](optimize-cost-queries.md)
* Dowiedz się więcej o [optymalizowaniu kosztów wieloregionowych kont usługi Azure Cosmos](optimize-cost-regions.md)
