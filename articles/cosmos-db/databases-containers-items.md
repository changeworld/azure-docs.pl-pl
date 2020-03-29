---
title: Praca z bazami danych, kontenerami i elementami w usłudze Azure Cosmos DB
description: W tym artykule opisano sposób tworzenia i używania baz danych, kontenerów i elementów w usłudze Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.reviewer: sngun
ms.openlocfilehash: 43a842c3b6d6d421eca4196c7f3facc7876318cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246788"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>Praca z bazami danych, kontenerami i elementami w usłudze Azure Cosmos DB

Po utworzeniu [konta usługi Azure Cosmos DB](account-overview.md) w ramach subskrypcji platformy Azure można zarządzać danymi na koncie, tworząc bazy danych, kontenery i elementy. W tym artykule opisano każdą z tych jednostek. 

Na poniższej ilustracji przedstawiono hierarchię różnych jednostek na koncie usługi Azure Cosmos DB:

![Jednostki kont usługi Azure Cosmos](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Bazy danych usługi Azure Cosmos

Można utworzyć jedną lub wiele baz danych usługi Azure Cosmos w ramach konta. Baza danych jest analogiczna do obszaru nazw. Baza danych jest jednostką zarządzania dla zestawu kontenerów usługi Azure Cosmos. W poniższej tabeli pokazano, jak baza danych usługi Azure Cosmos jest mapowana na różne jednostki specyficzne dla interfejsu API:

| Jednostka usługi Azure Cosmos | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- |
|Baza danych usługi Azure Cosmos | baza danych | Przestrzeń kluczy | baza danych | baza danych | Nie dotyczy |

> [!NOTE]
> Na kontach interfejsu API tabeli podczas tworzenia pierwszej tabeli domyślna baza danych jest automatycznie tworzona na koncie usługi Azure Cosmos.

### <a name="operations-on-an-azure-cosmos-database"></a>Operacje w bazie danych usługi Azure Cosmos

Możesz wchodzić w interakcje z bazą danych usługi Azure Cosmos za pomocą interfejsów API usługi Azure Cosmos, zgodnie z opisem w poniższej tabeli:

| Operacja | Interfejs wiersza polecenia platformy Azure | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- | --- |
|Wyliczaj wszystkie bazy danych| Tak | Tak | Tak (baza danych jest mapowana na przestrzeń kluczy) | Tak | Nie dotyczy | Nie dotyczy |
|Odczyt bazy danych| Tak | Tak | Tak (baza danych jest mapowana na przestrzeń kluczy) | Tak | Nie dotyczy | Nie dotyczy |
|Tworzenie nowej bazy danych| Tak | Tak | Tak (baza danych jest mapowana na przestrzeń kluczy) | Tak | Nie dotyczy | Nie dotyczy |
|Aktualizuj bazę danych| Tak | Tak | Tak (baza danych jest mapowana na przestrzeń kluczy) | Tak | Nie dotyczy | Nie dotyczy |


## <a name="azure-cosmos-containers"></a>Kontenery usługi Azure Cosmos

Kontener usługi Azure Cosmos jest jednostką skalowalności zarówno dla aprowizowanej przepływności i magazynu. Kontener jest podzielony na partycje poziome, a następnie replikowany w wielu regionach. Elementy, które można dodać do kontenera i przepływności, które można aprowizować na nim są automatycznie dystrybuowane między zestaw partycji logicznych na podstawie klucza partycji. Aby dowiedzieć się więcej o partycjonowaniu i kluczach partycji, zobacz [Dane partycji](partition-data.md). 

Podczas tworzenia kontenera usługi Azure Cosmos można skonfigurować przepływność w jednym z następujących trybów:

* **Tryb przepływności aprowizowanej dedykowanej aprowizowanej:** przepływność aprowizowana w kontenerze jest zarezerwowana wyłącznie dla tego kontenera i jest wspierana przez ławy SLA. Aby dowiedzieć się więcej, zobacz [Jak aprowizować przepływność w kontenerze usługi Azure Cosmos.](how-to-provision-container-throughput.md)

* **Tryb przepływności udostępnionej aprowizowanej:** Te kontenery współużytkują aprowizowaną przepływność z innymi kontenerami w tej samej bazie danych (z wyłączeniem kontenerów, które zostały skonfigurowane z dedykowaną aprowizowaną przepływnością). Innymi słowy aprowizowana przepływność w bazie danych jest współużytkowana przez wszystkie kontenery przepływności współużytkowanej. Aby dowiedzieć się więcej, zobacz [Jak aprowizować przepływność w bazie danych usługi Azure Cosmos.](how-to-provision-database-throughput.md)

> [!NOTE]
> Przepływność współużytkowana i dedykowana można skonfigurować tylko podczas tworzenia bazy danych i kontenera. Aby przełączyć się z trybu dedykowanej przepływności do trybu udostępnionej przepływności (i na odwrót) po utworzeniu kontenera, należy utworzyć nowy kontener i zmigrować dane do nowego kontenera. Dane można migrować przy użyciu funkcji kanału informacyjnego usługi Azure Cosmos DB.

Kontener usługi Azure Cosmos można skalować elastycznie, niezależnie od tego, czy tworzysz kontenery przy użyciu dedykowanych lub udostępnionych trybów aprowizowanej przepływności.

Kontener usługi Azure Cosmos jest kontenerem elementów od innych niż schemat. Elementy w kontenerze mogą mieć dowolne schematy. Na przykład element reprezentujący osobę i element reprezentujący samochód można umieścić w *tym samym kontenerze*. Domyślnie wszystkie elementy dodawane do kontenera są automatycznie indeksowane bez konieczności jawnego zarządzania indeksem lub schematem. Zachowanie indeksowania można dostosować, konfigurując [zasady indeksowania](index-overview.md) w kontenerze. 

Można ustawić [czas na czas na żywo (TTL)](time-to-live.md) dla wybranych elementów w kontenerze usługi Azure Cosmos lub dla całego kontenera bezpiecznie przeczyścić te elementy z systemu. Usługa Azure Cosmos DB automatycznie usuwa elementy po ich wygaśnięciu. Gwarantuje również, że kwerenda wykonywana w kontenerze nie zwraca wygasłych elementów w ramach stałej bound. Aby dowiedzieć się więcej, zobacz [Konfigurowanie czasu wygaśnięcia w kontenerze](how-to-time-to-live.md).

Można użyć [źródła danych zmiany,](change-feed.md) aby subskrybować dziennik operacji, który jest zarządzany dla każdej partycji logicznej kontenera. Plik danych można posuwu danych wejściowych dla zmian udostępnia dziennik wszystkich aktualizacji wykonywanych w kontenerze, wraz z obrazami przed i po elementach. Aby uzyskać więcej informacji, zobacz [Tworzenie aplikacji reaktywnych przy użyciu kanału informacyjnego zmian](serverless-computing-database.md). Można również skonfigurować czas przechowywania pliku danych o zmianach przy użyciu zasad pliku danych o zmianach w kontenerze. 

Można zarejestrować [procedury przechowywane, wyzwalacze, funkcje zdefiniowane przez użytkownika (UDFs)](stored-procedures-triggers-udfs.md)i [procedury scalania](how-to-manage-conflicts.md) kontenera usługi Azure Cosmos. 

Można określić [unikatowe ograniczenie klucza](unique-keys.md) w kontenerze usługi Azure Cosmos. Tworząc unikatową zasadę klucza, należy zapewnić unikatowość jednej lub więcej wartości na klucz partycji logicznej. Jeśli tworzysz kontener przy użyciu unikatowych zasad klucza, nie można tworzyć żadnych nowych lub zaktualizowanych elementów z wartościami, które powielają wartości określone przez ograniczenie klucza unikatowego. Aby dowiedzieć się więcej, zobacz [Unikatowe ograniczenia klucza](unique-keys.md).

Kontener usługi Azure Cosmos specjalizuje się w jednostkach specyficznych dla interfejsu API, jak pokazano w poniższej tabeli:

| Jednostka usługi Azure Cosmos | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- |
|Kontener usługi Azure Cosmos | Kontener | Tabela | Collection | Graph | Tabela |

### <a name="properties-of-an-azure-cosmos-container"></a>Właściwości kontenera usługi Azure Cosmos

Kontener usługi Azure Cosmos ma zestaw właściwości zdefiniowanych przez system. W zależności od używanego interfejsu API niektóre właściwości mogą nie być bezpośrednio udostępniane. W poniższej tabeli opisano listę właściwości zdefiniowanych przez system:

| Właściwość zdefiniowana przez system | Generowane przez system lub konfigurowalne przez użytkownika | Przeznaczenie | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Generowane przez system | Unikatowy identyfikator kontenera | Tak | Nie | Nie | Nie | Nie |
|\_Etag | Generowane przez system | Znacznik encji używany do kontroli współbieżności optymistycznej | Tak | Nie | Nie | Nie | Nie |
|\_Ts | Generowane przez system | Ostatnio aktualizowany znacznik czasu kontenera | Tak | Nie | Nie | Nie | Nie |
|\_Własny | Generowane przez system | Adresowalny identyfikator URI kontenera | Tak | Nie | Nie | Nie | Nie |
|id | Konfigurowane przez użytkownika | Zdefiniowana przez użytkownika unikatowa nazwa kontenera | Tak | Tak | Tak | Tak | Tak |
|indeksowaniePolityka | Konfigurowane przez użytkownika | Umożliwia zmianę ścieżki indeksu, typu indeksu i trybu indeksu | Tak | Nie | Nie | Nie | Tak |
|TimeToLive | Konfigurowane przez użytkownika | Umożliwia automatyczne usuwanie elementów z kontenera po upływie określonego czasu. Aby uzyskać szczegółowe informacje, zobacz [Czas na żywo](time-to-live.md). | Tak | Nie | Nie | Nie | Tak |
|zmianaFeedPolicy | Konfigurowane przez użytkownika | Służy do odczytywania zmian wprowadzonych do elementów w kontenerze. Aby uzyskać szczegółowe informacje, zobacz [Zmienianie kanału informacyjnego](change-feed.md). | Tak | Nie | Nie | Nie | Tak |
|uniqueKeyPolicy | Konfigurowane przez użytkownika | Służy do zapewnienia unikatowości jednej lub więcej wartości w partycji logicznej. Aby uzyskać więcej informacji, zobacz [Unikatowe ograniczenia klucza](unique-keys.md). | Tak | Nie | Nie | Nie | Tak |

### <a name="operations-on-an-azure-cosmos-container"></a>Operacje w kontenerze usługi Azure Cosmos

Kontener usługi Azure Cosmos obsługuje następujące operacje podczas korzystania z dowolnego interfejsu API usługi Azure Cosmos:

| Operacja | Interfejs wiersza polecenia platformy Azure | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- | --- |
| Wyliczaj kontenery w bazie danych | Tak | Tak | Tak | Tak | Nie dotyczy | Nie dotyczy |
| Odczytanie kontenera | Tak | Tak | Tak | Tak | Nie dotyczy | Nie dotyczy |
| Tworzenie nowego kontenera | Tak | Tak | Tak | Tak | Nie dotyczy | Nie dotyczy |
| Aktualizowanie kontenera | Tak | Tak | Tak | Tak | Nie dotyczy | Nie dotyczy |
| Usuwanie kontenera | Tak | Tak | Tak | Tak | Nie dotyczy | Nie dotyczy |

## <a name="azure-cosmos-items"></a>Elementy usługi Azure Cosmos

W zależności od używanego interfejsu API element usługi Azure Cosmos może reprezentować dokument w kolekcji, wiersz w tabeli lub węzeł lub krawędź na wykresie. W poniższej tabeli przedstawiono mapowanie jednostek specyficznych dla interfejsu API do elementu usługi Azure Cosmos:

| Jednostka Cosmos | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- |
|Element usługi Azure Cosmos | Dokument | Wiersz | Dokument | Węzeł lub krawędź | Element |

### <a name="properties-of-an-item"></a>Właściwości towaru

Każdy element usługi Azure Cosmos ma następujące właściwości zdefiniowane przez system. W zależności od używanego interfejsu API niektóre z nich mogą nie być bezpośrednio udostępniane.

| Właściwość zdefiniowana przez system | Generowane przez system lub konfigurowalne przez użytkownika| Przeznaczenie | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Generowane przez system | Unikatowy identyfikator elementu | Tak | Nie | Nie | Nie | Nie |
|\_Etag | Generowane przez system | Znacznik encji używany do kontroli współbieżności optymistycznej | Tak | Nie | Nie | Nie | Nie |
|\_Ts | Generowane przez system | Sygnatura czasowa ostatniej aktualizacji elementu | Tak | Nie | Nie | Nie | Nie |
|\_Własny | Generowane przez system | Adresowalny identyfikator URI elementu | Tak | Nie | Nie | Nie | Nie |
|id | Albo | Unikatowa nazwa zdefiniowana przez użytkownika w partycji logicznej. | Tak | Tak | Tak | Tak | Tak |
|Dowolne właściwości zdefiniowane przez użytkownika | Zdefiniowane przez użytkownika | Właściwości zdefiniowane przez użytkownika reprezentowane w reprezentacji natywnej interfejsu API (w tym JSON, BSON i CQL) | Tak | Tak | Tak | Tak | Tak |

> [!NOTE]
> Unikatowość `id` właściwości jest wymuszana tylko w ramach każdej partycji logicznej. Wiele dokumentów może `id` mieć tę samą właściwość z różnymi wartościami klucza partycji.

### <a name="operations-on-items"></a>Operacje na towarach

Elementy usługi Azure Cosmos obsługują następujące operacje. Do wykonania operacji można użyć dowolnego interfejsu API usługi Azure Cosmos.

| Operacja | Interfejs wiersza polecenia platformy Azure | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- | --- |
| Wstawianie, zamienianie, usuwanie, upsert, odczyt | Nie | Tak | Tak | Tak | Tak | Tak |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tych zadaniach i pojęciach:

* [Aprowizowanie przepływności dla bazy danych usługi Azure Cosmos DB](how-to-provision-database-throughput.md)
* [Aprowizowanie przepływności dla kontenera usługi Azure Cosmos](how-to-provision-container-throughput.md)
* [Praca z partycjami logicznymi](partition-data.md)
* [Konfigurowanie czasu wygaśnięcia w kontenerze usługi Azure Cosmos](how-to-time-to-live.md)
* [Tworzenie aplikacji reaktywnych przy użyciu kanału informacyjnego zmian](change-feed.md)
* [Konfigurowanie unikatowego ograniczenia klucza w kontenerze usługi Azure Cosmos](unique-keys.md)
