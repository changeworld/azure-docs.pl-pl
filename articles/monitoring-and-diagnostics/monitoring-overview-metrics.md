---
title: Przegląd metryk w systemie Microsoft Azure
description: Przegląd metryk i ich użycie w systemie Microsoft Azure
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/05/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: d61ac48aa7c51bc4b215a7d56b1bbedfdc613f9f
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2018
ms.locfileid: "43287560"
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>Przegląd metryk w systemie Microsoft Azure
W tym artykule opisano, jakie metryki są w systemie Microsoft Azure, ich zalety i jak rozpocząć korzystanie z nich.  

## <a name="what-are-metrics"></a>Co to są metryk?
Usługa Azure Monitor umożliwia korzystanie z telemetrii, aby uzyskać wgląd w wydajność i kondycja obciążeń na platformie Azure. Typ najważniejszych danych telemetrycznych platformy Azure jest metryki (nazywane również liczników wydajności) wyemitowane przez zasoby najbardziej platformy Azure. Usługa Azure Monitor zapewnia kilka sposobów na konfigurowanie i korzystanie z tych metryk monitorowania i rozwiązywania problemów.

## <a name="what-are-the-characteristics-of-metrics"></a>Co to są właściwości metryk?
Metryki mają następującą charakterystykę:

* Wszystkie metryki mają **częstotliwość jednominutowy** (chyba że określono inaczej w definicji metryk). Pojawi się wartość metryki co minutę z zasobu, co zapewnia niemal w czasie rzeczywistym wgląd w stan i kondycję zasobu.
* Metryki są **dostępna natychmiast**. Nie musisz zgodzić się na lub konfigurowanie dodatkowych diagnostyki.
* Możesz uzyskać dostęp **93 dni historii** dla każdego metryki. Można szybko zapoznać najnowsze i miesięczne trendy wydajności i kondycji zasobu.
* Niektóre metryki mogą mieć atrybuty pary nazwa wartość, o nazwie **wymiary**. Umożliwiają one dalsze segmentu i Poznaj metryki w sposób bardziej opisową.

## <a name="what-can-you-do-with-metrics"></a>Co można zrobić za pomocą metryk?
Metryki umożliwiają wykonywanie następujących zadań:


- Skonfiguruj metryki **alert regułę, która wyśle powiadomienie, lub Trwa automatyczne akcji** gdy Metryka przekracza wartość progową, która została zdefiniowana. Akcje są kontrolowane za pośrednictwem [grup akcji](monitoring-action-groups.md). Przykładowe działania obejmują poczty e-mail, telefon i powiadomień programu SMS, wywoływanie elementu webhook, uruchamianie elementu runbook i nie tylko. **Automatyczne skalowanie** to specjalne, zautomatyzowanych akcji, która pozwala na skalowanie, możesz teraz zasobów w górę i w dół, aby obsłużyć obciążenie, ale niskich kosztów niższe podczas nie pod obciążeniem. Możesz skonfigurować regułę ustawienie automatycznego skalowania, skalowanie w pionie lub poziomie na podstawie metryki przekroczenia wartości progowej.
- **Trasy** wszystkie metryki, aby *usługi Application Insights* lub *usługi Log Analytics* umożliwiające błyskawicznej analizie, wyszukiwania i niestandardowych alertów dla danych metryk z Twoich zasobów. Można również przesyłać strumieniowo metryki, aby *Centrum zdarzeń*, dzięki któremu można następnie kierowania ich do usługi Azure Stream Analytics lub niestandardowe aplikacje do analizy w czasie niemal rzeczywistym. Należy skonfigurować Centrum zdarzeń przesyłania strumieniowego przy użyciu ustawień diagnostycznych.
- **Archiwum** historii wydajności i kondycji zasobu pod kątem zgodności, inspekcji lub w trybie offline do celów raportowania.  Po skonfigurowaniu ustawień diagnostycznych dla zasobu, można kierować metryk do usługi Azure Blob storage.
- Użyj **witryny Azure portal** do odnajdywania, dostęp i przeglądać wszystkie metryki, po wybraniu zasobu i wykresu metryk na wykresie. Możesz śledzić wydajność zasobu (np. maszyn wirtualnych, witryny sieci Web lub aplikację logiki), przypinając ten wykres do pulpitu nawigacyjnego.  
- **Przeprowadzać zaawansowane analizy** lub zgłaszanie trendów wydajności lub użycia zasobu.
- **Zapytanie** metryki za pomocą poleceń cmdlet programu PowerShell lub interfejsu API REST dla wielu Platform.
- **Używanie** metryki za pomocą nowych interfejsów API REST monitorowania platformy Azure.

  ![Routing metryk w usłudze Azure Monitor](./media/monitoring-overview-metrics/Metrics_Overview_v4.png)

