---
title: Przełącz ze starszej wersji usługi Log Analytics alerty interfejsu API do nowego interfejsu API z alertów platformy Azure
description: Omówienie wycofanie starszych zapisanego wyszukiwania na podstawie interfejsu API usługi Log Analytics alertu i proces przełączania reguł alertów nowego ScheduledQueryRules interfejsu API, szczegóły adresowania typowych problemów klienta.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 1706fc050fecd2e4be3a40725ec3e63a9036b3a9
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486634"
---
# <a name="switch-api-preference-for-log-alerts"></a>Przełącz preferencji interfejsu API dla dziennika alertów

> [!NOTE]
> Zawartość określonych użytkowników do chmury publicznej platformy Azure tylko i **nie** dla chmury Azure Government lub Azure (Chiny).  

Niedawno, dopóki nie jest zarządzany reguły alertów w portalu Microsoft Operations Management Suite. Nowe środowisko alertów została zintegrowana z różnymi usługami na platformie Microsoft Azure, w tym usługi Log Analytics, a firma Microsoft poproszony o [rozszerzanie regułami alertów z portalu pakietu OMS na platformę Azure](alerts-extend.md). Jednak w celu zapewnienia minimalnym zakłóceniu dla klientów, ten proces nie spowodowała interfejs programistyczny dla jego użycie — [interfejsu API Log Analytics alertu](api-alerts.md) oparte na zapisanego wyszukiwania.

