---
title: Aktualizacja klasycznego monitorowania & alertów w usłudze Azure Monitor
description: Opis wycofania klasycznych usług monitorowania i funkcji, wcześniej pokazane w witrynie Azure portal w obszarze Alerty (klasyczny).
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: dec4d6824644cabf8b1872da207b8554fee0b3d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659479"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Ujednolicenie monitorowania & alertów w usłudze Azure Monitor zastępuje klasyczne monitorowanie & alertów

Usługa Azure Monitor stała się teraz zunifikowaną usługą monitorowania pełnego stosu, która obsługuje teraz "Jeden metryk" i "Jeden alert" w zasobach; Aby uzyskać więcej informacji, zobacz nasz [wpis w blogu na temat nowej usługi Azure Monitor](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). Nowe platformy monitorowania i alertów platformy Azure zostały stworzone tak, aby były szybsze, inteligentniejsze i rozszerzalne — nadążają za rosnącą przestrzenią przetwarzania w chmurze i są zgodne z filozofią inteligentnej chmury firmy Microsoft. 

Dzięki nowej platformie monitorowania i alertów platformy Azure wycofamy "klasyczną" platformę monitorowania i alertów - hostowaną w sekcji *alertów klasycznych widokowych alertów* platformy Azure, **która zostanie wycofana do sierpnia 2019**r. w chmurach publicznych platformy Azure. Nie będzie to miało wpływu na [chmurę platformy Azure dla instytucji rządowych](../../azure-government/documentation-government-welcome.md) i [platformę Azure China 21Vianet.](https://docs.azure.cn/)

> [!NOTE]
> Ze względu na opóźnienie we wprowadzaniu narzędzia migracji data wycofania dla migracji alertów klasycznych została [przedłużona do 31 sierpnia 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) r. z pierwotnie ogłoszonej daty 30 czerwca 2019 r.

 ![Alert klasyczny w witrynie Azure portal](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Zachęcamy do rozpoczęcia i odtworzenia alertów na nowej platformie. Dla klientów, którzy mają dużą liczbę alertów, [wprowadzamy etapy](alerts-understand-migration.md#rollout-phases), [dobrowolne narzędzie migracji,](alerts-using-migration-tool.md) aby przenieść istniejące klasyczne alerty do nowego systemu alertów bez zakłóceń lub dodatkowych kosztów.

> [!IMPORTANT]
> Klasyczne reguły alertów utworzone w dzienniku aktywności nie zostaną przestarzałe ani zmigrowane. Wszystkie klasyczne reguły alertów utworzone w dzienniku aktywności mogą być dostępne i używane jako — z nowego usługi Azure Monitor — alerty. Aby uzyskać więcej informacji, zobacz [Tworzenie, wyświetlanie i zarządzanie alertami dziennika aktywności za pomocą usługi Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md). Podobnie alerty dotyczące kondycji usługi mogą być dostępne i używane jako — z nowej sekcji Kondycja usługi. Aby uzyskać szczegółowe informacje, zobacz [alerty dotyczące powiadomień o kondycji usługi](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Ujednolicone metryki i alerty w usłudze Application Insights

Nowsza platforma metryk usługi Azure Monitor będzie teraz monitorować zasilanie pochodzące z usługi Application Insights. To przeniesienie oznacza, że usługa Application Insights zostanie załączona do grup akcji, umożliwiając znacznie więcej opcji niż tylko poprzednie wywołania poczty e-mail i elementu webhook. Alerty mogą teraz wyzwalać połączenia głosowe, funkcje platformy Azure, aplikacje logiczne, programy SMS i narzędzia ITSM, takie jak ServiceNow i Automation Runbooks. Dzięki monitorowaniu i alertom w czasie zbliżonym do rzeczywistego nowa platforma umożliwia użytkownikom usługi Application Insights korzystanie z tej samej technologii umożliwiającej monitorowanie w innych zasobach platformy Azure i stanowi podstawę monitorowania produktów firmy Microsoft.

Nowe ujednolicone monitorowanie i alerty dla usługi Application Insights obejmie:

- **Metryki platformy application insights —** która zapewnia popularne wstępnie utworzone metryki z produktu Application Insights. Aby uzyskać więcej informacji, zobacz ten artykuł dotyczący korzystania z [metryk platformy dla usługi Application Insights w nowym usłudze Azure Monitor.](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)
- **Dostępność usługi Application Insights i test sieci Web** , który zapewnia możliwość oceny responsywności i dostępności aplikacji sieci web lub serwera. Aby uzyskać więcej informacji, zobacz ten artykuł dotyczący [używania testów dostępności i alertów dla usługi Application Insights w nowym usłudze Azure Monitor.](../../azure-monitor/app/monitor-web-app-availability.md)
- **Metryki niestandardowe usługi Application Insights** — które umożliwiają definiowanie i emitowanie własnych metryk monitorowania i alertów. Aby uzyskać więcej informacji, zobacz ten artykuł dotyczący korzystania z [metryki niestandardowej dla aplikacji w usłudze Azure Monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Anomalie błędów usługi Application Insights (część inteligentnego wykrywania)** — które automatycznie powiadamia użytkownika w czasie zbliżonym do rzeczywistego, jeśli aplikacja sieci web doświadcza nieprawidłowego wzrostu liczby nieudanych żądań HTTP lub wywołań zależności. Aby uzyskać więcej informacji, zobacz ten artykuł dotyczący korzystania z [funkcji Inteligentne wykrywanie — anomalie błędów](https://docs.microsoft.com/azure/azure-monitor/app/proactive-failure-diagnostics).

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Ujednolicone metryki i alerty dla innych zasobów platformy Azure

Od marca 2018 r. następna generacja alertów i wielowymiarowego monitorowania zasobów platformy Azure jest w dostępności. Teraz nowsza platforma metryczna i alerty są szybsze dzięki możliwościom w czasie zbliżonym do rzeczywistego. Co ważniejsze, nowsze alerty platformy metryki zapewniają większą szczegółowość, ponieważ nowsza platforma zawiera opcję wymiarów, które umożliwiają wycinanie i filtrowanie do określonej kombinacji wartości, warunku lub operacji. Podobnie jak wszystkie alerty w nowym monitorze platformy Azure, nowsze alerty metryki są bardziej rozszerzalne za pomocą actiongroups — umożliwiając powiadomienia, aby rozwinąć poza e-mail lub webhook do wiadomości SMS, voice, funkcja azure, system funkcjonowania automatyzacji i więcej. Aby uzyskać więcej informacji, zobacz [Tworzenie, wyświetlanie alertów metryk i zarządzanie nimi za pomocą usługi Azure Monitor](../../azure-monitor/platform/alerts-metric.md).
Nowsze metryki zasobów platformy Azure są dostępne w następujący sposób:

- **Metryki platformy Azure Monitor Standard** — która zawiera popularne wstępnie wypełnione metryki z różnych usług i produktów platformy Azure. Aby uzyskać więcej informacji, zobacz ten artykuł na [temat obsługiwanych metryk w usłudze Azure Monitor](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported) i [alerty metryki pomocy technicznej w usłudze Azure Monitor.](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts)
- **Metryki niestandardowe usługi Azure Monitor** — który zawiera metryki ze źródeł opartych na użytkownikach, w tym agenta diagnostyki platformy Azure. Aby uzyskać więcej informacji, zobacz ten artykuł na [temat metryk niestandardowych w usłudze Azure Monitor](../../azure-monitor/platform/metrics-custom-overview.md). Korzystając z niestandardowych metryk, można również publikować metryki zebrane przez [agenta diagnostyki systemu Windows Azure](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md) i [agenta InfluxData Telegraf.](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md)

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Wycofanie klasycznej platformy monitorowania i ostrzegania

Jak wspomniano wcześniej, klasyczna platforma monitorowania i alertów aktualnie korzystająca z [sekcji Alerty (klasyczna) witryny](../../azure-monitor/platform/alerts-classic.overview.md) Azure portal zostanie wycofana w nadchodzących miesiącach, biorąc pod uwagę, że zostały zastąpione przez nowszy system.
Starsze klasyczne monitorowanie i ostrzeganie zostaną wycofane 31 sierpnia 2019 r.; w tym zamknięcie powiązanych interfejsów API, interfejsu portalu Azure i usług w nim. W szczególności te funkcje zostaną przestarzałe:

- Starsze (klasyczne) metryki i alerty dla zasobów platformy Azure, obecnie dostępne za pośrednictwem [sekcji Alerty (klasyczne) witryny](../../azure-monitor/platform/alerts-classic.overview.md) azure portal; dostępne jako [zasób microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)
- Starsza (klasyczna) platforma i niestandardowe metryki dla usługi Application Insights, a także alerty na nich, jak obecnie dostępne za pośrednictwem [sekcji Alerty (klasyczna) witryny](../../azure-monitor/platform/alerts-classic.overview.md) Azure portal i dostępne jako zasób [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)
- Starszy (klasyczny) alert anomalie awarii obecnie dostępne jako [inteligentne wykrywanie wewnątrz usługi Application Insights](../../azure-monitor/app/proactive-diagnostics.md) w witrynie Azure portal; z alertami skonfigurowanym w [sekcji Alerty (klasyczna)](../../azure-monitor/platform/alerts-classic.overview.md) witryny Azure portal

Wszystkie klasyczne systemy monitorowania i alertów, w tym odpowiednie [interfejsy API,](https://msdn.microsoft.com/library/azure/dn931945.aspx) [PowerShell,](../../azure-monitor/platform/alerts-classic-portal.md) [CLI,](../../azure-monitor/platform/alerts-classic-portal.md) [strona portalu Azure](../../azure-monitor/platform/alerts-classic-portal.md)i szablon [zasobów,](../../azure-monitor/platform/alerts-enable-template.md) będą nadal dostępne do końca sierpnia 2019 r. 

Pod koniec sierpnia 2019 r. w usłudze Azure Monitor:

- Klasyczna usługa monitorowania i alertów zostanie wycofana i nie będzie już dostępna do tworzenia nowych reguł alertów.
- Wszystkie reguły alertów, które nadal istnieją w alertach (klasyczne) po sierpniu 2019 r., będą nadal wykonywane i uruchamiane powiadomienia, ale nie będą dostępne do modyfikacji.
- Od września 2019 r. reguły alertów w klasycznym monitorowaniu & alerty, które mogą być migrowane, zostaną automatycznie przeniesione przez firmę Microsoft do ich odpowiednika w nowej platformie monitora platformy Azure w fazach obejmujących kilka tygodni. Proces będzie bezproblemowy bez przestojów, a klienci nie będą mieli strat w monitorowaniu zasięgu.
- Reguły alertów przeniesione do nowej platformy alertów zapewni monitorowanie pokrycia jak poprzednio, ale będzie ogień powiadomienia z nowych ładunków. Każdy adres e-mail, punkt końcowy elementu webhook lub łącze aplikacji logiki skojarzone z klasyczną regułą alertu zostaną przeniesione do przodu podczas migracji, ale mogą nie zachowywać się poprawnie, ponieważ ładunek alertu będzie inny na nowej platformie.
- Niektóre [klasyczne reguły alertów, których nie można automatycznie migrować](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated) i wymagają ręcznej akcji od użytkowników, będą działać do czerwca 2020 r.

> [!IMPORTANT]
> Monitor Platformy Microsoft Azure został wdrożony w narzędziu faz, [aby dobrowolnie przeprowadzić migrację](alerts-using-migration-tool.md) klasycznych reguł alertów na nową platformę wkrótce. I uruchomić go siłą dla wszystkich klasycznych reguł alertów, które nadal istnieją i mogą być migrowane, począwszy od września 2019. Klienci będą musieli upewnić się, że automatyzacja zużywa klasyczna ładunek reguły alertów jest dostosowana do obsługi nowego ładunku z [ujednoliconych metryk i alertów w usłudze Application Insights](#unified-metrics-and-alerts-in-application-insights) lub [Ujednoliconych metrykach i alertach dla innych zasobów platformy Azure](#unified-metrics-and-alerts-for-other-azure-resources), po migracji klasycznych reguł alertów. Aby uzyskać więcej informacji, zobacz [przygotowanie do klasycznej migracji reguł alertów](alerts-prepare-migration.md)

Ten artykuł będzie stale aktualizowany o łącza & szczegóły dotyczące nowej funkcji monitorowania platformy Azure & alertów, a także dostępność narzędzi ułatwiających użytkownikom przyjęcie nowej platformy Azure Monitor.

## <a name="pricing-for-migrated-alert-rules"></a>Ceny migrowanych reguł alertów

Wdrażamy narzędzie do migracji, aby ułatwić migrację [klasycznych alertów](../../azure-monitor/platform/alerts-classic.overview.md) usługi Azure Monitor do nowego środowiska alertów. Reguły alertów migrowanych i odpowiadające im zmigrowane grupy akcji (e-mail, webhook lub LogicApp) pozostaną bezpłatne. Funkcje, które zostały z klasycznych alertów, w tym możliwość edycji progu, typ agregacji i ziarnistość agregacji będą nadal dostępne bezpłatnie z regułą alertu migracji. Jeśli jednak zmigrowana reguła alertów zostanie zmiętowana, aby użyć dowolnej z nowych funkcji, powiadomień lub typów akcji platformy alertów, zostanie naliczona odpowiednia opłata. Aby uzyskać więcej informacji na temat cen reguł alertów i powiadomień, zobacz [Cennik usługi Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Poniżej przedstawiono przykłady przypadków, w których zostanie naliczonej opłaty za regułę alertu:

- Każda nowa (niezmigrowana) reguła alertów utworzona poza wolnymi jednostkami na nowej platformie Azure Monitor
- Wszelkie dane pojmowane i przechowywane poza bezpłatnymi jednostkami uwzględnionych przez usługę Azure Monitor
- Wszelkie testy sieci web z wieloma testami wykonywane przez aplikację Application Insights
- Wszelkie niestandardowe metryki przechowywane poza bezpłatnymi jednostkami w usłudze Azure Monitor
- Wszystkie zmigrowane reguły alertów, które są edytowane w celu używania nowszych funkcji alertów metryk, takich jak częstotliwość, wiele [zasobów/wymiarów, progi dynamiczne,](alerts-dynamic-thresholds.md)zmiana zasobu/sygnału itd.
- Wszystkie zmigrowane grupy akcji, które są edytowane w celu używania nowszych powiadomień lub typów akcji, takich jak SMS, Połączenie głosowe i/lub integracja z ITSM.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [nowym ujednoliconym monitorze platformy Azure](../../azure-monitor/overview.md).
* Dowiedz się więcej o nowych [alertach platformy Azure](../../azure-monitor/platform/alerts-overview.md).
