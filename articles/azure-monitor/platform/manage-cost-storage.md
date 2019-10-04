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
ms.date: 10/01/2019
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: fa0bd847596a601875d5662da1c000a5b1388eef
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960263"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Zarządzanie użyciem i kosztami za pomocą dzienników Azure Monitor

> [!NOTE]
> W tym artykule opisano sposób zrozumienia i kontrolowania kosztów Azure Monitor dzienników. W powiązanym artykule, [monitorowaniu użycia i szacowanych kosztów](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) opisano sposób wyświetlania użycia i szacowane koszty w wielu funkcjach monitorowania platformy Azure dla różnych modeli cen.

Dzienniki Azure Monitor są przeznaczone do skalowania i obsługi zbierania, indeksowania i przechowywania dużych ilości danych dziennie z dowolnego źródła w przedsiębiorstwie lub wdrożonego na platformie Azure.  Chociaż może to być podstawowy sterownik dla Twojej organizacji, kosztem wydajności jest ostatecznie podstawowy sterownik. W tym celu należy zrozumieć, że koszt obszaru roboczego Log Analytics nie jest oparty tylko na ilości zbieranych danych, zależy również od wybranego planu i od czasu wybrania do przechowywania danych generowanych przez połączone źródła.  

W tym artykule opisano, jak można aktywnie monitorować pozyskiwane ilości danych i przyrosty magazynu oraz definiować ograniczenia kontroli nad tymi kosztami. 

## <a name="pricing-model"></a>Model cen

Domyślna cena dla Log Analytics jest modelem **płatności zgodnie z rzeczywistym** użyciem na podstawie ilości danych pozyskiwanych i opcjonalnie do dłuższego przechowywania danych. Każdy obszar roboczy Log Analytics jest rozliczany jako osobna usługa i przyczynia się do rozliczenia za subskrypcję platformy Azure. Ilość pozyskiwania danych może być znaczna w zależności od następujących czynników: 

  - Liczba włączonych rozwiązań zarządzania i ich konfiguracja (np. 
  - Liczba monitorowanych maszyn wirtualnych
  - Typ danych zbieranych z każdej monitorowanej maszyny wirtualnej 
  
Oprócz modelu płatność zgodnie z rzeczywistym użyciem wprowadziliśmy **rezerwacje pojemności** dla log Analytics, co pozwala zaoszczędzić o 25% w porównaniu z ceną płatność zgodnie z rzeczywistym użyciem. Cennik rezerwacji zdolności produkcyjnych umożliwia zakupienie rezerwacji rozpoczynającej się o 100 GB/dzień. Każde użycie powyżej poziomu rezerwacji będzie rozliczane według stawki płatności zgodnie z rzeczywistym użyciem. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/monitor/) na temat log Analytics płatność zgodnie z rzeczywistym użyciem i Cennik rezerwacji zdolności produkcyjnych. 

