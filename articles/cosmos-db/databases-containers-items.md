---
title: Pracuj z bazami danych, kontenerami i elementami w Azure Cosmos DB
description: W tym artykule opisano sposób tworzenia i używania baz danych, kontenerów i elementów w programie Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.reviewer: sngun
ms.openlocfilehash: 43a842c3b6d6d421eca4196c7f3facc7876318cd
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78387012"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>Pracuj z bazami danych, kontenerami i elementami w Azure Cosmos DB

Po utworzeniu [konta Azure Cosmos DB](account-overview.md) w ramach subskrypcji platformy Azure Możesz zarządzać danymi na swoim koncie przez tworzenie baz danych, kontenerów i elementów. W tym artykule opisano każdą z tych jednostek. 

Na poniższej ilustracji przedstawiono hierarchię różnych jednostek w ramach konta Azure Cosmos DB:

![Jednostki konta usługi Azure Cosmos](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Bazy danych usługi Azure Cosmos

Na koncie można utworzyć jedną lub wiele baz danych usługi Azure Cosmos. Baza danych jest analogiczna do przestrzeni nazw. Baza danych to jednostka zarządzania dla zestawu kontenerów usługi Azure Cosmos. W poniższej tabeli przedstawiono, jak bazy danych Azure Cosmos jest mapowany na różnymi jednostkami specyficzne dla interfejsu API:

| Jednostka usługi Azure Cosmos | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- |
|Bazy danych usługi Azure Cosmos | Baza danych | Przestrzeń kluczy | Baza danych | Baza danych | Nie dotyczy |

> [!NOTE]
> Przy tworzeniu pierwszej tabeli przy użyciu kont interfejs API tabel domyślna baza danych zostanie automatycznie utworzona na koncie usługi Azure Cosmos.

### <a name="operations-on-an-azure-cosmos-database"></a>Operacje w bazie danych Azure Cosmos

Możesz korzystać z usługi Azure Cosmos Database za pomocą interfejsów API usługi Azure Cosmos, zgodnie z opisem w poniższej tabeli:

| Operacja | Interfejs wiersza polecenia platformy Azure | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- | --- |
|Wyliczanie wszystkich baz danych| Yes | Yes | Tak (bazy danych jest zamapowana na przestrzeń kluczy) | Yes | Nie dotyczy | Nie dotyczy |
|Baza danych odczytu| Yes | Yes | Tak (bazy danych jest zamapowana na przestrzeń kluczy) | Yes | Nie dotyczy | Nie dotyczy |
|Utwórz nową bazę danych| Yes | Yes | Tak (bazy danych jest zamapowana na przestrzeń kluczy) | Yes | Nie dotyczy | Nie dotyczy |
|Aktualizuj bazę danych| Yes | Yes | Tak (bazy danych jest zamapowana na przestrzeń kluczy) | Yes | Nie dotyczy | Nie dotyczy |


## <a name="azure-cosmos-containers"></a>Kontenery usługi Azure Cosmos

Kontener usługi Azure Cosmos jest jednostką skalowalności dla zainicjowanej przepływności i magazynu. Kontener jest podzielona na partycje w poziomie i następnie replikowane w wielu regionach. Elementy dodawane do kontenera i przepływność, które można na nim udostępnić, są automatycznie dystrybuowane w zestawie partycji logicznych na podstawie klucza partycji. Aby dowiedzieć się więcej na temat partycjonowania i kluczy partycji, zobacz [dane partycji](partition-data.md). 

Podczas tworzenia kontenera usługi Azure Cosmos należy skonfigurować przepływność w jednym z następujących trybów:

* **Dedykowany Tryb przepływności**: przepustowość zainicjowana w kontenerze jest zarezerwowana wyłącznie dla tego kontenera i jest obsługiwana przez umowy SLA. Aby dowiedzieć się więcej, zobacz [jak zainicjować przepływność na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).

* **Udostępniony Tryb przepływności**: te kontenery udostępniają przepływność administracyjną z innymi kontenerami w tej samej bazie danych (z wyjątkiem kontenerów, które zostały skonfigurowane z dedykowaną elastyczną przepustowością). Innymi słowy, zainicjowana przepływność w bazie danych jest współużytkowana przez wszystkie kontenery "udostępnione przepływność". Aby dowiedzieć się więcej, zobacz [jak zainicjować przepływność w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).

> [!NOTE]
> Można skonfigurować udostępnioną i dedykowaną przepływność tylko podczas tworzenia bazy danych i kontenera. Aby przełączyć się z trybu dedykowanej przepływności do trybu udostępnionej przepływności (i na odwrót) po utworzeniu kontenera, należy utworzyć nowy kontener i zmigrować dane do nowego kontenera. Dane można migrować za pomocą funkcji Azure Cosmos DB ze źródłem zmian.

Kontener usługi Azure Cosmos może być elastycznie skalowany, niezależnie od tego, czy tworzysz kontenery za pomocą dedykowanych, czy udostępnionych trybów przepływności.

Kontener usługi Azure Cosmos jest kontenerem niezależnej od schematu elementów. Elementy w kontenerze mogą zawierać dowolne schematy. Na przykład element, który reprezentuje osobę i element reprezentujący samochód, można umieścić w tym *samym kontenerze*. Domyślnie wszystkie elementy dodawane do kontenera są automatycznie indeksowane bez konieczności jawnego indeksowania lub zarządzania schematem. Można dostosować zachowanie indeksowania przez skonfigurowanie [zasad indeksowania](index-overview.md) w kontenerze. 

Możesz ustawić [czas wygaśnięcia (TTL)](time-to-live.md) dla wybranych elementów w kontenerze usługi Azure Cosmos lub dla całego kontenera, aby bezpiecznie przeczyścić te elementy z systemu. Azure Cosmos DB automatycznie usuwa elementy po ich wygaśnięciu. Gwarantuje również, że zapytanie wykonywane na kontenerze nie zwraca elementów wygasłych w ramach ustalonego powiązania. Aby dowiedzieć się więcej, zobacz [Konfigurowanie czasu wygaśnięcia w kontenerze](how-to-time-to-live.md).

Możesz użyć [kanału informacyjnego zmiany](change-feed.md) , aby subskrybować dziennik operacji, który jest zarządzany dla każdej partycji logicznej kontenera. Kanał informacyjny zmiany zapewnia dziennik wszystkich aktualizacji wykonanych w kontenerze wraz z obrazami przed i po. Aby uzyskać więcej informacji, zobacz [Tworzenie reaktywnych aplikacji przy użyciu źródła zmian](serverless-computing-database.md). Możesz również skonfigurować czas przechowywania dla kanału informacyjnego zmiany za pomocą zasad źródła zmian w kontenerze. 

Można rejestrować [procedury składowane, wyzwalacze, funkcje zdefiniowane przez użytkownika (UDF)](stored-procedures-triggers-udfs.md)i [procedury scalania](how-to-manage-conflicts.md) dla kontenera usługi Azure Cosmos. 

Możesz określić [unikatowe ograniczenie klucza](unique-keys.md) w kontenerze usługi Azure Cosmos. Tworząc zasady unikatowych kluczy, możesz zapewnić unikatowość co najmniej jedną wartość na klucz partycji logicznej. W przypadku utworzenia kontenera przy użyciu unikatowych zasad kluczy nie można utworzyć nowych lub zaktualizowanych elementów z wartościami, które duplikują wartości określone przez ograniczenie UNIQUE Key. Aby dowiedzieć się więcej, zobacz [unikalne ograniczenia klucza](unique-keys.md).

Kontener usługi Azure Cosmos jest wyspecjalizowany w jednostkach specyficznych dla interfejsu API, jak pokazano w poniższej tabeli:

| Jednostka usługi Azure Cosmos | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- |
|Usługa Azure container Cosmos | Kontener | Tabela | Collection | Graph | Tabela |

### <a name="properties-of-an-azure-cosmos-container"></a>Właściwości kontenera usługi Azure Cosmos

Kontener usługi Azure Cosmos ma zestaw właściwości zdefiniowanych przez system. W zależności od używanego interfejsu API niektóre właściwości mogą nie być bezpośrednio uwidocznione. W poniższej tabeli opisano listę właściwości zdefiniowanych przez system:

| Właściwość zdefiniowana przez system | Generowane przez system lub użytkownik — konfigurowalne | Przeznaczenie | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_RID | Generowane przez system | Unikatowy identyfikator kontenera | Yes | Nie | Nie | Nie | Nie |
|\_ETag | Generowane przez system | Tag jednostki używane do mechanizmu kontroli optymistycznej współbieżności | Yes | Nie | Nie | Nie | Nie |
|\_TS | Generowane przez system | Znacznik czasu ostatniej aktualizacji kontenera | Yes | Nie | Nie | Nie | Nie |
|\_siebie | Generowane przez system | Mogą być adresowane identyfikator URI kontenera | Yes | Nie | Nie | Nie | Nie |
|id | Użytkownik — konfigurowalne | Zdefiniowane przez użytkownika unikatową nazwę kontenera | Yes | Yes | Yes | Yes | Yes |
|indexingPolicy | Użytkownik — konfigurowalne | Zapewnia możliwość zmiany ścieżki indeksu, typu indeksu i trybu indeksowania | Yes | Nie | Nie | Nie | Yes |
|TimeToLive | Użytkownik — konfigurowalne | Zapewnia możliwość automatycznego usuwania elementów z kontenera po upływie określonego czasu. Aby uzyskać szczegółowe informacje, zobacz [Time to Live](time-to-live.md). | Yes | Nie | Nie | Nie | Yes |
|changeFeedPolicy | Użytkownik — konfigurowalne | Używane do odczytywania zmiany wprowadzone do elementów w kontenerze. Aby uzyskać szczegółowe informacje, zobacz [Zmienianie źródła danych](change-feed.md). | Yes | Nie | Nie | Nie | Yes |
|uniqueKeyPolicy | Użytkownik — konfigurowalne | Służy do zapewnienia unikatowości jednej lub więcej wartości w partycji logicznej. Aby uzyskać więcej informacji, zobacz [unikalne ograniczenia klucza](unique-keys.md). | Yes | Nie | Nie | Nie | Yes |

### <a name="operations-on-an-azure-cosmos-container"></a>Operacji na kontenerze usługi Azure Cosmos

Kontener usługi Azure Cosmos obsługuje następujące operacje, gdy używasz dowolnego z interfejsów API platformy Azure Cosmos:

| Operacja | Interfejs wiersza polecenia platformy Azure | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- | --- |
| Wyliczanie kontenerów w bazie danych | Yes | Yes | Yes | Yes | Nie dotyczy | Nie dotyczy |
| Przeczytaj kontenera | Yes | Yes | Yes | Yes | Nie dotyczy | Nie dotyczy |
| Utwórz nowy kontener | Yes | Yes | Yes | Yes | Nie dotyczy | Nie dotyczy |
| Aktualizowanie kontenera | Yes | Yes | Yes | Yes | Nie dotyczy | Nie dotyczy |
| Usuwanie kontenera | Yes | Yes | Yes | Yes | Nie dotyczy | Nie dotyczy |

## <a name="azure-cosmos-items"></a>Usługa Azure Cosmos elementów

W zależności od używanego interfejsu API element platformy Azure Cosmos może reprezentować dokument w kolekcji, wiersz w tabeli lub węzeł lub krawędź wykresu. W poniższej tabeli przedstawiono mapowanie jednostek specyficznych dla interfejsu API do elementu usługi Azure Cosmos:

| Jednostka Cosmos | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- |
|Usługa Azure Cosmos elementu | Dokument | wiersz | Dokument | Węzeł lub krawędź | Element |

### <a name="properties-of-an-item"></a>Właściwości elementu

Każdy element usługi Azure Cosmos ma następujące właściwości zdefiniowane przez system. W zależności od używanego interfejsu API niektóre z nich mogą nie być bezpośrednio uwidocznione.

| Właściwość zdefiniowana przez system | Generowane przez system lub użytkownik — konfigurowalne| Przeznaczenie | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_RID | Generowane przez system | Unikatowy identyfikator elementu | Yes | Nie | Nie | Nie | Nie |
|\_ETag | Generowane przez system | Tag jednostki używane do mechanizmu kontroli optymistycznej współbieżności | Yes | Nie | Nie | Nie | Nie |
|\_TS | Generowane przez system | Sygnatura czasowa ostatniej aktualizacji elementu | Yes | Nie | Nie | Nie | Nie |
|\_siebie | Generowane przez system | Mogą być adresowane identyfikator URI elementu | Yes | Nie | Nie | Nie | Nie |
|id | Albo | Unikatowa nazwa zdefiniowana przez użytkownika w partycji logicznej. | Yes | Yes | Yes | Yes | Yes |
|Dowolne właściwości zdefiniowanych przez użytkownika | Zdefiniowane przez użytkownika | Właściwości zdefiniowane przez użytkownika reprezentowane w reprezentacji natywnej interfejsu API (w tym JSON, BSON i CQL) | Yes | Yes | Yes | Yes | Yes |

> [!NOTE]
> Unikatowość właściwości `id` jest wymuszana tylko w obrębie każdej partycji logicznej. Wiele dokumentów może mieć taką samą `id` właściwość z różnymi wartościami klucza partycji.

### <a name="operations-on-items"></a>Operacje na elementach

Elementy usługi Azure Cosmos obsługują następujące operacje. Aby wykonać operacje, można użyć dowolnego interfejsu API usługi Azure Cosmos.

| Operacja | Interfejs wiersza polecenia platformy Azure | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- | --- |
| Wstaw, Zastąp i usuwanie, Upsert, przeczytaj | Nie | Yes | Yes | Yes | Yes | Yes |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat tych zadań i koncepcji:

* [Inicjowanie obsługi przepływności w bazie danych Azure Cosmos](how-to-provision-database-throughput.md)
* [Obsługa przepływności w kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md)
* [Pracuj z partycjami logicznymi](partition-data.md)
* [Konfigurowanie czasu wygaśnięcia w kontenerze usługi Azure Cosmos](how-to-time-to-live.md)
* [Tworzenie aplikacji reaktywnych za pomocą kanału zmiany](change-feed.md)
* [Konfigurowanie ograniczenia UNIQUE Key w kontenerze usługi Azure Cosmos](unique-keys.md)
