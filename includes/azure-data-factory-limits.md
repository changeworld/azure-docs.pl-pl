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
ms.openlocfilehash: d65c75a9b4f308ddd1bb6a6bff28c52c946e4f05
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320036"
---
Fabryka danych jest usługą wielodostępne, która ma następujące domyślne limity mające na celu upewnij się, że subskrypcjami klientów są chronione przez obciążeń. Wiele granic można łatwo wygenerowany dla Twojej subskrypcji do maksymalnego limitu za pośrednictwem pracowników pomocy technicznej.

### <a name="version-2"></a>Wersja 2

| Zasób | Limit domyślny | Limit maksymalny |
| -------- | ------------- | ------------- |
| Fabryki danych w subskrypcji platformy Azure | 50 | [Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Całkowita liczba jednostek (potoku, zestawy danych, wyzwalaczy, połączone usługi, integracji środowiska uruchomieniowe) w fabryce danych | 5000 | [Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Całkowita liczba rdzeni procesora CPU dla Runtime(s) integracji usług SSIS Azure w ramach jednej subskrypcji | 100 | [Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Potok współbieżnych jest uruchamiane na potoku | 100 | [Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Potok współbieżnych jest uruchamiane na fabryki danych | 10 000  | [Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Maksymalna liczba działań na potok (zawiera wewnętrzny działania dla kontenerów) | 40 | 40 |
| Maksymalna liczba parametrów na potoku | 50 | 50 |
| Elementy ForEach | 100,000 | 100,000 |
| Równoległość ForEach | 20 | 50 |
| Liczba znaków na wyrażenia | 8192 | 8192 |
| Minimalny interwał wyzwalacza okno wirowania | 15 min | 15 min |
| Maksymalny limit uruchomień działania w potoku | 7 dni | 7 dni |
| Bajtów dla każdego obiektu dla potoku obiektów <sup>1</sup> | 200 KB | 200 KB |
| Liczba bajtów na obiekt zestawu danych i obiektów powiązanych z <sup>1</sup> | 100 KB | 2000 KB |
| Jednostki przepływu danych na uruchamiania działania w chmurze <sup>3</sup> | 256 | [Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Zapis wywołań interfejsu API | 2500/godz.<br/><br/> Ten limit jest narzucone przez usługi Azure Resource Manager, nie fabryki danych Azure. | [Skontaktuj się z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Wywołania API odczytu | 12 500/godz.<br/><br/> Ten limit jest narzucone przez usługi Azure Resource Manager, nie fabryki danych Azure. | [Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |


### <a name="version-1"></a>Wersja 1

| **Zasób** | **Limit domyślny** | **Limit maksymalny** |
| --- | --- | --- |
| Fabryki danych w subskrypcji platformy Azure |50 |[Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Potoki w fabryce danych |2500 |[Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Zestawy danych w fabryce danych |5000 |[Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Współbieżne wycinki dla zestawu danych |10 |10 |
| Bajtów dla każdego obiektu dla potoku obiektów <sup>1</sup> |200 KB |200 KB |
| Liczba bajtów na obiekt zestawu danych i obiektów powiązanych z <sup>1</sup> |100 KB |2000 KB |
| Rdzeni na żądanie klastra usługi HDInsight w ramach subskrypcji <sup>2</sup> |60 |[Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Jednostki przepływu danych na uruchamiania działania w chmurze <sup>3</sup> |32 |[Kontakt z pomocą techniczną](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Liczba uruchomień działania potoku ponownych prób |1000 |MaxInt (32-bitowe) |

<sup>1</sup> potoku, zestawu danych i obiektów powiązanych z reprezentują logiczne grupowanie obciążenie. Limity dla tych obiektów nie odnoszą się do ilości danych, można przenieść i przetwarzania w usłudze fabryka danych Azure. Fabryka danych zaprojektowano w celu skalowania, umożliwiając obsługę petabajtów danych.

<sup>2</sup> rdzeni na żądanie usługi HDInsight są przydzielane poza subskrypcji, która zawiera fabryki danych. W związku z tym powyżej limitu jest fabryki danych wymuszone limit podstawowych rdzeni HDInsight na żądanie i różni się od limit rdzeni skojarzone z subskrypcją platformy Azure.

<sup>3</sup> jednostki przepływu danych w chmurze (DMU) jest używany w operacji kopiowania w chmurze z chmurą. Stanowi ona miarę, która odzwierciedla wydajność (kombinacja Procesora, pamięci i alokacji zasobów w sieci) w pojedynczą jednostkę w fabryce danych. Wyższej przepustowości kopii można osiągnąć za pomocą więcej DMUs w niektórych scenariuszach. Zapoznaj się [jednostki przepływu danych (V2) w chmurze](../articles/data-factory/copy-activity-performance.md#cloud-data-movement-units) i [jednostki przepływu danych (wersja 1) w chmurze](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units) sekcji Szczegóły.

<sup>4</sup> integrację środowiska uruchomieniowego (IR) jest używany przez fabryki danych Azure do zapewniają następujące możliwości integracji danych w różnych środowiskach sieci infrastruktury obliczeniowej: przenoszenie danych, wysyłki działań do obliczenia usług, Wykonanie pakietów SSIS. Aby uzyskać więcej informacji, zobacz [Omówienie środowiska uruchomieniowego integracji](../articles/data-factory/concepts-integration-runtime.md).

| **Zasób** | **Dolny limit domyślny** | **Minimalny limit** |
| --- | --- | --- |
| Interwał harmonogramu |15 minut |15 minut |
| Interwał między ponownymi próbami |1 sekunda |1 sekunda |
| Spróbuj ponownie wartość limitu czasu |1 sekunda |1 sekunda |

#### <a name="web-service-call-limits"></a>Ograniczenia wywołania usługi sieci Web
Usługa Azure Resource Manager ma limity dla interfejsu API. Istnieje możliwość wywołania interfejsu API z szybkością w [ogranicza interfejsu API usługi Azure Resource Manager](../articles/azure-subscription-service-limits.md#resource-group-limits).
