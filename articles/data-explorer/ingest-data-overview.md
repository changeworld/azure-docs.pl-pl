---
title: Pozyskiwania danych eksploratora danych platformy Azure
description: Dowiedz się więcej o różnych sposobach pozyskiwania (ładowania) danych w Eksploratorze danych platformy Azure
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 4846a19c403cce16bed704ed4e7c70499f3b5d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246398"
---
# <a name="azure-data-explorer-data-ingestion"></a>Pozyskiwania danych eksploratora danych platformy Azure

Pozyskiwanie danych to proces używany do ładowania rekordów danych z jednego lub więcej źródeł w celu utworzenia lub zaktualizowania tabeli w Eksploratorze danych platformy Azure. Po spożyciu dane stają się dostępne dla kwerendy. Na poniższym diagramie przedstawiono przepływ end-to-end do pracy w Eksploratorze danych platformy Azure, w tym pozyskiwania danych.

![Przepływ danych](media/ingest-data-overview/data-flow.png)

Usługa zarządzania danymi usługi Azure Data Explorer, która jest odpowiedzialna za pozyskiwania danych, zapewnia następujące funkcje:

1. **Pobieranie danych:** ściąganie danych ze źródeł zewnętrznych (centra zdarzeń) lub odczytywanie żądań pozyskiwania z kolejki platformy Azure.

1. **Przetwarzanie wsadowe:** Dane partii przepływające do tej samej bazy danych i tabeli w celu optymalizacji przepływności pozyskiwania.

1. **Sprawdzanie poprawności**: Wstępne sprawdzanie poprawności i konwersji formatu, jeśli to konieczne.

1. **Manipulowanie danymi**: Dopasowywanie schematu, organizowanie, indeksowanie, kodowanie i kompresowanie danych.

1. **Punkt trwałości w przepływie połknięcia:** Zarządzanie obciążeniem pozyskiwania na silniku i obsługiwanie ponownych prób po awarii przejściowych.

1. **Zatwierdzanie pozyskiwania danych:** Udostępnia dane dla kwerendy.

## <a name="ingestion-methods"></a>Metody połkwania

Usługa Azure Data Explorer obsługuje kilka metod pozyskiwania, z których każda ma własne scenariusze docelowe, zalety i wady. Usługa Azure Data Explorer oferuje potoki i łączniki do typowych usług, programowe pozyskiwania przy użyciu zestawów SDK i bezpośredni dostęp do aparatu do celów eksploracji.

### <a name="ingestion-using-pipelines-connectors-and-plugins"></a>Połknienia przy użyciu potoków, łączników i wtyczek

Usługa Azure Data Explorer obsługuje obecnie:

* Potok siatki zdarzeń, którym można zarządzać za pomocą kreatora zarządzania w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Pozyskiwania obiektów blob platformy Azure do Eksploratora danych platformy Azure](ingest-data-event-grid.md).

* Potok usługi Event Hub, którym można zarządzać za pomocą kreatora zarządzania w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Pozyskiwania danych z Centrum zdarzeń do Usługi Azure Data Explorer](ingest-data-event-hub.md).

* Logstash plugin, zobacz [Ingest danych z Logstash do Usługi Azure Data Explorer](ingest-data-logstash.md).

* Łącznik platformy Kafka, zobacz [Pozyskiwania danych z platformy Kafka do Usługi Azure Data Explorer](ingest-data-kafka.md).

### <a name="ingestion-using-integration-services"></a>Połknienia przy użyciu usług integracji

* Usługa Azure Data Factory (ADF), w pełni zarządzana usługa integracji danych dla obciążeń analitycznych na platformie Azure, do kopiowania danych do i z Usługi Azure Data Explorer przy użyciu [obsługiwanych magazynów i formatów danych.](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) Aby uzyskać więcej informacji, zobacz [Kopiowanie danych z usługi Azure Data Factory do Usługi Azure Data Explorer](/azure/data-explorer/data-factory-load-data).

### <a name="programmatic-ingestion"></a>Programowe połkowanie

Usługa Azure Data Explorer udostępnia zestawy SDK, które mogą być używane do pozyskiwania zapytań i danych. Programowe pozyskiwania jest zoptymalizowany pod kątem zmniejszenia kosztów pozyskiwania (COGs), minimalizując transakcje magazynu podczas i po procesie pozyskiwania.

**Dostępne SDK i projekty typu open source:**

Kusto oferuje zestaw SDK klienta, który może służyć do pozyskiwania i wyszukiwania danych z:

* [Python SDK](/azure/kusto/api/python/kusto-python-client-library)

* [Zestaw SDK platformy .NET](/azure/kusto/api/netfx/about-the-sdk)

* [Java SDK](/azure/kusto/api/java/kusto-java-client-library)

* [Zestaw SDK dla języka Node](/azure/kusto/api/node/kusto-node-client-library)

