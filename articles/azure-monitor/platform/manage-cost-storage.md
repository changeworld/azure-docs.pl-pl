---
title: Zarządzanie użyciem i kosztami Azure Monitor dzienników | Microsoft Docs
description: Dowiedz się, jak zmienić plan cenowy i zarządzać ilością danych i zasadami przechowywania dla obszaru roboczego Log Analytics w Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: 5e325f7766e7b0d9764949eb3fbf9753d65db8b3
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619397"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Zarządzanie użyciem i kosztami za pomocą dzienników Azure Monitor

> [!NOTE]
> W tym artykule opisano sposób kontrolowania kosztów w Azure Monitor przez ustawienie okresu przechowywania danych dla Log Analytics obszaru roboczego.  Zapoznaj się z poniższym artykułem, aby uzyskać informacje pokrewne.
> - [Monitorowanie użycia i szacowanych kosztów](usage-estimated-costs.md) zawiera opis sposobu wyświetlania użycie i szacowane koszty w wielu monitorowania funkcji różne modele cen platformy Azure. Opisuje ona również, jak można zmienić modelu cen.

Dzienniki Azure Monitor są przeznaczone do skalowania i obsługi zbierania, indeksowania i przechowywania dużych ilości danych dziennie z dowolnego źródła w przedsiębiorstwie lub wdrożonego na platformie Azure.  Chociaż może to być sterownik podstawowy dla Twojej organizacji, niskich kosztów ostatecznie to klient jest podstawowy sterownik. W tym celu należy zrozumieć, że koszt obszaru roboczego Log Analytics nie jest oparty tylko na ilości zbieranych danych, zależy również od wybranego planu i od czasu wybrania do przechowywania danych generowanych przez połączone źródła.  

W tym artykule omówimy, jak można proaktywnie monitorować wzrostu wielkości i magazynu danych i definiowania ograniczeń do kontrolowania tych powiązanych z nimi kosztów. 

Koszt dane mogą być znaczące w zależności od następujących czynników: 

- Ilość danych wygenerowanych i pozyskanych w obszarze roboczym 
    - Liczba włączonych rozwiązań do zarządzania
    - Liczba monitorowanych systemów
    - Typ danych zbieranych z każdego monitorowanego zasobu 
- Długość czasu, przez który użytkownik chce zachować dane 

## <a name="understand-your-workspaces-usage-and-estimated-cost"></a>Zrozumienie użycia obszaru roboczego i szacowanego kosztu

Dzienniki Azure Monitor ułatwiają zrozumienie, jakie koszty są prawdopodobnie oparte na najnowszych wzorcach użycia. W tym celu należy użyć **log Analytics użycia i szacowanych kosztów** , aby przejrzeć i analizować użycie danych. Pokazuje, jak dużo danych zbieranych przez każde rozwiązanie, jak dużo danych jest zachowywane i oszacowanie kosztów na podstawie ilości danych wprowadzanych i wszelkie dodatkowe przechowywanie danych ponad uwzględnioną kwotę.

