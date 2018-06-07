---
title: Omówienie metryk w Microsoft Azure | Dokumentacja firmy Microsoft
description: Omówienie metryki i ich użycie w Microsoft Azure
author: anirudhcavale
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 405ec51c-0946-4ec9-b535-60f65c4a5bd1
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: ancav
ms.openlocfilehash: ceabefa47b7627b8a9f952d487f78a96e338838d
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824748"
---
# <a name="overview-of-metrics-in-microsoft-azure"></a>Omówienie metryk w Microsoft Azure
W tym artykule opisano metryki są platformie Microsoft Azure, ich zalety oraz sposób rozpocząć korzystanie z nich.  

## <a name="what-are-metrics"></a>Co to są metryk?
Azure Monitor umożliwia korzystanie z telemetrię, aby uzyskać wgląd w wydajności i kondycji obciążeń na platformie Azure. Typ najważniejszych danych telemetrycznych platformy Azure jest metryki (nazywanych również liczniki wydajności) emitowane przez zasoby najbardziej platformy Azure. Azure Monitor udostępnia kilka sposobów konfigurowania i korzystać z tych metryk do monitorowania i rozwiązywania problemów.

## <a name="what-are-the-characteristics-of-metrics"></a>Co to są właściwości metryk?
Metryki mają następującą charakterystykę:

* Wszystkie metryki ma **częstotliwość co minutę** (chyba że określono inaczej w definicji Metryka). Pojawi się wartość metryki co minutę z zasobu, umożliwiając niemal w czasie rzeczywistym wgląd w stan i kondycja zasobu.
* Metryki są **dostępne natychmiast**. Nie musisz włączyć lub skonfigurować dodatkowe diagnostyki.
* Dostęp można uzyskać **93 dni historii** dla każdego metryki. Można szybko przyjrzeć się ostatnie i miesięczne trendów wydajności lub kondycji zasobu.
* Niektóre metryki mogą mieć atrybuty pary nazwa wartość o nazwie **wymiary**. Umożliwiają one dodatkowo podzielić i eksplorowania metrykę w sposób bardziej zrozumiałej.

## <a name="what-can-you-do-with-metrics"></a>Co należy zrobić z metryk
Metryki umożliwiają wykonywanie następujących zadań:


- Skonfiguruj metrykę **alert regułę, która wyśle powiadomienie, lub przyjmuje automatycznego akcji** po Metryka przekracza wartość progową, która została zdefiniowana. Akcje są kontrolowane przez [grupy akcji](monitoring-action-groups.md). Przykładowe działania obejmują poczty e-mail, phone i powiadomień programu SMS, wywołanie elementu webhook uruchamiania elementu runbook i inne. **Funkcja automatycznego skalowania** jest akcję do automatycznego specjalną, która umożliwia skalowanie użytkownik jest zasobem w górę i w dół do obsługi obciążenia jeszcze utrzymywanie kosztów niższe podczas nie pod obciążeniem. Można skonfigurować regułę Ustawienia skalowania automatycznego skalowania przychodzący lub wychodzący w oparciu metryki przekroczenia wartości progowej.
- **Trasy** wszystkie metryki do *usługi Application Insights* lub *analizy dzienników* umożliwia błyskawiczne analytics, wyszukiwanie i niestandardowe alerty dla danych metryk z Twoich zasobów. Można również strumienia metryk do *Centrum zdarzeń*, dzięki któremu można następnie kierowania ich do usługi Azure Stream Analytics lub niestandardowych aplikacji do analizy w czasie niemal rzeczywistym. Możesz skonfigurować Centrum zdarzeń przesyłania strumieniowego przy użyciu ustawień diagnostycznych.
- **Archiwum** historii wydajności lub kondycji zasobu zgodności, inspekcji i raportowania w trybie offline.  Twoje metryki może kierować do magazynu obiektów Blob platformy Azure, podczas konfigurowania ustawień diagnostycznych dla zasobu.
- Użyj **portalu Azure** do odnajdywania, dostęp i wyświetlać wszystkie metryki, po wybraniu zasobu i wykreślenia metryki na wykresie. Można śledzić wydajność zasobu (np. maszyna wirtualna, witryny sieci Web lub aplikacji logiki) przez przypinanie wykres do pulpitu nawigacyjnego.  
- **Wykonywać zaawansowane analizy** lub zgłaszanie trendów wydajności lub użycia zasobu.
- **Zapytanie** metryki przy użyciu poleceń cmdlet programu PowerShell lub interfejsu API REST i Platform.
- **Korzystać z** metryki przy użyciu nowych interfejsów API REST Monitor Azure.

  ![Routing metryk w Monitorze systemu Azure](./media/monitoring-overview-metrics/Metrics_Overview_v4.png)

