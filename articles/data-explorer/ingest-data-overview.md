---
title: Pozyskiwanie danych Eksplorator danych platformy Azure
description: Dowiedz się więcej na temat różnych sposobów pozyskiwania (ładowania) danych na platformie Azure Eksplorator danych
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 4846a19c403cce16bed704ed4e7c70499f3b5d13
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187994"
---
# <a name="azure-data-explorer-data-ingestion"></a>Pozyskiwanie danych Eksplorator danych platformy Azure

Pozyskiwanie danych to proces używany do ładowania rekordów danych z co najmniej jednego źródła w celu utworzenia lub zaktualizowania tabeli w usłudze Azure Eksplorator danych. Po pobraniu dane staną się dostępne do zapytania. Poniższy diagram przedstawia kompleksowy przepływ pracy na platformie Azure Eksplorator danych, w tym pozyskiwanie danych.

![Przepływ danych](media/ingest-data-overview/data-flow.png)

Usługa zarządzania danymi w usłudze Azure Eksplorator danych, która jest odpowiedzialna za pozyskiwanie danych, zapewnia następujące funkcje:

1. **Ściąganie danych**: ściąganie danych ze źródeł zewnętrznych (Event Hubs) lub odczytywanie żądań ściągnięcia z kolejki platformy Azure.

1. **Przetwarzanie wsadowe**: dane wsadowe przepływające do tej samej bazy danych i tabeli w celu zoptymalizowania przepływności pozyskiwania.

1. **Walidacja**: wstępne sprawdzanie poprawności i Konwersja formatu, jeśli jest to konieczne.

1. **Manipulowanie danymi**: dopasowywanie schematu, porządkowanie, indeksowanie, kodowanie i kompresowanie danych.

1. **Punkt trwałości w przepływie**pozyskiwania: Zarządzanie obciążeniem pozyskiwania w aparacie i dojściem ponawiania prób w przypadku błędów przejściowych.

1. Zatwierdź pozyskiwanie **danych**: udostępnia dane dla kwerendy.

## <a name="ingestion-methods"></a>Metody pozyskiwania

Usługa Azure Eksplorator danych obsługuje kilka metod pozyskiwania, z których każda ma własne scenariusze, zalety i wady. Usługa Azure Eksplorator danych oferuje potoki i łączniki dla typowych usług, programowe pozyskiwanie przy użyciu zestawów SDK i bezpośredni dostęp do aparatu w celach eksploracji.

### <a name="ingestion-using-pipelines-connectors-and-plugins"></a>Pozyskiwanie przy użyciu potoków, łączników i wtyczek

Obecnie usługa Azure Eksplorator danych obsługuje:

* Potok Event Grid, którym można zarządzać za pomocą Kreatora zarządzania w Azure Portal. Aby uzyskać więcej informacji, zobacz pozyskiwanie [obiektów blob platformy Azure w usłudze azure Eksplorator danych](ingest-data-event-grid.md).

* Potok centrum zdarzeń, którym można zarządzać za pomocą Kreatora zarządzania w Azure Portal. Aby uzyskać więcej informacji, zobacz pozyskiwanie [danych z centrum zdarzeń do usługi Azure Eksplorator danych](ingest-data-event-hub.md).

* Wtyczka logstash, zobacz pozyskiwanie [danych z logstash na platformie Azure Eksplorator danych](ingest-data-logstash.md).

* Łącznik Kafka, zobacz pozyskiwanie [danych z Kafka na platformie Azure Eksplorator danych](ingest-data-kafka.md).

### <a name="ingestion-using-integration-services"></a>Pozyskiwanie przy użyciu usług Integration Services

* Azure Data Factory (ADF) — w pełni zarządzana usługa integracji danych dla obciążeń analitycznych na platformie Azure, umożliwiająca kopiowanie danych do i z usługi Azure Eksplorator danych przy użyciu [obsługiwanych magazynów danych i formatów](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats). Aby uzyskać więcej informacji, zobacz [Kopiowanie danych z Azure Data Factory do platformy Azure Eksplorator danych](/azure/data-explorer/data-factory-load-data).

