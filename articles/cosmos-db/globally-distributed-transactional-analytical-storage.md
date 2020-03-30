---
title: Globalnie rozproszone magazyny transakcyjne i analityczne (w prywatnej wersji zapoznawczej) dla kontenerów usługi Azure Cosmos
description: Dowiedz się więcej o magazynie transakcyjnym i analitycznym oraz ich opcjach konfiguracji kontenerów usługi Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 1c2b79f8d0641b1a1386329a2add14ded766bf5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623393"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Globalnie rozproszony magazyn transakcyjny i analityczny dla kontenerów usługi Azure Cosmos

Kontener usługi Azure Cosmos jest wewnętrznie wspierany przez dwa aparaty magazynu — aparat magazynu transakcyjnego i aktualizowany aparat magazynu analitycznego (w prywatnej wersji zapoznawczej). Oba aparaty magazynu są ustrukturyzowane i zoptymalizowane pod kątem zapisu, aby uzyskać szybsze aktualizacje. Jednak każdy z nich jest zakodowany inaczej:

* **Aparat magazynu transakcyjnego** — jest zakodowany w formacie zorientowanym na wiersze dla szybkich odczytów transakcyjnych i zapytań.

* **Analityczny aparat pamięci masowej** — jest zakodowany w formacie kolumnowym dla szybkich zapytań analitycznych i skanowania.

![Aparaty pamięci masowej i mapowanie interfejsu API usługi Azure Cosmos DB](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

Transakcyjny aparat magazynowania jest wspierany przez lokalne dyski SSD, podczas gdy magazyn analityczny jest przechowywany w niedrogim magazynie SSD poza klastrem. W poniższej tabeli przedstawiono istotne różnice między magazynem transakcyjnym a analitycznym.


|Funkcja  |Magazyn transakcyjny  |Magazyn analityczny |
|---------|---------|---------|
|Maksymalna ilość miejsca do magazynowania w kontenerze usługi Azure Cosmos |   Unlimited (nieograniczony)      |    Unlimited (nieograniczony)     |
|Maksymalna ilość miejsca do magazynowania na klucz partycji logicznej   |   20 GB      |   Unlimited (nieograniczony)      |
|Kodowanie magazynu  |   Zorientowane na wiersze, przy użyciu formatu wewnętrznego.   |   Zorientowane na kolumny, przy użyciu formatu Apache Parquet. |
|Lokalizacja magazynu |   Replikowana pamięć masowa wspierana przez dyski SSD lokalne/wewnątrz klastra. |  Replikowana pamięć masowa poparta niedrogimi dyskami SSD zdalnie/poza klastrem.       |
|Trwałość  |    99.99999 (7-9 s)     |  99.99999 (7-9 s)       |
|Interfejsy API uzyskujące dostęp do danych  |   SQL, MongoDB, Cassandra, Gremlin, Tabele i etcd.       | Apache Spark         |
|Retencja (czas wygaśnięcia lub czas wygaśnięcia)   |  Oparte na zasadach, skonfigurowane w kontenerze usługi Azure Cosmos przy użyciu `DefaultTimeToLive` właściwości.       |   Oparte na zasadach, skonfigurowane w kontenerze usługi Azure Cosmos przy użyciu `ColumnStoreTimeToLive` właściwości.      |
|Cena za GB    |   Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/)     |   Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/)        |
|Cena za transakcje magazynu    |  Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/)         |   Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/)        |

## <a name="benefits-of-transactional-and-analytical-storage"></a>Korzyści z przechowywania transakcyjnego i analitycznego

### <a name="no-etl-operations"></a>Brak operacji ETL

Tradycyjne potoki analityczne są złożone z wieloma etapami, z których każdy wymaga operacji wyodrębniania i przekształcania (ETL) do i z warstw obliczeniowych i pamięci masowej. Skutkuje to złożonymi architekturami przetwarzania danych. Oznacza to wysokie koszty wielu etapów przechowywania i obliczeń oraz duże opóźnienia spowodowane ogromnymi ilościami danych przesyłanych między różnymi etapami przechowywania i obliczeń.  

Nie ma żadnych narzutów na wykonywanie operacji ETL z usługi Azure Cosmos DB. Każdy kontener usługi Azure Cosmos jest wspierany przez aparaty magazynu transakcyjnego i analitycznego, a transfer danych między aparatem magazynu transakcyjnego i analitycznego odbywa się w ramach aparatu bazy danych i bez przeskoków sieciowych. Wynikające z tego opóźnienia i koszty są znacznie niższe w porównaniu z tradycyjnymi rozwiązaniami analitycznymi. Otrzymujesz jeden globalnie rozproszony system pamięci masowej zarówno dla obciążeń transakcyjnych, jak i analitycznych.  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>Przechowywanie wielu wersji, aktualizowanie i wykonywanie zapytań o magazyn analityczny

Magazyn analityczny jest w pełni aktualizowane i zawiera pełną historię wersji wszystkich aktualizacji transakcyjnych, które wystąpiły w kontenerze usługi Azure Cosmos.

Każda aktualizacja dokonana w magazynie transakcyjnym jest gwarantowana, aby była widoczna dla magazynu analitycznego w ciągu 30 sekund. 

### <a name="globally-distributed-multi-master-analytical-storage"></a>Globalnie rozproszona, wielowymierna pamięć analityczna

Jeśli twoje konto usługi Azure Cosmos jest ograniczone do jednego regionu, dane przechowywane (w magazynie transakcyjnym i analitycznym) w kontenerach jest również ograniczony do jednego regionu. Z drugiej strony, jeśli konto usługi Azure Cosmos jest dystrybuowane globalnie, dane przechowywane w kontenerach są również dystrybuowane globalnie.

W przypadku kont usługi Azure Cosmos skonfigurowanych z wieloma regionami zapisu zapisy w kontenerze (zarówno do magazynu transakcyjnego, jak i analitycznego) są zawsze wykonywane w regionie lokalnym i dlatego są szybkie.

Dla jedno- lub wieloregionalnych kont usługi Azure Cosmos, niezależnie od tego, czy pojedynczy region zapisu (pojedynczy wzorzec) lub wiele regionów zapisu (znany również jako multi-master), zarówno transakcyjnych i analitycznych odczytów/kwerend są wykonywane lokalnie w danym regionie.

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>Izolacja wydajności między obciążeniami transakcyjnymi i analitycznymi

W danym regionie obciążeń transakcyjnych działają na kontenerze magazynu transakcyjnego/wiersza. Z drugiej strony obciążeń analitycznych działają na dysku magazynu analitycznego/kolumnowego kontenera. Dwa aparaty magazynowania działają niezależnie i zapewniają ścisłą izolację wydajności między obciążeniami.

Obciążenia transakcyjne zużywają aprowizowaną przepływność (RU). W przeciwieństwie do obciążeń transakcyjnych przepływność obciążeń analitycznych opiera się na rzeczywistym zużyciu. Obciążenia analityczne zużywają zasoby na żądanie.

## <a name="next-steps"></a>Następne kroki

* [Czas życia w usłudze Azure Cosmos DB](time-to-live.md)
