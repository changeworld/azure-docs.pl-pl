---
title: Globalnie dystrybuowany magazyn transakcyjny i analityczny dla kontenerów usługi Azure Cosmos
description: Dowiedz się więcej o magazynie transakcyjnym i analitycznym oraz ich opcjach konfiguracji dla kontenerów usługi Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: abf222b7a6d6e8fd053fa83c066d2b7850f575ab
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756904"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Globalnie dystrybuowany magazyn transakcyjny i analityczny dla kontenerów usługi Azure Cosmos

Kontener usługi Azure Cosmos jest wewnętrznie obsługiwany przez dwa aparaty magazynu — transakcyjny aparat magazynu i aktualizowalny aparat magazynu analitycznego. Zarówno aparaty magazynu mają strukturę dzienników, jak i są zoptymalizowane pod kątem zapisu w celu przyspieszenia aktualizacji. Jednak każdy z nich jest zakodowany inaczej:

* **Transakcyjny aparat magazynu** — jest zakodowany w formacie zorientowanym na wiersze dla szybkich operacji odczytu i zapytań transakcyjnych.

* **Aparat magazynu analitycznego** — jest on zakodowany w formacie kolumnowym w celu uzyskania szybkich zapytań analitycznych i skanowania.

![Aparaty magazynu i mapowanie interfejsów API Azure Cosmos DB](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

Transakcyjny aparat magazynu jest obsługiwany przez lokalną dysków SSD, podczas gdy magazyn analityczny jest przechowywany w niedrogim magazynie SSD w klastrze. W poniższej tabeli przedstawiono istotne różnice między transakcyjną i analityczną magazynem.


|Funkcja  |Magazyn transakcyjny  |Magazyn analityczny |
|---------|---------|---------|
|Maksymalny rozmiar magazynu na kontener usługi Azure Cosmos |   Bez ograniczeń      |    Bez ograniczeń     |
|Maksymalny rozmiar magazynu na klucz partycji logicznej   |   10 GB      |   Bez ograniczeń      |
|Kodowanie magazynu  |   Zorientowane na wiersze przy użyciu formatu wewnętrznego.   |   Zorientowane na kolumny przy użyciu formatu Apache Parquet. |
|Miejscowość magazynu |   Zreplikowane magazyny obsługiwane przez lokalne/wewnętrzne dysków SSD klastra. |  Replikowany magazyn został poddany niedrogim dysków SSD zdalnego/wyłączenia klastra.       |
|Trwałość  |    99,99999 (7-9 s)     |  99,99999 (7-9 s)       |
|Interfejsy API, które uzyskują dostęp do danych  |   SQL, MongoDB, Cassandra, Gremlin, tabele i Etcd.       | Apache Spark         |
|Przechowywanie (czas wygaśnięcia lub TTL)   |  Oparte na zasadach, skonfigurowane w kontenerze usługi Azure Cosmos za pomocą właściwości `DefaultTimeToLive`.       |   Oparte na zasadach, skonfigurowane w kontenerze usługi Azure Cosmos za pomocą właściwości `ColumnStoreTimeToLive`.      |
|Cena za GB    |   $0,25/GB      |  $0,02/GB       |
|Cena dla transakcji magazynu    | Zainicjowana przepływność jest naliczana na $0,008 za 100 RU/s przy użyciu rozliczeń godzinowych.        |  Przepływność na podstawie zużycia jest naliczana w wysokości $0,05 dla 10 000 transakcji zapisu i $0,004 dla transakcji do odczytu z 10 000.       |

## <a name="benefits-of-transactional-and-analytical-storage"></a>Zalety magazynu transakcyjnego i analitycznego

### <a name="no-etl-operations"></a>Brak operacji ETL

Tradycyjne potoki analityczne są złożone z wieloma etapami, które wymagają operacji extract-transform-load (ETL) do i z warstw obliczeniowych i magazynu. Powoduje to złożone architektury przetwarzania danych. Oznacza to wysokie koszty dotyczące wielu etapów magazynowania i obliczeń oraz duże opóźnienia wynikające z dużych ilości danych przesyłanych między różnymi etapami magazynu i obliczeń.  

Nie ma żadnych nakładów na wykonywanie operacji ETL przy użyciu Azure Cosmos DB. Każdy kontener usługi Azure Cosmos jest obsługiwany przez zarówno sieciowe, jak i analityczne aparaty magazynowe, a transfer danych między sieciowym i analitycznym aparatem magazynu odbywa się w aparacie bazy danych i bez żadnych przeskoków sieci. Wynikowe opóźnienie i koszt są znacznie niższe w porównaniu z tradycyjnymi rozwiązaniami analitycznymi. W przypadku obciążeń transakcyjnych i analitycznych uzyskuje się pojedynczy globalnie rozproszony system przechowywania.  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>Przechowywanie wielu wersji, aktualizowanie i wykonywanie zapytań do magazynu analitycznego

Magazyn analityczny jest w pełni aktualizowalny i zawiera pełną historię wersji wszystkich aktualizacji transakcyjnych, które wystąpiły w kontenerze usługi Azure Cosmos.

Wszystkie aktualizacje w magazynie transakcyjnym są gwarantowane, aby były widoczne dla magazynu analitycznego w ciągu 30 sekund. 

### <a name="globally-distributed-multi-master-analytical-storage"></a>Globalnie dystrybuowany magazyn analityczny z wieloma wzorcami

Jeśli Twoje konto usługi Azure Cosmos ma zakres do jednego regionu, dane przechowywane (w magazynie transakcyjnym i analitycznym) w kontenerach są również objęte zakresem jednego regionu. Z drugiej strony, jeśli konto usługi Azure Cosmos jest dystrybuowane globalnie, dane przechowywane w kontenerach również są dystrybuowane globalnie.

W przypadku kont usługi Azure Cosmos skonfigurowanych z wieloma regionami zapisu operacje zapisu w kontenerze (zarówno w przypadku magazynu transakcyjnego, jak i w magazynie analitycznym) są zawsze wykonywane w regionie lokalnym, a tym samym szybko.

W przypadku kont usługi Azure Cosmos w jednym lub wielu regionach, niezależnie od tego, czy pojedynczy region zapisu (pojedynczy wzorzec) lub wiele regionów zapisu (znany również jako wiele wzorców), operacje transakcyjne i analityczne są wykonywane lokalnie w danym regionie.

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>Izolacja wydajności między obciążeniami transakcyjnymi i analitycznymi

W danym regionie obciążenia transakcyjne działają względem magazynu transakcyjnych/wierszy kontenera. Z drugiej strony obciążenia analityczne działają na magazyn analityczny/kolumnowy kontenera. Dwa silniki magazynu działają niezależnie i zapewniają ścisłą izolację wydajności między obciążeniami.

Obciążenia transakcyjne zużywają zainicjowaną przepływność (jednostek ru). W przeciwieństwie do obciążeń transakcyjnych przepływność obciążeń analitycznych opiera się na rzeczywistym zużyciu. Obciążenia analityczne zużywają zasoby na żądanie.

### <a name="on-demand-snapshots-and-time-travel-analytics"></a>Migawki na żądanie i Analiza ruchu w czasie

W dowolnym momencie można wykonać migawki danych przechowywanych w magazynie analitycznym kontenerów usługi Azure Cosmos, wywołując polecenie `CreateSnapshot (name, timestamp)` w kontenerze. Migawki są nazywane "zakładkami" w historii aktualizacji, które były kiedykolwiek wykonywane w danym kontenerze.

![Migawki na żądanie i Analiza ruchu w czasie](./media/globally-distributed-transactional-analytical-storage/ondemand-analytical-data-snapshots.png)

W momencie tworzenia migawki, oprócz nazwy, można określić sygnaturę czasową, która definiuje stan kontenera w historii aktualizacji. Następnie można załadować dane migawki do platformy Spark i wykonać zapytania.

Obecnie w przypadku kontenera można wykonać tylko migawki na żądanie, możliwość automatycznego tworzenia migawek na podstawie harmonogramu lub zasad niestandardowych nie jest jeszcze obsługiwana.

### <a name="configure-and-tier-data-between-transactional-and-analytical-storage-independently"></a>Konfigurowanie i warstwowe dane między magazynem transakcyjnym i analitycznym niezależnie od siebie

W zależności od danego scenariusza można niezależnie włączać lub wyłączać poszczególne dwa aparaty pamięci masowej. W każdym scenariuszu są następujące konfiguracje:

|Scenariusz |Ustawienie magazynu transakcyjnego  |Ustawienie magazynu analitycznego |
|---------|---------|---------|
|Uruchamianie obciążeń analitycznych na wyłączność (z nieograniczonym przechowywaniem) |  DefaultTimeToLive = 0       |  ColumnStoreTimeToLive =-1       |
|Uruchamianie obciążeń transakcyjnych wyłącznie (z nieograniczonym przechowywaniem)  |   DefaultTimeToLive =-1      |  ColumnStoreTimeToLive = 0       |
|Uruchamianie obciążeń transakcyjnych i analitycznych (z nieograniczonym przechowywaniem)   |   DefaultTimeToLive =-1      | ColumnStoreTimeToLive =-1        |
|Uruchamianie obciążeń transakcyjnych i analitycznych (z różnymi interwałami przechowywania, nazywanymi również warstwami magazynowania)  |  DefaultTimeToLive = <Value1>       |     ColumnStoreTimeToLive = <Value2>    |

1. **Konfigurowanie kontenera wyłącznie dla obciążeń analitycznych (z nieograniczonym przechowywaniem)**

   Kontener usługi Azure Cosmos można skonfigurować wyłącznie na potrzeby obciążeń analitycznych. Ta konfiguracja ma zalety, w której nie jest wymagana opłata za magazyn transakcyjny. Jeśli celem jest użycie kontenera wyłącznie do obciążeń analitycznych, można wyłączyć magazyn transakcyjny przez ustawienie `DefaultTimeToLive` wartość 0 w kontenerze Cosmos. można włączyć magazyn analityczny z nieograniczonym przechowywaniem, ustawiając `ColumnStoreTimeToLive` do-1.

   ![Obciążenia analityczne z nieograniczonym przechowywaniem](./media/globally-distributed-transactional-analytical-storage/analytical-workload-configuration.png)

1. **Konfigurowanie kontenera wyłącznie dla obciążeń transakcyjnych (z nieograniczonym przechowywaniem)**

   Kontener usługi Azure Cosmos można skonfigurować wyłącznie dla obciążeń transakcyjnych. Można wyłączyć magazyn analityczny przez ustawienie `ColumnStoreTimeToLive` wartość 0 w kontenerze i można włączyć magazyn analityczny z nieograniczonym przechowywaniem, ustawiając `DefaultTimeToLive` do-1.

   ![Obciążenia transakcyjne z nieograniczonym przechowywaniem](./media/globally-distributed-transactional-analytical-storage/transactional-workload-configuration.png)

1. **Konfigurowanie kontenera dla obciążeń transakcyjnych i analitycznych (z nieograniczonym przechowywaniem)**

   Można skonfigurować kontener usługi Azure Cosmos dla obciążeń transakcyjnych i analitycznych z pełną izolacją wydajności. Można włączyć magazyn analityczny przez ustawienie `ColumnStoreTimeToLive` wartość-1 i włączenie magazynu transakcyjnego z nieograniczonym przechowywaniem przez ustawienie `DefaultTimeToLive ` do-1.

   ![Obciążenia transakcyjne i analityczne z nieograniczonym przechowywaniem](./media/globally-distributed-transactional-analytical-storage/analytical-transactional-configuration-infinite-retention.png)

1. **Konfigurowanie kontenera dla obciążeń transakcyjnych i analitycznych przy użyciu warstw magazynowania**

   Można skonfigurować kontener usługi Azure Cosmos dla obciążeń transakcyjnych i analitycznych z pełną izolacją wydajności między nimi przy użyciu różnych interwałów przechowywania. Azure Cosmos DB będzie wymuszać, że magazyn analityczny jest zawsze zachowywany przez dłuższy czas niż magazyn transakcyjny.

   Magazyn transakcyjny można włączyć z nieograniczonym przechowywaniem przez ustawienie `DefaultTimeToLive` < wartość 1 > i włączyć magazyn analityczny przez ustawienie `ColumnStoreTimeToLive` na < wartość 2 >. Azure Cosmos DB będzie wymuszać, że < wartość 2 > jest zawsze większa niż < wartość 1 >.

   ![Obciążenia transakcyjne i analityczne z obsługą warstw magazynowania](./media/globally-distributed-transactional-analytical-storage/analytical-transactional-configuration-specified-retention.png)

## <a name="next-steps"></a>Następne kroki

* [Czas wygaśnięcia w Azure Cosmos DB](time-to-live.md)
