---
title: Praca z bazy danych Azure Cosmos DB, kontenery i elementów
description: W tym artykule opisano sposób tworzenia i używania baz danych i kontenerów usługi Azure Cosmos DB, a także elementy
author: dharmas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: d834b7f43d961400e2d5080a46cf921d719f3393
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684862"
---
# <a name="working-with-azure-cosmos-databases-containers-and-items"></a>Praca z baz danych Azure Cosmos, kontenery i elementów

Po utworzeniu [konta usługi Azure Cosmos DB](account-overview.md) w ramach subskrypcji platformy Azure możesz zarządzać danymi na Twoim koncie, tworząc baz danych, kontenerów i elementów. W tym artykule opisano każdy z tych jednostek: baz danych, kontenerów i elementów. Na poniższej ilustracji przedstawiono hierarchię różnych obiektów na koncie usługi Azure Cosmos:

![Jednostki konta usługi Azure Cosmos](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Baz danych Azure Cosmos

W ramach konta usługi można utworzyć jeden lub więcej baz danych Azure Cosmos. Baza danych jest analogiczne do przestrzeni nazw, jest to jednostka zarządzania zbiór kontenerów w usłudze Azure Cosmos. W poniższej tabeli przedstawiono, jak bazy danych Azure Cosmos jest mapowany na różnymi jednostkami specyficzne dla interfejsu API:

| **Jednostki usługi Azure Cosmos** | **INTERFEJS API SQL** | **Interfejs API rozwiązania Cassandra** | **Interfejs API usługi MongoDB** | **Interfejs API języka gremlin** | **Interfejs API tabel** |
| --- | --- | --- | --- | --- | --- |
|Bazy danych usługi Azure Cosmos | Database (Baza danych) | Przestrzeń kluczy | Database (Baza danych) | Nie dotyczy | Nie dotyczy |

> [!NOTE]
> Za pomocą języka Gremlin i interfejsy API tabel kont, podczas tworzenia pierwszego wykresu lub tabeli domyślna baza danych jest tworzony automatycznie w ramach konta usługi Azure Cosmos.

### <a name="operations-on-an-azure-cosmos-database"></a>Operacje w bazie danych Azure Cosmos

Możesz porozmawiać z bazą danych Azure Cosmos przy użyciu następujących interfejsów API usługi Azure Cosmos:

| **Operacja** | **Interfejs wiersza polecenia platformy Azure**|**INTERFEJS API SQL** | **Interfejs API rozwiązania Cassandra** | **Interfejs API usługi MongoDB** | **Interfejs API języka gremlin** | **Interfejs API tabel** |
| --- | --- | --- | --- | --- | --- | --- |
|Wyliczanie wszystkich baz danych| Yes | Yes | Tak (bazy danych jest zamapowana na przestrzeń kluczy) | Yes | Nie dotyczy | Nie dotyczy |
|Baza danych odczytu| Yes | Yes | Tak (bazy danych jest zamapowana na przestrzeń kluczy) | Yes | Nie dotyczy | Nie dotyczy |
|Utwórz nową bazę danych| Yes | Yes | Tak (bazy danych jest zamapowana na przestrzeń kluczy) | Yes | Nie dotyczy | Nie dotyczy |
|Aktualizuj bazę danych| Yes | Yes | Tak (bazy danych jest zamapowana na przestrzeń kluczy) | Yes | Nie dotyczy | Nie dotyczy |


## <a name="azure-cosmos-containers"></a>Kontenery usługi Azure Cosmos

Kontener usługi Azure Cosmos jest jednostką skalowalności zarówno aprowizowaną przepływność i Magazyn elementów. Kontener jest podzielona na partycje w poziomie i następnie replikowane w wielu regionach. Elementy, które dodajesz do kontenera i przepływność, aprowizować w nim są automatycznie rozmieszczane w określonym zestawie partycji logicznej na podstawie klucza partycji. Aby dowiedzieć się więcej na temat partycji i klucza partycji, zobacz [partycjami logicznymi](partition-data.md) artykułu. 

Podczas tworzenia kontenera usługi Azure Cosmos, należy skonfigurować przepływność w jednym z następujących trybów:

* **Dedykowana przepływność aprowizowana** tryb: przepływnością aprowizowaną w kontenerze jest zastrzeżone wyłącznie dla niej i jest ona objęta umów SLA. Aby dowiedzieć się więcej, zobacz [jak aprowizować przepływność na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md).

* **Udostępnione aprowizowanej przepływności** tryb: te kontenery Udostępnianie innych kontenerów w tej samej bazy danych (z wyjątkiem tych kontenerów, które zostały skonfigurowane za pomocą dedykowanego aprowizowanej przepływności) aprowizowanej przepływności. Innymi słowy aprowizowaną przepływność w bazie danych jest udostępniany "udostępnione" kontenerów. Aby dowiedzieć się więcej, zobacz [sposobu konfigurowania aprowizowaną przepływność na bazę danych Azure Cosmos](how-to-provision-database-throughput.md).

Cosmos Azure kontenera można skalować elastycznie, czy tworzyć kontenery z oboma "udostępnione" lub "dedykowane" aprowizowana przepływność tryby, oznacza to, że mają nieograniczony magazyn i aprowizowanej przepływności.  

Kontener usługi Azure Cosmos jest kontenerem niezależnej od schematu elementów. Elementy w kontenerze mogą mieć dowolną schematów. Na przykład element reprezentujący osoby, element reprezentujący samochodów można umieścić w tym samym kontenerze. Domyślnie wszystkie elementy, które dodajesz do kontenera, Uzyskaj automatycznie indeksowane i bez wymagania żadnych jawnych indeksu ani zarządzania schematami. Możesz dostosować zachowanie indeksowania przez skonfigurowanie zasad indeksowania w kontenerze. 

Dla wybranych elementów w kontenerze usługi Azure Cosmos lub dla całego kontenera bezpiecznie przeczyścić te elementy z systemu, można ustawić czasu wygaśnięcia (TTL). Usługa Azure Cosmos DB automatycznie spowoduje usunięcie elementów, gdy wygasają. Gwarantuje również, zapytanie wykonana w kontenerze nie zwraca elementów wygasłych w obrębie stałej powiązane z. Aby dowiedzieć się więcej, zobacz [jak skonfigurować czas wygaśnięcia w kontenerze](how-to-time-to-live.md).

Za pomocą zestawienia zmian, możesz zasubskrybować dziennika operacji, które jest zarządzane dla każdej partycji logicznej kontenera. Kanału informacyjnego zmian zawiera dziennik wszystkie aktualizacje, które są wykonywane w kontenerze wraz z przed i po obrazów elementów. Zobacz [sposób kompilowania aplikacji reaktywnie, przy użyciu zmian źródła danych](change-feed.md). Można również skonfigurować okres przechowywania dla zmiany źródła danych za pomocą zestawienia zasad w kontenerze zmian. 

Można zarejestrować procedury składowane, wyzwalacze, funkcje zdefiniowane przez użytkownika (UDF) i procedur scalania z kontenera usługi Azure Cosmos. 

Można określić unikatowy klucz w kontenerze usługi Azure Cosmos. Tworząc zasady unikatowych kluczy, możesz zapewnić unikatowość co najmniej jedną wartość na klucz partycji logicznej. Po utworzeniu kontenera za pomocą zasady unikatowych kluczy uniemożliwia tworzenie nowych lub zaktualizowanych elementów z wartościami, które duplikują wartości określonych przez unikatowe ograniczenie klucza. Aby dowiedzieć się więcej, zobacz [unikatowych ograniczeń klucza](unique-keys.md).

Kontener usługi Azure Cosmos jest przeznaczone do jednostek specyficzne dla interfejsu API:

| **Jednostki usługi Azure Cosmos** | **INTERFEJS API SQL** | **Interfejs API rozwiązania Cassandra** | **Interfejs API usługi MongoDB** | **Interfejs API języka gremlin** | **Interfejs API tabel** |
| --- | --- | --- | --- | --- | --- |
|Usługa Azure container Cosmos | Kontener | Tabela | Collection | Graph | Tabela |

### <a name="properties-of-an-azure-cosmos-container"></a>Właściwości kontenera usługi Azure Cosmos

Kontener usługi Azure Cosmos ma zestaw właściwości zdefiniowane przez system. W zależności od wybranego interfejsu API niektóre z nich mogą nie być bezpośrednio widoczne. W poniższej tabeli opisano listy właściwości zdefiniowane przez system obsługiwane:

| **Właściwości zdefiniowane przez system** | **System wygenerowany lub do ustawienia użytkownika** | **Cel** | **INTERFEJS API SQL** | **Interfejs API rozwiązania Cassandra** | **Interfejs API usługi MongoDB** | **Interfejs API języka gremlin** | **Interfejs API tabel** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|__rid | Generowane przez system | Unikatowy identyfikator kontenera | Yes | Nie | Nie | Nie | Nie |
|__etag | Generowane przez system | Tag jednostki używane do mechanizmu kontroli optymistycznej współbieżności | Yes | Nie | Nie | Nie | Nie |
|__ts | Generowane przez system | Znacznik czasu ostatniej aktualizacji kontenera | Yes | Nie | Nie | Nie | Nie |
|__self | Generowane przez system | Mogą być adresowane identyfikator URI kontenera | Yes | Nie | Nie | Nie | Nie |
|id | Użytkownika można konfigurować | Zdefiniowane przez użytkownika unikatową nazwę kontenera | Yes | Yes | Yes | Yes | Yes |
|indexingPolicy | Użytkownika można konfigurować | Pozwala zmienić ścieżkę indeksu, ich dokładności i modelu spójności. | Yes | Nie | Nie | Nie | Yes |
|TimeToLive | Użytkownika można konfigurować | Umożliwia automatyczne usuwanie elementów z kontenera po określonym okresie czasu. Aby uzyskać więcej informacji, zobacz [Time To Live](time-to-live.md) artykułu. | Yes | Nie | Nie | Nie | Yes |
|changeFeedPolicy | Użytkownika można konfigurować | Używane do odczytywania zmiany wprowadzone do elementów w kontenerze. Aby uzyskać więcej informacji, zobacz [zestawienia zmian](change-feed.md) artykułu. | Yes | Nie | Nie | Nie | Yes |
|uniqueKeyPolicy | Użytkownika można konfigurować | Z unikatowymi kluczami można zapewnić unikatowość co najmniej jednej wartości w ramach partycji logicznej. Aby uzyskać więcej informacji, zobacz [unikatowe klucze](unique-keys.md) artykułu. | Yes | Nie | Nie | Nie | Yes |

### <a name="operations-on-an-azure-cosmos-container"></a>Operacji na kontenerze usługi Azure Cosmos

Kontener usługi Azure Cosmos obsługuje następujące operacje przy użyciu dowolnej z interfejsów API usługi Azure Cosmos.

| **Operacja** | **Interfejs wiersza polecenia platformy Azure** | **INTERFEJS API SQL** | **Interfejs API rozwiązania Cassandra** | **Interfejs API usługi MongoDB** | **Interfejs API języka gremlin** | **Interfejs API tabel** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Wyliczanie kontenerów w bazie danych | Tak* | Yes | Yes | Yes | Nie dotyczy | Nie dotyczy |
| Przeczytaj kontenera | Yes | Yes | Yes | Yes | Nie dotyczy | Nie dotyczy |
| Utwórz nowy kontener | Yes | Yes | Yes | Yes | Nie dotyczy | Nie dotyczy |
| Aktualizacja kontenera | Yes | Yes | Yes | Yes | Nie dotyczy | Nie dotyczy |
| Usuwanie kontenera | Yes | Yes | Yes | Yes | Nie dotyczy | Nie dotyczy |

## <a name="azure-cosmos-items"></a>Usługa Azure Cosmos elementów

W zależności od wybranego interfejsu API element Azure Cosmos może reprezentować dokument w kolekcji, jeden wiersz w tabeli lub węzeł/przewagę na wykresie. W poniższej tabeli przedstawiono mapowania między jednostkami specyficzne dla interfejsu API dla elementu w usłudze Azure Cosmos:

| **Jednostka cosmos** | **INTERFEJS API SQL** | **Interfejs API rozwiązania Cassandra** | **Interfejs API usługi MongoDB** | **Interfejs API języka gremlin** | **Interfejs API tabel** |
| --- | --- | --- | --- | --- | --- |
|Usługa Azure Cosmos elementu | Element | Wiersz | Dokument | Węzła lub krawędzi | Element |

### <a name="properties-of-an-item"></a>Właściwości elementu

Każdy element w usłudze Azure Cosmos ma następujące właściwości zdefiniowane przez system. W zależności od wybranego interfejsu API niektóre z nich mogą nie być bezpośrednio widoczne.

|**Właściwości zdefiniowane przez system** | **System wygenerowany lub do ustawienia użytkownika**| **Cel** | **INTERFEJS API SQL** | **Interfejs API rozwiązania Cassandra** | **Interfejs API usługi MongoDB** | **Interfejs API języka gremlin** | **Interfejs API tabel** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|__id | Generowane przez system | Unikatowy identyfikator elementu | Yes | Nie | Nie | Nie | Nie |
|__etag | Generowane przez system | Tag jednostki używane do mechanizmu kontroli optymistycznej współbieżności | Yes | Nie | Nie | Nie | Nie |
|__ts | Generowane przez system | Znacznik czasu ostatniej aktualizacji elementu | Yes | Nie | Nie | Nie | Nie |
|__self | Generowane przez system | Mogą być adresowane identyfikator URI elementu | Yes | Nie | Nie | Nie | Nie |
|id | Albo | Zdefiniowane przez użytkownika unikatową nazwę w ramach partycji logicznej. Jeśli użytkownik nie określono identyfikatora, system wygeneruje ją automatycznie. | Yes | Yes | Yes | Yes | Yes |
|Dowolne właściwości zdefiniowanych przez użytkownika | Zdefiniowane przez użytkownika | Zdefiniowane przez użytkownika właściwości, które są reprezentowane w reprezentacji natywnego interfejsu API (JSON, BSON, języka CQL itp.) | Yes | Yes | Yes | Yes | Yes |

### <a name="operations-on-items"></a>Operacje na elementach

Usługa Azure Cosmos elementu obsługuje następujące operacje, które mogą być wykonywane przy użyciu dowolnej z interfejsów API usługi Azure Cosmos.

| **Operacja** | **Interfejs wiersza polecenia platformy Azure** | **INTERFEJS API SQL** | **Interfejs API rozwiązania Cassandra** | **Interfejs API usługi MongoDB** | **Interfejs API języka gremlin** | **Interfejs API tabel** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Wstaw, Zastąp i usuwanie, Upsert, przeczytaj | Nie | Yes | Yes | Yes | Yes | Yes |

## <a name="next-steps"></a>Kolejne kroki

Teraz można przystąpić do Dowiedz się, jak aprowizować przepływność mierzoną na konto usługi Azure Cosmos lub zobacz inne pojęcia:

* [Jak skonfigurować aprowizowanej przepływności w bazie danych Azure Cosmos](how-to-provision-database-throughput.md)
* [Jak skonfigurować aprowizowaną przepływność na kontenerze usługi Azure Cosmos](how-to-provision-container-throughput.md)
* [Partycjami logicznymi](partition-data.md)
* [Jak skonfigurować czas wygaśnięcia w kontenerze usługi Azure Cosmos](how-to-time-to-live.md)
* [Sposób tworzenia aplikacji reaktywnie przy użyciu kanału informacyjnego zmian](change-feed.md)
* [Jak skonfigurować unikatowe ograniczenie klucza w kontenerze usługi Azure Cosmos](unique-keys.md)