Należy pamiętać, że niektóre rozwiązania, takie jak [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/) i [Azure](https://azure.microsoft.com/pricing/details/azure-sentinel/), mają własny model cen. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>Szacowanie kosztów związanych z zarządzaniem środowiskiem 

Jeśli jeszcze nie korzystasz z dzienników Azure Monitor, możesz użyć [kalkulatora cen Azure monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) , aby oszacować koszt używania log Analytics. Zacznij od wprowadzenia "Azure Monitor" w polu wyszukiwania i kliknięcia kafelka Azure Monitor wyników. Przewiń w dół stronę do Azure Monitor i wybierz pozycję Log Analytics z listy rozwijanej Typ.  W tym miejscu możesz wprowadzić liczbę maszyn wirtualnych i GB danych, które mają być zbierane z każdej maszyny wirtualnej. Typcially od 1 do 3 GB danych miesiąca jest pobierana z typowej maszyny wirtualnej platformy Azure. Jeśli już już oceniasz dzienniki Azure Monitor, możesz użyć statystyk danych z własnego środowiska. Poniżej znajdują się informacje na temat określania [liczby monitorowanych maszyn wirtualnych](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) i [ilości danych pobieranych przez obszar roboczy](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume). 

## <a name="understand-your-usage-and-estimate-costs"></a>Zapoznaj się z użyciem i szacunkowymi kosztami

Jeśli używasz dzienników Azure Monitor teraz, możesz łatwo zrozumieć, jakie koszty są prawdopodobnie oparte na ostatnich wzorcach użycia. W tym celu należy użyć **log Analytics użycia i szacowanych kosztów** , aby przejrzeć i analizować użycie danych. Pokazuje, ile danych jest zbieranych przez każde rozwiązanie, ile danych jest zachowywanych, i oszacowanie kosztów na podstawie ilości danych pozyskiwanych i dodatkowego okresu przechowywania poza uwzględnioną ilością.

![Użycie i szacunkowe koszty](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Aby eksplorować dane w bardziej szczegółowy sposób, kliknij ikonę w prawym górnym rogu jednego z wykresów na stronie **użycie i szacowane koszty** . Teraz możesz korzystać z tego zapytania, aby poznać więcej szczegółów dotyczących użycia.  

![Widok dzienników](media/manage-cost-storage/logs.png)

Na stronie **użycie i szacowane koszty** możesz sprawdzić ilość danych w danym miesiącu. Obejmuje to wszystkie dane odebrane i zachowane w obszarze roboczym Log Analytics.  Kliknij pozycję **szczegóły użycia** w górnej części strony, aby wyświetlić pulpit nawigacyjny użycie z informacjami o trendach ilości danych według źródła, komputerów i oferty. Aby wyświetlić i ustawić dzienny limit lub zmodyfikować okres przechowywania, kliknij przycisk **Zarządzanie ilością danych**.
 
Opłaty za Log Analytics są dodawane do rachunku na korzystanie z platformy Azure. Szczegóły rachunku na korzystanie z platformy Azure można znaleźć w sekcji rozliczenia Azure Portal lub [Azure Billing Portal](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Wyświetlanie Log Analytics użycia na rachunku na platformie Azure 

Platforma Azure oferuje bardzo przydatne funkcje w [Azure Cost Management i centrum rozliczeń](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) . Na przykład funkcja "analiza kosztów" umożliwia wyświetlanie wydatków dotyczących zasobów platformy Azure. Dodanie filtru według typu zasobu (do firmy Microsoft. operationalinsights/Workspace for Log Analytics) umożliwi śledzenie wydatków.

Więcej informacji o użyciu można uzyskać, [pobierając użycie z witryny Azure Portal](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). W pobranym arkuszu kalkulacyjnym można zobaczyć użycie na zasób platformy Azure (np. Log Analytics obszar roboczy) dziennie. W tym arkuszu kalkulacyjnym programu Excel można znaleźć informacje o wykorzystaniu z obszarów roboczych Log Analytics, wybierając najpierw filtrowanie w kolumnie "kategoria licznika", aby pokazać "szczegółowe dane i analizy" (używane przez niektóre starsze warstwy cenowe) i "Log Analytics", a następnie dodać filtr do wystąpienia Identyfikator "kolumna" zawiera obszar roboczy ". Użycie jest wyświetlane w kolumnie "zużyta ilość", a jednostka dla każdego wpisu jest pokazywana w kolumnie "jednostka miary".  Dostępne są więcej szczegółów, które ułatwiają [zrozumienie Microsoft Azure rachunku](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

## <a name="manage-your-maximum-daily-data-volume"></a>Zarządzanie maksymalnym dziennym woluminem danych

Można skonfigurować dzienny limit i ograniczyć dzienne pozyskiwanie obszaru roboczego, ale należy zachować ostrożność, ponieważ cel nie powinien być osiągnięty w dziennym limicie.  W przeciwnym razie utracisz dane przez pozostałą część dnia, co może mieć wpływ na inne usługi i rozwiązania platformy Azure, których funkcjonalność może zależeć od aktualnych danych dostępnych w obszarze roboczym.  W efekcie można obserwować i otrzymywać alerty w przypadku, gdy ma to wpływ na warunki kondycji zasobów obsługujących usługi IT.  Dzienny limit jest przeznaczony do użycia jako sposób zarządzania nieoczekiwanym wzrostem ilości danych z zarządzanych zasobów i pozostania w limicie lub w przypadku ograniczenia nieplanowanych opłat dla obszaru roboczego.  

Po osiągnięciu dziennego limitu zbieranie typów danych, które można rozliczać, jest przerywane dla pozostałej części dnia. Transparent ostrzegawczy pojawia się w górnej części strony dla wybranego obszaru roboczego Log Analytics, a zdarzenie operacji jest wysyłane do tabeli *operacji* w kategorii **LogManagement** . Zbieranie danych zostanie wznowione po upływie czasu resetowania określonego w obszarze *dzienny limit*. Zalecamy zdefiniowanie reguły alertu na podstawie tego zdarzenia operacji skonfigurowanego do powiadamiania o osiągnięciu dziennego limitu ilości danych. 

> [!NOTE]
> Dzienny limit nie zatrzymuje zbierania danych z Azure Security Center, z wyjątkiem obszarów roboczych, w których Azure Security Center został zainstalowany przed 19 czerwca 2017. 

### <a name="identify-what-daily-data-limit-to-define"></a>Określ dzienny limit danych do zdefiniowania

Przejrzyj [log Analytics użycie i szacowane koszty](usage-estimated-costs.md) , aby zrozumieć trend pozyskiwania danych i co to jest dzienny limit ilości do zdefiniowania. Należy wziąć pod uwagę opiekę, ponieważ nie będzie można monitorować zasobów po osiągnięciu limitu. 

### <a name="set-the-daily-cap"></a>Ustaw limit dzienny

W poniższych krokach opisano sposób konfigurowania limitu zarządzania ilością danych w obszarze roboczym Log Analytics na dzień.  

1. W obszarze roboczym wybierz pozycję **Użycie i szacunkowe koszty** w lewym okienku.
2. Na stronie **użycie i szacowane koszty** dla wybranego obszaru roboczego kliknij pozycję **Zarządzanie ilością danych** w górnej części strony. 
3. Dzienny limit jest domyślnie **wyłączony** **— kliknij pozycję Włącz,** aby włączyć ją, a następnie ustaw wartość w gigabajtach na dzień.

    ![Log Analytics skonfigurować limit danych](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Zgłoś alert po osiągnięciu dziennego limitu

Podczas prezentowania wizualnej wskazówki w Azure Portal po spełnieniu progu limitu danych takie zachowanie nie musi być dostosowane do sposobu zarządzania problemami operacyjnymi wymagającymi natychmiastowej uwagi.  Aby otrzymać powiadomienie o alercie, można utworzyć nową regułę alertu w Azure Monitor.  Aby dowiedzieć się więcej, zobacz [jak tworzyć, wyświetlać i zarządzać alertami](alerts-metric.md).

Aby rozpocząć, poniżej przedstawiono zalecane ustawienia alertu:

- Cel: wybierz zasób Log Analytics
- Określonych 
   - Nazwa sygnału: niestandardowe wyszukiwanie w dzienniku
   - Zapytanie wyszukiwania: operacja | gdzie szczegóły ma "limit przydziału"
   - Na podstawie: liczba wyników
   - Warunek: większe niż
   - Próg: 0
   - Okres: 5 (minuty)
   - Częstotliwość: 5 (minuty)
- Nazwa reguły alertu: osiągnięto dzienny limit danych
- Ważność: ostrzeżenie (ważność 1)

Po zdefiniowaniu alertu i osiągnięciu limitu zostanie wyzwolony alert i zostanie określona odpowiedź zdefiniowana w grupie akcji. Może ona wysyłać powiadomienia do zespołu za pośrednictwem wiadomości e-mail i SMS albo automatyzować akcje przy użyciu elementów webhook, Runbook Automation lub [integracji z zewnętrznym rozwiązaniem narzędzia ITSM](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Zmień okres przechowywania danych

W poniższych krokach opisano sposób konfigurowania czasu przechowywania danych dziennika w obszarze roboczym.

### <a name="default-retention"></a>Domyślne przechowywanie

Aby ustawić domyślne przechowywanie dla obszaru roboczego, 
 
1. W witrynie Azure Portal w obszarze roboczym wybierz pozycję **użycie i szacowane koszty** w okienku po lewej stronie.
2. Na stronie **Użycie i szacunkowe koszty** kliknij pozycję **Zarządzanie ilością danych** w górnej części strony.
3. W okienku przesuń suwak, aby zwiększyć lub zmniejszyć liczbę dni, a następnie kliknij przycisk **OK**.  W przypadku korzystania z warstwy *bezpłatna* nie będzie można modyfikować okresu przechowywania danych i należy przeprowadzić uaktualnienie do warstwy płatnej w celu kontrolowania tego ustawienia.

    ![Zmień ustawienie przechowywania danych obszaru roboczego](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
Przechowywanie można również [ustawić za pomocą ARM](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) przy użyciu parametru `retentionInDays`. Ponadto w przypadku ustawienia przechowywania danych na 30 dni można wyzwolić natychmiastowe przeczyszczanie starszych danych przy użyciu parametru `immediatePurgeDataOn30Days`, co może być przydatne w scenariuszach związanych ze zgodnością. Ta funkcja jest udostępniana tylko przez ARM. 

Dwa typy danych — `Usage` i `AzureActivity`--są domyślnie zachowywane przez 90 dni i nie są naliczane opłaty za korzystanie z tego 90go okresu przechowywania. Te typy danych są również wolne od opłat za pozyskiwanie danych. 

### <a name="retention-by-data-type"></a>Przechowywanie według typu danych

Można również określić różne ustawienia przechowywania dla poszczególnych typów danych. Każdy typ danych jest zasobem podrzędnym obszaru roboczego. Na przykład tabelę SecurityEvent można rozmieścić w [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) jako:

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Należy pamiętać, że typ danych (tabela) uwzględnia wielkość liter.  Aby uzyskać bieżące ustawienia przechowywania typu danych dla określonego typu danych (w tym przykładzie SecurityEvent), użyj:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

Aby uzyskać bieżące ustawienia przechowywania typu danych dla wszystkich typów danych w obszarze roboczym, wystarczy pominąć określony typ danych, na przykład:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Aby ustawić przechowywanie określonego typu danych (w tym przykładzie SecurityEvent) na 730 dzień, wykonaj

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

Nie można ustawić typów danych `Usage` i `AzureActivity` z przechowywaniem niestandardowym. Zajmie to maksymalny domyślny okres przechowywania obszaru roboczego lub 90 dni. 

Doskonałym narzędziem do nawiązywania bezpośredniego połączenia z platformą ARM w celu ustawienia przechowywania według typu danych jest narzędzie OSS [ARMclient](https://github.com/projectkudu/ARMClient).  Dowiedz się więcej na temat ARMclient z artykułów przez [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) i [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/). 

> [!NOTE]
> Ustawienie przechowywania poszczególnych typów danych może służyć do zmniejszania kosztów przechowywania danych.  W przypadku danych zbieranych od października 2019 (gdy ta funkcja została wydana) zmniejszenie ilości danych w czasie przechowywania może zmniejszyć koszt przechowywania w miarę upływu czasu.  W przypadku danych zbieranych wcześniej ustawienie mniejszego okresu przechowywania dla danego typu nie wpłynie na koszty przechowywania.  

## <a name="legacy-pricing-tiers"></a>Starsze warstwy cenowe

Subskrypcje, w których wystąpiły obszary robocze Log Analytics lub Application Insights zasobów, przed 2 kwietnia 2018 lub połączone z Umowa Enterpriseą, która rozpoczęła się przed 1 lutego 2019, będzie nadal mieć dostęp do korzystania ze starszych warstw cenowych: **bezpłatna**, **Autonomiczna (za GB)** i **na węzeł (OMS)** .  Obszary robocze w warstwie cenowej bezpłatna będą mieć dzienne pozyskiwanie danych ograniczone do 500 MB (z wyjątkiem typów danych zabezpieczeń zbieranych przez Azure Security Center), a przechowywanie danych jest ograniczone do 7 dni. Warstwa cenowa bezpłatna jest przeznaczona tylko do celów ewaluacyjnych. Obszary robocze w warstwach autonomicznych lub na węzeł mają możliwość skonfigurowania przez użytkownika maksymalnie 2 lata. 

Obszary robocze utworzone przed kwietnia 2016 mogą również uzyskiwać dostęp do oryginalnych warstw cenowych **Standard** i **Premium** , które mają odpowiednio stałe okresy 30 i 365. Nie można tworzyć nowych obszarów roboczych w warstwach cenowych **standardowa** lub **Premium** , a jeśli obszar roboczy jest przenoszony z tych warstw, nie można go przenieść z powrotem. 

Więcej szczegółów dotyczących ograniczeń warstwy cenowej można znaleźć [tutaj](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces).

> [!NOTE]
> Aby użyć uprawnień, które pochodzą z zakupu pakietu OMS E1, pakietu OMS E2 lub dodatku OMS dla programu System Center, wybierz warstwę cenową Log Analytics *na węzeł* .


## <a name="changing-pricing-tier"></a>Zmienianie warstwy cenowej

Jeśli obszar roboczy Log Analytics ma dostęp do starszych warstw cenowych, aby zmienić starsze warstwy cenowe:

1. W Azure Portal z okienka subskrypcje Log Analytics wybierz obszar roboczy.

2. W okienku obszaru roboczego w obszarze **Ogólne**wybierz pozycję **warstwa cenowa**.  

3. W obszarze **warstwa cenowa**wybierz warstwę cenową, a następnie kliknij pozycję **Wybierz**.  
    @no__t — plan cennika 0Selected @ no__t-1

Możesz również [ustawić warstwę cenową za pośrednictwem ARM](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) przy użyciu parametru `sku` (`pricingTier` w szablonie ARM). 

## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Rozwiązywanie problemów dlaczego Log Analytics nie zbiera już danych

Jeśli korzystasz z starszej warstwy cenowej bezpłatnej i w ciągu dnia wyślesz więcej niż 500 MB danych, zbieranie danych zostanie zatrzymane w pozostałej części dnia. Osiągnięcie dziennego limitu jest typowym powodem, Log Analytics przestaje zbierać dane lub wydaje się, że brakuje danych.  Log Analytics tworzy zdarzenie typu operacja podczas uruchamiania i zatrzymywania zbierania danych. Uruchom następujące zapytanie w obszarze wyszukiwania, aby sprawdzić, czy osiągnięto limit dzienny i brakujące dane: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Gdy zbieranie danych zostanie zatrzymane, OperationStatus jest **ostrzeżeniem**. Po rozpoczęciu zbierania danych OperationStatus **powiodło**się. W poniższej tabeli opisano przyczyny zatrzymania zbierania danych oraz sugerowaną akcję wznowienia zbierania danych:  

|Zakończenie zbierania danych o przyczynie| Rozwiązanie| 
|-----------------------|---------|
|Osiągnięto dzienny limit starszych bezpłatnych warstw cenowych |Poczekaj na automatyczne ponowne uruchomienie kolekcji lub Zmień ją na płatną warstwę cenową.|
|Osiągnięto dzienny limit Twojego obszaru roboczego|Poczekaj na automatyczne ponowne uruchomienie kolekcji lub Zwiększ dzienny limit ilości danych opisany w temacie Zarządzanie maksymalnym dziennym woluminem danych. Dzienny czas resetowania zostanie wyświetlony na stronie **Zarządzanie ilością danych** . |
|Subskrypcja platformy Azure jest w stanie wstrzymania z powodu:<br> Bezpłatna wersja próbna została zakończona<br> Upłynął okres ważności platformy Azure<br> Osiągnięto miesięczny limit wydatków (na przykład w subskrypcji MSDN lub Visual Studio)|Konwertuj na płatną subskrypcję<br> Usuń limit lub zaczekaj na zresetowanie limitu|

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

Aby uzyskać listę komputerów, które będą rozliczane jako węzły w przypadku, gdy obszar roboczy znajduje się w starszej warstwie cenowej węzła, poszukaj węzłów, które wysyłają **opłaty za typy danych** (niektóre typy danych są bezpłatne). Aby to zrobić, użyj [właściwości](log-standard-properties.md#_isbillable) `_IsBillable` i użyj pola z lewej strony w pełni kwalifikowanej nazwy domeny. Spowoduje to zwrócenie listy komputerów z danymi rozliczanymi:

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
> Te kwerendy `union withsource = tt *` są bardzo zróżnicowane w miarę wykonywania skanów dla różnych typów danych. To zapytanie zastępuje stary sposób wykonywania zapytań dotyczących informacji dla poszczególnych komputerów za pomocą typu danych użycia.  

Dokładniejsze obliczenie wartości rzeczywistej jest rozliczane, aby uzyskać liczbę komputerów na godzinę, które wysyłają rozliczane typy danych. (W przypadku obszarów roboczych w warstwie cenowej starszej dla węzła Log Analytics oblicza liczbę węzłów, które muszą być rozliczane godzinowo.) 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="understanding-ingested-data-volume"></a>Zrozumienie ilości pozyskiwanych danych

Na stronie **użycie i szacowane koszty** pozyskiwanie *danych na wykres rozwiązań* pokazuje łączną ilość wysłanych danych i ilość wysyłanych przez każde rozwiązanie. Dzięki temu można określić trendy, takie jak to, czy ogólne użycie danych (lub użycie określonego rozwiązania) rośnie, pozostało stałe lub zmniejszane. Zapytanie użyte do wygenerowania tego

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart
```

Należy zauważyć, że klauzula "gdzie ismiliarded = true" odfiltruje typy danych z niektórych rozwiązań, dla których nie jest naliczana opłata za pozyskiwanie. 

Możesz również przejść do szczegółów, aby zobaczyć trendy danych dla określonych typów danych, na przykład jeśli chcesz analizować dane z powodu dzienników IIS:

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart
```

### <a name="data-volume-by-computer"></a>Ilość danych według komputera

Aby wyświetlić **wielkość** rozliczania zdarzeń pobieranych na komputer, użyj [Właściwości](log-standard-properties.md#_billedsize)`_BilledSize`, która zapewnia rozmiar w bajtach:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize Bytes=sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

[Właściwość](log-standard-properties.md#_isbillable) `_IsBillable` Określa, czy pozyskane dane będą naliczane opłaty.

Aby zobaczyć liczbę zdarzeń **rozliczanych** pobieranych na komputer, użyj 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount=count() by computerName  | sort by count_ nulls last
```

Jeśli chcesz zobaczyć liczbę typów danych, które można rozliczać, wysyłaj dane do określonego komputera, użyj:

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

W przypadku danych z węzłów hostowanych na platformie Azure możesz uzyskać **wielkość** rozliczania zdarzeń pozyskanych w __ramach subskrypcji platformy Azure__, Przeanalizuj Właściwość `_ResourceId` jako:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

Zmiana wartości `subscriptionId` na `resourceGroup` spowoduje wyświetlenie ilości danych pozyskiwanych z rozliczeniami przez grupę zasobów platformy Azure. 


> [!NOTE]
> Niektóre pola typu danych użycia, ale nadal w schemacie, są przestarzałe i ich wartości nie będą już wypełnione. Są to **komputery** , a także pola związane z pozyskiwaniem (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** i **AverageProcessingTimeMs**.

### <a name="querying-for-common-data-types"></a>Wykonywanie zapytań dotyczących typowych typów danych

Aby Dig bardziej szczegółowo źródło danych dla określonego typu danych, Oto kilka przydatnych zapytań przykładowych:

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

### <a name="tips-for-reducing-data-volume"></a>Wskazówki dotyczące zmniejszania ilości danych

Niektóre sugestie dotyczące zmniejszenia ilości zbieranych dzienników obejmują:

| Źródło dużego woluminu danych | Jak zmniejszyć wolumin danych |
| -------------------------- | ------------------------- |
| Zdarzenia zabezpieczeń            | Wybierz [pospolite lub minimalne zdarzenia zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier). <br> Zmień zasady inspekcji zabezpieczeń w celu zbierania tylko potrzebnych zdarzeń. W szczególności zastanów się nad koniecznością zbierania następujących zdarzeń: <br> - [inspekcja platformy filtrowania](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [inspekcja rejestru](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [inspekcja systemu plików](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [inspekcja obiektu jądra](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [inspekcja manipulowania dojściem](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> Inspekcja magazynu wymiennego |
| Liczniki wydajności       | Zmień [konfigurację licznika wydajności](data-sources-performance-counters.md) w następujący sposób: <br> — Zmniejsz częstotliwość gromadzenia <br> — Zmniejsz liczbę liczników wydajności |
| Dzienniki zdarzeń                 | Zmień [konfigurację dziennika zdarzeń](data-sources-windows-events.md) w następujący sposób: <br> — Zmniejsz liczbę gromadzonych danych dzienników zdarzeń <br> — Zbieraj wyłącznie zdarzenia o wymaganym poziomie. Na przykład nie zbieraj zdarzeń na poziomie *Informacje*. |
| Dziennik systemu                     | Zmień [konfigurację dziennika systemu](data-sources-syslog.md) w następujący sposób: <br> — Zmniejsz liczbę urządzeń, z których zbierane są dane <br> — Zbieraj wyłącznie zdarzenia o wymaganym poziomie. Na przykład nie zbieraj zdarzeń na poziomie *Informacje* i *Debugowanie*. |
| AzureDiagnostics           | Zmień kolekcję dziennika zasobów, aby: <br> — zmniejszyć liczbę dzienników zasobów wysyłanych do usługi Log Analytics, <br> — zbierać tylko wymagane dzienniki. |
| Dane rozwiązań z komputerów, które nie wymagają rozwiązania | Użyj funkcji [określania celu rozwiązania](../insights/solution-targeting.md), aby zbierać dane tylko z wymaganych grup komputerów. |

### <a name="getting-security-and-automation-node-counts"></a>Pobieranie zabezpieczeń i liczby węzłów automatyzacji

Jeśli korzystasz z warstwy cenowej "na węzeł (OMS)", opłaty są naliczane na podstawie liczby używanych węzłów i rozwiązań, liczby analiz i węzłów analizy, dla których są naliczane opłaty, zostaną wyświetlone w tabeli na stronie **użycie i szacowany koszt** .  

Aby wyświetlić liczbę różnych węzłów zabezpieczeń, można użyć zapytania:

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

Aby wyświetlić liczbę różnych węzłów automatyzacji, użyj zapytania:

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

## <a name="data-transfer-charges-using-log-analytics"></a>Opłaty za transfer danych przy użyciu Log Analytics

Wysyłanie danych do Log Analytics może spowodować naliczenie opłat za przepustowość danych. Zgodnie z opisem na [stronie cennika przepustowości platformy Azure](https://azure.microsoft.com/pricing/details/bandwidth/)transfer danych między usługami platformy Azure znajdującymi się w dwóch regionach jest naliczany jako wychodzący transfer danych. Transfer danych przychodzących jest bezpłatny. Ta opłata jest jednak bardzo mała (kilka%) w porównaniu z kosztami pozyskiwania danych Log Analytics. W związku z tym, kontrolując koszty Log Analytics muszą skupić się na pozyskiwanym woluminie danych i mamy wskazówki ułatwiające zrozumienie tego w [tym miejscu](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume).   

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
