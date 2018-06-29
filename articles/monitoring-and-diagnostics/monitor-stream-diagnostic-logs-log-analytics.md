---
title: Strumienia Azure dzienników diagnostycznych do analizy dzienników
description: Dowiedz się, jak strumienia Azure dzienników diagnostycznych do obszaru roboczego analizy dzienników.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: d8966edb6061ed07f5aecb9682fca081ed589040
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2018
ms.locfileid: "37083978"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>Strumienia Azure dzienników diagnostycznych do analizy dzienników

**[Azure dzienników diagnostycznych](monitoring-overview-of-diagnostic-logs.md)**  przesyłane strumieniowo w najbliższym czasie rzeczywistym Analiza dzienników Azure za pomocą portalu, poleceń cmdlet programu PowerShell lub 2.0 interfejsu wiersza polecenia platformy Azure.

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>Co można zrobić w diagnostyce loguje analizy dzienników

Analiza dzienników Azure jest narzędziem elastyczne dziennik wyszukiwania i analiza, które pozwala uzyskiwać wgląd w danych pierwotnych dziennika wygenerowanych z zasobów platformy Azure. Niektóre funkcje obejmują:

* **Dziennik wyszukiwania** -zapisu zaawansowane zapytania na danych dziennika, dzienniki korelowanie z różnych źródeł, a nawet Generowanie wykresów, które można przypiąć do pulpitu nawigacyjnego platformy Azure.
* **Alerty** -Wykryj, kiedy jednego lub wielu zdarzeń pasuje do określonego zapytania i stają się powiadomienia za pomocą poczty e-mail lub elementu webhook wywołania.
* **Rozwiązania** -użyć wbudowanych widoki i pulpity nawigacyjne, które zapewniają błyskawiczny wgląd w dane dziennika.
* **Zaawansowane analizy** — zastosować uczenia maszynowego i wzorca zgodnego algorytmy umożliwiające identyfikowanie możliwe problemy ujawnionych w dziennikach.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>Przesyłania strumieniowego dzienników diagnostycznych do analizy dzienników

Można włączyć przesyłania strumieniowego dzienników diagnostycznych programowo, w portalu lub przy użyciu [interfejsów API REST Monitor Azure](https://docs.microsoft.com/en-us/rest/api/monitor/diagnosticsettings). W obu przypadkach należy utworzyć ustawienie diagnostyczne, w którym można określić obszaru roboczego analizy dzienników i kategorie dziennika i metryki, aby wysłać do tego obszaru roboczego. Diagnostyka **kategorii dziennika** jest typem dziennika, która może zapewnić zasobu.

Obszar roboczy analizy dzienników nie musi znajdować się w tej samej subskrypcji co zasób emitowanie dzienniki, dopóki użytkownik, który konfiguruje ustawienia ma odpowiedni dostęp RBAC do obu subskrypcji.

> [!NOTE]
> Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
>
> *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych metryka ta jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejkach w centrum zdarzeń.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Strumieniowe przesyłanie dzienników diagnostycznych przy użyciu portalu
1. W portalu przejdź do monitora Azure i kliknij na **ustawień diagnostycznych**

    ![Monitorowanie sekcji Azure Monitor](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-blade.png)

2. Opcjonalnie Filtruj listę według grupy zasobów lub typ zasobu, a następnie kliknij na zasobie, dla której chcesz skonfigurować ustawienie diagnostyczne.

3. Jeśli nie istnieją żadne ustawienia zasobu wybrany, zostanie wyświetlony monit, aby utworzyć ustawienie. Kliknij pozycję "Włącz diagnostykę."

   ![Dodaj ustawienie diagnostyczne - żadnych istniejących ustawień](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-none.png)

   W przypadku istniejących ustawień na zasobie, zostanie wyświetlona lista ustawień już skonfigurowana dla tego zasobu. Kliknij przycisk "Dodaj ustawienie diagnostyczne".

   ![Dodaj ustawienie diagnostyczne — istniejące ustawienia](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-multiple.png)

3. Nadaj nazwę ustawienia i pole wyboru dla **wysyłać do analizy dzienników**, następnie wybierz obszar roboczy analizy dzienników.

   ![Dodaj ustawienie diagnostyczne — istniejące ustawienia](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-configure.png)

4. Kliknij pozycję **Zapisz**.

Po kilku chwilach nowe ustawienie jest wyświetlane na liście ustawień dla tego zasobu, a dzienników diagnostycznych są przesyłane strumieniowo do tego obszaru roboczego zaraz po wygenerowaniu nowych danych zdarzenia. Należy pamiętać, że mogą być do piętnastu minut między gdy zdarzenie jest emitowany i po wyświetleniu w analizy dzienników.

### <a name="via-powershell-cmdlets"></a>Za pomocą poleceń cmdlet programu PowerShell
Przesyłania strumieniowego za pośrednictwem [poleceń cmdlet programu PowerShell Azure](insights-powershell-samples.md), można użyć `Set-AzureRmDiagnosticSetting` polecenia cmdlet z następującymi parametrami:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Należy pamiętać, że właściwość workspaceID przyjmuje identyfikator zasobu pełnej Azure obszaru roboczego nie obszaru roboczego identyfikator/klucz wyświetlane w portalu usługi Analiza dzienników.

### <a name="via-azure-cli-20"></a>Za pomocą interfejsu wiersza polecenia platformy Azure 2.0

Do przesyłania strumieniowego za pośrednictwem [Azure CLI 2.0](insights-cli-samples.md), można użyć [utworzyć ustawienia diagnostyki az monitora](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) polecenia.

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

Dodatkowe kategorie można dodać do dzienników diagnostycznych, dodając słowniki do tablicy JSON przekazany jako `--logs` parametru.

`--resource-group` Argument jest tylko w przypadku wymaganych `--workspace` nie jest identyfikatorem obiektu.

## <a name="how-do-i-query-the-data-in-log-analytics"></a>Jak zbadać danych analizy dzienników?

W bloku dziennika wyszukiwania w portalu lub analityka zaawansowane środowisko w ramach analizy dzienników jako część rozwiązania do zarządzania dziennika w tabeli AzureDiagnostics można badać dzienników diagnostycznych. Dostępne są także [kilka rozwiązań dla zasobów Azure](../log-analytics/log-analytics-add-solutions.md) można zainstalować, aby uzyskać błyskawiczny wgląd w dane dziennika są wysyłane do analizy dzienników.

## <a name="next-steps"></a>Kolejne kroki

* [Więcej informacji na temat dzienników diagnostycznych platformy Azure](monitoring-overview-of-diagnostic-logs.md)
