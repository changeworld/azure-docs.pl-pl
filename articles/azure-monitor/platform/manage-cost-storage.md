---
title: Zarządzanie użycia i kosztów dla usługi Azure Monitor dzienników | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zmienić plan taryfowy i zarządzanie zasadami ilości i przechowywania danych obszaru roboczego usługi Log Analytics w usłudze Azure Monitor.
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
ms.date: 05/30/2019
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: ead3122d2040a544c6f09e434f27b7970f0d5840
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417866"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Zarządzanie użycia i kosztów za pomocą usługi Azure Monitor dzienników

> [!NOTE]
> W tym artykule opisano sposób kontrolowania kosztów w usłudze Azure Monitor, ustawiając okres przechowywania danych dla obszaru roboczego usługi Log Analytics.  Zapoznaj się z następującym artykułem, aby uzyskać powiązane informacje.
> - [Monitorowanie użycia i szacowanych kosztów](usage-estimated-costs.md) zawiera opis sposobu wyświetlania użycie i szacowane koszty w wielu monitorowania funkcji różne modele cen platformy Azure. Opisuje ona również, jak można zmienić modelu cen.

Dzienniki platformy Azure Monitor jest przeznaczone do skalowania i zbieranie pomocy technicznej, indeksowanie i przechowywanie dużych ilości danych na dzień z dowolnego źródła w przedsiębiorstwie lub wdrożonych na platformie Azure.  Chociaż może to być sterownik podstawowy dla Twojej organizacji, niskich kosztów ostatecznie to klient jest podstawowy sterownik. W tym celu ważne jest zrozumienie, że koszt obszaru roboczego usługi Log Analytics nie jest oparte tylko na ilość danych zgromadzonych, również jest zależny od wybranym planem i jak długo został wybrany do przechowywania danych generowanych przez połączone źródła.  

W tym artykule omówimy, jak można proaktywnie monitorować wzrostu wielkości i magazynu danych i definiowania ograniczeń do kontrolowania tych powiązanych z nimi kosztów. 

Koszt dane mogą być znaczące w zależności od następujących czynników: 

- Ilość danych, wygenerowane i pozyskane do obszaru roboczego 
    - Liczba rozwiązań do zarządzania włączone
    - Liczba systemów monitorowania
    - Typ danych zebranych z każdego monitorowanego zasobu 
- Czas zdecydujesz się zachować dane 

## <a name="understand-your-workspaces-usage-and-estimated-cost"></a>Zrozumieć użycie i szacowany koszt Twojego obszaru roboczego

Azure ułatwia monitorowanie dzienników ułatwiają zrozumienie, co prawdopodobnie koszty oparte na najnowszych wzorców użycia. Aby to zrobić, należy użyć **użycie usługi Log Analytics i szacowane koszty** do przeglądania i analizowania użycia danych. Pokazuje, jak dużo danych zbieranych przez każde rozwiązanie, jak dużo danych jest zachowywane i oszacowanie kosztów na podstawie ilości danych wprowadzanych i wszelkie dodatkowe przechowywanie danych ponad uwzględnioną kwotę.

