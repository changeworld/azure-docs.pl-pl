---
title: Przełączanie ze starszego interfejsu API alertów usługi Log Analytics do nowego interfejsu API alertów platformy Azure
description: Omówienie starszego savedWyszukiwanie alertów interfejsu API i proces przełączania reguł alertów do nowego interfejsu API ScheduledQueryRules, ze szczegółami dotyczącymi typowych problemów klientów.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 05/30/2019
ms.subservice: alerts
ms.openlocfilehash: c1ca85ba9c79f828b5ddcf0d640cfe7f8b6e097c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249440"
---
# <a name="switch-api-preference-for-log-alerts"></a>Przełączanie preferencji interfejsu API dla alertów dziennika

> [!NOTE]
> Zawartość określona ma zastosowanie tylko do użytkowników chmury publicznej platformy Azure, a **nie** dla platformy Azure government lub chmury Azure China.  

> [!NOTE]
> Gdy użytkownik zdecyduje się przełączyć preferencje do nowego [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) nie jest możliwe, aby powrócić do korzystania ze starszego [starszego interfejsu API alertu usługi Log Analytics](api-alerts.md).

Do niedawna reguły alertów były zarządzane w portalu pakietu Microsoft Operations Management Suite. Nowe środowisko alertów zostało zintegrowane z różnymi usługami na platformie Microsoft Azure, w tym z usługą Log Analytics, a my poprosiliśmy o [rozszerzenie reguł alertów z portalu OMS na platformę Azure.](alerts-extend.md) Jednak aby zapewnić minimalne zakłócenia dla klientów, proces nie zmienił interfejsu programowego dla jego użycia — [interfejs API alertu usługi Log Analytics](api-alerts.md) oparty na SavedSearch.

