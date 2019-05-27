---
title: Praca z bazami danych, kontenerów i elementów w usłudze Azure Cosmos DB
description: W tym artykule opisano sposób tworzenia i używania baz danych, kontenerów i elementów w usłudze Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 7d607b4370d51ea2605fae6543bd3336853b0806
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954209"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>Praca z bazami danych, kontenerów i elementów w usłudze Azure Cosmos DB

Po utworzeniu [konta usługi Azure Cosmos DB](account-overview.md) w ramach subskrypcji platformy Azure możesz zarządzać danymi na Twoim koncie, tworząc baz danych, kontenerów i elementów. W tym artykule opisano każdy z tych jednostek. 

Na poniższej ilustracji przedstawiono hierarchię różnych obiektów na koncie usługi Azure Cosmos DB:

![Jednostki konta usługi Azure Cosmos](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Baz danych Azure Cosmos

W ramach konta usługi można utworzyć jeden lub wiele baz danych Azure Cosmos. Baza danych jest analogiczne do przestrzeni nazw. Baza danych jest jednostką zarządzania w zestaw kontenerów w usłudze Azure Cosmos. W poniższej tabeli przedstawiono, jak bazy danych Azure Cosmos jest mapowany na różnymi jednostkami specyficzne dla interfejsu API:

| Azure Cosmos entity | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- |
|Bazy danych usługi Azure Cosmos | Database (Baza danych) | Przestrzeń kluczy | Database (Baza danych) | Database (Baza danych) | Nie dotyczy |

> [!NOTE]
> Za pomocą kont usługi interfejsu API tabeli usługi podczas tworzenia pierwszej tabeli, domyślna baza danych są tworzone na Twoim koncie usługi Azure Cosmos.

### <a name="operations-on-an-azure-cosmos-database"></a>Operacje w bazie danych Azure Cosmos

Możesz porozmawiać z bazą danych Azure Cosmos za pomocą interfejsów API usługi Azure Cosmos zgodnie z opisem w poniższej tabeli:

| Operacja | Interfejs wiersza polecenia platformy Azure | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- | --- |
|Wyliczanie wszystkich baz danych| Yes | Yes | Tak (bazy danych jest zamapowana na przestrzeń kluczy) | Yes | Nie dotyczy | Nie dotyczy |
|Baza danych odczytu| Yes | Yes | Tak (bazy danych jest zamapowana na przestrzeń kluczy) | Yes | Nie dotyczy | Nie dotyczy |
|Utwórz nową bazę danych| Yes | Yes | Tak (bazy danych jest zamapowana na przestrzeń kluczy) | Yes | Nie dotyczy | Nie dotyczy |
|Aktualizuj bazę danych| Yes | Yes | Tak (bazy danych jest zamapowana na przestrzeń kluczy) | Yes | Nie dotyczy | Nie dotyczy |


## <a name="azure-cosmos-containers"></a>Kontenery usługi Azure Cosmos

Kontener usługi Azure Cosmos jest jednostką skalowalności, zarówno za aprowizowaną przepływność i magazyn. Kontener jest podzielona na partycje w poziomie i następnie replikowane w wielu regionach. Elementy, które dodajesz do kontenera i przepływność, które można aprowizować w nim są automatycznie rozpraszane określonym zestawie partycji logicznej na podstawie klucza partycji. Aby dowiedzieć się więcej na temat partycjonowania i klucze partycji, zobacz [partycjonowanie danych](partition-data.md). 

Podczas tworzenia kontenera usługi Azure Cosmos, należy skonfigurować przepływność w jednym z następujących trybów:

* **Tryb dedykowana przepływność aprowizowana**: Przepływność aprowizowana w kontenerze jest zastrzeżone wyłącznie dla tego kontenera i jest ona objęta umów SLA. Aby dowiedzieć się więcej, zobacz [jak aprowizować przepływność na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).

* **Tryb udostępnionego aprowizowanej przepływności**: Te kontenery udostępniać aprowizowanej przepływności innych kontenerów w tej samej bazy danych (z wyjątkiem kontenerów, które zostały skonfigurowane za pomocą dedykowanego aprowizowana przepływność). Innymi słowy aprowizowaną przepływność w bazie danych jest udostępniany wszystkie kontenery "udostępnionej przepływności". Aby dowiedzieć się więcej, zobacz [jak aprowizować przepływność mierzoną w bazie danych Azure Cosmos](how-to-provision-database-throughput.md).

Kontener usługi Azure Cosmos można skalować elastycznie, czy tworzyć kontenery przy użyciu tryby dedykowanej lub współdzielonej aprowizowanej przepływności.

Kontener usługi Azure Cosmos jest kontenerem niezależnej od schematu elementów. Elementy w kontenerze mogą mieć dowolną schematów. Na przykład elementu, który reprezentuje osobę i elementu, który reprezentuje samochodów mogą być umieszczane w *ten sam kontener*. Domyślnie wszystkie elementy, które dodajesz do kontenera są automatycznie indeksowane bez konieczności jawnego indeksu lub Zarządzanie schematami. Można dostosować zachowanie indeksowania, konfigurując [zasad indeksowania](index-overview.md) w kontenerze. 

Możesz ustawić [czas wygaśnięcia (TTL)](time-to-live.md) dla wybranych elementów w kontenerze usługi Azure Cosmos lub dla całego kontenera bezpiecznie przeczyścić te elementy z systemu. Usługa Azure Cosmos DB automatycznie usuwa elementy, gdy wygasają. Gwarantuje również, że zapytanie wykonana w kontenerze nie zwraca elementów wygasłych w obrębie stałej powiązane z. Aby dowiedzieć się więcej, zobacz [Konfigurowanie czasu wygaśnięcia na kontenerze](how-to-time-to-live.md).

Możesz użyć [zestawienia zmian](change-feed.md) do subskrybowania do dziennika operacji, który jest zarządzany, dla każdej partycji logicznej kontenera. Kanał informacyjny zmian zapewnia dziennika wszystkie aktualizacje, które są wykonywane w kontenerze, wraz z przed i po nim obrazów elementów. Aby uzyskać więcej informacji, zobacz [twórz reaktywne aplikacje za pomocą zestawienia zmian](serverless-computing-database.md). Można również skonfigurować okres przechowywania dla zmiany źródła danych za pomocą zestawienia zasad w kontenerze zmian. 

Możesz zarejestrować [procedury składowane, wyzwalacze, funkcje zdefiniowane przez użytkownika (UDF)](stored-procedures-triggers-udfs.md), i [scalania procedury](how-to-manage-conflicts.md) dla kontenera usługi Azure Cosmos. 

Można określić [unikatowego ograniczenia klucza](unique-keys.md) w kontenerze usługi Azure Cosmos. Tworząc zasady unikatowych kluczy, możesz zapewnić unikatowość co najmniej jedną wartość na klucz partycji logicznej. Jeśli utworzysz kontener za pomocą zasady unikatowych kluczy, można utworzyć nowych lub zaktualizowanych elementów z wartościami, które duplikują wartości określonych przez unikatowe ograniczenie klucza. Aby dowiedzieć się więcej, zobacz [unikatowych ograniczeń klucza](unique-keys.md).

Kontener usługi Azure Cosmos jest przeznaczone do jednostek specyficzne dla interfejsu API, jak pokazano w poniższej tabeli:

| Azure Cosmos entity | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- |
|Usługa Azure container Cosmos | Kolekcja | Tabela | Collection | Graph | Tabela |

### <a name="properties-of-an-azure-cosmos-container"></a>Właściwości kontenera usługi Azure Cosmos

Kontener usługi Azure Cosmos ma zestaw właściwości zdefiniowane przez system. Zależności od tego, w których możesz użyć interfejsu API niektóre właściwości mogą nie być bezpośrednio widoczne. W poniższej tabeli opisano listy właściwości zdefiniowanych w systemie:

| Właściwości zdefiniowane przez system | Wygenerowana przez system lub z możliwością konfiguracji użytkownika | Przeznaczenie | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_id | System-generated | Unikatowy identyfikator kontenera | Yes | Nie | Nie | Nie | Nie |
|\_Element etag | System-generated | Tag jednostki używane do mechanizmu kontroli optymistycznej współbieżności | Yes | Nie | Nie | Nie | Nie |
|\_usług terminalowych | System-generated | Znacznik czasu ostatniej aktualizacji kontenera | Yes | Nie | Nie | Nie | Nie |
|\_self | System-generated | Mogą być adresowane identyfikator URI kontenera | Yes | Nie | Nie | Nie | Nie |
|identyfikator | User-configurable | Zdefiniowane przez użytkownika unikatową nazwę kontenera | Yes | Yes | Yes | Yes | Yes |
|indexingPolicy | User-configurable | Pozwala zmienić ścieżkę indeksu, typ indeksu i tryb indeksu | Tak | Nie | Nie | Nie | Yes |
|TimeToLive | User-configurable | Umożliwia automatyczne usuwanie elementów z kontenera po ustawionym okresie. Aby uzyskać więcej informacji, zobacz [czas wygaśnięcia](time-to-live.md). | Tak | Nie | Nie | Nie | Yes |
|changeFeedPolicy | User-configurable | Używane do odczytywania zmiany wprowadzone do elementów w kontenerze. Aby uzyskać więcej informacji, zobacz [zestawienia zmian](change-feed.md). | Tak | Nie | Nie | Nie | Yes |
|uniqueKeyPolicy | User-configurable | Można zapewnić unikatowość co najmniej jednej wartości w partycji logicznej. Aby uzyskać więcej informacji, zobacz [unikatowych ograniczeń klucza](unique-keys.md). | Tak | Nie | Nie | Nie | Yes |

### <a name="operations-on-an-azure-cosmos-container"></a>Operacji na kontenerze usługi Azure Cosmos

Kontener usługi Azure Cosmos obsługuje następujące operacje przy użyciu jednej z interfejsów API usługi Azure Cosmos:

| Operacja | Interfejs wiersza polecenia platformy Azure | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- | --- |
| Wyliczanie kontenerów w bazie danych | Tak | Yes | Yes | Yes | Nie dotyczy | Nie dotyczy |
| Przeczytaj kontenera | Yes | Yes | Yes | Yes | Nie dotyczy | Nie dotyczy |
| Utwórz nowy kontener | Tak | Yes | Yes | Yes | Nie dotyczy | Nie dotyczy |
| Aktualizacja kontenera | Tak | Yes | Yes | Yes | Nie dotyczy | Nie dotyczy |
| Usuwanie kontenera | Tak | Yes | Yes | Yes | Nie dotyczy | Nie dotyczy |

## <a name="azure-cosmos-items"></a>Usługa Azure Cosmos elementów

Zależności od tego, w których możesz użyć interfejsu API element Azure Cosmos może reprezentować albo dokumentów w kolekcji, wiersz w tabeli, lub węzła lub krawędzi na wykresie. W poniższej tabeli przedstawiono mapowania jednostek specyficzne dla interfejsu API dla elementu w usłudze Azure Cosmos:

| Jednostka cosmos | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- |
|Usługa Azure Cosmos elementu | Dokument | Wiersz | Dokument | Węzła lub krawędzi | Element |

### <a name="properties-of-an-item"></a>Właściwości elementu

Każdy element Azure Cosmos ma następujące właściwości zdefiniowane przez system. Zależności od tego, w których możesz użyć interfejsu API niektóre z nich mogą nie być bezpośrednio widoczne.

| Właściwości zdefiniowane przez system | Wygenerowana przez system lub z możliwością konfiguracji użytkownika| Przeznaczenie | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_id | System-generated | Unikatowy identyfikator elementu | Tak | Nie | Nie | Nie | Nie |
|\_Element etag | System-generated | Tag jednostki używane do mechanizmu kontroli optymistycznej współbieżności | Yes | Nie | Nie | Nie | Nie |
|\_usług terminalowych | System-generated | Sygnatura czasowa ostatniej aktualizacji elementu | Tak | Nie | Nie | Nie | Nie |
|\_self | System-generated | Mogą być adresowane identyfikator URI elementu | Yes | Nie | Nie | Nie | Nie |
|id | Albo | Zdefiniowane przez użytkownika unikatową nazwą w partycji logicznej. Jeśli użytkownik nie określono Identyfikatora, system automatycznie generuje go. | Tak | Yes | Yes | Yes | Yes |
|Dowolne właściwości zdefiniowanych przez użytkownika | Zdefiniowane przez użytkownika | Zdefiniowane przez użytkownika właściwości, które są reprezentowane w reprezentacji natywnego interfejsu API (w tym JSON, BSON i języka CQL) | Tak | Yes | Yes | Yes | Yes |

### <a name="operations-on-items"></a>Operacje na elementach

Elementy usługi Azure Cosmos obsługuje następujące operacje. Do wykonania czynności, można użyć dowolnego z interfejsów API usługi Azure Cosmos.

| Operacja | Interfejs wiersza polecenia platformy Azure | Interfejs API SQL | Interfejs API rozwiązania Cassandra | Interfejs API usługi Azure Cosmos DB dla bazy danych MongoDB | Interfejs API języka Gremlin | Interfejs API tabel |
| --- | --- | --- | --- | --- | --- | --- |
| Wstaw, Zastąp i usuwanie, Upsert, przeczytaj | Nie | Yes | Yes | Yes | Yes | Yes |

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o tych zadaniach i pojęć:

* [Aprowizowanie przepływności na bazie danych Azure Cosmos](how-to-provision-database-throughput.md)
* [Aprowizowanie przepływności na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md)
* [Praca z partycjami logicznymi](partition-data.md)
* [Konfigurowanie czasu wygaśnięcia na kontenerze usługi Azure Cosmos](how-to-time-to-live.md)
* [Twórz reaktywne aplikacje za pomocą zestawienia zmian](change-feed.md)
* [Skonfiguruj ograniczenia unique kluczy w kontenerze usługi Azure Cosmos](unique-keys.md)
