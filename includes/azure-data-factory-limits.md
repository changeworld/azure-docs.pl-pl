---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 06/20/2018
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 4aa4809c57eaf26b10053d432f9191580ec143a0
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44381101"
---
Data factory to usługa dla wielu dzierżawców, który ma następujące domyślne limity w miejscu, aby upewnić się, że subskrypcjami klientów są chronione przed jego obciążeń. Wiele limity można łatwo wygenerowany dla Twojej subskrypcji do maksymalnego limitu, kontaktując się z pomocą techniczną.

### <a name="version-2"></a>Wersja 2

| Zasób | Limit domyślny | Limit maksymalny |
| -------- | ------------- | ------------- |
| Fabryki danych w subskrypcji platformy Azure | 50 | [Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Całkowita liczba jednostek (potok, zestawy danych, wyzwalacze, połączonych usług, środowisk Integration Runtime) w ramach usługi data factory | 5000 | [Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Łączna liczba rdzeni procesora CPU dla środowiska Azure-SSIS Integration Runtime(s) w ramach jednej subskrypcji | 128 | [Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Potok równoczesny przebiegów na potoku | 100 | [Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Potok równoczesny przebiegów na usługi data factory | 10 000  | [Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Maksymalna liczba działań na potok (w tym wewnętrznego działania dla kontenerów) | 40 | 40 |
| Maksymalna liczba parametrów dla potoku | 50 | 50 |
| Elementy ForEach | 100 000 | 100 000 |
| Równoległość ForEach | 20 | 50 |
| Liczba znaków w wyrażeniu | 8192 | 8192 |
| Minimalny interwał zarówno wyzwalacz okna wirowania | 15 min | 15 min |
| Maksymalny limit czasu uruchomienia działania potoku | 7 dni | 7 dni |
| Liczba bajtów na obiekt w potoku <sup>1</sup> | 200 KB | 200 KB |
| Liczba bajtów na obiekt dla zestawu danych i obiekty usługi połączonej <sup>1</sup> | 100 KB | 2000 KB |
| Jednostki integracji danych według uruchomienia działania kopiowania <sup>3</sup> | 256 | [Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Zapis wywołań interfejsu API | 2500/godz.<br/><br/> Ten limit są nakładane przez usługi Azure Resource Manager, nie usługi Azure Data Factory. | [Skontaktuj się z działem pomocy technicznej](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Odczyt interfejsu API | 12 500/godz.<br/><br/> Ten limit są nakładane przez usługi Azure Resource Manager, nie usługi Azure Data Factory. | [Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Monitorowanie zapytania na minutę: | 1000 | [Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Jednostki operacji CRUD na minutę | 50 | [Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |


### <a name="version-1"></a>Wersja 1

| **Zasób** | **Limit domyślny** | **Limit maksymalny** |
| --- | --- | --- |
| Fabryki danych w subskrypcji platformy Azure |50 |[Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Potoki w fabryce danych |2500 |[Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Zestawy danych w ramach usługi data factory |5000 |[Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Współbieżne wycinki dla zestawu danych |10 |10 |
| Liczba bajtów na obiekt w potoku <sup>1</sup> |200 KB |200 KB |
| Liczba bajtów na obiekt dla zestawu danych i obiekty usługi połączonej <sup>1</sup> |100 KB |2000 KB |
| W ramach subskrypcji, liczba rdzeni na żądanie klaster HDInsight <sup>2</sup> |60 |[Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Jednostek przenoszenia danych na uruchomienia działania kopiowania w chmurze <sup>3</sup> |32 |[Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Liczba uruchomień działania potoku ponownych prób |1000 |MaxInt (32-bitowa) |

<sup>1</sup> potok, zestaw danych i połączonej usługi obiektów reprezentują powodują ustawienie logicznego grupowania obciążenia. Limity dla tych obiektów nie odnoszą się do ilości danych, możesz przenosić i przetwarzać za pomocą usługi Azure Data Factory. Fabryka danych została zaprojektowana do skalowania, umożliwiając obsługę petabajtów danych.

<sup>2</sup> rdzeni HDInsight na żądanie są przydzielane poza subskrypcją, który zawiera usługi data factory. W wyniku powyżej limitu jest Data Factory wymuszone limit podstawowych rdzeni HDInsight na żądanie i różni się od limit liczby rdzeni, skojarzone z subskrypcją platformy Azure.

<sup>3</sup> jednostki integracji danych (DIU) w wersji 2 lub chmurze danych przemieszczania jednostki (DMU) dla v1 jest używana w operacji kopiowania w chmurze do chmury. To miary, która reprezentuje możliwości (kombinację procesora CPU, pamięci i alokacji zasobów sieciowych) w pojedynczą jednostkę w usłudze Data Factory. Można osiągnąć większą przepływność kopiowania przy użyciu więcej DMUs w niektórych scenariuszach. Zapoznaj się [jednostek integracji danych (wersja 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units) i [jednostek przenoszenia danych (wersja 1) w chmurze](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units) sekcji Szczegóły, i [stronę z cennikiem usługi Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/) dla domniemanie rozliczeniami.

<sup>4</sup> Integration Runtime (IR) to infrastruktura obliczeniowa używana przez usługę Azure Data Factory w celu zapewnienia następujących możliwości integracji danych w różnych środowiskach sieciowych: przenoszenie danych, wysyłanie działania do obliczeń usługi, wykonywanie pakietów usług SSIS. Aby uzyskać więcej informacji, zobacz [Omówienie środowiska Integration Runtime](../articles/data-factory/concepts-integration-runtime.md).

| **Zasób** | **Niższy limit domyślny** | **Minimalny limit** |
| --- | --- | --- |
| Interwał harmonogramu |15 minut |15 minut |
| Interwał między ponownymi próbami |1 sekunda |1 sekunda |
| Ponów próbę wykonania wartość limitu czasu |1 sekunda |1 sekunda |

#### <a name="web-service-call-limits"></a>Limity wywołania usługi sieci Web
Usługa Azure Resource Manager ma limity dla wywołań interfejsu API. Może wykonywać wywołania interfejsu API z szybkością, w ramach [limitów interfejsu API usługi Azure Resource Manager](../articles/azure-subscription-service-limits.md#resource-group-limits).