Ale teraz ogłaszasz dla usługi Log Analytics ostrzegając użytkowników prawdziwą alternatywą programową platformy Azure, [Azure Monitor - ScheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), która jest również refleksyjna w [rozliczeniach platformy Azure — dla alertów dziennika.](alerts-unified-log.md#pricing-and-billing-of-log-alerts) Aby dowiedzieć się więcej na temat zarządzania alertami dziennika przy użyciu interfejsu API, zobacz [Zarządzanie alertami dziennika przy użyciu szablonu zasobów platformy Azure](alerts-log.md#managing-log-alerts-using-azure-resource-template) i zarządzanie [alertami dziennika przy użyciu programu PowerShell](alerts-log.md#managing-log-alerts-using-powershell).

## <a name="benefits-of-switching-to-new-azure-api"></a>Korzyści z przejścia na nowy interfejs API platformy Azure

Istnieje kilka zalet tworzenia alertów i zarządzania nimi przy użyciu [interfejsu API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) za pośrednictwem [starszego interfejsu API alertów usługi Log Analytics;](api-alerts.md) wymieniliśmy niektóre z głównych poniżej:

- Możliwość [krzyżowego wyszukiwania dziennika obszaru roboczego](../log-query/cross-workspace-query.md) w regułach alertów i obejmujących zasoby zewnętrzne, takie jak obszary robocze usługi Log Analytics, a nawet aplikacje usługi Application Insights
- Gdy wiele pól używanych do grupowania w kwerendzie, przy użyciu [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) użytkownik może określić, które pole do agregacji w witrynie Azure portal
- Alerty dziennika utworzone przy użyciu [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) może mieć okres zdefiniowany do 48 godzin i pobierać dane przez dłuższy okres niż wcześniej
- Tworzenie reguł alertów w jednym ujęciu jako pojedynczy zasób bez konieczności tworzenia trzech poziomów zasobów, tak jak w [przypadku starszego interfejsu API alertów usługi Log Analytics](api-alerts.md)
- Pojedynczy interfejs programowy dla wszystkich wariantów alertów dziennika opartych na zapytaniach na platformie Azure — nowy [interfejs API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) może służyć do zarządzania regułami usługi Log Analytics oraz usługi Application Insights
- Zarządzanie alertami dziennika przy użyciu [poleceń cmdlet programu Powershell](alerts-log.md#managing-log-alerts-using-powershell)
- Wszystkie nowe funkcje alertów dziennika i przyszłego rozwoju będą dostępne tylko za pośrednictwem nowego [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Proces przełączania ze starszego interfejsu API alertów dzienników

Użytkownicy mogą swobodnie korzystać z [starszego interfejsu API alertów usługi Log Analytics](api-alerts.md) lub nowego [interfejsu API scheduledQueryRules.](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) Reguły alertów utworzone przez interfejs API będą *obsługiwane tylko przez ten sam interfejs API* — a także z witryny Azure portal. Domyślnie usługa Azure Monitor będzie nadal używać [starszego interfejsu API alertów usługi Log Analytics](api-alerts.md) do tworzenia nowej reguły alertów z witryny Azure portal dla istniejących obszarów roboczych usługi Log Analytics. Zgodnie [z zapowiedzią nowy obszar roboczy dziennika utworzony w dniu lub po 1 czerwca 2019](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/) r. — domyślnie będzie automatycznie używać nowego [interfejsu API scheduledQueryRules,](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) w tym w witrynie Azure portal.

Wpływ przejścia preferencji na scheduledQueryRules API są kompilowane poniżej:

- Wszystkie interakcje wykonywane w celu zarządzania alertami dziennika za pośrednictwem interfejsów programowych muszą być teraz wykonywane przy użyciu [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) zamiast. Aby uzyskać więcej informacji, zobacz [przykładowe użycie za pomocą szablonu zasobów platformy Azure](alerts-log.md#managing-log-alerts-using-azure-resource-template) i [przykładowe użycie za pośrednictwem programu PowerShell](alerts-log.md#managing-log-alerts-using-powershell)
- Każda nowa reguła alertów dziennika utworzona w witrynie Azure portal zostanie utworzona przy użyciu [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) i pozwoli użytkownikom korzystać z dodatkowych funkcji nowego interfejsu API za pośrednictwem [witryny](#benefits-of-switching-to-new-azure-api) Azure portal, a także
- Ważność reguł alertów dziennika zostanie przesunięta z: *Krytyczne, Ostrzeżenie & Informacje*, do *wartości ważności 0, 1 & 2*. Wraz z opcją tworzenia/aktualizowania reguł alertów z ważnością 3 i 4.

Proces przenoszenia reguł alertów z [starszego interfejsu API alertów usługi Log Analytics](api-alerts.md) nie wymaga zmiany definicji alertu, kwerendy ani konfiguracji w jakikolwiek sposób. Reguły alertów i monitorowanie pozostają nienaruszone, a alerty nie zostaną zatrzymane lub zablokowane, w trakcie lub po przełączeniu. Jedyne zmiany to:

- Zmiana preferencji interfejsu API i dostęp do reguł za pośrednictwem nowego interfejsu API.
- Zmodyfikowany identyfikator URI zasobu reguły alertu zawierający identyfikatory używane w [starszym interfejsie API alertu usługi Log Analytics](api-alerts.md) zamiast nazwy reguły alertu w tej strukturze `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`. Wyświetlana nazwa reguły alertu pozostanie niezmieniona.

Każdy klient, który chce dobrowolnie przełączyć się na nowe [zaplanowaneQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) i zablokować użycie ze [starszego interfejsu API alertów usługi Log Analytics;](api-alerts.md) można to zrobić, wykonując wywołanie PUT na poniższym interfejsie API, aby przełączyć wszystkie reguły alertów skojarzone z określonym obszarem roboczym usługi Log Analytics.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Z treścią żądania zawierającą poniższy JSON.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

Dostęp do interfejsu API można również uzyskać z wiersza polecenia programu PowerShell przy użyciu [narzędzia](https://github.com/projectkudu/ARMClient)wiersza polecenia typu open source, które upraszcza wywoływanie interfejsu API usługi Azure Resource Manager. Jak pokazano poniżej, w przykładowym wywołaniu PUT przy użyciu narzędzia ARMclient, aby przełączyć wszystkie reguły alertów skojarzone z określonym obszarem roboczym usługi Log Analytics.

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Jeśli przełączenie wszystkich reguł alertów w obszarze roboczym usługi Log Analytics do użycia nowych [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) zakończy się pomyślnie, zostanie podana następująca odpowiedź.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

Użytkownicy mogą również sprawdzić bieżący stan obszaru roboczego usługi Log Analytics i sprawdzić, czy został on włączony do używania tylko [scheduledQueryRules.](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) Aby sprawdzić, użytkownicy mogą wykonać wywołanie GET na poniższym interfejsie API.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Aby wykonać powyższe przy użyciu wiersza polecenia programu PowerShell za pomocą narzędzia [ARMClient,](https://github.com/projectkudu/ARMClient) zobacz poniższy przykład.

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Jeśli określony obszar roboczy usługi Log Analytics został przełączony do używania tylko [scheduledQueryRules;](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) następnie odpowiedź JSON będzie jak na poniższej liście.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Inaczej, jeśli określony obszar roboczy log analitycznych nie został jeszcze przełączony do korzystania [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) tylko; następnie odpowiedź JSON będzie jak na poniższej liście.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [monitorze azure — alerty dzienników](alerts-unified-log.md).
- Dowiedz się, jak tworzyć [alerty dzienników w alertach platformy Azure](alerts-log.md).
- Dowiedz się więcej o [usłudze Alerty platformy Azure](../../azure-monitor/platform/alerts-overview.md).