## <a name="access-metrics-via-the-portal"></a>Dostęp do metryk za pośrednictwem portalu
Poniżej przedstawiono szybki Przewodnik tworzenia wykresu metryki za pomocą witryny Azure portal.

### <a name="to-view-metrics-after-creating-a-resource"></a>Aby wyświetlić metryki po utworzeniu zasobu
1. Otwórz witrynę Azure Portal.
2. Utwórz witrynę sieci Web usługi Azure App Service.
3. Po utworzeniu witryny sieci Web, przejdź do **Przegląd** bloku witryny sieci Web.
4. Możesz wyświetlić nowe metryki jako **monitorowanie** kafelka. Następnie można edytować Kafelek i zaznacz więcej metryk.

   ![Metryki dla zasobu w usłudze Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview1.png)

### <a name="to-access-all-metrics-in-a-single-place"></a>Aby uzyskać dostęp wszystkie metryki w jednym miejscu
1. Otwórz witrynę Azure Portal.
2. Przejdź do nowego **Monitor** karty, wybierz opcję a następnie **metryki** opcji znajdujących się pod nim.
3. Wybierz subskrypcję, grupy zasobów i nazwę zasobu z listy rozwijanej.
4. Wyświetl listę dostępnych metryk. Następnie wybierz metrykę interesuje i go wykresu.
5. Możesz ją przypiąć do pulpitu nawigacyjnego, klikając numeru pin w prawym górnym rogu.

   ![Dostęp do wszystkich metryk w jednym miejscu w usłudze Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview2.png)

> [!NOTE]
> Dostępne metryki na poziomie hosta maszyn wirtualnych (opartych na usłudze Azure Resource Manager) i zestawy skalowania maszyn wirtualnych bez żadnej dodatkowej konfiguracji diagnostycznych. Te nowe metryki na poziomie hosta są dostępne dla wystąpień Windows i Linux. Te metryki są nie należy mylić z metrykami poziomie systemu operacyjnego gościa, że masz dostęp do po włączeniu usługi Azure Diagnostics na maszynach wirtualnych lub skalowania maszyn wirtualnych zestawów. Aby dowiedzieć się więcej na temat konfigurowania diagnostyki, zobacz [co to jest Microsoft Azure Diagnostics](../azure-diagnostics.md).
>
>