![Użycie i szacunkowe koszty](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Eksplorowanie danych bardziej szczegółowo, kliknij ikonę w prawym górnym rogu albo wykresów na **użycie i szacowane koszty** strony. Teraz możesz pracować z tym zapytaniem, aby poznać więcej szczegółów dotyczących użycia.  

![Wyświetl dzienniki](media/manage-cost-storage/logs.png)

Z **użycie i szacowane koszty** strony możesz przejrzeć ilość danych na miesiąc. Obejmuje to wszystkie dane odebrane i przechowywane w obszarze roboczym usługi Log Analytics.  Kliknij przycisk **szczegóły użycia** w górnej części strony, aby wyświetlić pulpit nawigacyjny użycia z informacjami na trendów woluminu danych źródłowych, komputerów i oferty. Aby wyświetlić i określić dzienny limit lub zmodyfikować okres przechowywania, kliknij przycisk **zarządzanie ilością danych**.
 
Log Analytics opłaty są dodawane na rachunku dotyczącym platformy Azure. Aby wyświetlić szczegóły subskrypcji platformy Azure, w sekcji rozliczeń w witrynie Azure Portal lub w przypadku płatności [portalu rozliczeń systemu Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="daily-cap"></a>Dzienny limit

Możesz skonfigurować dzienny limit i ograniczyć dziennie pozyskiwanie informacji do obszaru roboczego, ale zachować ostrożność, ponieważ cel nie powinno być osiągnięty dzienny limit.  W przeciwnym razie możesz utracić dane na pozostałą część dnia, w którym mogą mieć wpływ na inne usługi platformy Azure i rozwiązania, którego funkcja może zależeć od aktualnych danych dostępnych w obszarze roboczym.  W rezultacie możliwość obserwowania i otrzymywać powiadamia, gdy warunki zdrowia w zasobach obsługujących usługi IT ma wpływ.  Dzienny limit jest przeznaczona do użycia jako sposób na zarządzanie nieoczekiwany wzrost ilości danych z zarządzanych zasobów i pozostają w zakupionym limicie lub gdy chcesz ograniczaniu nieplanowanych opłat za obszar roboczy.  

Po osiągnięciu dziennego limitu kolekcję typów danych płatnych zatrzymuje się do końca dnia. Transparent ostrzeżenie pojawia się w górnej części strony dla wybranego obszaru roboczego usługi Log Analytics i zdarzenie operacji są wysyłane do *operacji* tabeli w ramach **LogManagement** kategorii. Zbieranie danych wznawia działanie po podczas resetowania zdefiniowane w obszarze *dzienny limit jest ustawiony na*. Firma Microsoft zaleca, definiując reguły alertu na podstawie zdarzeń tej operacji, skonfigurowana do wysyłania powiadomień, gdy został osiągnięty dzienny limit danych. 

### <a name="identify-what-daily-data-limit-to-define"></a>Zidentyfikować jakie dzienny limit danych, aby zdefiniować

Przegląd [użycie usługi Log Analytics i szacowane koszty](usage-estimated-costs.md) , aby zrozumieć trend pozyskiwania danych i co to jest dziennego limitu woluminu w celu zdefiniowania. Należy rozważyć ostrożnie, ponieważ nie będzie można monitorować swoje zasoby, po osiągnięciu limitu. 

### <a name="manage-the-maximum-daily-data-volume"></a>Zarządzanie Maksymalna dzienna ilość danych

Poniżej opisano sposób konfigurowania limitu zarządzanie ilością danych, który obszar roboczy usługi Log Analytics będzie pozyskiwania na dzień.  

1. W obszarze roboczym wybierz pozycję **Użycie i szacunkowe koszty** w lewym okienku.
2. Na **użycie i szacunkowe koszty** dla wybranego obszaru roboczego strony, kliknij przycisk **zarządzanie ilością danych** w górnej części strony. 
3. Dzienny limit jest **OFF** domyślnie — kliknij przycisk **ON** można ją włączyć, a następnie ustaw limit objętość danych w GB/dzień.

    ![Usługa log Analytics skonfigurować limit danych](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Alert, jeśli osiągnięty dzienny limit

Gdy możemy przedstawić wizualnie w witrynie Azure portal po spełnieniu próg limitu danych tego zachowania nie musi być wyrównane do, w jaki sposób zarządzasz problemów operacyjnych, które wymaga natychmiastowej uwagi.  Aby otrzymywać powiadomień o alertach, można utworzyć nową regułę alertu w usłudze Azure Monitor.  Aby dowiedzieć się więcej, zobacz [jak tworzenie, wyświetlanie i Zarządzanie alertami](alerts-metric.md).

Aby ułatwić rozpoczęcie pracy, Oto zalecane ustawienia dla alertu:

- Cel: Wybierz zasób usługi Log Analytics
- Kryteria: 
   - Nazwa sygnału: Przeszukiwanie dzienników niestandardowych
   - Zapytanie wyszukiwania: Operacja | których szczegóły zawiera "Przekroczenie"
   - Na podstawie: Liczba wyników
   - Warunek: Większe niż
   - Wartość progowa: 0
   - Okres: 5 (w minutach)
   - Częstotliwość: 5 (w minutach)
- Nazwa reguły alertu: Osiągnięto dzienny limit danych
- Ważność: Ostrzegawczy (ważność 1)

Gdy alert jest zdefiniowana i limit zostanie osiągnięty, alert zostanie wyzwolony i wykonuje odpowiedzi zdefiniowany w grupy akcji. Powiadamiaj zespół za pośrednictwem wiadomości e-mail i SMS lub Automatyzuj akcje przy użyciu elementów webhook i runbook usługi Automation lub [integrację z zewnętrznego rozwiązaniem ITSM](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Zmienić okres przechowywania danych

Poniżej opisano sposób konfigurowania dziennika jak długo dane są przechowywane w obszarze roboczym.
 
1. W obszarze roboczym wybierz pozycję **Użycie i szacunkowe koszty** w lewym okienku.
2. Na stronie **Użycie i szacunkowe koszty** kliknij pozycję **Zarządzanie ilością danych** w górnej części strony.
3. W okienku, przesuń suwak, aby zwiększyć lub zmniejszyć liczbę dni, a następnie kliknij przycisk **OK**.  Jeśli użytkownik pracuje na *bezpłatne* warstwy, nie można zmodyfikować okres przechowywania danych i należy uaktualnić do warstwy płatnej w celu kontrolowania tego ustawienia.

    ![Zmiana ustawienia przechowywania danych w obszarze roboczym](media/manage-cost-storage/manage-cost-change-retention-01.png)

## <a name="legacy-pricing-tiers"></a>Starsza wersja warstw cenowych

Klienci z umową Enterprise Agreement podpisaną przed 1 lipca 2018 r. lub twórcę już obszar roboczy usługi Log Analytics w ramach subskrypcji, możesz nadal mieć dostęp do *bezpłatna* planu. Jeśli Twoja subskrypcja nie jest związany z istniejącą rejestracją umowy EA *bezpłatna* warstwa nie jest dostępna podczas tworzenia obszaru roboczego w nowej subskrypcji po 2 kwietnia 2018 r.  Dane są ograniczone do siedmiu dni przechowywania dla *bezpłatna* warstwy.  Dla starszego *autonomiczny* lub *na węzeł* warstwy, a także bieżący 2018 pojedynczą warstwę cenową, zebranych danych jest dostępna w ciągu ostatnich 31 dni. *Bezpłatna* warstwa ma dziennego limitu pozyskiwania 500 MB, a jeśli okaże się stale przekraczać kwoty dozwolone woluminu, można zmienić obszar roboczy do innego planu, aby zbierać dane powyżej tego limitu. 

> [!NOTE]
> Aby używać uprawnień wynikających z zakupu pakietu OMS E1 zestawu, pakiet E2 OMS lub dodatku pakietu OMS dla programu System Center, wybierz usługi Log Analytics *na węzeł* warstwy cenowej.

## <a name="changing-pricing-tier"></a>Zmiana warstwy cenowej

Jeśli obszar roboczy usługi Log Analytics ma dostęp do starszych warstw cenowych między starszych warstw cenowych:

1. W witrynie Azure portal w okienku subskrypcji usługi Log Analytics wybierz obszar roboczy.

2. W okienku obszaru roboczego w obszarze **ogólne**, wybierz opcję **warstwa cenowa**.  

3. W obszarze **warstwa cenowa**, wybierz warstwę cenową, a następnie kliknij przycisk **wybierz**.  
    ![Wybrany plan taryfowy](media/manage-cost-storage/workspace-pricing-tier-info.png)

Jeśli chcesz przenieść obszar roboczy do bieżąca warstwa cenowa, należy zmienić swoją subskrypcję monitorowania [modelu cen w usłudze Azure Monitor](usage-estimated-costs.md#moving-to-the-new-pricing-model) która spowoduje zmianę warstwy cenowej wszystkich obszarów roboczych w tej subskrypcji.

> [!NOTE]
> Dowiedz się więcej na temat ustawiania warstwy cenowej podczas [przy użyciu szablonu usługi Azure Resource Manager](template-workspace-configuration.md#create-a-log-analytics-workspace) do utworzenia obszaru roboczego i upewnij się, że niezależnie od tego, czy powiedzie wdrożenia szablonu usługi Azure Resource Manager Subskrypcja jest w starszej wersji lub nowy model cen. 


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Rozwiązywanie problemów związanych z usługi Log Analytics nie jest już jest zbieranie danych

Jeśli znajdują się na warstwie cenowej bezpłatna starszej wersji i wysłane w ciągu dnia więcej niż 500 MB danych, zbierania danych nie będzie możliwy do końca dnia. Osiągnięcia dziennego limitu jest typową przyczyną, usługi Log Analytics zatrzymuje proces zbierania danych lub danych prawdopodobnie brakuje.  Usługa log Analytics tworzy zdarzenie typu operacji podczas zbierania danych uruchamia i zatrzymuje. W polu wyszukiwania, aby sprawdzić, jeśli osiągnięcia dziennego limitu i brakujące dane, uruchom następujące zapytanie: 

`Operation | where OperationCategory == 'Data Collection Status'`

Po zatrzymaniu zbierania danych jest OperationStatus **ostrzeżenie**. Podczas uruchamiania zbierania danych jest OperationStatus **Powodzenie**. W poniższej tabeli opisano powody, dla których zatrzymuje zbieranie danych i zalecaną akcję, aby wznowić zbieranie danych:  

|Zatrzymuje kolekcję Przyczyna| Rozwiązanie| 
|-----------------------|---------|
|Osiągnięto dzienny limit starsze warstwy cenowej bezpłatna |Poczekaj, aż do następnego dnia, aby Kolekcjonowanie mogło być automatycznie uruchomiony ponownie lub Zmień na płatną warstwę cenową.|
|Osiągnięto dzienny limit obszaru roboczego|Poczekaj, aż kolekcji do automatycznego ponownego uruchamiania lub zwiększenia dziennego limitu woluminu danych opisanych w zarządzać Maksymalna dzienna ilość danych. Godzina codziennego resetowania zakończenia są wyświetlane na **zarządzanie ilością danych** strony. |
|Subskrypcja platformy Azure jest w stanie wstrzymania ze względu na:<br> Bezpłatny okres próbny zakończył się<br> Azure — dostęp próbny wygasł<br> Co miesiąc limit wydatków osiągnięto (na przykład w ramach subskrypcji MSDN lub Visual Studio)|Konwertuj na płatną subskrypcję<br> Usuń limit lub poczekaj na zresetowanie limitu|

Aby otrzymać powiadomienie po zatrzymaniu zbierania danych, należy użyć procedury opisanej w *dziennego limitu danych utwórz* alert, aby otrzymywać powiadomienia, po zatrzymaniu zbierania danych. Wykonaj czynności opisane w [Utwórz grupę akcji](action-groups.md) na konfigurowanie poczty e-mail, elementu webhook lub akcję runbook dla reguły alertu. 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Rozwiązywanie problemów związanych z użyciem przekraczającym oczekiwania

Większe użycie jest spowodowane przez jedną lub obie z następujących przyczyn:
- Więcej węzłów niż oczekiwano, wysyłanie danych do obszaru roboczego usługi Log Analytics
- Więcej danych niż oczekiwano, są wysyłane do obszaru roboczego usługi Log Analytics

## <a name="understanding-nodes-sending-data"></a>Opis węzły wysyłające dane

Aby poznać liczbę komputerów wysyłających Puls z każdego dnia w ciągu ostatniego miesiąca, użyj

`Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart`

Aby uzyskać listę komputerów, które będą rozliczane jako węzły, jeśli obszar roboczy jest w starszej wersji na warstwie cenowej węzła, odszukaj węzły wysyłające **rozliczane typy danych** (niektóre typy danych są bezpłatne). Aby to zrobić, należy użyć `_IsBillable` [właściwość](log-standard-properties.md#_isbillable) i używać skrajnie po lewej stronie pola w pełni kwalifikowaną nazwę domeny. Spowoduje to zwrócenie listy komputerów z danymi rozliczane:

`union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName`

Można oszacować liczbę płatnych węzłów widoczne jako: 

`union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| billableNodes=dcount(computerName)`

> [!NOTE]
> Te `union withsource = tt *` zapytania oszczędnie skanowania różnych typów danych są kosztowne do wykonania. To zapytanie zastąpi stary sposób wykonywania zapytań na komputerze informacji o typie danych użycia.  

Bardziej precyzyjne obliczenie co faktycznie jest naliczana jest uzyskać liczbę komputerów, na godzinę, które wysyłają typy danych rozliczane. (W przypadku obszarów roboczych w starszej wersji warstwę cenową na węzeł, usługi Log Analytics oblicza liczbę węzłów, które będą musiały być rozliczane godzinowo). 

`union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc`

## <a name="understanding-ingested-data-volume"></a>Ilość danych pozyskanych opis

Na **użycie i szacowane koszty** stronie *pozyskiwanie danych na rozwiązanie* wykres przedstawia łączny wolumin danych wysyłanych i ile wysyłanych przez każde rozwiązanie. Dzięki temu można określić trendy, takie jak czy rośnie całkowite użycie danych (lub użycie przez konkretnego rozwiązania), pozostały stały, czy też maleje. Zapytanie używane do generowania, to jest

`Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

Należy pamiętać, że klauzuli "gdzie IsBillable = true" odfiltrowuje typy danych, z niektórych rozwiązań, dla których nie są pobierane opłaty pozyskiwania. 

Możesz przejść dostosowaną Zobacz dane trendów dla konkretnych typów danych, na przykład jeśli chcesz badanie danych z powodu dzienniki usług IIS:

`Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

### <a name="data-volume-by-computer"></a>Objętość danych według komputera

Aby wyświetlić **rozmiar** płatnych zdarzeń wprowadzanych na komputerze, użyj `_BilledSize` [właściwość](log-standard-properties.md#_billedsize), zapewniającą rozmiar w bajtach:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize Bytes=sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

`_IsBillable` [Właściwość](log-standard-properties.md#_isbillable) Określa, czy pozyskiwanych danych spowoduje naliczenie opłat.

Aby wyświetlić liczbę **płatnych** zdarzenia wprowadzanych na komputerze, użyj 

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

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Objętość danych według zasobów platformy Azure, grupę zasobów lub subskrypcji

W przypadku danych z węzłów hostowanych na platformie Azure możesz uzyskać **rozmiar** płatnych zdarzeń pozyskanych __na komputer__, użyj _ResourceId [właściwość](log-standard-properties.md#_resourceid), zapewniającą pełną ścieżkę do Zasób:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

W przypadku danych z węzłów hostowanych na platformie Azure możesz uzyskać **rozmiar** płatnych zdarzeń pozyskanych __subskrypcję platformy Azure__, przeanalizować `_ResourceId` właściwość jako:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

Zmiana `subscriptionId` do `resourceGroup` pokaże woluminu płatnych pozyskiwanych danych według grupy zasobów platformy Azure. 


> [!NOTE]
> Niektóre pola typu danych użycia, chociaż nadal w schemacie, są przestarzałe i spowoduje, że ich wartości są już wypełnione. Są to **komputera** oraz pola powiązane z pozyskiwania (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**,  **BatchesCapped** i **AverageProcessingTimeMs**.

### <a name="querying-for-common-data-types"></a>Wykonanie zapytania dotyczącego standardowe typy danych

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

### <a name="getting-security-and-automation-node-counts"></a>Pobieranie liczby węzłów zabezpieczeń i automatyzacji

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

## <a name="create-an-alert-when-data-collection-is-high"></a>Tworzenie alertu, gdy zbieranie danych jest wysoka

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

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [przeszukiwanie dzienników w usłudze Azure Monitor dziennikach](../log-query/log-query-overview.md) dowiesz się, jak używać języka wyszukiwania. Możesz użyć zapytań wyszukiwania w celu przeprowadzenia dodatkowej analizy danych użycia.
- Wykonaj kroki opisane w sekcji dotyczącej [tworzenia nowego alertu dziennika](alerts-metric.md), aby otrzymywać powiadomienie, gdy kryteria wyszukiwania zostaną spełnione.
- Użyj funkcji [określania celu rozwiązania](../insights/solution-targeting.md), aby zbierać dane tylko z wymaganych grup komputerów.
- Aby skonfigurować zasady zbierania zdarzeń skuteczne, zapoznaj się z [usługi Azure Security Center zasady filtrowania](../../security-center/security-center-enable-data-collection.md).
- Zmień [konfigurację licznika wydajności](data-sources-performance-counters.md).
- Aby zmodyfikować ustawienia zbierania zdarzeń, przejrzyj [konfigurację dziennika zdarzeń](data-sources-windows-events.md).
- Aby zmodyfikować ustawienia zbierania dla dziennika systemowego, przejrzyj [konfigurację dziennika systemowego](data-sources-syslog.md).