---
title: Praca z bazy danych Azure Cosmos DB, kontenery i elementów
description: W tym artykule opisano sposób tworzenia i używania baz danych i kontenerów usługi Azure Cosmos DB, a także elementy
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 8eaca83b7ea89737a63fe56a18505c8df7e93fdc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60889390"
---
# <a name="work-with-databases-containers-and-items"></a>Praca z bazami danych, kontenerami i elementami

Po utworzeniu [konta usługi Azure Cosmos](account-overview.md) w ramach subskrypcji platformy Azure możesz zarządzać danymi na Twoim koncie, tworząc baz danych, kontenerów i elementów. W tym artykule opisano każdy z tych jednostek: baz danych, kontenerów i elementów. Na poniższej ilustracji przedstawiono hierarchię różnych obiektów na koncie usługi Azure Cosmos:

![Jednostki konta usługi Azure Cosmos](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Baz danych Azure Cosmos

W ramach konta usługi można utworzyć jeden lub więcej baz danych Azure Cosmos. Baza danych jest analogiczne do przestrzeni nazw. Jest to jednostka zarządzania zbiór kontenerów w usłudze Azure Cosmos. W poniższej tabeli przedstawiono, jak bazy danych Azure Cosmos jest mapowany na różnymi jednostkami specyficzne dla interfejsu API:

| **Jednostki usługi Azure Cosmos** | **INTERFEJS API SQL** | **Interfejs API rozwiązania Cassandra** | **Interfejs API usługi Azure Cosmos DB, bazy danych mongodb** | **Interfejs API języka gremlin** | **Interfejs API tabel** |
| --- | --- | --- | --- | --- | --- |
|Bazy danych usługi Azure Cosmos | Database (Baza danych) | Przestrzeń kluczy | Database (Baza danych) | Database (Baza danych) | Nie dotyczy |

> [!NOTE]
> Za pomocą kont usługi interfejsu API tabel podczas tworzenia pierwszej tabeli, domyślna baza danych jest tworzony automatycznie w ramach konta usługi Azure Cosmos.

### <a name="operations-on-an-azure-cosmos-database"></a>Operacje w bazie danych Azure Cosmos

Interaktywnie bazy danych Azure Cosmos, za pomocą interfejsów API usługi Azure Cosmos w następujący sposób:

| **Operacja** | **Interfejs wiersza polecenia platformy Azure**|**INTERFEJS API SQL** | **Interfejs API rozwiązania Cassandra** | **Interfejs API usługi Azure Cosmos DB, bazy danych mongodb** | **Interfejs API języka gremlin** | **Interfejs API tabel** |
| --- | --- | --- | --- | --- | --- | --- |
|Wyliczanie wszystkich baz danych| Yes | Yes | Tak (bazy danych jest zamapowana na przestrzeń kluczy) | Yes | Nie dotyczy | Nie dotyczy |
|Baza danych odczytu| Yes | Yes | Tak (bazy danych jest zamapowana na przestrzeń kluczy) | Yes | Nie dotyczy | Nie dotyczy |
|Utwórz nową bazę danych| Yes | Yes | Tak (bazy danych jest zamapowana na przestrzeń kluczy) | Yes | Nie dotyczy | Nie dotyczy |
|Aktualizuj bazę danych| Yes | Yes | Tak (bazy danych jest zamapowana na przestrzeń kluczy) | Yes | Nie dotyczy | Nie dotyczy |


## <a name="azure-cosmos-containers"></a>Kontenery usługi Azure Cosmos

Kontener usługi Azure Cosmos jest jednostką skalowalności magazynu i aprowizowanej przepływności. Kontener jest podzielona na partycje w poziomie i następnie replikowane w wielu regionach. Elementy, które dodajesz do kontenera i przepływność, które można aprowizować w nim są automatycznie rozpraszane określonym zestawie partycji logicznej na podstawie klucza partycji. Aby dowiedzieć się więcej na temat partycjonowania i klucze partycji, zobacz [to](partition-data.md) artykułu. 

Podczas tworzenia kontenera usługi Azure Cosmos, należy skonfigurować przepływność w jednym z następujących trybów:

* **Dedykowana przepływność aprowizowana** trybu: Przepływność aprowizowana w kontenerze jest zastrzeżone wyłącznie dla tego kontenera i jest ona objęta umów SLA. Aby dowiedzieć się więcej, zobacz [jak aprowizować przepływność na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).

* **Udostępnione aprowizowanej przepływności** trybu: Te kontenery udostępniać aprowizowanej przepływności innych kontenerów w tej samej bazy danych (z wyjątkiem tych kontenerów, które zostały skonfigurowane za pomocą dedykowanego aprowizowana przepływność). Innymi słowy aprowizowaną przepływność w bazie danych jest udostępniany wszystkie kontenery "udostępnionej przepływności". Aby dowiedzieć się więcej, zobacz [sposobu konfigurowania aprowizowaną przepływność na bazę danych Azure Cosmos](how-to-provision-database-throughput.md).

Kontener usługi Azure Cosmos można elastycznie skalować, tworzenie kontenerów przy użyciu albo "udostępnione" lub "dedykowane" aprowizowana przepływność tryby.

Kontener usługi Azure Cosmos jest kontenerem niezależnej od schematu elementów. Elementy w kontenerze mogą mieć dowolną schematów. Na przykład element reprezentujący osoby, element reprezentujący samochodów można umieścić w *ten sam kontener*. Domyślnie wszystkie elementy, które dodajesz do kontenera, Uzyskaj automatycznie indeksowane i bez wymagania żadnych jawnych indeksu ani zarządzania schematami. Można dostosować zachowanie indeksowania, konfigurując [zasad indeksowania](index-overview.md) w kontenerze. 

Możesz ustawić [czas wygaśnięcia (TTL)](time-to-live.md) dla wybranych elementów w kontenerze usługi Azure Cosmos lub dla całego kontenera bezpiecznie przeczyścić te elementy z systemu. Usługa Azure Cosmos DB automatycznie spowoduje usunięcie elementów, gdy wygasają. Gwarantuje również, zapytanie wykonana w kontenerze nie zwraca elementów wygasłych w obrębie stałej powiązane z. Aby dowiedzieć się więcej, zobacz [jak skonfigurować czas wygaśnięcia w kontenerze](how-to-time-to-live.md).

Za pomocą [kanału informacyjnego zmian](change-feed.md), możesz zasubskrybować dziennika operacji, która jest zarządzana w przypadku każdego z partycjami logicznymi kontenera. Kanału informacyjnego zmian zawiera dziennik wszystkie aktualizacje, które są wykonywane w kontenerze wraz z przed i po obrazów elementów. Zobacz [sposób kompilowania aplikacji reaktywnie przy użyciu kanału informacyjnego zmian](serverless-computing-database.md). Można również skonfigurować okres przechowywania dla zmiany źródła danych, za pomocą zestawienia zasad w kontenerze zmian. 

Możesz zarejestrować [procedury składowane, wyzwalacze, funkcje zdefiniowane przez użytkownika (UDF)](stored-procedures-triggers-udfs.md) i [scalania procedury](how-to-manage-conflicts.md) z kontenera usługi Azure Cosmos. 

Można określić [unikatowego ograniczenia klucza](unique-keys.md) w kontenerze usługi Azure Cosmos. Tworząc zasady unikatowych kluczy, możesz zapewnić unikatowość co najmniej jedną wartość na klucz partycji logicznej. Po utworzeniu kontenera za pomocą zasady unikatowych kluczy uniemożliwia tworzenie nowych lub zaktualizowanych elementów z wartościami, które duplikują wartości określonych przez unikatowe ograniczenie klucza. Aby dowiedzieć się więcej, zobacz [unikatowych ograniczeń klucza](unique-keys.md).

Kontener usługi Azure Cosmos jest przeznaczone do jednostek specyficzne dla interfejsu API:

| **Jednostki usługi Azure Cosmos** | **INTERFEJS API SQL** | **Interfejs API rozwiązania Cassandra** | **Interfejs API usługi Azure Cosmos DB, bazy danych mongodb** | **Interfejs API języka gremlin** | **Interfejs API tabel** |
| --- | --- | --- | --- | --- | --- |
|Usługa Azure container Cosmos | Collection | Tabela | Collection | Graph | Tabela |

### <a name="properties-of-an-azure-cosmos-container"></a>Właściwości kontenera usługi Azure Cosmos

Kontener usługi Azure Cosmos ma zestaw właściwości zdefiniowane przez system. W zależności od wybranego interfejsu API niektóre z nich mogą nie być bezpośrednio widoczne. W poniższej tabeli opisano listy właściwości zdefiniowanych w systemie:

| **Właściwości zdefiniowane przez system** | **System wygenerowany lub konfigurowanych przez użytkownika** | **Cel** | **INTERFEJS API SQL** | **Interfejs API rozwiązania Cassandra** | **Interfejs API usługi Azure Cosmos DB, bazy danych mongodb** | **Interfejs API języka gremlin** | **Interfejs API tabel** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|_rid | Generowane przez system | Unikatowy identyfikator kontenera | Yes | Nie | Nie | Nie | Nie |
|_etag | Generowane przez system | Tag jednostki używane do mechanizmu kontroli optymistycznej współbieżności | Yes | Nie | Nie | Nie | Nie |
|_ts | Generowane przez system | Znacznik czasu ostatniej aktualizacji kontenera | Yes | Nie | Nie | Nie | Nie |
|_self | Generowane przez system | Mogą być adresowane identyfikator URI kontenera | Yes | Nie | Nie | Nie | Nie |
|id | Użytkownika można konfigurować | Zdefiniowane przez użytkownika unikatową nazwę kontenera | Yes | Yes | Yes | Yes | Yes |
|indexingPolicy | Użytkownika można konfigurować | Pozwala zmienić ścieżkę indeksu, typ indeksu i tryb indeksu. | Yes | Nie | Nie | Nie | Yes |
|TimeToLive | Użytkownika można konfigurować | Umożliwia automatyczne usuwanie elementów z kontenera po określonym okresie czasu. Aby uzyskać więcej informacji, zobacz [Time To Live](time-to-live.md) artykułu. | Yes | Nie | Nie | Nie | Yes |
|changeFeedPolicy | Użytkownika można konfigurować | Używane do odczytywania zmiany wprowadzone do elementów w kontenerze. Aby uzyskać więcej informacji, zobacz [kanału informacyjnego zmian](change-feed.md) artykułu. | Yes | Nie | Nie | Nie | Yes |
|uniqueKeyPolicy | Użytkownika można konfigurować | Można zapewnić unikatowość co najmniej jednej wartości w ramach partycji logicznej. Aby uzyskać więcej informacji, zobacz [ograniczenia Unique Key](unique-keys.md) artykułu. | Yes | Nie | Nie | Nie | Yes |

### <a name="operations-on-an-azure-cosmos-container"></a>Operacji na kontenerze usługi Azure Cosmos

Kontener usługi Azure Cosmos obsługuje następujące operacje przy użyciu dowolnej z interfejsów API usługi Azure Cosmos.

| **Operacja** | **Interfejs wiersza polecenia platformy Azure** | **INTERFEJS API SQL** | **Interfejs API rozwiązania Cassandra** | **Interfejs API usługi Azure Cosmos DB, bazy danych mongodb** | **Interfejs API języka gremlin** | **Interfejs API tabel** |
| --- | --- | --- | --- | --- | --- | --- |
| Wyliczanie kontenerów w bazie danych | Yes | Yes | Yes | Yes | Nie dotyczy | Nie dotyczy |
| Przeczytaj kontenera | Yes | Yes | Yes | Yes | Nie dotyczy | Nie dotyczy |
| Utwórz nowy kontener | Yes | Yes | Yes | Yes | Nie dotyczy | Nie dotyczy |
| Aktualizacja kontenera | Yes | Yes | Yes | Yes | Nie dotyczy | Nie dotyczy |
| Usuwanie kontenera | Yes | Yes | Yes | Yes | Nie dotyczy | Nie dotyczy |

## <a name="azure-cosmos-items"></a>Usługa Azure Cosmos elementów

W zależności od wybranego interfejsu API element Azure Cosmos może reprezentować dokument w kolekcji, jeden wiersz w tabeli lub węzeł/przewagę na wykresie. W poniższej tabeli przedstawiono mapowania między jednostkami specyficzne dla interfejsu API dla elementu w usłudze Azure Cosmos:

| **Jednostka cosmos** | **INTERFEJS API SQL** | **Interfejs API rozwiązania Cassandra** | **Interfejs API usługi Azure Cosmos DB, bazy danych mongodb** | **Interfejs API języka gremlin** | **Interfejs API tabel** |
| --- | --- | --- | --- | --- | --- |
|Usługa Azure Cosmos elementu | Dokument | Wiersz | Dokument | Węzła lub krawędzi | Element |

### <a name="properties-of-an-item"></a>Właściwości elementu

Każdy element w usłudze Azure Cosmos ma następujące właściwości zdefiniowane przez system. W zależności od wybranego interfejsu API niektóre z nich mogą nie być bezpośrednio widoczne.

|**Właściwości zdefiniowane przez system** | **System wygenerowany lub konfigurowanych przez użytkownika**| **Cel** | **INTERFEJS API SQL** | **Interfejs API rozwiązania Cassandra** | **Interfejs API usługi Azure Cosmos DB, bazy danych mongodb** | **Interfejs API języka gremlin** | **Interfejs API tabel** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|_identyfikator | Generowane przez system | Unikatowy identyfikator elementu | Yes | Nie | Nie | Nie | Nie |
|_etag | Generowane przez system | Tag jednostki używane do mechanizmu kontroli optymistycznej współbieżności | Yes | Nie | Nie | Nie | Nie |
|_ts | Generowane przez system | Sygnatura czasowa ostatniej aktualizacji elementu | Yes | Nie | Nie | Nie | Nie |
|_self | Generowane przez system | Mogą być adresowane identyfikator URI elementu | Yes | Nie | Nie | Nie | Nie |
|id | Albo | Zdefiniowane przez użytkownika unikatową nazwę w ramach partycji logicznej. Jeśli użytkownik nie określono Identyfikatora, system wygeneruje ją automatycznie. | Yes | Yes | Yes | Yes | Yes |
|Dowolne właściwości zdefiniowanych przez użytkownika | Zdefiniowane przez użytkownika | Zdefiniowane przez użytkownika właściwości, które są reprezentowane w reprezentacji natywnego interfejsu API (JSON, BSON, języka CQL itp.) | Yes | Yes | Yes | Yes | Yes |

### <a name="operations-on-items"></a>Operacje na elementach

Usługa Azure Cosmos elementu obsługuje następujące operacje, które mogą być wykonywane przy użyciu dowolnej z interfejsów API usługi Azure Cosmos.

| **Operacja** | **Interfejs wiersza polecenia platformy Azure** | **INTERFEJS API SQL** | **Interfejs API rozwiązania Cassandra** | **Interfejs API usługi Azure Cosmos DB, bazy danych mongodb** | **Interfejs API języka gremlin** | **Interfejs API tabel** |
| --- | --- | --- | --- | --- | --- | --- |
| Wstaw, Zastąp i usuwanie, Upsert, przeczytaj | Nie | Yes | Yes | Yes | Yes | Yes |

## <a name="next-steps"></a>Kolejne kroki

Teraz można przystąpić do następujące pojęcia:

* [Jak skonfigurować aprowizowaną przepływność na bazie danych Azure Cosmos](how-to-provision-database-throughput.md)
* [Jak skonfigurować aprowizowaną przepływność na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md)
* [Partycjami logicznymi](partition-data.md)
* [Jak skonfigurować czas wygaśnięcia w kontenerze usługi Azure Cosmos](how-to-time-to-live.md)
* [Sposób tworzenia aplikacji reaktywnie przy użyciu kanału informacyjnego zmian](change-feed.md)
* [Jak skonfigurować unikatowe ograniczenie klucza w kontenerze usługi Azure Cosmos](unique-keys.md)
