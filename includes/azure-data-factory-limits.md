---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 1/10/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 6d06ac6efd08c14f77fd963ddf2c67de54260959
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64733753"
---
Usługa Azure Data Factory to wielodostępna usługa, która ma następujące domyślne limity w miejscu, aby upewnić się, że subskrypcjami klientów są chronione przed jego obciążeń. Aby zwiększyć limity do maksimum dla Twojej subskrypcji, skontaktuj się z działem pomocy technicznej.

### <a name="version-2"></a>Wersja 2

| Resource | Limit domyślny | Limit maksymalny |
| -------- | ------------- | ------------- |
| Fabryki danych w subskrypcji platformy Azure | 50 | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Całkowita liczba jednostek, takich jak potoki, zestawy danych, wyzwalacze, połączonych usług i środowisk integration Runtime w fabryce danych | 5000 | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Łączna liczba rdzeni procesora CPU dla środowiska Azure-SSIS Integration Runtime w ramach jednej subskrypcji | 256 | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Potok równoczesny przebiegów na usługi data factory, która jest współużytkowana przez wszystkie potoki w fabryce | 10 000  | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maksymalna działań na potok, który zawiera wewnętrzny działania dla kontenerów | 40 | 40 |
| Maksymalna liczba środowisk połączonych integration Runtime, utworzony względem pojedynczej własnego środowiska integration runtime | 100 | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Maksymalna parametry dla potoku | 50 | 50 |
| Elementy ForEach | 100 000 | 100 000 |
| Równoległość ForEach | 20 | 50 |
| Liczba znaków w wyrażeniu | 8192 | 8192 |
| Interwał wyzwalacza okna wirowania minimalne | 15 min | 15 min |
| Uruchamia maksymalnego limitu czasu dla działania potoku | 7 dni | 7 dni |
| Liczba bajtów na obiekt w potoku<sup>1</sup> | 200 KB | 200 KB |
| Liczba bajtów na obiekt dla zestawu danych i obiekty usługi połączonej<sup>1</sup> | 100 KB | 2000 KB |
| Jednostki integracji danych według uruchomienia działania kopiowania<sup>3</sup> | 256 | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Zapis wywołań interfejsu API | 2500/godz.<br/><br/> Ten limit są nakładane przez usługi Azure Resource Manager, nie usługi Azure Data Factory. | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Odczyt interfejsu API | 12 500/godz.<br/><br/> Ten limit są nakładane przez usługi Azure Resource Manager, nie usługi Azure Data Factory. | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Monitorowanie zapytania na minutę: | 1000 | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Jednostki operacji CRUD na minutę | 50 | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |


### <a name="version-1"></a>Wersja 1

| **Zasób** | **Limit domyślny** | **Maksymalny limit** |
| --- | --- | --- |
| Fabryki danych w subskrypcji platformy Azure |50 |[Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Potoki w fabryce danych |2,500 |[Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Zestawy danych w ramach usługi data factory |5000 |[Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Współbieżne wycinki dla zestawu danych |10 |10 |
| Liczba bajtów na obiekt w potoku<sup>1</sup> |200 KB |200 KB |
| Liczba bajtów na obiekt danych zestawu i połączone obiekty usługi<sup>1</sup> |100 KB |2000 KB |
| Azure HDInsight na żądanie klaster rdzeni w ramach subskrypcji<sup>2</sup> |60 |[Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Jednostek przenoszenia danych na uruchomienia działania kopiowania w chmurze<sup>3</sup> |32 |[Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Liczba uruchomień działania potoku ponownych prób |1000 |MaxInt (32-bitowa) |

<sup>1</sup>potok, zestaw danych i połączonej usługi obiektów reprezentują powodują ustawienie logicznego grupowania obciążenia. Limity dla tych obiektów nie odnoszą się do ilości danych można przenosić i przetwarzać za pomocą usługi Azure Data Factory. Fabryka danych została zaprojektowana do skalowania, umożliwiając obsługę petabajtów danych.

<sup>2</sup>rdzeni HDInsight na żądanie są przydzielane poza subskrypcją, który zawiera usługi data factory. W wyniku poprzedniego limit wynosi limit liczby rdzeni wymuszane fabryki danych, liczby rdzeni HDInsight na żądanie. Jest inny niż limit liczby rdzeni, która jest skojarzona z subskrypcją platformy Azure.

<sup>3</sup>jednostka integracji danych (DIU) w wersji 2 lub chmurze jednostek przenoszenia danych (DMU) w wersji 1 jest używana w operacji kopiowania w chmurze do chmury. Jest miarą, który reprezentuje pojedynczą jednostkę w usłudze Data Factory. Łączy ona procesora CPU, pamięci i alokacji zasobów sieciowych. Można osiągnąć większą przepływność kopiowania przy użyciu więcej DMUs w niektórych scenariuszach. Aby uzyskać więcej informacji, zobacz [jednostek integracji danych (wersja 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units) i [jednostek przenoszenia danych (wersja 1) w chmurze](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Aby uzyskać informacje dotyczące rozliczeń, zobacz [cennika usługi Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>4</sup>środowiska integration runtime (IR) to infrastruktura obliczeniowa używana przez usługę Azure Data Factory do zapewnienia możliwości integracji danych w różnych środowiskach sieciowych, takich jak przenoszenia danych, wysyłanie działania do obliczeń usługi, i wykonywanie pakietów usług SSIS. Aby uzyskać więcej informacji, zobacz [Omówienie środowiska Integration runtime](../articles/data-factory/concepts-integration-runtime.md).

| **Zasób** | **Niższy limit domyślny** | **Minimalny limit** |
| --- | --- | --- |
| Interwał harmonogramu |15 minut |15 minut |
| Interwał między ponownymi próbami |1 sekunda |1 sekunda |
| Ponów próbę wykonania wartość limitu czasu |1 sekunda |1 sekunda |

#### <a name="web-service-call-limits"></a>Limity wywołania usługi sieci Web
Usługa Azure Resource Manager ma limity dla wywołań interfejsu API. Może wykonywać wywołania interfejsu API z szybkością, w ramach [limitów interfejsu API usługi Azure Resource Manager](../articles/azure-subscription-service-limits.md#resource-group-limits).