## <a name="access-metrics-via-the-portal"></a>Metryki dostęp za pośrednictwem portalu
Poniżej znajduje się Przewodnik szybkiego tworzenia metryki wykresu przy użyciu portalu Azure.

### <a name="to-view-metrics-after-creating-a-resource"></a>Aby wyświetlić metryk po utworzeniu zasobu
1. Otwórz witrynę Azure Portal.
2. Tworzenie witryny sieci Web platformy Azure App Service.
3. Po utworzeniu witryny sieci Web, przejdź do **omówienie** bloku witryny sieci Web.
4. Możesz wyświetlić nowe metryki jako **monitorowanie** kafelka. Następnie możesz edytować kafelka i wybrać więcej metryki.

   ![Metryki dla zasobu w monitorze Azure](./media/monitoring-overview-metrics/MetricsOverview1.png)

### <a name="to-access-all-metrics-in-a-single-place"></a>Aby dostęp do wszystkich metryki w jednym miejscu
1. Otwórz witrynę Azure Portal.
2. Przejdź do nowego **Monitor** kartę, wybierz a następnie **metryki** opcji podrzędne.
3. Wybierz subskrypcję, grupy zasobów, a nazwa zasobu z listy rozwijanej.
4. Wyświetl listę dostępne metryki. Następnie wybierz metrykę interesuje i wykreślenia go.
5. Można przypiąć go do pulpitu nawigacyjnego, klikając numeru pin w prawym górnym rogu.

   ![Dostęp do wszystkich metryki w jednym miejscu w monitorze Azure](./media/monitoring-overview-metrics/MetricsOverview2.png)

> [!NOTE]
> Dostępne metryki na poziomie hosta maszyn wirtualnych (w oparciu o usługi Azure Resource Manager) i zestawach skali maszyn wirtualnych bez żadnych dodatkowych diagnostyczne instalacji. Te nowe metryki poziomu hostów są dostępne dla wystąpień systemu Windows i Linux. Te metryki są nie należy mylić z metryki poziomie systemu operacyjnego gościa, czy masz dostęp do po ponownym włączeniu diagnostyki Azure na maszynach wirtualnych lub skalowania maszyny wirtualnej z zestawów. Aby dowiedzieć się więcej o konfigurowaniu diagnostycznych, zobacz [co to jest Microsoft Azure Diagnostics](../azure-diagnostics.md).
>
>

