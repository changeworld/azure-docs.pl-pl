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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086156"
---
Usługa Azure Data Factory to usługa wielodostępna, która ma następujące domyślne limity, aby upewnić się, że subskrypcje klientów są chronione przed obciążeniami innych. Aby podnieść limity do maksimum dla subskrypcji, skontaktuj się z pomocą techniczną.

### <a name="version-2"></a>Wersja 2

| Zasób | Limit domyślny | Limit maksymalny |
| -------- | ------------- | ------------- |
| Fabryki danych w ramach subskrypcji platformy Azure | 800 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Całkowita liczba jednostek, takich jak potoki, zestawy danych, wyzwalacze, połączone usługi i środowiska wykonawcze integracji, w fabryce danych | 5000 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Całkowita liczba rdzeni procesora cpu dla środowiska wykonawczego integracji platformy Azure-SSIS w ramach jednej subskrypcji | 256 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Potok współbieżny działa na fabrykę danych, która jest współużytkowana przez wszystkie rurociągi w fabryce | 10 000  | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Równoczesne działanie zewnętrzne uruchamia się na subskrypcję na [region środowiska wykonawczego integracji platformy Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Działania zewnętrzne są zarządzane w czasie wykonywania integracji, ale są wykonywane w połączonych usługach, w tym Databricks, procedura składowana, HDInsights, Web i inne.</small> | 3000 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Działanie potoku równoczesnych uruchamia się na subskrypcję na [region środowiska wykonawczego integracji platformy Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>Działania potoku są wykonywane w czasie wykonywania integracji, w tym odnośnik, GetMetadata i Delete.</small>| 1000 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Równoczesne operacje tworzenia na subskrypcję na [region środowiska wykonawczego integracji platformy Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>W tym połączenie testowe, przeglądanie listy folderów i listy tabel, wyświetlanie podglądu danych. | 200 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Jednostki równoczesne integracji danych<sup>1</sup> zużycie na subskrypcję na [region środowiska wykonawczego integracji platformy Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Grupa regionów 1<sup>2</sup>: 6000<br>Grupa regionów 2<sup>2</sup>: 3000<br>Grupa regionów 3<sup>2</sup>: 1500 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maksymalna liczba działań na rurociąg, co obejmuje działania wewnętrzne dla kontenerów | 40 | 40 |
| Maksymalna liczba połączonych uruchomień integracji, które można utworzyć w jednym własnym czasie wykonywania integracji | 100 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maksymalne parametry na potok | 50 | 50 |
| Elementy ForEach | 100 000 | 100 000 |
| ForEach równoległości | 20 | 50 |
| Maksymalna liczba uruchomień w kolejce na potok | 100 | 100 |
| Znaki na wyrażenie | 8192 | 8192 |
| Minimalny interwał wyzwalania okna tumbling | 15 min. | 15 min. |
| Maksymalny limit czasu dla przebiegów działania potoku | 7 dni | 7 dni |
| Bajty na obiekt dla obiektów potoku<sup>3</sup> | 200 KB | 200 KB |
| Bajty na obiekt dla zestawu danych i połączonych obiektów usługi<sup>3</sup> | 100 KB | 2000 KB |
| Jednostki integracji danych<sup>1</sup> na przebieg działania kopiowania | 256 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Pisanie wywołań interfejsu API | 1200/h<br/><br/> Ten limit jest narzucany przez usługę Azure Resource Manager, a nie usługę Azure Data Factory. | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Odczytywanie wywołań interfejsu API | 12 500/h<br/><br/> Ten limit jest narzucany przez usługę Azure Resource Manager, a nie usługę Azure Data Factory. | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Monitorowanie zapytań na minutę | 1000 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Jednostki CRUD operacji na minutę | 50 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maksymalny czas sesji debugowania przepływu danych | 8 godz. | 8 godz. |
| Jednoczesna liczba przepływów danych na fabrykę | 50 | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Jednoczesna liczba sesji debugowania przepływu danych na użytkownika na fabrykę | 3 | 3 |
| Limit czasu wygaśnięcia usługi Azure ir usługi Data Flow | 4 godz. | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> Jednostka integracji danych (DIU) jest używana w operacji kopiowania między chmurami, dowiedz się więcej z [jednostek integracji danych (wersja 2).](../articles/data-factory/copy-activity-performance.md#data-integration-units) Aby uzyskać informacje na temat rozliczeń, zobacz [Ceny usługi Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [Środowisko uruchomieniowe integracji platformy Azure](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) jest dostępne na całym [świecie,](https://azure.microsoft.com/global-infrastructure/services/) aby zapewnić zgodność danych, wydajność i niższe koszty transferu danych wychodzących do sieci. 

| Grupa regionów | Regiony | 
| -------- | ------ |
| Grupa regionów 1 | Stany Zjednoczone Środkowe, Wschodnie stany USA, Wschodnie STANY USA2, Europa Północna, Europa Zachodnia, Zachodnie stany USA, Zachodnie stany USA 2 |
| Grupa regionów 2 | Australia Wschodnia, Australia Południowo-Wschodnia, Brazylia Południowa, Indie Środkowe, Japonia Wschodnia, Północnocentralne STANY USA, Południowo-środkowe STANY USA, Azja Południowo-Wschodnia, Środkowe Stany Zjednoczone |
| Grupa regionów 3 | Kanada Środkowa, Azja Wschodnia, Francja Środkowa, Korea Środkowa, Wielka Brytania Południowa |

<sup>3</sup> Potok, zestaw danych i połączone obiekty usługi reprezentują logiczne grupowanie obciążenia. Limity dla tych obiektów nie odnoszą się do ilości danych, które można przenieść i przetworzyć za pomocą usługi Azure Data Factory. Fabryka danych jest przeznaczona do skalowania do obsługi petabajtów danych.

### <a name="version-1"></a>Wersja 1

| **Zasobów** | **Limit domyślny** | **Maksymalny limit** |
| --- | --- | --- |
| Rurociągi w fabryce danych |2500 |[Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Zestawy danych w fabryce danych |5000 |[Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Równoczesne wycinki na zestaw danych |10 |10 |
| Bajty na obiekt dla obiektów potoku<sup>1</sup> |200 KB |200 KB |
| Bajty na obiekt dla zestawu danych i połączonych obiektów usługi<sup>1</sup> |100 KB |2000 KB |
| Rdzenie klastra usługi Azure HDInsight na żądanie w ramach subskrypcji<sup>2</sup> |60 |[Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Jednostki przenoszenia danych w chmurze na przebieg działania kopiowania<sup>3</sup> |32 |[Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Liczba ponownych prób dla przebiegów działania potoku |1000 |MaxInt (32 bit) |

<sup>1</sup> Potok, zestaw danych i połączone obiekty usługi reprezentują logiczne grupowanie obciążenia. Limity dla tych obiektów nie odnoszą się do ilości danych, które można przenieść i przetworzyć za pomocą usługi Azure Data Factory. Fabryka danych jest przeznaczona do skalowania do obsługi petabajtów danych.

<sup>2</sup> Rdzenie HDInsight na żądanie są przydzielane z subskrypcji zawierającej fabrykę danych. W rezultacie poprzedni limit jest data factory wymuszone limit rdzeni dla rdzeni HDInsight na żądanie. Różni się od limitu podstawowego skojarzonego z subskrypcją platformy Azure.

<sup>3</sup> Jednostka przenoszenia danych w chmurze (DMU) w wersji 1 jest używana w operacji kopiowania między chmurami, dowiedz się więcej z [jednostek przenoszenia danych w chmurze (wersja 1).](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units) Aby uzyskać informacje na temat rozliczeń, zobacz [Ceny usługi Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

| **Zasobów** | **Domyślny dolny limit** | **Minimalny limit** |
| --- | --- | --- |
| Interwał planowania |15 minut |15 minut |
| Interwał między próbami ponawiania |1 sekunda |1 sekunda |
| Ponów próbę przeokreślenia limitu czasu |1 sekunda |1 sekunda |

#### <a name="web-service-call-limits"></a>Limity połączeń usługi sieci Web
Usługa Azure Resource Manager ma limity dla wywołań interfejsu API. Można wykonywać wywołania interfejsu API z szybkością w [ramach limitów interfejsu API usługi Azure Resource Manager](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).