Ale teraz ogłaszają dla alertów użytkowników w usłudze Log Analytics true platformy Azure zamiast programowy, [usługi Azure Monitor — interfejs API ScheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), która jest również odbijającą w swojej [rozliczenia platformy Azure — dla dziennika alertów](alerts-unified-log.md#pricing-and-billing-of-log-alerts). Aby dowiedzieć się więcej na temat zarządzania alertami dziennika przy użyciu interfejsu API, zobacz [alertów dzienników zarządzanie przy użyciu szablonu usługi Azure Resource](alerts-log.md#managing-log-alerts-using-azure-resource-template) i [alertów dzienników zarządzanie przy użyciu programu PowerShell, interfejsu wiersza polecenia lub interfejsu API](alerts-log.md#managing-log-alerts-using-powershell-cli-or-api).

## <a name="benefits-of-switching-to-new-azure-api"></a>Zalety przełączenie do nowego interfejsu API platformy Azure

Ma kilka zalet tworzenia i zarządzania alertów za pomocą [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) za pośrednictwem [starszej wersji Log Analytics alertu interfejsu API](api-alerts.md); podajemy niektóre z najważniejszych podane poniżej:

- Możliwość [krzyżowe przeszukiwania dzienników w obszarze roboczym](../log-query/cross-workspace-query.md) reguł alertów oraz zakres zasobów zewnętrznych, takich jak obszary robocze usługi Log Analytics lub nawet aplikacje usługi Application Insights
- W przypadku wielu pól używanych do grupy w zapytaniu, za pomocą [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) użytkownik może określić które pole ma być agregacji na w witrynie Azure portal
- Alerty utworzone przy użyciu dzienników [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) można okres zdefiniować maksymalnie 48 godzin i pobierania danych dla dłuższego niż wcześniej
- Tworzenie reguł alertów w jednym działaniu jako pojedynczy zasób bez konieczności tworzenia trzech poziomów zasobów jako [interfejsu API starszych Log Analytics alertu](api-alerts.md)
- Pojedynczy interfejs programistyczny dla wszystkich wariantów alertów opartych na zapytaniach dzienników na platformie Azure — nowe [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) może służyć do zarządzania regułami dla usługi Log Analytics, a także usługi Application Insights
- Wszystkie nowe dziennika funkcji alertów i przyszłego rozwoju będą dostępne tylko w nowej witrynie [scheduledQueryRules interfejsu API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Proces przełączania ze starszej wersji interfejsu API z alertów dziennika

Proces przechodzenia reguł alertów z [interfejsu API starszych Log Analytics alertu](api-alerts.md) nie wymaga zmian w definicji alertu, kwerenda lub konfigurację w jakikolwiek sposób. Regułami alertów i monitorowania są bez zmian oraz alertów nie powoduje wstrzymania lub wstrzymany w trakcie lub po przełącznika.

Użytkownicy mogą użyć [starszej wersji Log Analytics alertu interfejsu API](api-alerts.md) lub do nowej [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Reguły utworzone przez interfejs API, albo będą alertów *mógł być zarządzany przez ten sam interfejs API tylko* — oraz jak w witrynie Azure portal. Domyślnie usługa Azure Monitor będzie nadal używać [interfejsu API starszych Log Analytics alertu](api-alerts.md) tworzenia żadnych nową regułę alertu z witryny Azure portal.

Wpływ przełącznika preferencję scheduledQueryRules interfejsu API są kompilowane poniżej:

- Wszystkie interakcje wykonywane Zarządzanie alertów dzienników za pośrednictwem interfejsów programistycznych teraz jest to niezbędne przy użyciu [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) zamiast tego. Aby uzyskać więcej informacji znajduje się pozycja [próbki użycia za pomocą szablonu usługi Azure Resource](alerts-log.md#managing-log-alerts-using-azure-resource-template) i [próbki użycia za pomocą wiersza polecenia platformy Azure i programu PowerShell](alerts-log.md#managing-log-alerts-using-powershell-cli-or-api)
- Wszelkie nowe reguł alertów dzienników utworzone w witrynie Azure portal, zostanie utworzona z użyciem [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) tylko i zezwolić użytkownikom na stosowanie [dodatkowe funkcje nowego interfejsu API](#benefits-of-switching-to-new-azure-api) także w portalu Azure
- Poziom ważności w przypadku reguł alertów dzienników będą przesunięcie z: *Krytyczne, ostrzeżenie i komunikat o charakterze informacyjnym*, *wartości ważności 0, 1 i 2*. Wraz z opcją do utworzenia/zaktualizowania reguł alertów o ważności oraz 4.

> [!CAUTION]
> Po użytkownik zdecyduje się przełączyć preferencji do nowego [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), zasady nie mogą zrezygnować z powrotem lub Przywróć użycia starszej wersji [starszej wersji Log Analytics alertu interfejsu API](api-alerts.md).

Każdy klient, który zamierza dobrowolnie przełączyć się do nowego [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) i zablokować użycie od [interfejsu API starszych Log Analytics alertu](api-alerts.md); to zrobić, wykonując wywołania PUT na poniżej interfejsu API, aby przełączyć wszystkie alertu reguły skojarzone z określonym obszarem roboczym usługi Log Analytics.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Za pomocą zawierającą treść żądań poniżej JSON.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

Interfejsu API mogą być również dostępne z wiersza polecenia programu PowerShell przy użyciu [ARMClient](https://github.com/projectkudu/ARMClient), narzędzie wiersza polecenia typu open source, które upraszcza wywoływanie interfejsu API usługi Azure Resource Manager. Jak pokazano poniżej, w wywołaniu PUT przykładowe przełączanie wszystkich reguł alertów za pomocą narzędzia ARMclient skojarzone z określonym obszarem roboczym usługi Log Analytics.

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Jeśli przełącznik wszystkich reguł alertów w obszarze roboczym usługi Log Analytics, aby użyć nowych [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) to się powiedzie, następującą odpowiedź będą dostępne.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

Użytkownicy mogą również sprawdzić bieżący stan obszaru roboczego usługi Log Analytics i sprawdzić, czy ma lub nie został przełączony do użycia [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) tylko. Aby sprawdzić, użytkownicy mogą wykonywać wywołanie GET na poniżej interfejsu API.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Wykonanie powyższych przy użyciu wiersza polecenia programu PowerShell przy użyciu [ARMClient](https://github.com/projectkudu/ARMClient) narzędzia, zobacz poniższy przykład.

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Jeśli określony obszar roboczy usługi Log Analytics została przełączona do użycia [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) tylko; odpowiedź JSON zostaną wymienione poniżej.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Else, jeśli określony obszar roboczy Log Analytics nie ma jeszcze przełączenie do użycia [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) tylko; odpowiedź JSON zostaną wymienione poniżej.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [usługa Azure Monitor — alertów dzienników](alerts-unified-log.md).
- Dowiedz się, jak utworzyć [alerty dzienników w alertach Azure](alerts-log.md).
- Dowiedz się więcej o [środowisko Azure Alerts](../../azure-monitor/platform/alerts-overview.md).
