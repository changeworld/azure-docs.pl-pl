---
title: Pozyskiwanie danych w usłudze Azure Eksplorator danych
description: Dowiedz się więcej o różnych sposobów umożliwia pobieranie danych (załaduj) w Eksploratorze danych platformy Azure
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 94f96d949f2a05f71e9565fdcbc7b48ed2c2a5c5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972663"
---
# <a name="azure-data-explorer-data-ingestion"></a>Pozyskiwanie danych w usłudze Azure Eksplorator danych

Pozyskiwanie danych polega na używana do ładowania rekordów danych z co najmniej jednego źródła, można utworzyć lub zaktualizować tabeli w Eksploratorze danych platformy Azure. Gdy pozyskiwane, dane będą dostępne dla zapytania. Na poniższym diagramie przedstawiono przepływ end-to-end do pracy w Eksploratorze danych platformy Azure, w tym pozyskiwanie danych **(2)**.

![Przepływ danych ogólnej](media/ingest-data-overview/overall-data-flow.png)

Usługa zarządzania danych Eksploratora danych usługi Azure, która jest odpowiedzialna za przetwarzanie danych, oferuje następujące funkcje:

1. **Ściągania danych**: pobierania danych z zewnętrznych źródeł (usługa Event Hubs) lub przeczytaj pozyskiwania żądania z kolejki usługi Azure.

1. **Przetwarzanie wsadowe**: dane przepływają w tej samej bazy danych i tabeli w celu zoptymalizowania przepływności pozyskiwania partii.

1. **Sprawdzanie poprawności**: wstępnego sprawdzania poprawności i formatu konwersji w razie potrzeby.

1. **Manipulowanie danymi**: pasującego schematu, organizowanie, indeksowanie, kodowanie i kompresji danych.

1. **Punkt stanu trwałego w usłudze flow pozyskiwania**: Zarządzanie obciążenie pozyskiwania aparatu i obsługują ponownych prób na przejściowe awarie.

1. **Zatwierdzenie pozyskiwania danych**: sprawia, że dane dostępne dla zapytania.

> [!NOTE]
> Zasady przechowywania skuteczne pozyskiwanych danych jest tworzony na podstawie zasad przechowywania bazy danych. Zobacz [zasady przechowywania](https://docs.microsoft.com/azure/kusto/concepts/retentionpolicy) Aby uzyskać szczegółowe informacje. Dane wprowadzane wymaga **pozwalająca tabeli** lub **dużych możliwościach skalowania bazy danych** uprawnienia.

## <a name="ingestion-methods"></a>Metody wprowadzania

Eksplorator danych usługi Azure obsługuje kilka metod wprowadzania, każdy z własną target scenariuszy, zalety i wady. Eksplorator danych usługi Azure udostępnia łączniki do typowych usług, wprowadzanie programowe przy użyciu zestawów SDK i bezpośredni dostęp do aparatu do celów eksploracji.

### <a name="ingestion-using-connectors"></a>Wprowadzanie za pomocą łączników

Eksplorator danych usługi Azure obsługuje obecnie łącznika Centrum zdarzeń, które mogą być zarządzane za pomocą Kreatora zarządzania w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Szybki Start: przesyłanie danych z Centrum zdarzeń do Eksploratora danych usługi Azure](ingest-data-event-hub.md).

### <a name="programmatic-ingestion"></a>Wprowadzanie programowe

Eksplorator danych usługi Azure udostępnia zestawy SDK, które mogą służyć do kwerendy i danych. Wprowadzanie programowe jest zoptymalizowane pod kątem pozwala zmniejszyć koszty pozyskiwania (KWS), minimalizując transakcje magazynu, podczas i po procesu pozyskiwania.

**Dostępne zestawy SDK i projektów typu open source**:

Kusto oferuje zestaw SDK, który może służyć do odbierania i wysyłania zapytań dotyczących danych przy użyciu klienta:

* [Zestaw SDK dla języka Python](https://docs.microsoft.com/azure/kusto/api/python/kusto-python-client-library)

* [Zestaw SDK platformy .NET](https://docs.microsoft.com/azure/kusto/api/netfx/about-the-sdk)

* [Zestaw SDK Java](https://docs.microsoft.com/azure/kusto/api/java/kusto-java-client-library)

* [Interfejs API REST](https://docs.microsoft.com/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Wprowadzanie programowe technik**:

* Dane wprowadzane bezpośrednio do aparatu Eksplorator danych platformy Azure (najbardziej odpowiednie do potrzeb eksploracji i tworzenia prototypów):

  * **Wbudowane pozyskiwania**: polecenia sterowania (.ingest wbudowany) zawierający dane wewnątrzpasmowe jest przeznaczony do testowania zapytań ad-hoc.

  * **Pozyskiwanie z kwerendy**: polecenia sterowania (.set, .set lub dołączania, .set lub zastępowanie), który wskazuje na wyniki zapytania jest używana do generowania raportów lub małych tabel tymczasowych.

  * **Pozyskiwanie z magazynu**: polecenia sterowania (.ingest do) za pomocą — dane przechowywane zewnętrznie (np. usługi Azure Blob Storage) umożliwia wydajne zbiorcze pozyskiwania danych.

* Wprowadzania danych przez usługę zarządzania danych Eksploratora danych usługi Azure (o wysokiej przepływności i niezawodne pozyskiwanie)

  * [**Batch pozyskiwania** ](https://docs.microsoft.com/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) (udostępnione przez zestaw SDK): klient przesyła dane do usługi Azure Blob storage (wyznaczony przez usługę zarządzania danych Eksplorator danych platformy Azure) i wysyła powiadomienie do kolejki usługi Azure. Jest to zalecana technika mocno obciążające, niezawodne i tanie pozyskiwania.

**Opóźnienie różnych metod**:

| Metoda | Opóźnienie |
| --- | --- |
| **Pozyskiwanie wbudowane** | Natychmiast |
| **Pozyskiwanie z zapytania** | Czas wykonywania zapytania i czas przetwarzania |
| **Pozyskiwanie z magazynu** | Czas pobierania i czas przetwarzania |
| **Pozyskiwanie umieszczonych w kolejce** | Przetwarzanie wsadowe, czas i czas przetwarzania |
| |

Czas przetwarzania zależy od rozmiaru danych zazwyczaj mniej niż kilka sekund. Przetwarzanie wsadowe godziny wartość domyślna to 5 minut.

## <a name="choosing-the-most-appropriate-ingestion-method"></a>Wybranie najbardziej odpowiedniej metody wprowadzania

Przed rozpoczęciem pozyskiwania danych, użytkownik powinien samodzielnie na następujące pytania.

* Gdzie ma się moje dane? 
* Co to jest format danych i można je zmienić? 
* Co to są wymagane pola, aby można wykonywać zapytania? 
* Co to jest ilość oczekiwanych danych i prędkości? 
* Ile typy zdarzeń powinny (widoczna jako liczba tabel)? 
* Jak często schematu zdarzeń oczekuje się, aby zmienić? 
* Ilu węzłów wygeneruje dane? 
* Co to jest źródłowego systemu operacyjnego? 
* Jakie są wymagania dotyczące opóźnień? 
* Jedną z istniejących potoków pozyskiwania zarządzanej można używać? 

W przypadku organizacji z istniejącą infrastrukturą, oparte na usługą obsługi wiadomości, takich jak Centrum zdarzeń przy użyciu łącznika prawdopodobnie jest najbardziej odpowiednie rozwiązania. Pozyskiwanie umieszczonych w kolejce jest odpowiednia dla dużych ilości danych.

## <a name="supported-data-formats"></a>Obsługiwane formaty danych

Dla wszystkich pozyskiwania danych ze strumieni metod innych niż pobieranie z kwerendy danych muszą być sformatowane w jednym z formatów obsługiwanych danych tak, aby można go przeanalizować, Eksplorator danych platformy Azure.

* CSV, TSV, WSPÓŁCZYNNIK, SCSV, RAPORTU O KONDYCJI
* Avro (oddzielone w wierszu, wiele wierszy), JSON
* ZIP i GZIP 

> [!NOTE]
> Gdy dane są pozyskiwane, są wnioskowane typy danych, na podstawie kolumn tabeli docelowej. Jeśli rekord jest niekompletne lub pola nie może być analizowana jako typ danych, odpowiednie kolumny tabeli zostaną wypełnione przy użyciu wartości null.

## <a name="schema-mapping"></a>Mapowanie schematu

Mapowanie schematu pomaga w sposób deterministyczny powiązać pola danych źródła kolumny tabeli docelowej.

* [Mapowanie CSV](https://docs.microsoft.com/azure/kusto/management/mappings?branch=master#csv-mapping) (opcjonalnie) współpracuje z wszystkich numer formatów i może być przekazywany jako parametr polecenia pozyskiwania lub [wstępnie utworzone w tabeli](https://docs.microsoft.com/azure/kusto/management/tables?branch=master#create-ingestion-mapping) i do którego istnieje odwołanie parametru polecenia pozyskiwania.
* [Mapowanie JSON](https://docs.microsoft.com/azure/kusto/management/mappings?branch=master#json-mapping) (obowiązkowe) i [mapowania Avro](https://docs.microsoft.com/azure/kusto/management/mappings?branch=master#avro-mapping) (obowiązkowe) może być przekazywany jako parametr polecenia pozyskiwania lub [wstępnie utworzone w tabeli](https://docs.microsoft.com/azure/kusto/management/tables#create-ingestion-mapping) i do którego istnieje odwołanie parametru polecenia pozyskiwania.

## <a name="next-steps"></a>Kolejne kroki

[Szybki Start: Przesyłanie danych z Centrum zdarzeń do Eksploratora danych usługi Azure](ingest-data-event-hub.md)

[Szybki Start: Pozyskiwanie danych przy użyciu biblioteki Python Eksploratora danych platformy Azure](python-ingest-data.md)