### <a name="programmatic-ingestion"></a>Pozyskiwanie programistyczne

Usługa Azure Eksplorator danych udostępnia zestawy SDK, których można używać na potrzeby pozyskiwania zapytań i danych. Pozyskiwanie programistyczne jest zoptymalizowane pod kątem zmniejszenia kosztów pozyskiwania (KWS) przez zminimalizowanie transakcji magazynu podczas procesu pozyskiwania.

**Dostępne zestawy SDK i projekty open source**:

Kusto oferuje zestaw SDK klienta, który może służyć do pozyskiwania i wykonywania zapytań dotyczących danych za pomocą:

* [Zestaw SDK dla języka Python](/azure/kusto/api/python/kusto-python-client-library)

* [Zestaw SDK platformy .NET](/azure/kusto/api/netfx/about-the-sdk)

* [Zestaw SDK Java](/azure/kusto/api/java/kusto-java-client-library)

* [Zestaw SDK węzła](/azure/kusto/api/node/kusto-node-client-library)

* [Interfejs API REST](/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Techniki pozyskiwania programistycznego**:

* Pozyskiwanie danych za pomocą usługi zarządzania danymi w usłudze Azure Eksplorator danych (Wysoka przepływność i niezawodne pozyskiwanie):

    Pozyskiwanie [**wsadowe**](/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) (udostępniane przez zestaw SDK): klient przekazuje dane do usługi Azure Blob Storage (oznaczonej przez usługę zarządzania danymi w usłudze Azure Eksplorator danych) i publikuje powiadomienie w usłudze Azure Queue. Pozyskiwanie wsadowe jest zalecaną techniką do pozyskiwania, niezawodnego i taniego pozyskiwania danych.

* Pozyskiwanie danych bezpośrednio w aparacie Eksplorator danych platformy Azure (najbardziej odpowiednie dla eksploracji i tworzenia prototypów):

  * Pozyskanie w **tekście**: polecenie sterowania (. pozyskiwanie wbudowane) zawierające dane w paśmie jest przeznaczone do celów testowych ad hoc.

  * Pozyskiwanie **z zapytania**: polecenie sterowania (. Set,. Set-lub-append,. Set-lub-Replace), które wskazuje na wyniki zapytania, służy do generowania raportów lub małych tabel tymczasowych.

  * Pozyskiwanie **z magazynu**: polecenie sterowania (. pozyskiwanie w programie) z danymi przechowywanymi zewnętrznie (na przykład Azure Blob Storage) umożliwia wydajne pozyskiwanie danych.

**Opóźnienie różnych metod**:

| Metoda | Opóźnienie |
| --- | --- |
| **Pozyskiwanie wbudowane** | Natychmiastowe |
| **Pozyskiwanie z zapytania** | Czas wykonywania zapytania + czas przetwarzania |
| **Pozyskiwanie z magazynu** | Czas pobierania + czas przetwarzania |
| **Przyjmowanie w kolejce** | Przetwarzanie wsadowe + czas przetwarzania |
| |

Czas przetwarzania zależy od rozmiaru danych, który jest krótszy niż kilka sekund. Wartość domyślna czasu przetwarzania wsadowego na 5 minut.

## <a name="choosing-the-most-appropriate-ingestion-method"></a>Wybieranie najbardziej odpowiedniej metody pozyskiwania

Przed rozpoczęciem pozyskiwania danych należy zadać sobie następujące pytania.

* Gdzie znajdują się moje dane? 
* Co to jest format danych i czy można go zmienić? 
* Jakie pola są wymagane do zapytania? 
* Jaki jest oczekiwany wolumin danych i szybkość pracy? 
* Ile typów zdarzeń jest oczekiwanych (odzwierciedlonych jako liczba tabel)? 
* Jak często schemat zdarzenia powinien zostać zmieniony? 
* Ile węzłów będzie generować dane? 
* Co to jest źródłowy system operacyjny? 
* Jakie są wymagania dotyczące opóźnień? 
* Czy można użyć jednego z istniejących potoków pozyskiwania? 

W przypadku organizacji z istniejącą infrastrukturą opartą na usłudze obsługi komunikatów, takiej jak centrum zdarzeń i IoT Hub, używanie łącznika jest prawdopodobnie najbardziej odpowiednim rozwiązaniem. Przyjmowanie w kolejce jest odpowiednie dla dużych ilości danych.

## <a name="supported-data-formats"></a>Obsługiwane formaty danych

W przypadku wszystkich metod pozyskiwania innych niż pozyskiwanie z kwerendy sformatuj dane, aby usługa Azure Eksplorator danych mogła ją przeanalizować. 
* Obsługiwane formaty danych to: TXT, CSV, TSV, TSVE, PSV, SCSV, raport o kondycji, JSON (rozdzielone wierszami, wiele wierszy), Avro, Orc i Parquet. 
* Obsługuje kompresję ZIP i GZIP.

> [!NOTE]
> Gdy dane są pozyskiwane, typy danych są wywnioskowane na podstawie kolumn tabeli docelowej. Jeśli rekord jest niekompletny lub nie można przeanalizować pola jako wymaganego typu danych, odpowiednie kolumny tabeli zostaną wypełnione wartościami o wartości null.

## <a name="ingestion-recommendations-and-limitations"></a>Zalecenia i ograniczenia dotyczące pozyskiwania

* Efektywne zasady przechowywania danych uzyskanych pochodzą z zasad przechowywania bazy danych. Aby uzyskać szczegółowe informacje, zobacz [zasady przechowywania](/azure/kusto/concepts/retentionpolicy) . Pozyskiwanie danych wymaga uprawnień do pozyskiwania **tabel** lub pozyskiwania **baz danych** .
* Pozyskiwanie obsługuje maksymalny rozmiar pliku wynoszący 5 GB. Zaleca się pozyskiwanie plików z zakresu od 100 MB do 1 GB.

## <a name="schema-mapping"></a>Mapowanie schematu

Mapowanie schematu pomaga powiązać pola danych źródłowych z kolumnami tabeli docelowej.

* [Mapowanie CSV](/azure/kusto/management/mappings?branch=master#csv-mapping) (opcjonalnie) działa ze wszystkimi formatami opartymi na liczbie porządkowej. Można go wykonać przy użyciu parametru polecenia pozyskiwania lub [wstępnie utworzonego w tabeli](/azure/kusto/management/create-ingestion-mapping-command) i przywoływanych przez parametr polecenia pozyskiwania.
* [Mapowanie JSON](/azure/kusto/management/mappings?branch=master#json-mapping) (obowiązkowe) i [Mapowanie Avro](/azure/kusto/management/mappings?branch=master#avro-mapping) (obowiązkowe) można wykonać za pomocą parametru polecenia pozyskiwania. Mogą być również [wstępnie utworzone w tabeli](/azure/kusto/management/create-ingestion-mapping-command) i przywoływane przez parametr polecenia pozyskiwania.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Pozyskiwanie danych z centrum zdarzeń do usługi Azure Eksplorator danych](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [Pozyskiwanie danych przy użyciu subskrypcji Event Grid na platformie Azure Eksplorator danych](ingest-data-event-grid.md)

> [!div class="nextstepaction"]
> [Pozyskiwanie danych z Kafka na platformie Azure Eksplorator danych](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [Pozyskiwanie danych przy użyciu biblioteki języka Python Eksplorator danych platformy Azure](python-ingest-data.md)

> [!div class="nextstepaction"]
> [Pozyskiwanie danych przy użyciu biblioteki węzłów Eksplorator danych platformy Azure](node-ingest-data.md)

> [!div class="nextstepaction"]
> [Pozyskiwanie danych przy użyciu usługi Azure Eksplorator danych .NET Standard SDK (wersja zapoznawcza)](net-standard-ingest-data.md)

> [!div class="nextstepaction"]
> [Pozyskiwanie danych z logstash do platformy Azure Eksplorator danych](ingest-data-logstash.md)
