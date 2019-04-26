---
title: Usługa Azure Service Fabric zdarzeń agregacji z Windows Azure Diagnostics | Dokumentacja firmy Microsoft
description: Więcej informacji na temat agregowania i zbieranie zdarzeń za pomocą funkcji WAD do monitorowania i diagnostyki klastrów usługi Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/03/2018
ms.author: srrengar
ms.openlocfilehash: 641f9150d1135f4f214038150b95b6691a37ecc0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60393335"
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Zdarzenie agregacji i kolekcji przy użyciu Windows Azure Diagnostics
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Po uruchomieniu klastra usługi Azure Service Fabric to dobry pomysł, aby zbierać dzienniki z wszystkimi węzłami w centralnej lokalizacji. Posiadanie dzienniki w centralnej lokalizacji, ułatwiają analizowanie i rozwiązywanie problemów w klastrze lub problemy w aplikacji i usług działających w klastrze.

Jednym ze sposobów przekazywania i zbierania dzienników jest użycie rozszerzenia diagnostyki Azure Windows (WAD, Domain Name System), przekazuje dzienniki do usługi Azure Storage, która ma również możliwość przesyłania dzienników do usługi Azure Application Insights lub centrów zdarzeń. Umożliwia także procesu zewnętrznego do odczytywania zdarzeń z magazynu i umieszczenie ich w produkcie platformy analizy, takie jak [dzienniki usługi Azure Monitor](../log-analytics/log-analytics-service-fabric.md) lub innego rozwiązania do analizowania dziennika.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule używane są następujące narzędzia:

