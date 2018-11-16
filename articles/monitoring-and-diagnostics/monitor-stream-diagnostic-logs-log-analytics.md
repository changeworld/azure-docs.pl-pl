---
title: Stream dzienniki diagnostyczne platformy Azure do usługi Log Analytics
description: Dowiedz się, jak przesyłać strumieniowo dzienniki diagnostyczne platformy Azure z obszarem roboczym usługi Log Analytics.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: fe1557a6f9e5fd4e463af254fa1dd52726e73024
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51713048"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>Stream dzienniki diagnostyczne platformy Azure do usługi Log Analytics

**[Dzienniki diagnostyczne platformy Azure](monitoring-overview-of-diagnostic-logs.md)**  może być przesyłany strumieniowo w czasie zbliżonym do rzeczywistego usługą Azure Log Analytics przy użyciu portalu, poleceń cmdlet programu PowerShell lub wiersza polecenia platformy Azure.

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>Co można zrobić przy użyciu diagnostyki dzienników w usłudze Log Analytics

Usługa Azure Log Analytics jest narzędziem elastyczne dziennika analizy i wyszukiwania, która umożliwia uzyskiwanie szczegółowych danych nieprzetworzonych danych dzienników generowanych na podstawie zasobów platformy Azure. Niektóre funkcje obejmują:

* **Wyszukiwanie w dzienniku** -zapisu zaawansowanych zapytań danych dziennika, korelowanie dzienniki z różnych źródeł, a nawet Generowanie wykresów, które można przypiąć do pulpitu nawigacyjnego platformy Azure.
* **Alerty** -Wykryj, kiedy jeden lub więcej zdarzeń pasuje do określonego zapytania i otrzymywania powiadomień za pomocą wywołania adresu e-mail lub elementu webhook.
* **Rozwiązania** — Użyj wstępnie utworzonych widoków i pulpitów nawigacyjnych, które zapewniają natychmiastowy wgląd w dane dziennika.
* **Advanced analytics** — stosowanie usługi machine learning i wzorca dopasowania algorytmów w celu identyfikacji możliwych problemów ujawnionych w dziennikach.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>Włączanie przesyłania strumieniowego dzienników diagnostycznych do usługi Log Analytics

Aby umożliwić przesyłanie strumieniowe dzienników diagnostycznych programowo, w portalu lub przy użyciu [interfejsów API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). W obu przypadkach można utworzyć ustawienie diagnostyczne, w którym należy określić obszar roboczy usługi Log Analytics i kategorie dzienników i metryk, które chcesz wysłać do tego obszaru roboczego. Diagnostyka **kategoria dziennika** jest typem dziennika, który może dostarczyć zasób.

Obszar roboczy usługi Log Analytics nie musi znajdować się w tej samej subskrypcji co zasób emitowane dzienniki, jak długo użytkownik, który konfiguruje ustawienie ma odpowiedni dostęp RBAC do obu subskrypcji.

> [!NOTE]
> Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
>
> *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych metryka ta jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejkach w centrum zdarzeń.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Dzienniki diagnostyczne Stream przy użyciu portalu
1. W portalu, przejdź do usługi Azure Monitor, a następnie kliknij pozycję **ustawień diagnostycznych**

    ![Monitorowanie sekcji usługi Azure Monitor](media/monitor-stream-diagnostic-logs-log-analytics/diagnostic-settings-blade.png)

2. Opcjonalnie filtrować listę według grupy zasobów lub typ zasobu, a następnie kliknij na zasób, dla którego chcesz skonfigurować ustawienie diagnostyczne.

3. Jeśli ustawienia nie istnieje w zasobie wybrany, zostanie wyświetlony monit, aby utworzyć ustawienie. Kliknij przycisk "Włącz diagnostykę."

   ![Dodaj ustawienie diagnostyczne — nie istniejące ustawienia](media/monitor-stream-diagnostic-logs-log-analytics/diagnostic-settings-none.png)

   W przypadku ustawienia istniejące na zasób zostanie wyświetlona lista ustawień już skonfigurowany dla tego zasobu. Kliknij pozycję "Dodaj ustawienie diagnostyczne".

   ![Dodaj ustawienie diagnostyczne — istniejące ustawienia](media/monitor-stream-diagnostic-logs-log-analytics/diagnostic-settings-multiple.png)

3. Nadaj nazwę ustawienia i zaznacz pole **wysyłanie do usługi Log Analytics**, następnie wybierz obszar roboczy usługi Log Analytics.

   ![Dodaj ustawienie diagnostyczne — istniejące ustawienia](media/monitor-stream-diagnostic-logs-log-analytics/diagnostic-settings-configure.png)

4. Kliknij pozycję **Zapisz**.

Po kilku chwilach nowe ustawienie jest wyświetlane na liście ustawień dla tego zasobu, i jak tylko nowe dane zdarzenia są generowane dzienniki diagnostyczne są przesyłane strumieniowo do tego obszaru roboczego. Należy pamiętać, że może być maksymalnie 15 minut między podczas zdarzenia są emitowane i, gdy pojawia się w usłudze Log Analytics.

### <a name="via-powershell-cmdlets"></a>Za pomocą poleceń cmdlet programu PowerShell
Przesyłania strumieniowego za pośrednictwem [poleceń cmdlet programu PowerShell Azure](insights-powershell-samples.md), możesz użyć `Set-AzureRmDiagnosticSetting` polecenia cmdlet z następującymi parametrami:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Należy pamiętać, że właściwość workspaceID przyjmuje identyfikator zasobu pełnej platformy Azure nie obszaru roboczego klucz/Identyfikatora wyświetlane w portalu usługi Log Analytics w obszarze roboczym.

### <a name="via-azure-cli"></a>Za pomocą wiersza polecenia platformy Azure

Do przesyłania strumieniowego za pośrednictwem [wiersza polecenia platformy Azure](insights-cli-samples.md), możesz użyć [tworzenie az monitor diagnostic-settings](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) polecenia.

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Dodatkowe kategorie można dodawać do dzienników diagnostycznych, dodając słowników do tablicy JSON przekazywane jako `--logs` parametru.

`--resource-group` Argument tylko jest wymagany, jeżeli `--workspace` nie jest identyfikator obiektu.

## <a name="how-do-i-query-the-data-in-log-analytics"></a>Jak zapytanie danych w usłudze Log Analytics?

W bloku przeszukiwania dzienników w portalu lub środowisku Advanced Analytics w ramach usługi Log Analytics można badać dzienniki diagnostyczne, jako część z rozwiązaniem zarządzanie dziennikiem pod tabelą AzureDiagnostics. Dostępne są także [kilka rozwiązań dla zasobów platformy Azure](../azure-monitor/insights/solutions.md) można zainstalować na uzyskiwanie natychmiastowego wglądu w dane dziennika są wysyłane do usługi Log Analytics.

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej o dziennikach diagnostycznych platformy Azure](monitoring-overview-of-diagnostic-logs.md)
