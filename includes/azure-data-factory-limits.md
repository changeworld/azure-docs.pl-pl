---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 5/30/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: ac0747dcff4e74363a58dc9aaf6da4dbd4c6a1c7
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427584"
---
Usługa Azure Data Factory to wielodostępna usługa, która ma następujące domyślne limity w miejscu, aby upewnić się, że subskrypcjami klientów są chronione przed jego obciążeń. Aby zwiększyć limity do maksimum dla Twojej subskrypcji, skontaktuj się z działem pomocy technicznej.

### <a name="version-2"></a>Wersja 2

| Resource | Limit domyślny | Limit maksymalny |
| -------- | ------------- | ------------- |
| Fabryki danych w subskrypcji platformy Azure | 50 | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Całkowita liczba jednostek, takich jak potoki, zestawy danych, wyzwalacze, połączonych usług i środowisk integration Runtime w fabryce danych | 5,000 | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Łączna liczba rdzeni procesora CPU dla środowiska Azure-SSIS Integration Runtime w ramach jednej subskrypcji | 256 | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Potok równoczesny przebiegów na usługi data factory, która jest współużytkowana przez wszystkie potoki w fabryce | 10 000  | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Jednoczesną aktywność zewnętrznego, który jest uruchamiany na subskrypcję na [regionu Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Działań zewnętrznych odbywa się na środowisko integration runtime, ale wykonanie w połączonych usług, w tym usługi Databricks, procedury składowanej, HDInsights i inne.</small> | 3000 | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Równoczesne działanie potoku, który jest uruchamiany na subskrypcję na [regionu Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>Działania potoku wykonania na środowisko integration runtime, w tym Lookup, GetMetadata i Usuń. </small>| 1000 | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Równoczesne tworzenie operacje na subskrypcję na [regionu Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Połączenie testowe, przeglądania listy folderów i listy tabel, w tym Podgląd danych. | 200 | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Współbieżne jednostek integracji danych<sup>1</sup> użycia na subskrypcję na [regionu Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Region grupy 1<sup>2</sup>: 6000<br>Region — grupa 2<sup>2</sup>: 3000<br>Region, grupa 3<sup>2</sup>: 1500 | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maksymalna działań na potok, który zawiera wewnętrzny działania dla kontenerów | 40 | 40 |
| Maksymalna liczba środowisk połączonych integration Runtime, utworzony względem pojedynczej własnego środowiska integration runtime | 100 | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maksymalna parametry dla potoku | 50 | 50 |
| Elementy ForEach | 100,000 | 100,000 |
| Równoległość ForEach | 20 | 50 |
| Liczba znaków w wyrażeniu | 8192 | 8192 |
| Interwał wyzwalacza okna wirowania minimalne | 15 min | 15 min |
| Uruchamia maksymalnego limitu czasu dla działania potoku | 7 dni | 7 dni |
| Liczba bajtów na obiekt w potoku<sup>3</sup> | 200 KB | 200 KB |
| Liczba bajtów na obiekt dla zestawu danych i obiekty usługi połączonej<sup>3</sup> | 100 KB | 2000 KB |
| Jednostki integracji danych<sup>1</sup> według uruchomienia działania kopiowania | 256 | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Zapis wywołań interfejsu API | 2500/godz.<br/><br/> Ten limit są nakładane przez usługi Azure Resource Manager, nie usługi Azure Data Factory. | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Odczyt interfejsu API | 12 500/godz.<br/><br/> Ten limit są nakładane przez usługi Azure Resource Manager, nie usługi Azure Data Factory. | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Monitorowanie zapytania na minutę: | 1000 | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Jednostki operacji CRUD na minutę | 50 | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> jednostka integracji danych (DIU) jest używany w operacji kopiowania w chmurze do chmury, Dowiedz się więcej z [jednostek integracji danych (wersja 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Aby uzyskać informacje dotyczące rozliczeń, zobacz [cennika usługi Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) jest [dostępnie](https://azure.microsoft.com/global-infrastructure/services/) w celu zapewnienia zgodności danych, wydajność i mniejsze sieci ruch wychodzący kosztów. 

| Grupa region | Regiony | 
| -------- | ------ |
| Region grupy 1 | Środkowe stany USA, wschodnie stany USA, wschodnie stany USA 2, Europa Północna, Europa Zachodnia, zachodnie stany USA, zachodnie stany USA 2 |
| Region — grupa 2 | Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Indie środkowe, Japonia, część wschodnia, Northcentral Stanów Zjednoczonych, Southcentral USA, Azja południowo-wschodnia, zachodnie środkowe stany USA |
| Region, grupa 3 | Kanada Środkowa, Azja Wschodnia, Francja środkowa, Korea środkowa, Zjednoczone Królestwo Południowe |

<sup>3</sup> potok, zestaw danych i połączonej usługi obiektów reprezentują powodują ustawienie logicznego grupowania obciążenia. Limity dla tych obiektów nie odnoszą się do ilości danych można przenosić i przetwarzać za pomocą usługi Azure Data Factory. Fabryka danych została zaprojektowana do skalowania, umożliwiając obsługę petabajtów danych.

### <a name="version-1"></a>Wersja 1

| **Zasób** | **Limit domyślny** | **Maksymalny limit** |
| --- | --- | --- |
| Fabryki danych w subskrypcji platformy Azure |50 |[Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Potoki w fabryce danych |2,500 |[Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Zestawy danych w ramach usługi data factory |5,000 |[Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Współbieżne wycinki dla zestawu danych |10 |10 |
| Liczba bajtów na obiekt w potoku<sup>1</sup> |200 KB |200 KB |
| Liczba bajtów na obiekt danych zestawu i połączone obiekty usługi<sup>1</sup> |100 KB |2000 KB |
| Azure HDInsight na żądanie klaster rdzeni w ramach subskrypcji<sup>2</sup> |60 |[Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Jednostek przenoszenia danych na uruchomienia działania kopiowania w chmurze<sup>3</sup> |32 |[Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Liczba uruchomień działania potoku ponownych prób |1000 |MaxInt (32-bitowa) |

<sup>1</sup> potok, zestaw danych i połączonej usługi obiektów reprezentują powodują ustawienie logicznego grupowania obciążenia. Limity dla tych obiektów nie odnoszą się do ilości danych można przenosić i przetwarzać za pomocą usługi Azure Data Factory. Fabryka danych została zaprojektowana do skalowania, umożliwiając obsługę petabajtów danych.

<sup>2</sup> rdzeni HDInsight na żądanie są przydzielane poza subskrypcją, który zawiera usługi data factory. W wyniku poprzedniego limit wynosi limit liczby rdzeni wymuszane fabryki danych, liczby rdzeni HDInsight na żądanie. Jest inny niż limit liczby rdzeni, która jest skojarzona z subskrypcją platformy Azure.

<sup>3</sup> jednostek przenoszenia danych w chmurze (DMU) w wersji 1 jest używany w operacji kopiowania w chmurze do chmury, Dowiedz się więcej z [jednostek przenoszenia danych (wersja 1) w chmurze](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Aby uzyskać informacje dotyczące rozliczeń, zobacz [cennika usługi Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

| **Zasób** | **Niższy limit domyślny** | **Minimalny limit** |
| --- | --- | --- |
| Interwał harmonogramu |15 minut |15 minut |
| Interwał między ponownymi próbami |1 sekunda |1 sekunda |
| Ponów próbę wykonania wartość limitu czasu |1 sekunda |1 sekunda |

#### <a name="web-service-call-limits"></a>Limity wywołania usługi sieci Web
Usługa Azure Resource Manager ma limity dla wywołań interfejsu API. Może wykonywać wywołania interfejsu API z szybkością, w ramach [limitów interfejsu API usługi Azure Resource Manager](../articles/azure-subscription-service-limits.md#resource-group-limits).