* [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Szablon usługi Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-platform-events"></a>Zdarzenia platformy usługi Service Fabric
Usługa Service Fabric konfiguruje możesz za pomocą kilku [kanałów poza okno rejestrowania](service-fabric-diagnostics-event-generation-infra.md), następujące kanały są wstępnie skonfigurowane przy użyciu rozszerzenia do wysłania, monitorowania i danych diagnostycznych do tabeli magazynu lub w innym miejscu:
  * [Zdarzenia operacyjne](service-fabric-diagnostics-event-generation-operational.md): operacje wyższego poziomu, które wykonuje platformy usługi Service Fabric. Przykłady obejmują tworzenie aplikacji i usług, zmian stanu węzła oraz informacje dotyczące uaktualniania. Te są emitowane jako dzienniki zdarzeń śledzenia dla Windows (ETW)
  * [Elementy Reliable Actors zdarzeń modelu programowania](service-fabric-reliable-actors-diagnostics.md)
  * [Zdarzeń modelu programowania usług Reliable Services](service-fabric-reliable-services-diagnostics.md)

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>Wdrażanie rozszerzenie diagnostyki za pośrednictwem portalu
Pierwszym krokiem podczas zbierania dzienników jest wdrażanie rozszerzenia diagnostyki w węzłach zestawu skalowania maszyn wirtualnych w klastrze usługi Service Fabric. Rozszerzenie diagnostyki zbiera dzienniki na każdej maszynie Wirtualnej i przekazuje je do konta magazynu, który określisz. Następujące kroki przedstawiają sposób wykonania tego dla nowych i istniejących klastrów za pomocą witryny Azure portal i szablonom usługi Azure Resource Manager.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Wdrażanie rozszerzenia diagnostyki w ramach tworzenia klastra za pośrednictwem witryny Azure portal
Podczas tworzenia klastra, w kroku konfiguracji klastra, rozwiń opcjonalne ustawienia i upewnij się, że Diagnostyka jest ustawiona na **na** (ustawienie domyślne).

![Ustawienia diagnostyki usługi Azure w portalu do tworzenia klastra](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

Zdecydowanie zaleca się pobranie szablonu **przed kliknięciem przycisku Utwórz** w ostatnim kroku. Aby uzyskać szczegółowe informacje, zapoznaj się [Konfigurowanie klastra usługi Service Fabric przy użyciu szablonu usługi Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Konieczne jest szablon, dokonać zmian w kanałach, jakie (wymienionych powyżej) zbieranie danych z.

![Szablon klastra](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

Teraz, gdy masz agregowania zdarzeń w usłudze Azure Storage [Konfigurowanie dzienników usługi Azure Monitor](service-fabric-diagnostics-oms-setup.md) uzyskiwanie szczegółowych informacji i wykonywania zapytań w witrynie Azure Monitor dzienniki portalu

>[!NOTE]
>Obecnie nie ma sposobu filtrowania lub pielęgnacja zdarzenia, które są wysyłane do tabel. Jeśli nie możesz wdrożyć proces Usuń zdarzenia z tabeli, tabeli będą w dalszym ciągu Rozwijaj (cap domyślną jest 50 GB). Instrukcje dotyczące sposobu zmiany są [dalsze poniżej w tym artykule](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota). Ponadto jest przykładem pielęgnacji Usługa danych uruchomiona na [przykładowe strażnika](https://github.com/Azure-Samples/service-fabric-watchdog-service), i jest zalecana zapisu jedną dla siebie, chyba że powody do przechowywania dzienników ponad 30 lub 90 przedział czasu dnia.



## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>Wdrażanie rozszerzenie diagnostyki za pomocą usługi Azure Resource Manager

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>Tworzenie klastra przy użyciu rozszerzenia diagnostyki
Aby utworzyć klaster przy użyciu usługi Resource Manager, należy dodać plik JSON z konfiguracją diagnostyki pełny szablon usługi Resource Manager. Firma Microsoft udostępnia przykładowy szablon usługi Resource Manager klastra maszyny Wirtualnej na pięciu o konfiguracji diagnostyki dodawanych do niego jako część nasze przykłady szablonów usługi Resource Manager. Można go było wyświetlić w tym miejscu w galerii przykładów dla platformy Azure: [Klaster z pięcioma węzłami przy użyciu przykładowych szablonów Menedżera zasobów diagnostycznych](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

Aby wyświetlić ustawienia diagnostyki w szablonie usługi Resource Manager, otwórz plik azuredeploy.json, a następnie wyszukaj **IaaSDiagnostics**. Aby utworzyć klaster za pomocą tego szablonu, wybierz **Wdróż na platformie Azure** przycisk jest dostępny w poprzedniej konsolidacji.

Alternatywnie można pobrać przykładowy usługi Resource Manager, wprowadzić zmiany i tworzenie klastra za pomocą zmodyfikowanego szablonu przy użyciu `New-AzResourceGroupDeployment` polecenia w oknie programu Azure PowerShell. Zobacz poniższy kod dla parametrów, które można przekazać do polecenia. Aby uzyskać szczegółowe informacje na temat wdrażania grupy zasobów przy użyciu programu PowerShell, zobacz artykuł [wdrażanie grupy zasobów za pomocą szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>Dodaj rozszerzenie diagnostyki do istniejącego klastra
W przypadku istniejącego klastra, który nie ma wdrożonych diagnostyki można dodać lub zaktualizować go za pomocą szablonu klastra. Zmodyfikuj szablon usługi Resource Manager, który jest używany do tworzenia istniejącego klastra lub pobrać szablon z poziomu portalu, zgodnie z wcześniejszym opisem. Zmodyfikuj plik template.json, wykonując następujące czynności:

Dodać nowego zasobu usługi storage do szablonu, dodając do sekcji zasobów.

```json
{
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
    "name": "[parameters('applicationDiagnosticsStorageAccountType')]"
    "tier": "standard"
  },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Następnie dodaj do sekcji parametrów zaraz po definicji konta magazynu, między `supportLogStorageAccountName`. Zastąp tekst zastępczy *tu będzie nazwa konta magazynu* nazwą konta magazynu, którą chcesz.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Następnie zaktualizuj `VirtualMachineProfile` sekcji plik template.json, dodając następujący kod w tablicy rozszerzenia. Pamiętaj dodać przecinek na początku lub końcu, w zależności od tego, gdzie jest wstawiana.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

Po zmodyfikowaniu plik template.json, zgodnie z opisem, należy ponownie opublikować szablonu usługi Resource Manager. Jeśli szablon został wyeksportowany, uruchamiając plik deploy.ps1 je opublikuje szablonu. Po wdrożeniu, upewnij się, że **ProvisioningState** jest **Powodzenie**.

> [!TIP]
> Jeśli zamierzasz wdrażanie kontenerów do klastra, należy włączyć narzędzie diagnostyczne wczytać statystyk platformy docker, dodając ten element, aby Twoje **WadCfg > DiagnosticMonitorConfiguration** sekcji.
>
>```json
>"DockerSources": {
>    "Stats": {
>        "enabled": true,
>        "sampleRate": "PT1M"
>    }
>},
>```

### <a name="update-storage-quota"></a>Aktualizowanie limitu przydziału pamięci masowej

Od tabel wypełnione przez rozszerzenie powiększa się dopóki nie zostanie osiągnięty limit przydziału, warto rozważyć zmniejszenie rozmiaru przydziału. Wartością domyślną jest 50 GB i można skonfigurować w szablonie w obszarze `overallQuotaInMB` pole w obszarze `DiagnosticMonitorConfiguration`

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>Konfiguracje kolekcji dzienników
Dzienniki z dodatkowych kanałów są również dostępne dla kolekcji, poniżej przedstawiono niektóre najbardziej typowe konfiguracje wprowadzone w szablonie dla klastrów działających na platformie Azure.

* Kanał operacyjny - Base: Domyślnie włączone, wysokiego poziomu operacji wykonywanych przez usługi Service Fabric i klastra, w tym zdarzenia dla węzła pojawi się, wdrażania nowej aplikacji lub uaktualnienia wycofywania itd. Aby uzyskać listę zdarzeń, zobacz [zdarzenia operacyjne kanału](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-operational).
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* Kanał operacyjny — szczegółowe: W tym raportów o kondycji i równoważenie decyzje, a także wszystkich elementów w podstawowej kanał operacyjny obciążenia. Te zdarzenia są generowane przez system lub kodu przy użyciu kondycji lub załadować interfejsy API raportowania, takie jak [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) lub [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Aby wyświetlić te zdarzenia w Podglądzie zdarzeń diagnostycznych w programie Visual Studio Dodaj "Microsoft-ServiceFabric:4:0x4000000000000008" do listy dostawców ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Dane i kanału wiadomości - Base: Dzienniki krytyczne i zdarzenia generowane w wiadomości (obecnie tylko elementu ReverseProxy) i ścieżki danych oprócz kanał operacyjny szczegółowe dzienniki. Te zdarzenia są przetwarzania błędów i innych krytycznych problemów z elementu ReverseProxy żądania, a także przetwarzania żądań. **To jest nasza Rekomendacja, kompleksowe rejestrowania**. Aby wyświetlić te zdarzenia w Podglądzie zdarzeń diagnostycznych w programie Visual Studio, należy dodać "Microsoft-ServiceFabric:4:0x4000000000000010" do listy dostawców ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Kanał obsługi komunikatów — szczegółowe & danych: Pełne kanał, który zawiera wszystkie niekrytyczne dzienniki danych i komunikatów w klastrze i szczegółowe kanał operacyjny. Aby uzyskać szczegółowe procedury rozwiązywania problemów z wszystkich zdarzeń zwrotny serwer proxy, zobacz [przewodnik Diagnostyka zwrotnego serwera proxy](service-fabric-reverse-proxy-diagnostics.md).  Aby wyświetlić te zdarzenia w Podglądzie zdarzeń diagnostycznych programu Visual Studio, należy dodać "Microsoft-ServiceFabric:4:0x4000000000000020" do listy dostawców ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Ten kanał ma bardzo dużej liczby zdarzeń, włączanie zbierania zdarzeń z tego szczegółowe wyniki kanału w partii ślady szybko wygenerowany i wykorzystywać pojemność magazynu. Tylko włączyć to jeśli jest to absolutnie konieczne.


Aby włączyć **kanał operacyjnej bazy** nasza Rekomendacja, kompleksowe rejestrowania z najmniejszą ilością szumu, `EtwManifestProviderConfiguration` w `WadCfg` szablonu będzie wyglądała podobnie do poniższego:

```json
  "WadCfg": {
        "DiagnosticMonitorConfiguration": {
          "overallQuotaInMB": "50000",
          "EtwProviders": {
            "EtwEventSourceProviderConfiguration": [
              {
                "provider": "Microsoft-ServiceFabric-Actors",
                "scheduledTransferKeywordFilter": "1",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricReliableActorEventTable"
                }
              },
              {
                "provider": "Microsoft-ServiceFabric-Services",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricReliableServiceEventTable"
                }
              }
            ],
            "EtwManifestProviderConfiguration": [
              {
                "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                "scheduledTransferLogLevelFilter": "Information",
                "scheduledTransferKeywordFilter": "4611686018427387904",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                  "eventDestination": "ServiceFabricSystemEventTable"
                }
              }
            ]
          }
        }
      },
```

## <a name="collect-from-new-eventsource-channels"></a>Zbieranie z nowych kanałów EventSource

Można zaktualizować diagnostykę, aby zbierać dzienniki z nowych kanałów źródła zdarzeń, które reprezentują one o do wdrożenia, wykonanie takie same nowej aplikacji opisane wcześniej dla ustawień diagnostycznych istniejącego klastra.

Aktualizuj `EtwEventSourceProviderConfiguration` sekcji w pliku template.json, aby dodać wpisy dla nowych kanałów EventSource przed zastosowaniem konfiguracji aktualizacji za pomocą `New-AzResourceGroupDeployment` polecenia programu PowerShell. Nazwa źródła zdarzeń jest zdefiniowana jako część kodu w pliku ServiceEventSource.cs generowanych przez program Visual Studio.

Na przykład jeśli źródło zdarzeń nosi Moje Eventsource, Dodaj następujący kod, aby umieścić zdarzenia z Moje źródła zdarzeń w tabeli o nazwie MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Aby zebrać liczników wydajności lub dzienniki zdarzeń, należy zmodyfikować szablon usługi Resource Manager za pomocą przykłady podane w [Utwórz maszynę wirtualną Windows z funkcjami monitorowania i diagnostyki przy użyciu szablonu usługi Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Następnie należy ponownie opublikować szablonu usługi Resource Manager.

## <a name="collect-performance-counters"></a>Zbierz liczniki wydajności

Aby zbierać metryki wydajności z klastrem, należy dodać liczniki wydajności do usługi "WadCfg > DiagnosticMonitorConfiguration" w szablonie usługi Resource Manager dla klastra. Zobacz [monitorowanie wydajności za pomocą funkcji WAD](service-fabric-diagnostics-perf-wad.md) kroki dotyczące modyfikowania swoje `WadCfg` można zebrać liczników wydajności określonych. Odwołanie [liczniki wydajności usługi Service Fabric](service-fabric-diagnostics-event-generation-perf.md) dla liczników listę wydajności, które firma Microsoft zaleca zbierania.
  
Jeśli używasz ujścia usługi Application Insights, zgodnie z opisem w dalszej części tego artykułu, a ma te metryki pojawienie się w usłudze Application Insights, następnie upewnij się dodać nazwę obiektu sink w sekcji "sink", jak pokazano powyżej. Liczniki wydajności, które są indywidualnie skonfigurować automatycznie zostanie wysłane do zasobu usługi Application Insights.


## <a name="send-logs-to-application-insights"></a>Wysyłanie dzienników do usługi Application Insights

### <a name="configuring-application-insights-with-wad"></a>Konfigurowanie usługi Application Insights za pomocą funkcji WAD

>[!NOTE]
>To ma zastosowanie tylko do klastrów Windows w tej chwili.

Istnieją dwa podstawowe sposoby wysyłania danych z WAD do usługi Azure Application Insights, który jest osiągane poprzez dodanie ujścia usługi Application Insights do konfiguracji WAD, za pośrednictwem witryny Azure portal lub szablonu usługi Azure Resource Manager.

#### <a name="add-an-application-insights-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Dodaj klucz Instrumentacji usługi Application Insights, podczas tworzenia klastra w witrynie Azure portal

![Dodawanie AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Podczas tworzenia klastra, jeśli włączono diagnostyki "Włączone", zostaną wyświetlone pole opcjonalne, aby wprowadzić klucz Instrumentacja usługi Application Insights. Jeśli wkleisz klucz Insights aplikacji ujścia usługi Application Insights jest automatycznie konfigurowany dla Ciebie w szablonie usługi Resource Manager, który jest używany do wdrażania klastra.

#### <a name="add-the-application-insights-sink-to-the-resource-manager-template"></a>Dodaj ujścia Application Insights do szablonu usługi Resource Manager

W "WadCfg" szablonu usługi Resource Manager należy dodać "Sink", umieszczając następujące dwie zmiany:

1. Dodaj konfigurację ujścia bezpośrednio po deklarowania elementu `DiagnosticMonitorConfiguration` zostało zakończone:

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. Obejmują ujścia w `DiagnosticMonitorConfiguration` , dodając następujący wiersz w `DiagnosticMonitorConfiguration` z `WadCfg` (tuż przedtem `EtwProviders` są deklarowane):

    ```json
    "sinks": "applicationInsights"
    ```

W obu poprzedzających fragmentów kodu nazwa "applicationInsights" był używany do opisania ujścia. Nie jest to wymagane, i tak długo, jak nazwa ujścia jest uwzględniony w "sink", można ustawić nazwę do dowolnego ciągu.

Obecnie dzienników z klastra są wyświetlane jako **ślady** w przeglądarce dzienników usługi Application Insights. Ponieważ większość ślady przychodzące od platformy z poziomu "Informacyjne", możesz też rozważyć zmiana konfiguracji ujścia tylko do wysyłania dzienników typu "Ostrzeżenie" lub "Błąd". Można to zrobić, dodając "Kanały" do obiektu sink, jak pokazano w [w tym artykule](../azure-monitor/platform/diagnostics-extension-to-application-insights.md).

>[!NOTE]
>Jeśli używasz nieprawidłowy klucz usługi Application Insights w witrynie portal lub w szablonie usługi Resource Manager, należy ręcznie zmienić wartość klucza i zaktualizować klaster / wdrażając go ponownie.

## <a name="next-steps"></a>Kolejne kroki

Po skonfigurowaniu poprawnie diagnostyki platformy Azure, zobaczysz dane w tabelach magazynu z dzienników zdarzeń systemu Windows i źródła zdarzeń. Jeśli zdecydujesz się używać dzienników usługi Azure Monitor, Kibana lub dowolnej innej danych analizy i wizualizacji platformy, która nie skonfigurowano bezpośrednio w szablonie usługi Resource Manager, upewnij się zdefiniować platformę możliwość odczytu danych z tych tabel magazynu. W ten sposób dzienników usługi Azure Monitor jest stosunkowo prosta i została wyjaśniona w [analizy zdarzeń i dzienników](service-fabric-diagnostics-event-analysis-oms.md). Usługa Application Insights jest nieco szczególny przypadek, w tym sensie, ponieważ może on zostać skonfigurowany jako część konfiguracji rozszerzenia diagnostyki, aby zapoznać się [odpowiedniego artykułu](service-fabric-diagnostics-event-analysis-appinsights.md) Jeśli zdecydujesz się używać rozwiązań sztucznej Inteligencji.

>[!NOTE]
>Obecnie nie ma sposobu filtrowania lub pielęgnacja zdarzenia, które są wysyłane do tabeli. Jeśli nie implementują proces usuwania zdarzenia z tabeli, tabeli będzie nadal rosnąć. Obecnie ma przykład pielęgnacji Usługa danych uruchomiona na [przykładowe strażnika](https://github.com/Azure-Samples/service-fabric-watchdog-service), i jest zalecana zapisu jedną dla siebie, chyba że powody do przechowywania dzienników ponad 30 lub 90 przedział czasu dnia.

* [Dowiedz się, jak zbierać dzienniki lub liczniki wydajności za pomocą rozszerzenia diagnostyki](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Analiza zdarzeń i wizualizacji przy użyciu usługi Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Analiza zdarzeń i wizualizacji przy użyciu dzienników usługi Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)
* [Analiza zdarzeń i wizualizacji przy użyciu usługi Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Analiza zdarzeń i wizualizacji przy użyciu dzienników usługi Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)
