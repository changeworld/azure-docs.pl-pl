---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 01/08/2020
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 217e48ab21439f03e0b52c894c9aace5b51b1502
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086156"
---
Azure Data Factory to wielodostępna usługa, w której obowiązują następujące domyślne limity chroniące subskrypcje klientów przed ich obciążeniami. Aby podnieść te limity do wartości maksymalnej dla subskrypcji, skontaktuj się z pomocą techniczną.

### <a name="version-2"></a>Wersja 2

| Zasób | Limit domyślny | Limit maksymalny |
| -------- | ------------- | ------------- |
| Fabryki danych w subskrypcji platformy Azure | 800 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Łączna liczba jednostek, takich jak potoki, zestawy danych, wyzwalacze, połączone usługi i środowiska Integration Runtime, w ramach fabryki danych | 5000 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Łączna liczba rdzeni procesora dla środowiska Azure-SSIS Integration Runtime w ramach jednej subskrypcji | 256 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Współbieżne uruchomienia potoków na fabrykę danych współdzielone przez wszystkie potoki w fabryce | 10 000  | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Współbieżne uruchomienia działań zewnętrznych na subskrypcję na [region środowiska Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Działania zewnętrzne są zarządzane w środowisku Integration Runtime, ale wykonywane w połączonych usługach, w tym Databricks, procedurze składowanej, HDInsight, usługach internetowych i innych.</small> | 3000 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Współbieżne uruchomienia działania potoku na subskrypcję na [region środowiska Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>Działania potoku są wykonywane w środowisku Integration Runtime, w tym Lookup, GetMetadata i Delete.</small>| 1000 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Współbieżne operacje tworzenia na subskrypcję na [region środowiska Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>W tym połączenie testowe, przeglądanie listy folderów i listy tabel oraz podgląd danych. | 200 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Współbieżne zużycie jednostek integracji danych<sup>1</sup> na subskrypcję na [region środowiska Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Grupa regionów 1<sup>2</sup>: 6000<br>Grupa regionów 2<sup>2</sup>: 3000<br>Grupa regionów 3<sup>2</sup>: 1500 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maksymalna liczba działań na potok, które obejmują działania wewnętrzne dla kontenerów | 40 | 40 |
| Maksymalna liczba połączonych środowisk Integration Runtime, które można utworzyć dla pojedynczego własnego środowiska Integration Runtime | 100 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maksymalna liczba parametrów na potok | 50 | 50 |
| Elementy ForEach | 100 000 | 100 000 |
| Równoległość ForEach | 20 | 50 |
| Maksymalna liczba przebiegów w kolejce na potok | 100 | 100 |
| Liczba znaków na wyrażenie | 8192 | 8192 |
| Minimalny interwał wyzwalania okna wirowania | 15 min. | 15 min. |
| Maksymalny limit czasu dla uruchomień działania potoku | 7 dni | 7 dni |
| Bajty na obiekt dla obiektów potoku<sup>3</sup> | 200 KB | 200 KB |
| Bajty na obiekt dla obiektów połączonej usługi i zestawu danych<sup>3</sup> | 100 KB | 2000 KB |
| Jednostki integracji danych<sup>1</sup> na uruchomienie działania kopiowania | 256 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Wywołania interfejsu API zapisu | 1200/godz.<br/><br/> Ten limit jest nakładany przez usługę Azure Resource Manager, a nie Azure Data Factory. | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Wywołania interfejsu API odczytu | 12 500/godz.<br/><br/> Ten limit jest nakładany przez usługę Azure Resource Manager, a nie Azure Data Factory. | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Zapytania monitorowane na minutę | 1000 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Operacje CRUD jednostki na minutę | 50 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maksymalny czas trwania sesji debugowania przepływu danych | 8 godzin | 8 godzin |
| Liczba współbieżnych przepływów danych na fabrykę | 50 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Liczba współbieżnych sesji debugowania przepływu danych na użytkownika na fabrykę | 3 | 3 |
| Limit czasu wygaśnięcia środowiska Azure IR przepływu danych | 4 godziny | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> Jednostka integracji danych (DIU) jest używana w operacji kopiowania z chmury do chmury. Dowiedz się więcej: [Jednostki integracji danych (wersja 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Aby uzyskać informacje dotyczące rozliczeń, zobacz [cennik usługi Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [Środowisko Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) jest [dostępne globalnie](https://azure.microsoft.com/global-infrastructure/services/), co zapewnia zgodność danych, wydajność i niższe koszty wyjścia z sieci. 

| Grupa regionów | Regiony | 
| -------- | ------ |
| Grupa regionów 1 | Środkowe stany USA, Wschodnie stany USA, Wschodnie stany USA 2, Europa Północna, Europa Zachodnia, Zachodnie stany USA, Zachodnie stany USA 2 |
| Grupa regionów 2 | Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Indie Środkowe, Japonia Wschodnia, Północno-środkowe stany USA, Południowo-środkowe stany USA, Azja Południowo-Wschodnia, Zachodnio-środkowe stany USA |
| Grupa regionów 3 | Kanada Środkowa, Azja Wschodnia, Francja Środkowa, Korea Środkowa, Południowe Zjednoczone Królestwo |

<sup>3</sup> Potok, zestaw danych i obiekty połączonej usługi reprezentują logiczne grupowanie obciążenia. Limity dla tych obiektów nie odnoszą się do ilości danych, które można przenosić i przetwarzać za pomocą usługi Azure Data Factory. Usługa Data Factory jest zaprojektowana z myślą o skalowaniu do obsługi petabajtów danych.

### <a name="version-1"></a>Wersja 1

| **Zasób** | **Limit domyślny** | **Limit maksymalny** |
| --- | --- | --- |
| Potoki w fabryce danych |2500 |[Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Zestawy danych w fabryce danych |5000 |[Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Wycinki współbieżne na zestaw danych |10 |10 |
| Bajty na obiekt dla obiektów potoku<sup>1</sup> |200 KB |200 KB |
| Bajty na obiekt dla obiektów połączonej usługi i zestawu danych<sup>1</sup> |100 KB |2000 KB |
| Rdzenie klastra na żądanie usługi Azure HDInsight w subskrypcji<sup>2</sup> |60 |[Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Jednostki przenoszenia danych w chmurze na uruchomienie działania kopiowania<sup>3</sup> |32 |[Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Liczba ponownych prób dla uruchomień działania potoku |1000 |MaxInt (wersja 32-bitowa) |

<sup>1</sup> Potok, zestaw danych i obiekty połączonej usługi reprezentują logiczne grupowanie obciążenia. Limity dla tych obiektów nie odnoszą się do ilości danych, które można przenosić i przetwarzać za pomocą usługi Azure Data Factory. Usługa Data Factory jest zaprojektowana z myślą o skalowaniu do obsługi petabajtów danych.

<sup>2</sup> Rdzenie usługi HDInsight na żądanie są przydzielone poza subskrypcją zawierającą fabrykę danych. Z tego powodu poprzednim limitem jest limit rdzeni wymuszany przez usługę Data Factory dla rdzeni usługi HDInsight na żądanie. Jest on inny od limitu rdzeni skojarzonego z Twoją subskrypcją platformy Azure.

<sup>3</sup> Jednostka przenoszenia danych w chmurze (DMU) dla wersji 1 jest używana w operacji kopiowania z chmury do chmury. Dowiedz się więcej: [Jednostki przenoszenia danych w chmurze (wersja 1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Aby uzyskać informacje dotyczące rozliczeń, zobacz [cennik usługi Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

| **Zasób** | **Domyślny dolny limit** | **Minimalny limit** |
| --- | --- | --- |
| Interwał planowania |15 minut |15 minut |
| Interwał między ponownymi próbami |1 sekunda |1 sekunda |
| Wartość limitu czasu ponawiania |1 sekunda |1 sekunda |

#### <a name="web-service-call-limits"></a>Limity wywołań usług internetowych
W usłudze Azure Resource Manager obowiązują limity wywołań interfejsu API. Interfejsy API można wywoływać z szybkością mieszczącą się w [limitach interfejsu API usługi Azure Resource Manager](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).
