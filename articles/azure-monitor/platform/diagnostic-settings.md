---
title: Tworzenie ustawień diagnostycznych w celu zbierania dzienników i metryk na platformie Azure
description: Tworzenie ustawień diagnostycznych do przesyłania dalej dzienników platformy Azure do dzienników Azure Monitor, Azure Storage lub Azure Event Hubs.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: fb2f9ff5af68575d9f9d29e9a6aca83d603395b3
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77672416"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Utwórz ustawienie diagnostyczne, aby zbierać dzienniki platformy i metryki na platformie Azure
[Dzienniki platformy](platform-logs-overview.md) na platformie Azure, w tym dziennik aktywności platformy Azure i dzienniki zasobów, zapewniają szczegółowe informacje diagnostyczne i inspekcji dla zasobów platformy Azure oraz platformy platformy Azure, od których zależą. Ten artykuł zawiera szczegółowe informacje na temat tworzenia i konfigurowania ustawień diagnostycznych w celu wysyłania dzienników platformy do różnych miejsc docelowych.

> [!IMPORTANT]
> Przed utworzeniem ustawienia diagnostycznego w celu zebrania dziennika aktywności należy najpierw wyłączyć starszą konfigurację. Aby uzyskać szczegółowe informacje, zobacz [zbieranie dziennika aktywności platformy Azure ze starszymi ustawieniami](diagnostic-settings-legacy.md) .

Każdy zasób platformy Azure wymaga własnego ustawienia diagnostycznego, które definiuje następujące elementy:

- Kategorie dzienników i dane metryk wysyłane do miejsc docelowych zdefiniowanych w ustawieniu. Dostępne kategorie różnią się w zależności od typów zasobów.
- Co najmniej jeden miejsce docelowe do wysłania dzienników. Bieżące miejsca docelowe obejmują Log Analytics obszaru roboczego, Event Hubs i usługi Azure Storage.
 
Pojedyncze ustawienie diagnostyczne może definiować nie więcej niż jeden z elementów docelowych. Jeśli chcesz wysłać dane do więcej niż jednego określonego typu miejsca docelowego (na przykład dwa różne obszary robocze Log Analytics), a następnie Utwórz wiele ustawień. Każdy zasób może mieć do 5 ustawień diagnostycznych.


> [!NOTE]
> [Metryki platformy](metrics-supported.md) są zbierane automatycznie, aby [Azure monitor metryki](data-platform-metrics.md). Za pomocą ustawień diagnostycznych można zbierać metryki dla określonych usług platformy Azure w Azure Monitor dzienników do analizy z innymi danymi monitorowania przy użyciu [zapytań dzienników](../log-query/log-query-overview.md).

## <a name="destinations"></a>Docelowym 
Dzienniki platformy można wysyłać do miejsc docelowych w poniższej tabeli. Konfiguracja dla każdego miejsca docelowego odbywa się przy użyciu tego samego procesu tworzenia ustawień diagnostycznych opisanych w tym artykule. Aby uzyskać szczegółowe informacje na temat wysyłania danych do tego miejsca docelowego, należy postępować zgodnie z poniższymi tabelami.

| Element docelowy | Opis |
|:---|:---|
| [Obszar roboczy usługi Log Analytics](resource-logs-collect-workspace.md) | Zbieranie dzienników w obszarze roboczym Log Analytics pozwala analizować je za pomocą innych danych monitorowania zbieranych przez Azure Monitor przy użyciu zaawansowanych zapytań dzienników, a także korzystać z innych funkcji Azure Monitor, takich jak alerty i wizualizacje. |
| [Centra zdarzeń](resource-logs-stream-event-hubs.md) | Wysyłanie dzienników do Event Hubs umożliwia przesyłanie strumieniowe danych do systemów zewnętrznych, takich jak rozwiązań Siem innych firm i inne rozwiązania do analizy dzienników. |
| [Konto usługi Azure Storage](resource-logs-collect-storage.md) | Archiwizowanie dzienników na koncie usługi Azure Storage jest przydatne w przypadku inspekcji, analizy statycznej lub tworzenia kopii zapasowych. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Tworzenie ustawień diagnostycznych w Azure Portal
Ustawienia diagnostyczne można skonfigurować w Azure Portal z menu Azure Monitor lub z menu zasobów.

