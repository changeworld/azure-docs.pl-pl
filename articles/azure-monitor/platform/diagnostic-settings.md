---
title: Tworzenie ustawień diagnostycznych w celu zbierania dzienników i metryk na platformie Azure
description: Tworzenie ustawień diagnostycznych w celu przesyłania dalej dzienników platformy Azure do dzienników usługi Azure Monitor, usługi Azure storage lub usługi Azure Event Hubs.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: fb2f9ff5af68575d9f9d29e9a6aca83d603395b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672416"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Tworzenie ustawień diagnostycznych w celu zbierania dzienników i metryk platformy na platformie Azure
[Dzienniki platformy](platform-logs-overview.md) na platformie Azure, w tym dzienniki aktywności platformy Azure i dzienniki zasobów, zawierają szczegółowe informacje diagnostyczne i inspekcji dla zasobów platformy Azure i platformy Azure, na których zależą. Ten artykuł zawiera szczegółowe informacje na temat tworzenia i konfigurowania ustawień diagnostycznych do wysyłania dzienników platformy do różnych miejsc docelowych.

> [!IMPORTANT]
> Przed utworzeniem ustawienia diagnostycznego do zbierania dziennika aktywności należy najpierw wyłączyć wszystkie starsze konfiguracje. Zobacz [Zbieranie dziennika aktywności platformy Azure ze starszymi ustawieniami,](diagnostic-settings-legacy.md) aby uzyskać szczegółowe informacje.

Każdy zasób platformy Azure wymaga własnego ustawienia diagnostycznego, które definiuje następujące elementy:

- Kategorie dzienników i danych metryk wysyłanych do miejsc docelowych zdefiniowanych w ustawieniu. Dostępne kategorie różnią się w zależności od typu zasobu.
- Co najmniej jedno miejsce docelowe do wysłania dzienników. Bieżące miejsca docelowe obejmują obszar roboczy usługi Log Analytics, usługę Event Hubs i usługę Azure Storage.
 
Pojedyncze ustawienie diagnostyczne można zdefiniować nie więcej niż jeden z każdego miejsca docelowego. Jeśli chcesz wysyłać dane do więcej niż jednego określonego typu miejsca docelowego (na przykład do dwóch różnych obszarów roboczych usługi Log Analytics), utwórz wiele ustawień. Każdy zasób może mieć do 5 ustawień diagnostycznych.


> [!NOTE]
> [Metryki platformy](metrics-supported.md) są zbierane automatycznie do [metryk usługi Azure Monitor](data-platform-metrics.md). Ustawienia diagnostyczne mogą służyć do zbierania metryk dla niektórych usług platformy Azure do dzienników usługi Azure Monitor do analizy z innymi danymi monitorowania przy użyciu [zapytań dziennika.](../log-query/log-query-overview.md)

## <a name="destinations"></a>Miejsca docelowe 
Dzienniki platformy mogą być wysyłane do miejsc docelowych w poniższej tabeli. Konfiguracja dla każdego miejsca docelowego jest wykonywana przy użyciu tego samego procesu tworzenia ustawień diagnostycznych opisanych w tym artykule. Kliknij każdy link w poniższej tabeli, aby uzyskać szczegółowe informacje na temat wysyłania danych do tego miejsca docelowego.

| Element docelowy | Opis |
|:---|:---|
| [Obszar roboczy usługi Log Analytics](resource-logs-collect-workspace.md) | Zbieranie dzienników do obszaru roboczego usługi Log Analytics umożliwia ich analizowanie przy użyciu innych danych monitorowania zebranych przez usługę Azure Monitor przy użyciu zaawansowanych zapytań dziennika, a także w celu wykorzystania innych funkcji usługi Azure Monitor, takich jak alerty i wizualizacje. |
| [Centra zdarzeń](resource-logs-stream-event-hubs.md) | Wysyłanie dzienników do centrów zdarzeń umożliwia przesyłanie strumieniowe danych do systemów zewnętrznych, takich jak siemy innych firm i inne rozwiązania do analizy dzienników. |
| [Konto magazynu platformy Azure](resource-logs-collect-storage.md) | Archiwizowanie dzienników do konta magazynu platformy Azure jest przydatne do inspekcji, analizy statycznej lub tworzenia kopii zapasowych. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Tworzenie ustawień diagnostycznych w witrynie Azure portal
Ustawienia diagnostyczne można skonfigurować w witrynie Azure portal z menu Usługi Azure Monitor lub z menu zasobu.

