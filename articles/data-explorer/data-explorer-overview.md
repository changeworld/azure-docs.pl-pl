---
title: Co to jest usługa Azure Data Explorer?
description: Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 02b239922f481240341225ea68d8d0ee7492c48b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "60445815"
---
# <a name="what-is-azure-data-explorer"></a>Co to jest usługa Azure Data Explorer?

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Pomaga ona w obsłudze wielu strumieni danych emitowanych przez nowoczesne oprogramowanie, dlatego można zbierać, przechowywać i analizować dane. Usługa Azure Data Explorer to idealne rozwiązanie do analizowania dużych ilości zróżnicowanych danych z dowolnego źródła danych, takiego jak witryny internetowe, aplikacje, urządzenia IoT i inne. Te dane są używane na potrzeby diagnostyki, monitorowania, raportowania, uczenia maszynowego i dodatkowych możliwości analizy. Usługa Azure Data Explorer ułatwia pozyskiwania tych danych i umożliwia wykonywanie złożonych zapytań ad hoc na dane w ciągu kilku sekund.

## <a name="what-makes-azure-data-explorer-unique"></a>Co sprawia, że usługa Azure Data Explorer jest unikatowa?

- Szybko skaluje do terabajtów danych w ciągu kilku minut, umożliwiając szybkie iteracje podczas eksplorowania danych, co pozwala na odnajdowanie właściwych szczegółowych informacji.

- Oferuje innowacyjny język zapytań zoptymalizowany pod kątem analizy danych o wysokiej wydajności.

- Obsługuje analizę dużych ilości danych heterogenicznych (ze strukturą i bez struktury).

- Umożliwia kompilowanie i wdrażanie dokładnie tych elementów, których potrzebujesz, łącząc się z innymi usługami w celu udostępnienia kompleksowego, zaawansowanego i interaktywnego rozwiązania do analizy danych.

## <a name="data-warehousing-workflow"></a>Przepływ pracy podczas magazynowania danych

Usługa Azure Data Explorer integruje się z innymi usługami głównymi w celu udostępnienia kompleksowego rozwiązania, które obejmuje następujące czynności związane z danymi: zbieranie, pozyskiwanie, magazynowanie, indeksowanie, wykonywanie zapytań i wizualizacja. Odgrywa ona zasadniczą rolę w przepływie magazynowania danych, wykonując krok **EKSPLOROWANIE** przepływu na terabajtach różnych danych pierwotnych.

![Diagram magazynu danych](media/data-explorer-overview/data-warehouse.png)

Usługa Azure Data Explorer obsługuje kilka metod pozyskiwania, w tym łączniki z typowymi usługami, takimi jak Event Hubs, pozyskiwanie programowe za pomocą zestawów SDK, takich jak .NET i Python, oraz bezpośredni dostęp do aparatu dla celów eksploracji. Usługa Azure Data Explorer integruje się z funkcją analizy i usługami modelowania w celu przeprowadzania dodatkowej analizy i wizualizacji danych.

## <a name="azure-data-explorer-flow"></a>Przepływ w usłudze Azure Data Explorer

Poniższy diagram przedstawia różne aspekty pracy z usługą Azure Data Explorer.

![Przepływ w usłudze Azure Data Explorer](media/data-explorer-overview/workflow.png)

Ogólnie praca z usługą Azure Data Explorer odbywa się zgodnie z następującym wzorcem:

1. **Tworzenie bazy danych:** tworzenie *klastra*, a następnie tworzenie co najmniej jednej *bazy danych* w klastrze. [Szybki start: tworzenie klastra i bazy danych usługi Azure Data Explorer](create-cluster-database-portal.md)

1. **Pozyskiwanie danych:** ładowanie danych do tabel bazy danych, co pozwala na wykonywanie związanych z nimi zapytań. [Szybki start: pozyskiwanie danych z centrum zdarzeń do usługi Azure Data Explorer](ingest-data-event-hub.md)

1. **Wykonywanie zapytań dotyczących bazy danych:** uruchamianie, przeglądanie i udostępnianie zapytań oraz wyników przy użyciu naszej aplikacji internetowej. Jest ona dostępna w witrynie Azure Portal oraz jako aplikacja autonomiczna. Ponadto można wysyłać zapytania programowo (przy użyciu zestawu SDK) lub do punktu końcowego interfejsu API REST. [Szybki start: wykonywanie zapytań o dane w usłudze Azure Data Explorer](web-query-data.md)

## <a name="query-experience"></a>Środowisko pracy z zapytaniami

Zapytanie w usłudze Azure Data Explorer to przeznaczone tylko do odczytu żądanie przetwarzające dane i zwracające wyniki tego przetwarzania bez modyfikowania danych lub metadanych. Zapytania można uściślać do momentu ukończenia analizy. Usługa Azure Data Explorer ułatwia ten proces ze względu na bardzo szybkie środowisko zapytań ad hoc.

Usługa Azure Data Explorer równie efektywnie obsługuje duże ilości danych ze strukturą, częściową strukturą (zagnieżdżone typy w notacji JSON) oraz bez struktury (dowolny tekst). Umożliwia ona wyszukiwanie terminów z określonym tekstem, lokalizowanie konkretnych zdarzeń i wykonywanie obliczeń w stylu metryk na danych strukturalnych. Usługa Azure Data Explorer łączy światy dzienników tekstowych bez struktury oraz numerów i wymiarów ze strukturą, wyodrębniając wartości w czasie wykonywania z pól tekstowych w dowolnej postaci. Eksplorowanie danych jest upraszczane przez połączenie szybkiego indeksowania tekstu, magazynu kolumn i operacje na szeregach czasowych.

Możliwości usługi Azure Data Explorer zostały rozszerzone o inne usługi stworzone w oparciu o zaawansowany język zapytań, w tym [dzienniki usługi Azure Monitor](/azure/log-analytics/), usługi [Application Insights](/azure/application-insights/) i [Time Series Insights](/azure/time-series-insights/) oraz [zaawansowaną ochronę przed zagrożeniami w usłudze Windows Defender](/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection/).

## <a name="feedback"></a>Opinia

Bardzo chcemy poznać Twoją opinię na temat usługi Azure Data Explorer oraz jej języku zapytań:

- Zadawaj pytania
  - [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-data-explorer)
  - [Społeczność techniczna firmy Microsoft](https://techcommunity.microsoft.com/t5/Azure-Data-Explorer/bd-p/Kusto)
  - [MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureKusto)
- [Przedstaw sugestie dotyczące produktów w witrynie User Voice](https://aka.ms/AzureDataExplorer.UserVoice)

## <a name="next-steps"></a>Następne kroki

[Szybki start: tworzenie klastra i bazy danych usługi Azure Data Explorer](create-cluster-database-portal.md)

[Szybki start: pozyskiwanie danych z centrum zdarzeń do usługi Azure Data Explorer](ingest-data-event-hub.md)

[Szybki start: wykonywanie zapytań o dane w usłudze Azure Data Explorer](web-query-data.md)
