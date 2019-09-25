---
title: Tworzenie ustawień diagnostycznych w celu zbierania dzienników i metryk na platformie Azure | Microsoft Docs
description: Tworzenie ustawień diagnostycznych do przesyłania dalej dzienników platformy Azure do dzienników Azure Monitor, Azure Storage lub Azure Event Hubs.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: ec1842d534dcb1e9ddef149d3ae879677b29e715
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262520"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Utwórz ustawienie diagnostyczne, aby zbierać dzienniki platformy i metryki na platformie Azure
[Dzienniki platformy](resource-logs-overview.md) na platformie Azure zapewniają szczegółowe informacje diagnostyczne i inspekcji dla zasobów platformy Azure i platformy Azure, od których zależą. Ten artykuł zawiera szczegółowe informacje na temat tworzenia i konfigurowania ustawień diagnostycznych w celu zbierania dzienników platformy do różnych miejsc docelowych.

Każdy zasób platformy Azure wymaga własnego ustawienia diagnostycznego. Ustawienie diagnostyczne definiuje następujące zasoby:

- Kategorie dzienników i dane metryk wysyłane do miejsc docelowych zdefiniowanych w ustawieniu. Dostępne kategorie różnią się w zależności od typów zasobów.
- Co najmniej jeden miejsce docelowe do wysłania dzienników. Bieżące miejsca docelowe obejmują Log Analytics obszaru roboczego, Event Hubs i usługi Azure Storage.
- Zasady przechowywania danych przechowywanych w usłudze Azure Storage.
 
Pojedyncze ustawienie diagnostyczne może definiować jedno z miejsc docelowych. Jeśli chcesz wysłać dane do więcej niż jednego określonego typu miejsca docelowego (na przykład dwa różne obszary robocze Log Analytics), a następnie Utwórz wiele ustawień. Każdy zasób może mieć do 5 ustawień diagnostycznych.