* [INTERFEJS API ODPOCZYNKU](/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Programowe techniki pozyskiwania:**

* Pojmowanie danych za pośrednictwem usługi zarządzania danymi usługi Azure Data Explorer (wysoka przepływność i niezawodne pozyskiwania):

    [**Wsadowe pozyskiwania**](/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) (dostarczone przez zestaw SDK): klient przekazuje dane do magazynu obiektów Blob platformy Azure (wyznaczony przez usługę zarządzania danymi usługi Azure Data Explorer) i publikuje powiadomienie do kolejki platformy Azure. Połknienie wsadowe jest zalecaną techniką pozyskiwania dużych ilości danych, niezawodnych i tanich.

* Posyłanie danych bezpośrednio do aparatu Usługi Azure Data Explorer (najbardziej odpowiednie do eksploracji i prototypowania):

  * **Połknienie w wierszu:** polecenie sterowania (.ingest inline) zawierające dane w paśmie jest przeznaczone do celów testowania ad hoc.

  * **Pozyskiwanie z kwerendy:** polecenie control (.set, .set-or-append, .set-or-replace) wskazujące wyniki kwerendy jest używane do generowania raportów lub małych tabel tymczasowych.

  * **Połknąć z magazynu:** polecenie control (.ingest into) z danymi przechowywanymi zewnętrznie (na przykład usługa Azure Blob Storage) umożliwia wydajne zbiorcze pobieranie danych.

**Opóźnienie różnych metod:**

| Metoda | Opóźnienie |
| --- | --- |
| **Połknienie w linii** | Natychmiastowe |
| **Pozyskiwania z kwerendy** | Czas zapytania + czas przetwarzania |
| **Połknienie z magazynu** | Czas pobierania + czas przetwarzania |
| **W kolejce pozyskiwania** | Czas wsadowania + czas przetwarzania |
| |

Czas przetwarzania zależy od rozmiaru danych, mniej niż kilka sekund. Domyślny czas przetwarzania wsadowego to 5 minut.

## <a name="choosing-the-most-appropriate-ingestion-method"></a>Wybór najodpowiedniejszej metody położenia

Przed rozpoczęciem pozyskiwania danych należy zadać sobie następujące pytania.

* Gdzie znajdują się moje dane? 
* Jaki jest format danych i czy można go zmienić? 
* Jakie są wymagane pola, które mają być poszukiwane? 
* Jaki jest oczekiwany wolumen i prędkość danych? 
* Ile typów zdarzeń jest oczekiwanych (odzwierciedlenie jako liczba tabel)? 
* Jak często oczekuje się zmiany schematu zdarzenia? 
* Ile węzłów wygeneruje dane? 
* Co to jest system operacyjny źródłowy? 
* Jakie są wymagania dotyczące opóźnień? 
* Czy można użyć jednego z istniejących zarządzanych potoków pozyskiwania? 

W przypadku organizacji z istniejącą infrastrukturą, które są oparte na usłudze obsługi wiadomości, takiej jak Event Hub i IoT Hub, użycie łącznika jest prawdopodobnie najbardziej odpowiednim rozwiązaniem. W kolejce pozyskiwania jest odpowiedni dla dużych woluminów danych.

## <a name="supported-data-formats"></a>Obsługiwane formaty danych

Dla wszystkich metod pozyskiwania innych niż pozyskiwania z kwerendy, sformatować dane, tak aby Usługa Azure Data Explorer można przeanalizować go. 
* Obsługiwane formaty danych to: TXT, CSV, TSV, TSVE, PSV, SCSV, SOH, JSON (oddzielone linią, wieloliniowe), Avro, Orc i Parquet. 
* Obsługuje kompresję ZIP i GZIP.

> [!NOTE]
> Podczas pozyskiwania danych typy danych są wywnioskowane na podstawie kolumn tabeli docelowej. Jeśli rekord jest niekompletny lub nie można przeanalizować pola jako wymaganego typu danych, odpowiednie kolumny tabeli zostaną wypełnione wartościami null.

## <a name="ingestion-recommendations-and-limitations"></a>Zalecenia i ograniczenia dotyczące pozyskiwania

* Zasady skutecznego przechowywania pozyskanych danych pochodzą z zasad przechowywania bazy danych. Szczegółowe informacje można znaleźć w [zasadach przechowywania.](/azure/kusto/concepts/retentionpolicy) Pojmowanie danych wymaga uprawnień **table ingestor** lub **database ingestor.**
* Połknienie obsługuje maksymalny rozmiar pliku 5 GB. Zaleca się, aby łowić pliki od 100 MB do 1 GB.

## <a name="schema-mapping"></a>Mapowanie schematu

Mapowanie schematu pomaga powiązać pola danych źródłowych z kolumnami tabeli docelowej.

* [Mapowanie CSV](/azure/kusto/management/mappings?branch=master#csv-mapping) (opcjonalnie) działa ze wszystkimi formatami opartymi na porządkach. Można go wykonać przy użyciu parametru polecenia pozyskiwania lub [wstępnie utworzonego w tabeli](/azure/kusto/management/create-ingestion-mapping-command) i odwołuje się z parametru polecenia pozyskiwania.
* [Mapowanie JSON](/azure/kusto/management/mappings?branch=master#json-mapping) (obowiązkowe) i [mapowanie Avro](/azure/kusto/management/mappings?branch=master#avro-mapping) (obowiązkowe) można wykonać za pomocą parametru polecenia pozyskiwania. Można je również [wstępnie utworzyć w tabeli](/azure/kusto/management/create-ingestion-mapping-command) i odwoływać się z parametru polecenia pozyskiwania.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [pozyskiwanie danych z centrum zdarzeń do usługi Azure Data Explorer](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [Pozyskiwania danych przy użyciu subskrypcji usługi Event Grid do Eksploratora danych platformy Azure](ingest-data-event-grid.md)

> [!div class="nextstepaction"]
> [pozyskiwanie danych z platformy Kafka do usługi Azure Data Explorer](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [pozyskiwanie danych przy użyciu biblioteki języka Python w usłudze Azure Data Explorer](python-ingest-data.md)

> [!div class="nextstepaction"]
> [Pozyskiwanie danych przy użyciu biblioteki środowiska Node w usłudze Azure Data Explorer](node-ingest-data.md)

> [!div class="nextstepaction"]
> [Pozyskiwania danych przy użyciu standardowego sdk Usługi Azure Data Explorer .NET (wersja zapoznawcza)](net-standard-ingest-data.md)

> [!div class="nextstepaction"]
> [pozyskiwanie danych z usługi Logstash do usługi Azure Data Explorer](ingest-data-logstash.md)
