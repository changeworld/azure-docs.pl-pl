---
title: Monitorowanie portalu zarządzania obciążeniami
description: Wskazówki dotyczące monitorowania w portalu zarządzania obciążeniami w usłudze Azure Synapse Analytics.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 01/13/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: f3baaab59031c4cfad036a7181318502d1969715
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942427"
---
# <a name="azure-synapse-analytics--workload-management-portal-monitoring-preview"></a>Azure Synapse Analytics — Monitorowanie portal zarządzania obciążenia (wersja zapoznawcza)
W tym artykule wyjaśniono, jak monitorować wykorzystanie zasobów [grupy obciążeń](sql-data-warehouse-workload-isolation.md#workload-groups) i działania zapytań. Aby uzyskać szczegółowe informacje na temat konfigurowania Eksplorator metryk platformy Azure, zobacz artykuł [wprowadzenie do usługi azure Eksplorator metryk](../azure-monitor/platform/metrics-getting-started.md) .  Aby uzyskać szczegółowe informacje na temat monitorowania zużycia zasobów systemowych, zobacz sekcję [użycie zasobów](sql-data-warehouse-concept-resource-utilization-query-activity.md#resource-utilization) w dokumentacji dotyczącej monitorowania Azure SQL Data Warehouse.
Dostępne są dwie różne kategorie metryk grup obciążenia do monitorowania zarządzania obciążeniami: aktywność przydziału zasobów i zapytań.  Te metryki można podzielić i filtrować według grupy obciążeń.  Metryki mogą być podzielone i filtrowane w zależności od tego, czy są zdefiniowane przez system (grupy obciążenia klasy zasobów) czy zdefiniowane przez użytkownika (utworzone przez użytkownika ze składnią [tworzenia grupy obciążeń](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) ).

## <a name="workload-management-metric-definitions"></a>Definicje metryk zarządzania obciążeniami

|Nazwa metryki                    |Opis  |Typ agregacji |
|-------------------------------|-------------|-----------------|
|Procent zasobów obowiązujących dla limitu | *Procent zasobów obowiązujących* dla limitu jest stałym limitem procentu zasobów dostępnych dla grupy obciążeń, uwzględniając *efektywną minimalną stawkę procentową zasobów* przydzieloną dla innych grup obciążeń. Metryka *procentu zasobów efektywnych* jest konfigurowana przy użyciu parametru `CAP_PERCENTAGE_RESOURCE` w SKŁADNI [tworzenia grupy obciążeń](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Wartość efektywna jest opisana tutaj.<br><br>Na przykład jeśli grupa obciążeń `DataLoads` jest tworzona z `CAP_PERCENTAGE_RESOURCE` = 100 i zostanie utworzona inna grupa obciążeń z efektywną wartością procentową zasobu wynoszącą 25%, *procent efektywnego zasobu* dla grupy obciążeń `DataLoads` wynosi 75%.<br><br>*Procent zasobu efektywnego limitu* określa górną granicę współbieżności (i w ten sposób potencjalną przepływność), którą może osiągnąć Grupa obciążeń.  Jeśli potrzebujesz dodatkowej przepływności poza tym, co jest aktualnie zgłaszane przez metrykę *procentową zasobów obowiązujących* , zwiększ `CAP_PERCENTAGE_RESOURCE`, Zmniejsz `MIN_PERCENTAGE_RESOURCE` innych grup obciążeń lub Skaluj wystąpienie w górę, aby dodać więcej zasobów.  Zmniejszenie `REQUEST_MIN_RESOURCE_GRANT_PERCENT` może zwiększyć współbieżność, ale nie może zwiększyć ogólnej przepływności.| Min, średnia, maksimum |
|Efektywny minimalny procent zasobów |*Efektywny procent zasobów* jest minimalnym procentem zasobów zarezerwowanych i izolowanych dla grupy obciążeń biorąc pod uwagę minimalny poziom usług.  Metryka efektywnej wartości procentowej zasobu jest konfigurowana przy użyciu parametru `MIN_PERCENTAGE_RESOURCE` w składni [tworzenia grupy obciążeń](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Wartość efektywna jest opisana [tutaj](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values).<br><br>Użyj sumy typu agregacji, jeśli ta Metryka jest odfiltrowana i nie można jej podzielić, aby monitorować łączną izolację obciążeń skonfigurowaną w systemie.<br><br>*Efektywny minimalny procent zasobów* określa dolną granicę gwarantowanej współbieżności (i w ten sposób gwarantowaną przepływność), którą może osiągnąć Grupa obciążeń.  Jeśli potrzebujesz dodatkowych zasobów gwarantowanych poza tym, co jest aktualnie zgłaszane przez metrykę *procent efektywnego zasobu* , zwiększ `MIN_PERCENTAGE_RESOURCE` parametr skonfigurowany dla grupy obciążenia.  Zmniejszenie `REQUEST_MIN_RESOURCE_GRANT_PERCENT` może zwiększyć współbieżność, ale nie może zwiększyć ogólnej przepływności. |Min, średnia, maksimum|
|Aktywne zapytania grupy obciążenia  |Ta Metryka służy do raportowania aktywnych zapytań w grupie obciążenia.  Użycie tej metryki nie zostało odfiltrowane i rozdzielenie spowoduje wyświetlenie wszystkich aktywnych zapytań uruchomionych w systemie.|Suma         |
|Alokacja grupy obciążeń według maksymalnego procentu zasobów |Ta Metryka przedstawia procentową alokację zasobów względem *procentu zasobów obowiązujących* dla grupy obciążenia.  Ta Metryka zapewnia efektywne wykorzystanie grupy obciążeń.<br><br>Należy wziąć pod uwagę grupę obciążeń `DataLoads` z *procentową wartością zasobu zakończenia* wynoszącą 75% i `REQUEST_MIN_RESOURCE_GRANT_PERCENT` skonfigurowanym na 25%.  Wartość *procentowa przydziału grup obciążeń według maksymalnej wartości procentowej zasobu* przefiltrowana do `DataLoads` będzie równa 33% (25%/75%) Jeśli jedno zapytanie zostało uruchomione w tej grupie obciążenia.<br><br>Ta Metryka służy do identyfikowania wykorzystania grupy obciążeń.  Wartość bliska 100% wskazuje, że wszystkie zasoby dostępne dla grupy obciążeń są używane.  Ponadto *Metryka zakolejkowanej grupy obciążenia* dla tej samej grupy obciążeń, która przedstawia wartość większą od zera, będzie wskazywać, że grupa obciążenia będzie używać dodatkowych zasobów, jeśli są przydzieleni.  Odwrotnie, jeśli ta Metryka jest stale niska i *aktywne zapytania grupy obciążenia* są niskie, Grupa obciążeń nie jest używana.  Ta sytuacja jest szczególnie problematyczna w przypadku, gdy *procent efektywnego zasobu* jest większy niż zero, co oznacza [nieużywaną izolację obciążenia](#underutilized-workload-isolation).|Min, średnia, maksimum |
|Alokacja grupy obciążeń według wartości procentowej systemu | Ta Metryka przedstawia procentową alokację zasobów względem całego systemu.<br><br>Rozważ użycie grupy obciążeń `DataLoads` z `REQUEST_MIN_RESOURCE_GRANT_PERCENT` skonfigurowanym na 25%.  *Alokacja grupy obciążeń według wartości procentowej systemu* przefiltrowanej na `DataLoads` byłyby 25% (25%/100%) Jeśli jedno zapytanie zostało uruchomione w tej grupie obciążenia.|Min, średnia, maksimum |
|Limity czasu zapytania grupy obciążeń |Zapytania dotyczące grupy obciążeń, które przekroczyły limit czasu.  Limity czasu zapytania zgłoszone przez tę metrykę są wykonywane dopiero po rozpoczęciu wykonywania zapytania (nie obejmuje to czasu oczekiwania z powodu blokady lub oczekiwania na zasób).<br><br>Limit czasu zapytania jest konfigurowany przy użyciu `QUERY_EXECUTION_TIMEOUT_SEC` parametru w składni [tworzenia grupy obciążeń](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Zwiększenie wartości może zmniejszyć liczbę przekroczeń limitu czasu zapytania.<br><br>Rozważ zwiększenie `REQUEST_MIN_RESOURCE_GRANT_PERCENT` parametru grupy obciążenia, aby zmniejszyć liczbę limitów czasu i przydzielić więcej zasobów na zapytanie.  Należy pamiętać, że zwiększenie `REQUEST_MIN_RESOURCE_GRANT_PERCENT` zmniejsza liczbę współbieżności dla grupy obciążenia. |Suma |
|Zakolejkowane zapytania grupy obciążenia | Zapytania dla grupy obciążeń, które znajdują się obecnie w kolejce oczekujące na rozpoczęcie wykonywania.  Zapytania mogą być kolejkami, ponieważ oczekują na zasoby lub blokady.<br><br>Zapytania mogą oczekiwać z wielu powodów.  Jeśli system jest przeciążony, a żądanie współbieżności jest większe niż to, co jest dostępne, kwerendy będą kierowane do kolejki.<br><br>Rozważ dodanie większej liczby zasobów do grupy obciążeń przez zwiększenie parametru `CAP_PERCENTAGE_RESOURCE` w instrukcji [Create obciążenie grupy](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) .  Jeśli `CAP_PERCENTAGE_RESOURCE` jest większa niż Metryka *procentu efektywnego zasobu* , skonfigurowana izolacja obciążenia dla innej grupy obciążeń ma wpływ na zasoby przydzielono do tej grupy obciążeń.  Rozważ obniżenie `MIN_PERCENTAGE_RESOURCE` innych grup obciążeń lub przeskalowanie w górę wystąpienia, aby dodać więcej zasobów. |Suma |

## <a name="monitoring-scenarios-and-actions"></a>Scenariusze monitorowania i akcje
Poniżej przedstawiono serię konfiguracji wykresu, aby wyróżnić użycie metryki zarządzania obciążeniem na potrzeby rozwiązywania problemów wraz ze skojarzonymi akcjami w celu rozwiązania problemu.

### <a name="underutilized-workload-isolation"></a>Izolacja niewykorzystywanego obciążenia
Weź pod uwagę następującą konfigurację grupy obciążeń i klasyfikatora, w której jest tworzona Grupa obciążeń o nazwie `wgPriority`, a *TheCEO* `membername` jest zamapowana na nią przy użyciu klasyfikatora obciążeń `wcCEOPriority`.  Grupa obciążeń `wgPriority` ma dla niej skonfigurowaną funkcję izolacji obciążeń (`MIN_PERCENTAGE_RESOURCE` = 25) o 25%.  Każde zapytanie przesłane przez *TheCEO* ma 5% zasobów systemowych (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 5).
```sql
CREATE WORKLOAD GROUP wgPriority 
WITH ( MIN_PERCENTAGE_RESOURCE = 25   
      ,CAP_PERCENTAGE_RESOURCE = 50 
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5); 

CREATE WORKLOAD CLASSIFIER wcCEOPriority 
WITH ( WORKLOAD_GROUP = 'wgPriority'
      ,MEMBERNAME = 'TheCEO');
```
Poniższy wykres jest skonfigurowany w następujący sposób: Metric 1: *efektywna wartość procentowa zasobu* (średnia agregacja, `blue line`) Metryka 2: *alokacja grupy obciążeń według procentu systemowego* (średnia agregacja, `purple line`) filtr: [grupa obciążeń] = `wgPriority`
![underutilized-wg  W takim przypadku wartość parametru `MIN_PERCENTAGE_RESOURCE` można obniżyć do zakresu od 10 do 15 i zezwolić innym obciążeniom w systemie na korzystanie z zasobów.

### <a name="workload-group-bottleneck"></a>Wąskie gardła grupy obciążeń
Weź pod uwagę następującą konfigurację grupy obciążeń i klasyfikatora, w której jest tworzona Grupa obciążeń o nazwie `wgDataAnalyst`, a `membername` *dataanalizatorze* jest mapowany do niego przy użyciu klasyfikatora obciążeń `wcDataAnalyst`.  Grupa obciążeń `wgDataAnalyst` ma dla niej ustawioną 6% izolację obciążenia (`MIN_PERCENTAGE_RESOURCE` = 6) i limit zasobów równy 9% (`CAP_PERCENTAGE_RESOURCE` = 9).  Każde zapytanie przesłane przez *dataanalizować* ma 3% zasobów systemowych (`REQUEST_MIN_RESOURCE_GRANT_PERCENT` = 3).

```sql
CREATE WORKLOAD GROUP wgDataAnalyst  
WITH ( MIN_PERCENTAGE_RESOURCE = 6   
      ,CAP_PERCENTAGE_RESOURCE = 9 
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 3); 

CREATE WORKLOAD CLASSIFIER wcDataAnalyst 
WITH ( WORKLOAD_GROUP = 'wgDataAnalyst'
      ,MEMBERNAME = 'DataAnalyst');
```
Poniższy wykres jest skonfigurowany w następujący sposób: Metric 1: *procent efektywnego zasobu zakończenia* (średnia agregacja, `blue line`) Metryka 2: *alokacja grup obciążeń według maksymalnego procentu zasobów* (średnia agregacja, `purple line`) Metryka 3: *grupy obciążeń w kolejce* (agregacja SUM, `turquoise line`) filtr: [grupa obciążeń] = `wgDataAnalyst`
![przewężenie butelki — wg](media/sql-data-warehouse-workload-management-portal-monitor/bottle-necked-wg.png) wykres pokazuje, że w przypadku zasobów zostanie wyświetlona wartość z *przedziału 9% Metryka procentu zasobów*).  Istnieje stała kolejkowanie zapytań, jak pokazano w *kolejce metryki zapytań grupy obciążenia*.  W takim przypadku zwiększenie `CAP_PERCENTAGE_RESOURCE` do wartości większej niż 9% pozwoli na współbieżne wykonywanie większej liczby zapytań.  Zwiększenie `CAP_PERCENTAGE_RESOURCE` zakłada, że dostępne są wystarczające zasoby i nie są odizolowane od innych grup obciążeń.  Sprawdź, czy limit wzrostu został zwiększony przez sprawdzenie *metryki procentowej zasobu limitu*.  Jeśli jest wymagana większa przepływność, należy również rozważyć zwiększenie `REQUEST_MIN_RESOURCE_GRANT_PERCENT` do wartości większej niż 3.  Zwiększenie `REQUEST_MIN_RESOURCE_GRANT_PERCENT` może pozwolić na szybsze działanie zapytań.

## <a name="next-steps"></a>Następne kroki
[Szybki Start: Konfigurowanie izolacji obciążenia przy użyciu języka T-SQL](quickstart-configure-workload-isolation-tsql.md)<br>
[Utwórz grupę obciążeń (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)<br>
[Utwórz klasyfikator obciążeń (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)<br>
[Monitorowanie wykorzystania zasobów](sql-data-warehouse-concept-resource-utilization-query-activity.md)