Azure Monitor ma również nowe metryki wykresów środowisko dostępne w wersji zapoznawczej. To środowisko umożliwia użytkownikom nakładanie metryki z wielu zasobów w jeden wykres. Użytkownicy mogą również wykreślenia segmentu i filtrować metryki wielowymiarowej przy użyciu tej nowej metryki wykresów środowisko. Aby dowiedzieć się więcej [kliknij tutaj](https://aka.ms/azuremonitor/new-metrics-charts)

## <a name="access-metrics-via-the-rest-api"></a>Metryki dostęp za pośrednictwem interfejsu API REST
Metryki Azure są dostępne za pośrednictwem interfejsów API usługi Azure monitora. Istnieją dwa interfejsy API, które ułatwiają odnajdywanie i dostęp do metryk:

* Użyj [Metryka Monitor Azure definicji interfejsu API REST](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) dostęp do listy metryki i wszystkie wymiary, które są dostępne dla usługi.
* Użyj [interfejsu API REST Azure Monitor metryki](https://docs.microsoft.com/rest/api/monitor/metrics) segmentu, filtrować i uzyskać dostęp do danych rzeczywistych metryki.

> [!NOTE]
> W tym artykule opisano metryki za pośrednictwem [nowy interfejs API dla metryki](https://docs.microsoft.com/rest/api/monitor/) dla zasobów platformy Azure. Wersja interfejsu API dla nowych definicji metryk i metryki interfejsów API jest 2018-01-01. Starsze definicji metryk i metryki jest możliwy przy użyciu interfejsu API w wersji 2014-04-01.
>
>

Bardziej szczegółowy przewodnik przy użyciu interfejsów API REST Monitor Azure, zobacz [interfejsu API REST Monitor Azure wskazówki](monitoring-rest-api-walkthrough.md).

## <a name="export-metrics"></a>Metryki eksportu
Można przejść do **ustawień diagnostycznych** bloku w obszarze **Monitor** i wyświetlając opcji eksportu dla metryki. Można wybrać metryki (i dzienników diagnostycznych) być kierowane do magazynu obiektów Blob Azure Event Hubs, lub do analizy dzienników dla przypadków użycia, które zostały wymienione wcześniej w tym artykule.

 ![Opcji eksportu dla metryki w monitorze Azure](./media/monitoring-overview-metrics/MetricsOverview3.png)

Ustawienie to można skonfigurować za pomocą szablonów usługi Resource Manager [PowerShell](insights-powershell-samples.md), [interfejsu wiersza polecenia Azure](insights-cli-samples.md), lub [interfejsów API REST](https://msdn.microsoft.com/library/dn931943.aspx).

> [!NOTE]
> Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
>
> *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych metryka ta jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejkach w centrum zdarzeń.
>
>

## <a name="take-action-on-metrics"></a>Podejmij działanie metryk
Aby otrzymywać powiadomienia lub podjąć akcje automatyczne na dane, można skonfigurować reguły alertów lub ustawienia skalowania automatycznego.

### <a name="configure-alert-rules"></a>Skonfiguruj reguły alertu
Reguły alertów można skonfigurować na metryki. Te reguły alertów można sprawdzić, czy metrykę przekroczyło określony próg. Dostępne są dwie możliwości alertów metryki oferowane przez Azure Monitor.

Metryki alertów: można następnie powiadomienie za pośrednictwem poczty e-mail lub wyzwalać elementu webhook, który może służyć do uruchomienia dowolnego skryptu niestandardowego. Elementu webhook służy również do konfigurowania integracji produktów innych firm.

 ![Metryki i reguły alertów w monitorze Azure](./media/monitoring-overview-metrics/MetricsOverview4.png)

Nowsze metryki alerty mają możliwość monitorowania wielu metryki i progi dla zasobu i powiadamiać użytkowników za pomocą [grupy akcji](/monitoring-action-groups.md). Dowiedz się więcej o [nowszej alerty tutaj](https://aka.ms/azuremonitor/near-real-time-alerts).


### <a name="autoscale-your-azure-resources"></a>Funkcja automatycznego skalowania Azure zasobów
Niektórych zasobów platformy Azure obsługuje skalowanie out lub w wielu wystąpień do obsługi obciążeń. Funkcja automatycznego skalowania ma zastosowanie do usługi aplikacji (aplikacje sieci Web), zestawy skalowania maszyny wirtualnej i klasycznym usługi w chmurze Azure. Można skonfigurować reguł skalowania automatycznego skalowania out lub w przypadku niektórych metryka ma wpływ na obciążenie przecina od wartości progowej. Aby uzyskać więcej informacji, zobacz [omówienie Skalowanie automatyczne](monitoring-overview-autoscale.md).

 ![Metryki i automatycznego skalowania w Monitorze systemu Azure](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="learn-about-supported-services-and-metrics"></a>Więcej informacji na temat obsługiwanych usług i metryki
Można wyświetlić szczegółową listę obsługiwanych usług i ich metryk [Azure Monitor metryki — obsługiwanych metryki na typ zasobu](monitoring-supported-metrics.md).

## <a name="next-steps"></a>Kolejne kroki
Zapoznaj się łącza w tym artykule. Ponadto więcej informacji na temat:  

* [Typowe metryki skalowania automatycznego](insights-autoscale-common-metrics.md)
* [Jak tworzyć reguły alertów](insights-alerts-portal.md)
* [Analizowanie dzienników z usługi Azure storage z analizy dzienników](../log-analytics/log-analytics-azure-storage.md)