Usługa Azure Monitor udostępnia również nowe metryki wykresów środowisko dostępne w wersji zapoznawczej. Środowisko to umożliwia użytkownikom nakładki metryki z wielu zasobów na jednym wykresie. Użytkownicy mogą również wykreślania segmentu i filtrować metryk wielowymiarowych za pomocą tego nowego metryk wykresów środowisko. Aby dowiedzieć się więcej [kliknij tutaj](https://aka.ms/azuremonitor/new-metrics-charts)

## <a name="access-metrics-via-the-rest-api"></a>Dostęp do metryk za pośrednictwem interfejsu API REST
Metryki platformy Azure są dostępne za pośrednictwem interfejsów API usługi Azure Monitor. Istnieją dwa interfejsy API, które ułatwiają odnajdywanie i dostęp do metryk:

* Użyj [definicje usługi Azure Monitor metryki interfejsu API REST](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) dostępu do listy metryk i żadnymi wymiarami, które są dostępne dla usługi.
* Użyj [API REST usługi Azure Monitor metryki](https://docs.microsoft.com/rest/api/monitor/metrics) na segmentowanie, filtrowanie i uzyskać dostęp do danych rzeczywistych metryki.

> [!NOTE]
> W tym artykule opisano metryk za pośrednictwem [nowego interfejsu API dla metryki](https://docs.microsoft.com/rest/api/monitor/) dla zasobów platformy Azure. Wersja interfejsu API dla nowych definicji metryk i metryk interfejsów API jest 2018-01-01. Starsze definicje metryk i metryki są dostępne za pomocą interfejsu API w wersji 2014-04-01.
>
>

Aby uzyskać bardziej szczegółowy przewodnik, za pomocą interfejsów API REST usługi Azure Monitor, zobacz [wskazówki interfejsu API REST usługi Azure Monitor](monitoring-rest-api-walkthrough.md).

## <a name="export-metrics"></a>Eksportowanie metryk
Możesz przejść do **ustawień diagnostycznych** bloku w obszarze **Monitor** kartę i wyświetlić opcje eksportowania metryki. Można wybrać metryki (i dzienników diagnostycznych) można kierować do magazynu obiektów Blob usługi Azure Event hubs, lub do usługi Log Analytics dla przypadków użycia, które zostały wymienione wcześniej w tym artykule.

 ![Wybranie opcji eksportu dla metryki w usłudze Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview3.png)

Można to skonfigurować za pomocą szablonów usługi Resource Manager [PowerShell](insights-powershell-samples.md), [wiersza polecenia platformy Azure](insights-cli-samples.md), lub [interfejsów API REST](https://msdn.microsoft.com/library/dn931943.aspx).

> [!NOTE]
> Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
>
> *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych metryka ta jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejkach w centrum zdarzeń.
>
>

## <a name="take-action-on-metrics"></a>Podejmowanie akcji na metryki
Aby otrzymywać powiadomienia lub wykonywanie zautomatyzowanych akcji na dane metryk, można skonfigurować reguły alertów lub ustawienia automatycznego skalowania.

### <a name="configure-alert-rules"></a>Konfigurowanie reguł alertów
Możesz skonfigurować reguły alertów dotyczących metryk. Te reguły alertów można sprawdzić, jeśli Metryka przekroczyło określonego progu. Istnieją dwie metryki alertów możliwości oferowanych przez usługi Azure Monitor.

Alerty metryki: można następnie powiadamianiu Cię przy użyciu poczty e-mail lub wyzwolenie elementu webhook, który może służyć do uruchamiania dowolnego skryptu niestandardowego. Można również użyć elementu webhook do konfigurowania integracji produktów innych firm.

 ![Metryki i reguł alertów w usłudze Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview4.png)

Nowszych alertów metryk mają możliwość monitorowania wielu metryk i progów dla zasobu i następnie powiadomi o nich za pośrednictwem [grupy akcji](monitoring-action-groups.md). Dowiedz się więcej o [nowszych alertów tutaj](https://aka.ms/azuremonitor/near-real-time-alerts).


### <a name="autoscale-your-azure-resources"></a>Automatyczne skalowanie platformy Azure zasobów
Niektóre zasoby platformy Azure obsługuje skalowanie w poziomie lub w wielu wystąpień do obsługi obciążenia. Funkcja automatycznego skalowania ma zastosowanie do usługi App Service (aplikacje sieci Web), zestawy skalowania maszyn wirtualnych i klasycznym Azure Cloud Services. Można skonfigurować reguły skalowania automatycznego skalowania w poziomie lub w gdy niektóre metryki, która ma wpływ na obciążenie przekracza wartość progową, który określisz. Aby uzyskać więcej informacji, zobacz [omówieniem skalowania automatycznego](monitoring-overview-autoscale.md).

 ![Metryki i automatyczne skalowanie w usłudze Azure Monitor](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="learn-about-supported-services-and-metrics"></a>Dowiedz się więcej o obsługiwanych usług i metryk
Można wyświetlić szczegółową listę wszystkich obsługiwanych usług i ich metryk [metryk usługi Azure Monitor — obsługiwane metryki dla typu zasobu](monitoring-supported-metrics.md).

## <a name="next-steps"></a>Kolejne kroki
Zapoznaj się z linkami w tym artykule. Ponadto Dowiedz się więcej:  

* [Typowe metryki automatycznego skalowania](insights-autoscale-common-metrics.md)
* [Jak tworzyć reguły alertów](insights-alerts-portal.md)
* [Analizowanie dzienników z usługi Azure storage za pomocą usługi Log Analytics](../log-analytics/log-analytics-azure-storage.md)