> [!NOTE]
> Dziennik aktywności można przesłać dalej do tych samych lokalizacji docelowych co inne dzienniki platformy, ale nie jest on skonfigurowany przy użyciu ustawień diagnostycznych. Aby uzyskać szczegółowe informacje [, zobacz Omówienie dzienników platformy na platformie Azure](platform-logs-overview.md#destinations) .

> [!NOTE]
> [Metryki platformy](metrics-supported.md) są zbierane automatycznie, aby [Azure monitor metryki](data-platform-metrics.md). Za pomocą ustawień diagnostycznych można zbierać metryki dla określonych usług platformy Azure w Azure Monitor dzienników do analizy z innymi danymi monitorowania przy użyciu [zapytań dzienników](../log-query/log-query-overview.md).

## <a name="destinations"></a>Cele 
Dzienniki platformy można wysyłać do miejsc docelowych w poniższej tabeli. Konfiguracja dla każdego miejsca docelowego odbywa się przy użyciu tego samego procesu tworzenia ustawień diagnostycznych opisanych w tym artykule. Aby uzyskać szczegółowe informacje na temat wysyłania danych do tego miejsca docelowego, należy postępować zgodnie z poniższymi tabelami.

| Destination | Opis |
|:---|:---|
| [Obszar roboczy usługi Log Analytics](resource-logs-collect-workspace.md) | Zbieranie dzienników w obszarze roboczym Log Analytics pozwala analizować je za pomocą innych danych monitorowania zbieranych przez Azure Monitor przy użyciu zaawansowanych zapytań dzienników, a także korzystać z innych funkcji Azure Monitor, takich jak alerty i wizualizacje. |
| [Centra zdarzeń](resource-logs-stream-event-hubs.md) | Wysyłanie dzienników do Event Hubs umożliwia przesyłanie strumieniowe danych do systemów zewnętrznych, takich jak rozwiązań Siem innych firm i inne rozwiązania do analizy dzienników. |
| [Konto usługi Azure Storage](resource-logs-collect-storage.md) | Archiwizowanie dzienników na koncie usługi Azure Storage jest przydatne w przypadku inspekcji, analizy statycznej lub tworzenia kopii zapasowych. |




## <a name="create-diagnostic-settings-in-azure-portal"></a>Tworzenie ustawień diagnostycznych w Azure Portal
Ustawienia diagnostyczne można skonfigurować w Azure Portal z menu Azure Monitor lub z menu zasobów.

1. W menu Azure Monitor w Azure Portal kliknij pozycję **Ustawienia diagnostyczne** w obszarze **Ustawienia** , a następnie kliknij zasób.

    ![Ustawienia diagnostyczne](media/diagnostic-settings/menu-monitor.png)

    Lub z menu zasobów w Azure Portal kliknij pozycję **Ustawienia diagnostyczne** w obszarze **monitorowanie**.

    ![Ustawienia diagnostyczne](media/diagnostic-settings/menu-resource.png)

2. Jeśli ustawienia nie istnieje w zasobie wybrany, zostanie wyświetlony monit, aby utworzyć ustawienie. Kliknij pozycję **Włącz diagnostykę**.

   ![Dodaj ustawienie diagnostyczne — nie istniejące ustawienia](media/diagnostic-settings/add-setting.png)

   Jeśli w zasobie istnieją istniejące ustawienia, zostanie wyświetlona lista ustawień, które zostały już skonfigurowane. Kliknij pozycję **Dodaj ustawienie diagnostyczne** , aby dodać nowe ustawienie lub **edytować ustawienie** , aby edytować istniejące. Każde ustawienie nie może mieć więcej niż jednego z typów docelowych.

   ![Dodaj ustawienie diagnostyczne — istniejące ustawienia](media/diagnostic-settings/edit-setting.png)

3. Nadaj ustawieniu nazwę, jeśli jeszcze jej nie ma.
4. Zaznacz pole wyboru dla każdego miejsca docelowego, aby wysłać dzienniki. Kliknij pozycję **Konfiguruj** , aby określić swoje ustawienia zgodnie z opisem w poniższej tabeli.

    | Ustawienie | Opis |
    |:---|:---|
    | Obszar roboczy usługi Log Analytics | Nazwa obszaru roboczego. |
    | Konto magazynu | Nazwa konta magazynu. |
    | Przestrzeń nazw centrum zdarzeń | Przestrzeń nazw, w której jest tworzony centrum zdarzeń (jeśli jest to pierwsze dzienniki przesyłania strumieniowego) lub przesyłane strumieniowo do programu (jeśli istnieją już zasoby, które są przesyłane strumieniowo do tej przestrzeni nazw).
    | Nazwa centrum zdarzeń | Opcjonalnie można określić nazwę centrum zdarzeń, aby wysłać wszystkie dane w ustawieniu. Jeśli nie określisz nazwy, centrum zdarzeń zostanie utworzone dla każdej kategorii dzienników. W przypadku wysyłania wielu kategorii warto określić nazwę, aby ograniczyć liczbę utworzonych centrów zdarzeń. Szczegóły można znaleźć w temacie limity [przydziału i limity Event Hubs platformy Azure](../../event-hubs/event-hubs-quotas.md) . |
    | Nazwa zasad Centrum zdarzeń | Definiuje uprawnienia, które ma mechanizm przesyłania strumieniowego. |

    ![Dodaj ustawienie diagnostyczne — istniejące ustawienia](media/diagnostic-settings/setting-details.png)

5. Zaznacz pole wyboru dla każdej kategorii danych do wysłania do określonych miejsc docelowych. W przypadku wybrania opcji **archiwizowania na koncie magazynu**należy również określić [okres przechowywania](resource-logs-collect-storage.md#data-retention).



> [!NOTE]
> Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
>
> *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych metryka ta jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejkach w centrum zdarzeń.

4. Kliknij pozycję **Zapisz**.

Po kilku chwilach nowe ustawienie zostanie wyświetlone na liście ustawień dla tego zasobu, a dzienniki są przesyłane strumieniowo do określonych lokalizacji docelowych w miarę generowania nowych danych zdarzeń. Należy pamiętać, że po wydaniu zdarzenia i [wyświetleniu go w obszarze roboczym log Analytics](data-ingestion-time.md)może istnieć do 15 minut.



## <a name="create-diagnostic-settings-using-powershell"></a>Tworzenie ustawień diagnostycznych przy użyciu programu PowerShell
Użyj polecenia cmdlet [Set-AzDiagnosticSetting](https://docs.microsoft.com/en-us/powershell/module/az.monitor/set-azdiagnosticsetting) , aby utworzyć ustawienie diagnostyczne z [Azure PowerShell](powershell-quickstart-samples.md). Zapoznaj się z dokumentacją tego polecenia cmdlet, aby zapoznać się z opisami jego parametrów.

Poniżej znajduje się przykładowe polecenie cmdlet programu PowerShell służące do tworzenia ustawień diagnostycznych przy użyciu wszystkich trzech miejsc docelowych.


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -RetentionEnabled $true -RetentionInDays 7 -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>Tworzenie ustawień diagnostycznych przy użyciu interfejsu wiersza polecenia platformy Azure
Użyj polecenia [AZ monitor Diagnostic-Settings Create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) , aby utworzyć ustawienie diagnostyczne przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). Zapoznaj się z dokumentacją tego polecenia, aby zapoznać się z opisami jego parametrów.

Poniżej znajduje się przykładowe polecenie interfejsu wiersza polecenia służące do tworzenia ustawień diagnostycznych przy użyciu wszystkich trzech miejsc docelowych.



```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true,"retentionPolicy": {"days": 7,"enabled": true}}]' \
--metrics '[{"category": "AllMetrics","enabled": true,"retentionPolicy": {"days": 7,"enabled": true}}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>Konfigurowanie ustawień diagnostycznych przy użyciu interfejsu API REST
Zobacz [Ustawienia diagnostyczne](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) , aby utworzyć lub zaktualizować ustawienia diagnostyczne przy użyciu [interfejsu API REST Azure monitor](https://docs.microsoft.com/rest/api/monitor/).


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Konfigurowanie ustawień diagnostycznych przy użyciu szablonu Menedżer zasobów
Zobacz [Automatyczne włączanie ustawień diagnostycznych podczas tworzenia zasobów przy użyciu szablonu Menedżer zasobów](diagnostic-settings-template.md) do tworzenia lub aktualizowania ustawień diagnostycznych przy użyciu szablonu Menedżer zasobów.

## <a name="next-steps"></a>Następne kroki

* [Przeczytaj więcej na temat dzienników platformy Azure](resource-logs-overview.md)