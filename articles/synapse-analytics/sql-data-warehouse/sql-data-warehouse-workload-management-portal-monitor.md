---
title: Monitorowanie portalu zarządzania obciążeniem
description: Wskazówki dotyczące monitorowania portalu zarządzania obciążeniami w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: b1f21a996f7394def4d6b1e8bde9a5ccdf703dbb
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632422"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring-preview"></a>Usługa Azure Synapse Analytics — monitorowanie portalu zarządzania obciążeniem (wersja zapoznawcza)

W tym artykule wyjaśniono, jak monitorować wykorzystanie zasobów [grupy obciążenia](sql-data-warehouse-workload-isolation.md#workload-groups) i działanie kwerend.
Aby uzyskać szczegółowe informacje na temat konfigurowania Eksploratora metryk platformy Azure, zobacz artykuł [Wprowadzenie do Eksploratora metryk platformy Azure.](../../azure-monitor/platform/metrics-getting-started.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)  Zobacz sekcję [Wykorzystanie zasobów](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) w dokumentacji monitorowania usługi Azure Synapse Analytics, aby uzyskać szczegółowe informacje na temat monitorowania zużycia zasobów systemowych.
Istnieją dwie różne kategorie metryk grupy obciążenia przewidziane do monitorowania zarządzania obciążeniem: alokacji zasobów i działania kwerendy.  Te metryki można podzielić i filtrować według grupy obciążenia.  Metryki mogą być dzielone i filtrowane na podstawie tego, czy są zdefiniowane przez system (grupy obciążenia klasy zasobów) lub zdefiniowane przez użytkownika (utworzone przez użytkownika ze [składnią CREATE WORKLOAD GROUP).](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)

## <a name="workload-management-metric-definitions"></a>Definicje metryki zarządzania obciążeniem

|Nazwa metryki                    |Opis  |Typ agregacji |
|-------------------------------|-------------|-----------------|
|Efektywny procent zasobów cap | *Procent zasobu efektywnego limitu* jest twardym limitem procentu zasobów dostępnych dla grupy obciążenia, z uwzględnieniem *efektywnej procentu zasobów min* przydzielonego dla innych grup obciążenia. *Metryka procentu zasobów limitu efektywnego* jest konfigurowana przy użyciu parametru `CAP_PERCENTAGE_RESOURCE` w składni CREATE WORKLOAD [GROUP.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Wartość efektywna jest opisana w tym miejscu.<br><br>Na przykład jeśli `DataLoads` grupa obciążenia `CAP_PERCENTAGE_RESOURCE` jest tworzona z = 100 i innej grupy obciążenia jest tworzony z `DataLoads` efektywne minimalny procent zasobów 25%, *procent zasobu efektywne limit* dla grupy obciążenia wynosi 75%.<br><br>*Procent zasobu limitu efektywnego* określa górną granicę współbieżności (a tym samym potencjalnej przepływności) grupy obciążenia może osiągnąć.  Jeśli wymagana jest dodatkowa przepływność poza to, co jest obecnie zgłaszane przez `CAP_PERCENTAGE_RESOURCE` *Metrykę procentu zasobów efektywnego limitu,* zwiększ , zmniejsz `MIN_PERCENTAGE_RESOURCE` inne grupy obciążenia lub skaluj w górę wystąpienie, aby dodać więcej zasobów.  Zmniejszenie `REQUEST_MIN_RESOURCE_GRANT_PERCENT` może zwiększyć współbieżność, ale nie może zwiększyć ogólną przepływność.| Min, Średnia, Maks. |
|Efektywny procent minimalnego zasobu |*Procent zasobu efektywnego min* jest minimalnym procentem zasobów zarezerwowanych i odizolowanych dla grupy obciążenia, biorąc pod uwagę minimalny poziom usług.  Metryka Procent procentu efektywnego `MIN_PERCENTAGE_RESOURCE` minimalnego zasobu jest konfigurowana przy użyciu parametru w składni [CREATE WORKLOAD GROUP.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Wartość efektywna jest [opisana tutaj](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values).<br><br>Użyj typu agregacji sumy, gdy ta metryka jest niefiltrowana i nierozświetlona, aby monitorować całkowitą izolację obciążenia skonfigurowana w systemie.<br><br>*Procent zasobu Efektywne min* określa dolną granicę gwarantowanej współbieżności (a tym samym gwarantowanej przepływności) grupy obciążenia może osiągnąć.  Jeśli potrzebne są dodatkowe zasoby gwarantowane poza to, co jest obecnie `MIN_PERCENTAGE_RESOURCE` zgłaszane przez *Metrykę procentowy efektywnego minimalnego zasobu,* zwiększ parametr skonfigurowany dla grupy obciążenia.  Zmniejszenie `REQUEST_MIN_RESOURCE_GRANT_PERCENT` może zwiększyć współbieżność, ale nie może zwiększyć ogólną przepływność. |Min, Średnia, Maks.|
|Aktywne kwerendy grupy obciążenia  |Ta metryka zgłasza aktywne zapytania w grupie obciążenia.  Za pomocą tej metryki niefiltrowane i unsplit wyświetla wszystkie aktywne zapytania uruchomione w systemie.|Suma         |
|Alokacja grupy obciążenia według maksymalnego procentu zasobów |Ta metryka wyświetla procent alokacji zasobów w stosunku do *procentu zasobu Efektywny limit* na grupę obciążenia.  Ta metryka zapewnia efektywne wykorzystanie grupy obciążenia.<br><br>Rozważmy grupę `DataLoads` obciążenia z *procentem zasobu efektywnego limitu* 75% i `REQUEST_MIN_RESOURCE_GRANT_PERCENT` skonfigurowanym na poziomie 25%.  *Alokacja grupy obciążenia według maksymalnej* `DataLoads` wartości procentowej zasobów filtrowanych do 33% (25% / 75%) jeśli w tej grupie obciążenia uruchomiono pojedynczą kwerendę.<br><br>Ta metryka służy do identyfikowania wykorzystania grupy obciążenia.  Wartość bliska 100% wskazuje, że używane są wszystkie zasoby dostępne dla grupy obciążenia.  Ponadto grupa *obciążenia w kolejce kwerend metrics* dla tej samej grupy obciążenia pokazując wartość większą niż zero wskazuje, że grupa obciążenia będzie korzystać z dodatkowych zasobów, jeśli przydzielone.  Z drugiej strony, jeśli ta metryka jest stale niska, a *aktywne zapytania grupy obciążenia* są niskie, grupa obciążenia nie jest wykorzystywana.  Sytuacja ta jest szczególnie problematyczna, jeśli *procent zasobów efektywnego limitu* jest większy od zera, co wskazuje na [niedostatecznie przywykłą izolację obciążenia.](#underutilized-workload-isolation)|Min, Średnia, Maks. |
|Alokacja grupy obciążenia według procentu systemu | Ta metryka wyświetla procent alokacji zasobów względem całego systemu.<br><br>Należy wziąć `DataLoads` pod `REQUEST_MIN_RESOURCE_GRANT_PERCENT` uwagę grupę obciążenia ze skonfigurowanym na 25%.  *Alokacja grupy obciążenia według* przefiltrowanego procentu `DataLoads` wartości systemu wyniesie 25% (25% / 100%) jeśli w tej grupie obciążenia uruchomiono pojedynczą kwerendę.|Min, Średnia, Maks. |
|Limity czasu kwerend grupy obciążenia |Zapytania dla grupy obciążenia, które zostały przesunął limit czasu.  Limity czasu kwerendy zgłoszone przez tę metrykę są tylko wtedy, gdy kwerenda rozpoczęła wykonywanie (nie obejmuje czas oczekiwania z powodu blokowania lub oczekiwania zasobów).<br><br>Limit czasu kwerendy jest `QUERY_EXECUTION_TIMEOUT_SEC` konfigurowany przy użyciu parametru w składni [GRUPY TWORZENIA OBCIĄŻENIA.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Zwiększenie wartości może zmniejszyć liczbę limitów czasu kwerendy.<br><br>Należy rozważyć `REQUEST_MIN_RESOURCE_GRANT_PERCENT` zwiększenie parametru dla grupy obciążenia, aby zmniejszyć ilość limitów czasu i przydzielić więcej zasobów na kwerendę.  Uwaga: `REQUEST_MIN_RESOURCE_GRANT_PERCENT` zwiększenie zmniejsza ilość współbieżności dla grupy obciążenia. |Suma |
|Kwerendy w kolejce grupy obciążeń | Zapytania dla grupy obciążenia, które są obecnie w kolejce oczekiwania na rozpoczęcie wykonywania.  Kwerendy mogą być kolejką, ponieważ oczekują na zasoby lub blokady.<br><br>Zapytania mogą czekać z wielu powodów.  Jeśli system jest przeciążony, a zapotrzebowanie na współbieżność jest większe niż to, co jest dostępne, kwerendy będą kolejkowane.<br><br>Należy rozważyć dodanie większej liczby zasobów `CAP_PERCENTAGE_RESOURCE` do grupy obciążenia, zwiększając parametr w [instrukcji CREATE WORKLOAD GROUP.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)  Jeśli `CAP_PERCENTAGE_RESOURCE` jest większa niż Metryka *procentu zasobów limitu efektywnego,* skonfigurowana izolacja obciążenia dla innej grupy obciążenia ma wpływ na zasoby przydzielone do tej grupy obciążenia.  Należy rozważyć `MIN_PERCENTAGE_RESOURCE` obniżenie innych grup obciążenia lub skalowanie w górę wystąpienia, aby dodać więcej zasobów. |Suma |

## <a name="monitoring-scenarios-and-actions"></a>Monitorowanie scenariuszy i działań

Poniżej znajduje się seria konfiguracji wykresu, aby wyróżnić użycie metryki zarządzania obciążeniem do rozwiązywania problemów wraz ze skojarzonymi akcjami w celu rozwiązania problemu.

### <a name="underutilized-workload-isolation"></a>Niedostatecznie przywdziona izolacja obciążenia

Należy wziąć pod uwagę następującą grupę obciążenia `wgPriority` i konfigurację klasyfikatora, w `wcCEOPriority` której tworzona jest grupa obciążenia o nazwie i *TheCEO* `membername` jest mapowana do niej przy użyciu klasyfikatora obciążenia.  Grupa `wgPriority` obciążenia ma skonfigurowano dla niej izolację`MIN_PERCENTAGE_RESOURCE` obciążenia 25% ( = 25).  Każde zapytanie przesłane przez *TheCEO* otrzymuje 5% zasobów systemowych (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5).

```sql
CREATE WORKLOAD GROUP wgPriority
WITH ( MIN_PERCENTAGE_RESOURCE = 25
      ,CAP_PERCENTAGE_RESOURCE = 50
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5);

CREATE WORKLOAD CLASSIFIER wcCEOPriority
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```

Poniższy wykres jest skonfigurowany w następujący sposób:<br>
Metryka 1: *Efektywny procent minimalnego zasobu* (średnia agregacja) `blue line`<br>
Metryka 2: *Alokacja grupy obciążenia według procentu systemu* (średnia agregacja) `purple line`<br>
Filtr: [Grupa obciążenia] =`wgPriority`<br>
![underutilized-wg.png](./media/sql-data-warehouse-workload-management-portal-monitor/underutilized-wg.png) Wykres pokazuje, że przy izolacji obciążenia 25% tylko 10% jest używane średnio.  W takim przypadku `MIN_PERCENTAGE_RESOURCE` wartość parametru może zostać obniżona do 10 lub 15 i umożliwić innym obciążeń w systemie zużywać zasoby.

### <a name="workload-group-bottleneck"></a>Wąskie gardło grupy obciążenia

Należy wziąć pod uwagę następującą grupę obciążenia `wgDataAnalyst` i konfigurację klasyfikatora, w której `wcDataAnalyst` grupa obciążenia o nazwie jest tworzona, a *DataAnalyst* `membername` jest mapowany do niej przy użyciu klasyfikatora obciążenia.  Grupa `wgDataAnalyst` obciążenia ma skonfigurowaną dla niej izolację obciążenia 6% (`MIN_PERCENTAGE_RESOURCE` = 6) i limit zasobów 9% (`CAP_PERCENTAGE_RESOURCE` = 9).  Każde zapytanie przesłane przez *DataAnalyst* otrzymuje 3% zasobów systemowych (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 3).

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6
      ,CAP_PERCENTAGE_RESOURCE = 9
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3);

CREATE WORKLOAD CLASSIFIER wcDataAnalyst
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```

Poniższy wykres jest skonfigurowany w następujący sposób:<br>
Metryka 1: *Efektywny procent zasobu limitu* (średnia agregacja) `blue line`<br>
Metryka 2: *Alokacja grupy obciążenia według maksymalnego procentu zasobów* (średnia agregacja) `purple line`<br>
Metryka 3: *Kwerendy w kolejce* `turquoise line`grupy obciążeń (agregacja sumy)<br>
Filtr: [Grupa obciążenia] =`wgDataAnalyst`<br>
![bottle-necked-wg](./media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png) Wykres pokazuje, że przy 9% limit zasobów, grupa obciążenia jest 90% + wykorzystywane (z *alokacji grupy obciążenia przez maksymalny procent zasobów metryki*).  Istnieje stałe kolejkowanie zapytań, jak pokazano na *podstawie metryki kwerend w kolejce grupy obciążenia*.  W takim przypadku `CAP_PERCENTAGE_RESOURCE` zwiększenie do wartości wyższej niż 9% pozwoli więcej zapytań do wykonania jednocześnie.  Zwiększenie `CAP_PERCENTAGE_RESOURCE` zakłada, że istnieje wystarczająca ilość zasobów dostępnych i nie izolowane przez inne grupy obciążenia.  Sprawdź, czy limit został zwiększony, sprawdzając *metrykę procentowy zasobu Efektywnego limitu*.  Jeśli wymagana jest większa przepustowość, `REQUEST_MIN_RESOURCE_GRANT_PERCENT` należy również rozważyć zwiększenie wartości większej niż 3.  Zwiększenie `REQUEST_MIN_RESOURCE_GRANT_PERCENT` może umożliwić kwerendy działać szybciej.

## <a name="next-steps"></a>Następne kroki

- [Szybki start: konfigurowanie izolacji obciążenia przy użyciu funkcji T-SQL](quickstart-configure-workload-isolation-tsql.md)<br>
- [TWORZENIE GRUPY OBCIĄŻEŃ (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)<br>
- [TWORZENIE KLASYFIKATORA OBCIĄŻENIA (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)<br>
- [Monitorowanie wykorzystania zasobów](sql-data-warehouse-concept-resource-utilization-query-activity.md)