1. Gdzie skonfigurować ustawienia diagnostyczne w witrynie Azure portal zależy od zasobu.

   - W przypadku pojedynczego zasobu kliknij pozycję **Ustawienia diagnostyczne** w obszarze **Monitor** w menu zasobu.

        ![Ustawienia diagnostyczne](media/diagnostic-settings/menu-resource.png)

    - W przypadku co najmniej jednego zasobu kliknij pozycję **Ustawienia diagnostyczne** w obszarze **Ustawienia** w menu Usługi Azure Monitor, a następnie kliknij zasób.
    
        ![Ustawienia diagnostyczne](media/diagnostic-settings/menu-monitor.png)

    - W przypadku dziennika aktywności kliknij pozycję **Dziennik aktywności** w menu **Azure Monitor,** a następnie **w ustawieniach diagnostycznych**. Upewnij się, że wyłączysz wszystkie starsze konfiguracje dziennika aktywności. Zobacz [Wyłączanie istniejących ustawień,](diagnostic-settings-legacy.md#disable-existing-settings) aby uzyskać szczegółowe informacje.

        ![Ustawienia diagnostyczne](media/diagnostic-settings/menu-activity-log.png)

2. Jeśli w wybranym zasobie nie istnieją żadne ustawienia, zostanie wyświetlony monit o utworzenie ustawienia. Kliknij **pozycję Dodaj ustawienie diagnostyczne**.

   ![Dodawanie ustawień diagnostycznych - brak istniejących ustawień](media/diagnostic-settings/add-setting.png)

   Jeśli w zasobie istnieją ustawienia, zostanie wyświetlona lista ustawień już skonfigurowanych. Kliknij pozycję **Dodaj ustawienie diagnostyczne,** aby dodać nowe ustawienie, lub **ustawienie Edytuj,** aby edytować istniejące. Każde ustawienie może mieć nie więcej niż jeden z każdego typu docelowego.

   ![Dodawanie ustawień diagnostycznych — istniejące ustawienia](media/diagnostic-settings/edit-setting.png)

3. Nadaj ustawieniu nazwę, jeśli jeszcze jej nie ma.
4. Zaznacz pole wyboru dla każdego miejsca docelowego, aby wysłać dzienniki. Kliknij **przycisk Konfiguruj,** aby określić ich ustawienia zgodnie z opisem w poniższej tabeli.

    | Ustawienie | Opis |
    |:---|:---|
    | Obszar roboczy usługi Log Analytics | Nazwa obszaru roboczego. |
    | Konto magazynu | Nazwa konta magazynu. |
    | Przestrzeń nazw centrum zdarzeń | Obszar nazw, w którym jest tworzony centrum zdarzeń (jeśli jest to po raz pierwszy dzienniki przesyłania strumieniowego) lub przesyłane strumieniowo do (jeśli istnieją już zasoby, które są przesyłane strumieniowo tej kategorii dziennika do tego obszaru nazw).
    | Nazwa centrum zdarzeń | Opcjonalnie określ nazwę centrum zdarzeń, aby wysłać wszystkie dane w ustawieniach. Jeśli nie określisz nazwy, centrum zdarzeń zostanie utworzone dla każdej kategorii dziennika. Jeśli wysyłasz wiele kategorii, możesz określić nazwę, aby ograniczyć liczbę utworzonych centrów zdarzeń. Szczegółowe informacje można znaleźć w [przypadku przydziałów i limitów usługi Azure Event Hubs.](../../event-hubs/event-hubs-quotas.md) |
    | Nazwa zasad Centrum zdarzeń | Definiuje uprawnienia, które ma mechanizm przesyłania strumieniowego. |

    ![Dodawanie ustawień diagnostycznych — istniejące ustawienia](media/diagnostic-settings/setting-details.png)

5. Zaznacz pole wyboru dla każdej kategorii danych do wysłania do określonych miejsc docelowych. Lista kategorii będzie się różnić dla każdej usługi platformy Azure.

   > [!NOTE]
   > Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
   >
   > *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych metryka ta jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejkach w centrum zdarzeń.

6. Kliknij przycisk **Zapisz**.

Po kilku chwilach nowe ustawienie pojawi się na liście ustawień dla tego zasobu, a dzienniki są przesyłane strumieniowo do określonych miejsc docelowych podczas generowania nowych danych zdarzeń. Należy zauważyć, że może istnieć maksymalnie piętnaście minut między emitowaniem zdarzenia a [pojawia się w obszarze roboczym usługi Log Analytics.](data-ingestion-time.md)



## <a name="create-diagnostic-settings-using-powershell"></a>Tworzenie ustawień diagnostycznych przy użyciu programu PowerShell
Użyj polecenia cmdlet [Set-AzDiagnosticSetting,](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) aby utworzyć ustawienie diagnostyczne za pomocą [programu Azure PowerShell](powershell-quickstart-samples.md). Opisy jego parametrów można znaleźć w dokumentacji tego polecenia cmdlet.

> [!IMPORTANT]
> Nie można użyć tej metody dla dziennika aktywności platformy Azure. Zamiast tego użyj [tworzenie ustawień diagnostycznych w usłudze Azure Monitor przy użyciu szablonu Menedżera zasobów,](diagnostic-settings-template.md) aby utworzyć szablon Menedżera zasobów i wdrożyć go za pomocą programu PowerShell.

Poniżej przedstawiono przykład polecenia cmdlet programu PowerShell, aby utworzyć ustawienie diagnostyczne przy użyciu wszystkich trzech miejsc docelowych.


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>Tworzenie ustawień diagnostycznych przy użyciu interfejsu wiersza polecenia platformy Azure
Użyj polecenia [tworzenia ustawień diagnostycznych az monitora,](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) aby utworzyć ustawienie diagnostyczne za pomocą [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest)CLI . Opisy jego parametrów można znaleźć w dokumentacji tego polecenia.

> [!IMPORTANT]
> Nie można użyć tej metody dla dziennika aktywności platformy Azure. Zamiast tego użyj [tworzenie ustawień diagnostycznych w usłudze Azure Monitor przy użyciu szablonu Menedżera zasobów,](diagnostic-settings-template.md) aby utworzyć szablon Menedżera zasobów i wdrożyć go w celu utworzenia interfejsu wiersza polecenia.

Poniżej przedstawiono przykładowe polecenie interfejsu wiersza polecenia, aby utworzyć ustawienie diagnostyczne przy użyciu wszystkich trzech miejsc docelowych.



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

### <a name="configure-diagnostic-settings-using-rest-api"></a>Konfigurowanie ustawień diagnostycznych przy użyciu interfejsu REST API
Zobacz [Ustawienia diagnostyczne,](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) aby utworzyć lub zaktualizować ustawienia diagnostyczne przy użyciu [interfejsu API REST usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/).


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Konfigurowanie ustawień diagnostycznych przy użyciu szablonu Menedżera zasobów
Zobacz [Tworzenie ustawień diagnostycznych w usłudze Azure Monitor przy użyciu szablonu Menedżera zasobów](diagnostic-settings-template.md) w celu utworzenia lub zaktualizowania ustawień diagnostycznych za pomocą szablonu Menedżera zasobów.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o dziennikach platformy Azure](platform-logs-overview.md)