1. Konfigurowanie ustawień diagnostycznych w Azure Portal zależy od zasobu.

   - W przypadku pojedynczego zasobu kliknij pozycję **Ustawienia diagnostyczne** w obszarze **monitor** w menu zasób.

        ![Ustawienia diagnostyczne](media/diagnostic-settings/menu-resource.png)

    - W przypadku co najmniej jednego zasobu kliknij pozycję **Ustawienia diagnostyczne** w obszarze **ustawienia** w menu Azure Monitor a następnie kliknij zasób.
    
        ![Ustawienia diagnostyczne](media/diagnostic-settings/menu-monitor.png)

    - W dzienniku aktywności kliknij pozycję **Dziennik aktywności** w menu **Azure monitor** a następnie wybierz polecenie **Ustawienia diagnostyczne**. Upewnij się, że dla dziennika aktywności została wyłączona każda starsza konfiguracja. Aby uzyskać szczegółowe informacje, zobacz temat [wyłączanie istniejących ustawień](diagnostic-settings-legacy.md#disable-existing-settings) .

        ![Ustawienia diagnostyczne](media/diagnostic-settings/menu-activity-log.png)

2. Jeśli ustawienia nie istnieje w zasobie wybrany, zostanie wyświetlony monit, aby utworzyć ustawienie. Kliknij pozycję **Dodaj ustawienie diagnostyczne**.

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

5. Zaznacz pole wyboru dla każdej kategorii danych do wysłania do określonych miejsc docelowych. Lista kategorii różni się w zależności od usługi platformy Azure.

   > [!NOTE]
   > Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
   >
   > *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych metryka ta jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejkach w centrum zdarzeń.

6. Kliknij przycisk **Save** (Zapisz).

Po kilku chwilach nowe ustawienie zostanie wyświetlone na liście ustawień dla tego zasobu, a dzienniki są przesyłane strumieniowo do określonych lokalizacji docelowych w miarę generowania nowych danych zdarzeń. Należy pamiętać, że po wydaniu zdarzenia i [wyświetleniu go w obszarze roboczym log Analytics](data-ingestion-time.md)może istnieć do 15 minut.



## <a name="create-diagnostic-settings-using-powershell"></a>Tworzenie ustawień diagnostycznych przy użyciu programu PowerShell
Użyj polecenia cmdlet [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) , aby utworzyć ustawienie diagnostyczne z [Azure PowerShell](powershell-quickstart-samples.md). Zapoznaj się z dokumentacją tego polecenia cmdlet, aby zapoznać się z opisami jego parametrów.

> [!IMPORTANT]
> Nie można użyć tej metody dla dziennika aktywności platformy Azure. Zamiast tego użyj opcji [Utwórz ustawienia diagnostyczne w Azure monitor przy użyciu szablonu Menedżer zasobów](diagnostic-settings-template.md) , aby utworzyć szablon Menedżer zasobów i wdrożyć go przy użyciu programu PowerShell.

Poniżej znajduje się przykładowe polecenie cmdlet programu PowerShell służące do tworzenia ustawień diagnostycznych przy użyciu wszystkich trzech miejsc docelowych.


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>Tworzenie ustawień diagnostycznych przy użyciu interfejsu wiersza polecenia platformy Azure
Użyj polecenia [AZ monitor Diagnostic-Settings Create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) , aby utworzyć ustawienie diagnostyczne przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). Zapoznaj się z dokumentacją tego polecenia, aby zapoznać się z opisami jego parametrów.

> [!IMPORTANT]
> Nie można użyć tej metody dla dziennika aktywności platformy Azure. Zamiast tego użyj opcji [Utwórz ustawienia diagnostyczne w Azure monitor przy użyciu szablonu Menedżer zasobów](diagnostic-settings-template.md) , aby utworzyć szablon Menedżer zasobów i wdrożyć go przy użyciu interfejsu wiersza polecenia.

Poniżej znajduje się przykładowe polecenie interfejsu wiersza polecenia służące do tworzenia ustawień diagnostycznych przy użyciu wszystkich trzech miejsc docelowych.



```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>Konfigurowanie ustawień diagnostycznych przy użyciu interfejsu API REST
Zobacz [Ustawienia diagnostyczne](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) , aby utworzyć lub zaktualizować ustawienia diagnostyczne przy użyciu [interfejsu API REST Azure monitor](https://docs.microsoft.com/rest/api/monitor/).


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Konfigurowanie ustawień diagnostycznych przy użyciu szablonu Menedżer zasobów
Zobacz [Tworzenie ustawień diagnostycznych w Azure monitor przy użyciu szablonu Menedżer zasobów](diagnostic-settings-template.md) do tworzenia lub aktualizowania ustawień diagnostycznych za pomocą szablonu Menedżer zasobów.

## <a name="next-steps"></a>Następne kroki

* [Przeczytaj więcej na temat dzienników platformy Azure](platform-logs-overview.md)