![Użycie i szacunkowe koszty](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Eksplorowanie danych bardziej szczegółowo, kliknij ikonę w prawym górnym rogu albo wykresów na **użycie i szacowane koszty** strony. Teraz możesz pracować z tym zapytaniem, aby poznać więcej szczegółów dotyczących użycia.  

![Wyświetl dzienniki](media/manage-cost-storage/logs.png)

Na stronie **użycie i szacowane koszty** możesz sprawdzić ilość danych w danym miesiącu. Obejmuje to wszystkie dane odebrane i przechowywane w obszarze roboczym usługi Log Analytics.  Kliknij pozycję **szczegóły użycia** w górnej części strony, aby wyświetlić pulpit nawigacyjny użycie z informacjami o trendach ilości danych według źródła, komputerów i oferty. Aby wyświetlić i określić dzienny limit lub zmodyfikować okres przechowywania, kliknij przycisk **zarządzanie ilością danych**.
 
Log Analytics opłaty są dodawane na rachunku dotyczącym platformy Azure. Aby wyświetlić szczegóły subskrypcji platformy Azure, w sekcji rozliczeń w witrynie Azure Portal lub w przypadku płatności [portalu rozliczeń systemu Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="daily-cap"></a>Dzienny limit

Możesz skonfigurować dzienny limit i ograniczyć dziennie pozyskiwanie informacji do obszaru roboczego, ale zachować ostrożność, ponieważ cel nie powinno być osiągnięty dzienny limit.  W przeciwnym razie możesz utracić dane na pozostałą część dnia, w którym mogą mieć wpływ na inne usługi platformy Azure i rozwiązania, którego funkcja może zależeć od aktualnych danych dostępnych w obszarze roboczym.  W rezultacie możliwość obserwowania i otrzymywać powiadamia, gdy warunki zdrowia w zasobach obsługujących usługi IT ma wpływ.  Dzienny limit jest przeznaczony do użycia jako sposób zarządzania nieoczekiwanym wzrostem ilości danych z zarządzanych zasobów i pozostania w limicie lub w przypadku ograniczenia nieplanowanych opłat dla obszaru roboczego.  

Po osiągnięciu dziennego limitu kolekcję typów danych płatnych zatrzymuje się do końca dnia. Transparent ostrzeżenie pojawia się w górnej części strony dla wybranego obszaru roboczego usługi Log Analytics i zdarzenie operacji są wysyłane do *operacji* tabeli w ramach **LogManagement** kategorii. Zbieranie danych wznawia działanie po podczas resetowania zdefiniowane w obszarze *dzienny limit jest ustawiony na*. Firma Microsoft zaleca, definiując reguły alertu na podstawie zdarzeń tej operacji, skonfigurowana do wysyłania powiadomień, gdy został osiągnięty dzienny limit danych. 

> [!NOTE]
> Dzienny limit nie zatrzymuje zbierania danych z Azure Security Center.

### <a name="identify-what-daily-data-limit-to-define"></a>Zidentyfikować jakie dzienny limit danych, aby zdefiniować

Przegląd [użycie usługi Log Analytics i szacowane koszty](usage-estimated-costs.md) , aby zrozumieć trend pozyskiwania danych i co to jest dziennego limitu woluminu w celu zdefiniowania. Należy rozważyć ostrożnie, ponieważ nie będzie można monitorować swoje zasoby, po osiągnięciu limitu. 

### <a name="manage-the-maximum-daily-data-volume"></a>Zarządzanie Maksymalna dzienna ilość danych

W poniższych krokach opisano sposób konfigurowania limitu zarządzania ilością danych w obszarze roboczym Log Analytics na dzień.  

1. W obszarze roboczym wybierz pozycję **Użycie i szacunkowe koszty** w lewym okienku.
2. Na **użycie i szacunkowe koszty** dla wybranego obszaru roboczego strony, kliknij przycisk **zarządzanie ilością danych** w górnej części strony. 
3. Dzienny limit jest **OFF** domyślnie — kliknij przycisk **ON** można ją włączyć, a następnie ustaw limit objętość danych w GB/dzień.

    ![Log Analytics skonfigurować limit danych](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Zgłoś alert po osiągnięciu dziennego limitu

Gdy możemy przedstawić wizualnie w witrynie Azure portal po spełnieniu próg limitu danych tego zachowania nie musi być wyrównane do, w jaki sposób zarządzasz problemów operacyjnych, które wymaga natychmiastowej uwagi.  Aby otrzymywać powiadomień o alertach, można utworzyć nową regułę alertu w usłudze Azure Monitor.  Aby dowiedzieć się więcej, zobacz [jak tworzyć, wyświetlać i zarządzać alertami](alerts-metric.md).

Aby ułatwić rozpoczęcie pracy, Oto zalecane ustawienia dla alertu:

- Obiekt docelowy: Wybierz zasób Log Analytics
- Kryteria: 
   - Nazwa sygnału: Niestandardowe przeszukiwanie dzienników
   - Zapytanie wyszukiwania: Operacja | gdzie szczegóły ma "limit przydziału"
   - Na podstawie: Liczba wyników
   - Warunek: Większe niż
   - Próg: 0
   - Czasu 5 (minuty)
   - Jaką 5 (minuty)
- Nazwa reguły alertu: Osiągnięto dzienny limit danych
- Ważność: Ostrzeżenie (ważność 1)

Gdy alert jest zdefiniowana i limit zostanie osiągnięty, alert zostanie wyzwolony i wykonuje odpowiedzi zdefiniowany w grupy akcji. Powiadamiaj zespół za pośrednictwem wiadomości e-mail i SMS lub Automatyzuj akcje przy użyciu elementów webhook i runbook usługi Automation lub [integrację z zewnętrznego rozwiązaniem ITSM](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Zmienić okres przechowywania danych

Poniżej opisano sposób konfigurowania dziennika jak długo dane są przechowywane w obszarze roboczym.
 
1. W obszarze roboczym wybierz pozycję **Użycie i szacunkowe koszty** w lewym okienku.
2. Na stronie **Użycie i szacunkowe koszty** kliknij pozycję **Zarządzanie ilością danych** w górnej części strony.
3. W okienku, przesuń suwak, aby zwiększyć lub zmniejszyć liczbę dni, a następnie kliknij przycisk **OK**.  Jeśli użytkownik pracuje na *bezpłatne* warstwy, nie można zmodyfikować okres przechowywania danych i należy uaktualnić do warstwy płatnej w celu kontrolowania tego ustawienia.

    ![Zmień ustawienie przechowywania danych obszaru roboczego](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
Przechowywanie można również [ustawić za pomocą ARM](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) przy użyciu `dataRetention` parametru. Ponadto w przypadku ustawienia przechowywania danych na 30 dni można wyzwolić natychmiastowe przeczyszczanie starszych danych przy użyciu `immediatePurgeDataOn30Days` parametru, co może być przydatne w scenariuszach związanych ze zgodnością. Ta funkcja jest udostępniana tylko przez ARM. 

## <a name="legacy-pricing-tiers"></a>Starsze warstwy cenowe

Subskrypcje, w których wystąpiły obszary robocze Log Analytics lub Application Insights zasobów, przed 2 kwietnia 2018 lub połączone z Umowa Enterprise, które zostały uruchomione przed 1 lutego 2019, będą nadal miały dostęp do korzystania ze starszych warstw cenowych: **Bezpłatna**, **autonomiczna (za GB)** i **na węzeł (OMS)** .  Obszary robocze w warstwie cenowej bezpłatna będą mieć dzienne pozyskiwanie danych ograniczone do 500 MB (z wyjątkiem typów danych zabezpieczeń zbieranych przez Azure Security Center), a przechowywanie danych jest ograniczone do 7 dni. Warstwa cenowa bezpłatna jest przeznaczona tylko do celów ewaluacyjnych. Obszary robocze w warstwach autonomicznych lub na węzeł mają możliwość skonfigurowania przez użytkownika maksymalnie 2 lata. 

Obszary robocze utworzone przed 2016 kwietnia mają również dostęp do oryginalnych warstw cenowych **Standard** i **Premium** , które mają odpowiednio stałe dane dotyczące 30 i 365. Nie można tworzyć nowych obszarów roboczych w warstwach cenowych **standardowa** lub **Premium** , a jeśli obszar roboczy jest przenoszony z tych warstw, nie można go przenieść z powrotem. 

Więcej szczegółów dotyczących ograniczeń warstwy cenowej można znaleźć [tutaj](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces).

> [!NOTE]
> Aby użyć uprawnień, które pochodzą z zakupu pakietu OMS E1, pakietu OMS E2 lub dodatku OMS dla programu System Center, wybierz warstwę cenową Log Analytics *na węzeł* .


## <a name="changing-pricing-tier"></a>Zmienianie warstwy cenowej

Jeśli obszar roboczy Log Analytics ma dostęp do starszych warstw cenowych, aby zmienić starsze warstwy cenowe:

1. W witrynie Azure portal w okienku subskrypcji usługi Log Analytics wybierz obszar roboczy.

2. W okienku obszaru roboczego w obszarze **ogólne**, wybierz opcję **warstwa cenowa**.  

3. W obszarze **warstwa cenowa**, wybierz warstwę cenową, a następnie kliknij przycisk **wybierz**.  
    ![Wybrany plan taryfowy](media/manage-cost-storage/workspace-pricing-tier-info.png)

Możesz również [ustawić warstwę cenową za pośrednictwem ARM](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) przy `ServiceTier` użyciu parametru. 

## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Rozwiązywanie problemów dlaczego Log Analytics nie zbiera już danych

Jeśli korzystasz z starszej warstwy cenowej bezpłatnej i w ciągu dnia wyślesz więcej niż 500 MB danych, zbieranie danych zostanie zatrzymane w pozostałej części dnia. Osiągnięcia dziennego limitu jest typową przyczyną, usługi Log Analytics zatrzymuje proces zbierania danych lub danych prawdopodobnie brakuje.  Usługa log Analytics tworzy zdarzenie typu operacji podczas zbierania danych uruchamia i zatrzymuje. Uruchom następujące zapytanie w obszarze wyszukiwania, aby sprawdzić, czy osiągnięto limit dzienny i brakujące dane: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Gdy zbieranie danych zostanie zatrzymane, OperationStatusjest ostrzeżeniem. Po rozpoczęciu zbierania danych OperationStatus **powiodło**się. W poniższej tabeli opisano powody, dla których zatrzymuje zbieranie danych i zalecaną akcję, aby wznowić zbieranie danych:  

|Zatrzymuje kolekcję Przyczyna| Rozwiązanie| 
|-----------------------|---------|
|Osiągnięto dzienny limit starszych bezpłatnych warstw cenowych |Poczekaj, aż do następnego dnia, aby Kolekcjonowanie mogło być automatycznie uruchomiony ponownie lub Zmień na płatną warstwę cenową.|
|Osiągnięto dzienny limit Twojego obszaru roboczego|Poczekaj na automatyczne ponowne uruchomienie kolekcji lub Zwiększ dzienny limit ilości danych opisany w temacie Zarządzanie maksymalnym dziennym woluminem danych. Dzienny czas resetowania zostanie wyświetlony na stronie **Zarządzanie ilością danych** . |
|Subskrypcja platformy Azure jest w stanie wstrzymania ze względu na:<br> Bezpłatny okres próbny zakończył się<br> Azure — dostęp próbny wygasł<br> Co miesiąc limit wydatków osiągnięto (na przykład w ramach subskrypcji MSDN lub Visual Studio)|Konwertuj na płatną subskrypcję<br> Usuń limit lub poczekaj na zresetowanie limitu|

Aby otrzymywać powiadomienia, gdy zbieranie danych zostanie zatrzymane, wykonaj kroki opisane w temacie Tworzenie alertu *dziennego zakończenia danych* , aby otrzymywać powiadomienia o zatrzymaniu zbierania danych. Wykonaj kroki opisane w sekcji [Tworzenie grupy akcji](action-groups.md) , aby skonfigurować akcję poczty e-mail, elementu webhook lub elementu Runbook dla reguły alertu. 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Rozwiązywanie problemów związanych z użyciem przekraczającym oczekiwania

Większe użycie jest spowodowane przez jedną lub obie z następujących przyczyn:
- Więcej węzłów niż oczekiwano wysłania danych do obszaru roboczego Log Analytics
- Więcej danych niż oczekiwano do wysłania do obszaru roboczego Log Analytics

## <a name="understanding-nodes-sending-data"></a>Informacje o węzłach wysyłających dane

Aby zrozumieć liczbę komputerów, które zgłaszają pulsy każdego dnia w ostatnim miesiącu, użyj

```kusto
Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```

Aby uzyskać listę komputerów, które będą rozliczane jako węzły w przypadku, gdy obszar roboczy znajduje się w starszej warstwie cenowej węzła, poszukaj węzłów, które wysyłają **opłaty za typy danych** (niektóre typy danych są bezpłatne). W tym celu należy użyć `_IsBillable` [Właściwości](log-standard-properties.md#_isbillable) i użyć pola z lewej strony w w pełni kwalifikowanej nazwy domeny. Spowoduje to zwrócenie listy komputerów z danymi rozliczanymi:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Liczba obserwowanych węzłów może być Szacowana jako: 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| billableNodes=dcount(computerName)
```

> [!NOTE]
> Te zapytania `union withsource = tt *` są oszczędnie zależą od tego, jak skanowanie między typami danych jest kosztowne. To zapytanie zastępuje stary sposób wykonywania zapytań dotyczących informacji dla poszczególnych komputerów za pomocą typu danych użycia.  

Dokładniejsze obliczenie wartości rzeczywistej jest rozliczane, aby uzyskać liczbę komputerów na godzinę, które wysyłają rozliczane typy danych. (W przypadku obszarów roboczych w warstwie cenowej starszej dla węzła Log Analytics oblicza liczbę węzłów, które muszą być rozliczane godzinowo.) 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="understanding-ingested-data-volume"></a>Zrozumienie ilości pozyskiwanych danych

Na **użycie i szacowane koszty** stronie *pozyskiwanie danych na rozwiązanie* wykres przedstawia łączny wolumin danych wysyłanych i ile wysyłanych przez każde rozwiązanie. Dzięki temu można określić trendy, takie jak czy rośnie całkowite użycie danych (lub użycie przez konkretnego rozwiązania), pozostały stały, czy też maleje. Zapytanie używane do generowania, to jest

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart
```

Należy pamiętać, że klauzuli "gdzie IsBillable = true" odfiltrowuje typy danych, z niektórych rozwiązań, dla których nie są pobierane opłaty pozyskiwania. 

Możesz przejść dostosowaną Zobacz dane trendów dla konkretnych typów danych, na przykład jeśli chcesz badanie danych z powodu dzienniki usług IIS:

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart
```

### <a name="data-volume-by-computer"></a>Objętość danych według komputera

Aby wyświetlić **wielkość** rozliczania zdarzeń pobieranych na komputer, użyj `_BilledSize` [Właściwości](log-standard-properties.md#_billedsize), która zapewnia rozmiar w bajtach:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize Bytes=sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

Właściwość określa, czy pozyskane dane będą naliczane opłaty. [](log-standard-properties.md#_isbillable) `_IsBillable`

Aby zobaczyć liczbę zdarzeń **rozliczanych** pobieranych na komputer, użyj 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount=count() by computerName  | sort by count_ nulls last
```

Jeśli chcesz wyświetlić liczniki dla typów danych płatnych wysyłania danych do konkretnego komputera, należy użyć:

```kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Ilość danych według zasobu platformy Azure, grupy zasobów lub subskrypcji

W przypadku danych z węzłów hostowanych na platformie Azure można uzyskać **wielkość** rozliczania zdarzeń pobieranych __na komputer__przy użyciu [Właściwości](log-standard-properties.md#_resourceid)_ResourceId, która zapewnia pełną ścieżkę do zasobu:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

W przypadku danych z węzłów hostowanych na platformie Azure można uzyskać **wielkość** rozliczania zdarzeń pozyskanych w __ramach subskrypcji platformy Azure__ `_ResourceId` , przeanalizować właściwość jako:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

Zmiana `subscriptionId` na`resourceGroup` wartość spowoduje wyświetlenie ilości danych pozyskanych do rozliczenia przez grupę zasobów platformy Azure. 


> [!NOTE]
> Niektóre pola typu danych użycia, ale nadal w schemacie, są przestarzałe i ich wartości nie będą już wypełnione. Są to **komputera** oraz pola powiązane z pozyskiwania (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**,  **BatchesCapped** i **AverageProcessingTimeMs**.

### <a name="querying-for-common-data-types"></a>Wykonywanie zapytań dotyczących typowych typów danych

Aby wyświetlić elementy podrzędne źródło danych dla określonego typu danych, poniżej przedstawiono pewne przydatne przykładowe zapytania:

+ Rozwiązanie **zabezpieczające**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ Rozwiązanie do **zarządzania dziennikami**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ Typ danych **Perf**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ Typ danych **Event**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ Typ danych **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ Typ danych **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>Jak zmniejszyć wolumin danych

Sugestie dotyczące zmniejszyć wolumin zebranych danych dzienników obejmują:

| Źródło dużego woluminu danych | Jak zmniejszyć wolumin danych |
| -------------------------- | ------------------------- |
| Zdarzenia zabezpieczeń            | Wybierz [pospolite lub minimalne zdarzenia zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier). <br> Zmień zasady inspekcji zabezpieczeń w celu zbierania tylko potrzebnych zdarzeń. W szczególności zastanów się nad koniecznością zbierania następujących zdarzeń: <br> - [inspekcja platformy filtrowania](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [inspekcja rejestru](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [inspekcja systemu plików](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [inspekcja obiektu jądra](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [inspekcja manipulowania dojściem](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> — Inspekcja magazynu wymiennego |
| Liczniki wydajności       | Zmień [konfigurację licznika wydajności](data-sources-performance-counters.md) w następujący sposób: <br> — Zmniejsz częstotliwość gromadzenia <br> — Zmniejsz liczbę liczników wydajności |
| Dzienniki zdarzeń                 | Zmień [konfigurację dziennika zdarzeń](data-sources-windows-events.md) w następujący sposób: <br> — Zmniejsz liczbę gromadzonych danych dzienników zdarzeń <br> — Zbieraj wyłącznie zdarzenia o wymaganym poziomie. Na przykład nie zbieraj zdarzeń na poziomie *Informacje*. |
| Dziennik systemu                     | Zmień [konfigurację dziennika systemu](data-sources-syslog.md) w następujący sposób: <br> — Zmniejsz liczbę urządzeń, z których zbierane są dane <br> — Zbieraj wyłącznie zdarzenia o wymaganym poziomie. Na przykład nie zbieraj zdarzeń na poziomie *Informacje* i *Debugowanie*. |
| AzureDiagnostics           | Zmień kolekcję dziennika zasobów, aby: <br> — zmniejszyć liczbę dzienników zasobów wysyłanych do usługi Log Analytics, <br> — zbierać tylko wymagane dzienniki. |
| Dane rozwiązań z komputerów, które nie wymagają rozwiązania | Użyj funkcji [określania celu rozwiązania](../insights/solution-targeting.md), aby zbierać dane tylko z wymaganych grup komputerów. |

### <a name="getting-security-and-automation-node-counts"></a>Pobieranie zabezpieczeń i liczby węzłów automatyzacji

Jeśli użytkownik pracuje na "Na węzeł (OMS)" warstwy cenowej, a następnie opłaty są naliczane na podstawie liczby węzłów i rozwiązań można używać, numer wglądu w szczegółowe dane oraz węzły Analytics, dla których są naliczane będą wyświetlane w tabeli **użycie i szacowane koszty**strony.  

Aby wyświetlić liczbę liczymy węzły zabezpieczeń, można użyć zapytania:

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

Aby wyświetlić liczbę liczymy węzły usługi Automation, użyj zapytania:

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="create-an-alert-when-data-collection-is-high"></a>Utwórz alert, gdy zbieranie danych jest wysokie

W tej sekcji opisano sposób tworzenia alertu w sytuacji, gdy:
- Ilość danych przekracza określoną wartość.
- Przewiduje się, że ilość danych przekroczy określoną wartość.

Alerty platformy Azure obsługują [alerty dziennika](alerts-unified-log.md), które korzystają z zapytań wyszukiwania. 

Poniższe zapytanie daje rezultat, jeśli w ciągu ostatnich 24 godzin zebrano więcej niż 100 GB danych:

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type 
| where DataGB > 100
```

Następujące zapytanie używa prostej formuły umożliwiającej przewidywanie, kiedy w ciągu jednego dnia zostanie wysłanych więcej niż 100 GB danych: 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type 
| where EstimatedGB > 100
```

Aby utworzyć alerty dotyczące innego woluminu danych, zmień w zapytaniach wartość 100 na liczbę gigabajtów, po przekroczeniu której ma zostać wyświetlony alert.

Wykonaj kroki opisane w sekcji dotyczącej [tworzenia nowego alertu dziennika](alerts-metric.md), aby otrzymywać powiadomienia w sytuacji, gdy ilość zebranych danych jest większa niż oczekiwano.

Podczas tworzenia alertu dla pierwszego zapytania odnoszącego się do przypadku, gdy w ciągu 24 godzin występuje więcej niż 100 GB danych, ustaw wartości elementów:  

- **Zdefiniuj warunek alertu** — określ obszar roboczy usługi Log Analytics jako element docelowy zasobu.
- **Kryteria alertu** — określ następujące informacje:
   - **Nazwa sygnału** — wybierz pozycję **Przeszukiwanie dzienników niestandardowych**
   - **Zapytanie wyszukiwania** na `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`
   - **Alert logiki** **opiera się na** *liczbie wyników*, a **warunek** jest *większy niż*  **próg**  wynoszący *0*
   - **Okres** o długości *1440* minut i **częstotliwość alertów** o wartości *60* minut, ponieważ dane użycia są aktualizowane tylko raz na godzinę.
- **Zdefiniuj szczegóły alertu** — określ następujące informacje:
   - **Nazwa** na *Data volume greater than 100 GB in 24 hours* (Wolumin danych większy niż 100 GB w ciągu 24 godzin)
   - **Ważność** na *Ostrzeżenie*

Określ istniejącą [grupę akcji](action-groups.md) lub utwórz nową, tak aby otrzymywać powiadomienie, gdy alert dziennika spełni kryteria.

Podczas tworzenia alertu dla drugiego zapytania dotyczącego przypadku, w którym przewiduje się, że w ciągu 24 godzin wystąpi więcej niż 100 GB danych, ustaw wartości elementów:

- **Zdefiniuj warunek alertu** — określ obszar roboczy usługi Log Analytics jako element docelowy zasobu.
- **Kryteria alertu** — określ następujące informacje:
   - **Nazwa sygnału** — wybierz pozycję **Przeszukiwanie dzienników niestandardowych**
   - **Zapytanie wyszukiwania** na `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
   - **Alert logiki** **opiera się na** *liczbie wyników*, a **warunek** jest *większy niż*  **próg**  wynoszący *0*
   - **Okres** o długości *180* minut i **częstotliwość alertów** o wartości *60* minut, ponieważ dane użycia są aktualizowane tylko raz na godzinę.
- **Zdefiniuj szczegóły alertu** — określ następujące informacje:
   - **Nazwa** na *Data volume expected to greater than 100 GB in 24 hours* (Oczekiwany wolumin danych większy niż 100 GB w ciągu 24 godzin)
   - **Ważność** na *Ostrzeżenie*

Określ istniejącą [grupę akcji](action-groups.md) lub utwórz nową, tak aby otrzymywać powiadomienie, gdy alert dziennika spełni kryteria.

Po otrzymaniu alertu wykonaj kroki przedstawione w poniższej sekcji, aby rozwiązać problemy związane z większym niż oczekiwano użyciem.

## <a name="limits-summary"></a>Podsumowanie limitów

Istnieją pewne dodatkowe limity Log Analytics, które są zależne od warstwy cenowej Log Analytics. Opisano je [tutaj](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces).


## <a name="next-steps"></a>Następne kroki

- Zobacz [Wyszukiwanie w dzienniku w](../log-query/log-query-overview.md) dziennikach Azure monitor, aby dowiedzieć się, jak używać języka wyszukiwania. Możesz użyć zapytań wyszukiwania w celu przeprowadzenia dodatkowej analizy danych użycia.
- Wykonaj kroki opisane w sekcji dotyczącej [tworzenia nowego alertu dziennika](alerts-metric.md), aby otrzymywać powiadomienie, gdy kryteria wyszukiwania zostaną spełnione.
- Użyj funkcji [określania celu rozwiązania](../insights/solution-targeting.md), aby zbierać dane tylko z wymaganych grup komputerów.
- Aby skonfigurować efektywne zasady zbierania zdarzeń, przejrzyj [zasady filtrowania Azure Security Center](../../security-center/security-center-enable-data-collection.md).
- Zmień [konfigurację licznika wydajności](data-sources-performance-counters.md).
- Aby zmodyfikować ustawienia zbierania zdarzeń, przejrzyj [konfigurację dziennika zdarzeń](data-sources-windows-events.md).
- Aby zmodyfikować ustawienia zbierania dla dziennika systemowego, przejrzyj [konfigurację dziennika systemowego](data-sources-syslog.md).