---
title: Usługa Azure sieci szkieletowej zdarzeń agregacji z systemem Windows Azure Diagnostics | Dokumentacja firmy Microsoft
description: Więcej informacji na temat agregowania i zbierania zdarzeń przy użyciu WAD monitorowania i diagnostyki klastrów sieci szkieletowej usług Azure.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: dekapur;srrengar
ms.openlocfilehash: ede128d23ca73dc46f2d4dc4b1dd4b1f83a2bc3f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Agregacja zdarzeń i kolekcji przy użyciu systemu Windows Azure Diagnostics
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Jeśli korzystasz z klastra usługi sieć szkieletowa usług Azure, jest dobrym pomysłem jest zebrać dzienniki ze wszystkich węzłów w centralnej lokalizacji. Posiadanie dzienniki w centralnej lokalizacji ułatwiają analizowanie i rozwiązywanie problemów w klastrze lub problemy w aplikacji i usług działających w klastrze.

Jednym ze sposobów przekazywania i zbierania dzienników jest używane rozszerzenie systemu Windows Azure Diagnostics (WAD), który przekazuje dzienniki usługi Azure Storage, a także ma możliwość przesyłania dzienników usługi Application Insights dla platformy Azure lub usługi Event Hubs. Umożliwia także procesu zewnętrznego do odczytywania zdarzenia z magazynu i umieścić w produkcie platformy analizy, takich jak [analizy dzienników OMS](../log-analytics/log-analytics-service-fabric.md) lub innego rozwiązania do analizowania dziennika.

## <a name="prerequisites"></a>Wymagania wstępne
Te narzędzia są używane do wykonywania niektórych operacji w tym dokumencie:

* [Diagnostyka Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (związane z usług Azure Cloud Services, ale ma dobrej informacje i przykłady)
* [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Klient usługi Azure Resource Manager](https://github.com/projectkudu/ARMClient)
* [Szablon usługi Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="log-and-event-sources"></a>Źródła dziennika i zdarzenie

### <a name="service-fabric-platform-events"></a>Zdarzenia platformy Service Fabric
Zgodnie z opisem w [w tym artykule](service-fabric-diagnostics-event-generation-infra.md), Service Fabric konfiguruje możesz z kilku kanałów poza pole rejestrowania, w których następujące kanały łatwo są skonfigurowane przy użyciu WAD do wysłania, monitorowania i diagnostyki danych do tabeli magazynu lub w innym miejscu:
  * Zdarzenia operacyjne: operacje wyższego poziomu, które wykonuje platformy sieć szkieletowa usług. Tworzenie aplikacji i usług, zmian stanu węzła i informacje o uaktualnianiu przykładami. Te są emitowane jako dzienniki zdarzeń śledzenia dla systemu Windows (ETW)
  * [Reliable Actors zdarzeń modelu programowania](service-fabric-reliable-actors-diagnostics.md)
  * [Niezawodne usługi zdarzeń modelu programowania](service-fabric-reliable-services-diagnostics.md)

### <a name="application-events"></a>Zdarzenia aplikacji
 Zdarzenia wysyłanego z Twojej aplikacji i usług kodu i zapisany przy użyciu Pomocnika EventSource — klasa w szablony programu Visual Studio. Aby uzyskać więcej informacji na temat zapisywania dzienników źródła zdarzeń z aplikacji, zobacz [monitora i diagnozowania usług w Instalatorze programowanie komputera lokalnego](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

## <a name="deploy-the-diagnostics-extension"></a>Wdrażanie rozszerzenia diagnostyki
Pierwszym etapem zbierania dzienników jest wdrażanie rozszerzenia diagnostyki na wszystkich maszynach wirtualnych w klastrze usługi sieć szkieletowa usług. Rozszerzenie diagnostycznych zbiera dzienniki na każdej maszynie Wirtualnej i przekazuje je do konta magazynu, który określisz. Kroki zależą od nieco przy użyciu portalu Azure lub usługi Azure Resource Manager. Kroki również różnić w zależności od tego, czy wdrożenie jest częścią tworzenia klastra lub jest dla klastra, który już istnieje. Oto kroki dla każdego scenariusza.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Wdrażanie rozszerzenia diagnostyki w ramach tworzenia klastra za pośrednictwem portalu Azure
Aby wdrożyć rozszerzenie diagnostyki maszyny wirtualne w klastrze, w ramach tworzenia klastra, użyj panelu Ustawienia diagnostyki pokazano na poniższej ilustracji — upewnij się, że diagnostyki jest ustawiona na **na** (ustawienie domyślne). Po utworzeniu klastra, nie możesz zmienić te ustawienia przy użyciu portalu.

![Azure ustawień diagnostycznych w portalu do tworzenia klastra](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics.png)

Podczas tworzenia klastra przy użyciu portalu, zalecamy pobranie szablonu **przed kliknięciem przycisku OK** do utworzenia klastra. Aby uzyskać więcej informacji, zapoznaj się [Konfigurowanie klastra sieci szkieletowej usług za pomocą szablonu usługi Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Musisz szablonu, aby wprowadzić zmiany później, ponieważ nie można wprowadzić kilka zmian za pomocą portalu.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>Wdrażanie rozszerzenia diagnostyki w ramach tworzenia klastra przy użyciu usługi Azure Resource Manager
Aby utworzyć klaster przy użyciu usługi Resource Manager, należy dodać konfiguracji diagnostyki JSON do szablonu usługi Resource Manager pełne klastra przed utworzeniem klastra. Udostępniamy przykładowy szablon Menedżera zasobów klastra maszyny Wirtualnej pięciu o konfiguracji diagnostyki dodana jako część nasze przykłady szablonu usługi Resource Manager. Można to sprawdzić w tej lokalizacji w galerii Azure przykładów: [pięcioma węzłami klastra z przykładowy szablon Menedżera zasobów diagnostyki](https://azure.microsoft.com/en-in/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

Aby wyświetlić ustawienia diagnostyki w szablonie usługi Resource Manager, otwórz plik azuredeploy.json i wyszukaj **IaaSDiagnostics**. Aby utworzyć klaster przy użyciu tego szablonu, wybierz **wdrażanie na platformie Azure** przycisk dostępny w poprzedniej konsolidacji.

Alternatywnie można pobrać przykładowych Menedżera zasobów, wprowadzić zmiany i utworzyć klaster przy użyciu szablonu modyfikacji, przy użyciu `New-AzureRmResourceGroupDeployment` polecenie w oknie programu PowerShell systemu Azure. Zobacz poniższy kod dla parametrów, które przekazujesz do polecenia. Aby uzyskać szczegółowe informacje na temat wdrażania grupy zasobów za pomocą programu PowerShell, zobacz artykuł [wdrażanie grupy zasobów przy użyciu szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>Wdrażanie rozszerzenia diagnostyki do istniejącego klastra
Jeśli masz istniejący klaster nie ma wdrożonych diagnostyki lub jeśli chcesz zmodyfikować istniejącą konfigurację, można dodać lub zaktualizować. Zmodyfikuj szablonu usługi Resource Manager, który służy do tworzenia istniejącego klastra lub pobrać szablonu z portalu zgodnie z wcześniejszym opisem. Zmodyfikuj plik template.json, wykonując następujące zadania.

Dodaj nowy zasób magazynu do szablonu przez dodanie do sekcji zasobów.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Następnie dodaj do sekcji Parametry zaraz po definicji konta magazynu, między `supportLogStorageAccountName` i `vmNodeType0Name`. Zastąp tekst zastępczy *nazwy konta magazynu tu* z nazwą konta magazynu.

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
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Następnie zaktualizuj `VirtualMachineProfile` sekcji pliku template.json przez dodanie poniższego kodu w tablicy rozszerzeń. Pamiętaj dodać przecinek na początku lub na końcu, w zależności od tego, gdzie jest wstawiana.

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

Po zmodyfikowaniu pliku template.json zgodnie z opisem, ponownie opublikować szablonu usługi Resource Manager. Jeśli szablon został wyeksportowany, uruchamiania pliku deploy.ps1 je opublikuje szablonu. Po wdrożeniu, upewnij się, że **ProvisioningState** jest **zakończyło się pomyślnie**.

> [!TIP]
> Jeśli zamierzasz wdrażanie kontenerów do klastra, należy włączyć WAD podnieść Statystyka docker przez dodanie do Twojej **WadCfg > DiagnosticMonitorConfiguration** sekcji.
>
>```json
>"DockerSources": {
>    "Stats": {
>        "enabled": true,
>        "sampleRate": "PT1M"
>    }
>},
>```

## <a name="log-collection-configurations"></a>Konfiguracje zbierania dzienników
Dzienniki z dodatkowych kanałów są również dostępne dla kolekcji, Oto niektóre spośród najbardziej typowe konfiguracje wprowadzone w szablonie dla klastrów działających na platformie Azure.

* Kanał Operational - Base: Włączona domyślnie wysokiego poziomu operacje wykonywane przez sieć szkieletowa usług i klastra, w tym zdarzenia dotyczące węzła przygotowanie Nowa aplikacja jest wdrażana, oraz wycofywania uaktualnienia, itp. Aby uzyskać listę zdarzeń, zapoznaj się [zdarzenia operacyjne kanału](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-diagnostics-event-generation-operational).
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* Kanał Operational — szczegółowe: W tym raportów o kondycji i równoważenie decyzji, a także wszystkich elementów w podstawowej kanału operacyjne obciążenia. Te zdarzenia są generowane przez system lub kodu za pomocą kondycji lub załadować API raportowania, takie jak [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) lub [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Aby wyświetlić te zdarzenia w Podglądzie zdarzeń diagnostycznych programu Visual Studio Dodaj "Microsoft-ServiceFabric:4:0x4000000000000008" do listy dostawców ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Dane i kanał wiadomości - Base: dzienniki krytyczne i zdarzenia generowane w wiadomości (obecnie tylko ReverseProxy) oraz ścieżki danych oprócz szczegółowe kanału operacyjnych dziennikach. Te zdarzenia są przetwarzania błędów i innych zagadnień krytycznych ReverseProxy i żądań przetwarzanych żądań. **To Nasze zalecenia dla rejestrowania kompleksowe**. Aby wyświetlić te zdarzenia w Podglądzie zdarzeń diagnostycznych programu Visual Studio, należy dodać "Microsoft-ServiceFabric:4:0x4000000000000010" do listy dostawców ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Kanał wiadomości - szczegółowe & danych: pełne kanału, który zawiera wszystkie dzienniki niekrytyczne z danych i komunikatów w klastrze i szczegółowe operacyjne kanału. Szczegółowe Rozwiązywanie problemów z wszystkich zdarzeń zwrotnego serwera proxy, zobacz [przewodnik diagnostyki zwrotnego serwera proxy](service-fabric-reverse-proxy-diagnostics.md).  Aby wyświetlić te zdarzenia w Podglądzie zdarzeń diagnostycznych programu Visual Studio, należy dodać "Microsoft-ServiceFabric:4:0x4000000000000020" do listy dostawców ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Ten kanał ma bardzo dużą liczbę zdarzeń, włączanie zbierania zdarzeń z tego szczegółowe wyniki kanału w partii śladów Trwa generowanie szybko i może wykorzystać pojemności magazynu. Tylko włączenie tej funkcji, jeśli jest to bezwzględnie konieczne.


Aby włączyć **bazy danych i kanał wiadomości** Nasze zalecenia dla rejestrowania kompleksowe `EtwManifestProviderConfiguration` w `WadCfg` szablonu będzie wyglądać podobnie do następującej:

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
                "scheduledTransferKeywordFilter": "4611686018427387928",
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

## <a name="collect-from-new-eventsource-channels"></a>Zbierać z nowych kanałów źródła zdarzeń

Można zaktualizować diagnostykę, aby zbieranie dzienników z nowych kanałów EventSource reprezentujących nową aplikację Cię o do wdrożenia, wykonanie takie same opisane wcześniej ustawień diagnostycznych istniejącego klastra.

Zaktualizuj `EtwEventSourceProviderConfiguration` sekcji w pliku template.json można dodawać nowych kanałów EventSource przed zastosowaniem konfiguracji aktualizacji przy użyciu `New-AzureRmResourceGroupDeployment` polecenia programu PowerShell. Nazwa źródła zdarzeń jest zdefiniowany jako części kodu w pliku ServiceEventSource.cs generowanych przez program Visual Studio.

Na przykład jeśli źródło zdarzenia jest o nazwie Mój Eventsource, Dodaj następujący kod, aby umieścić zdarzenia Moje Eventsource w tabeli o nazwie MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Aby zebrać liczników wydajności lub dzienniki zdarzeń, należy modyfikować szablonu usługi Resource Manager za pomocą przykłady podane w [Utwórz maszynę wirtualną systemu Windows z monitorowania i diagnostyki za pomocą szablonu usługi Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Następnie należy ponownie opublikować szablonu usługi Resource Manager.

## <a name="collect-performance-counters"></a>Zebrać liczników wydajności

Na potrzeby zbierania miar wydajności z klastra, należy dodać liczniki wydajności do użytkownika "WadCfg > DiagnosticMonitorConfiguration" w szablonie usługi Resource Manager dla klastra. Zobacz [monitorowania wydajności z WAD](service-fabric-diagnostics-perf-wad.md) instrukcje na temat modyfikowania Twojej `WadCfg` zbierać określone liczniki wydajności. Odwołanie [liczniki wydajności sieci szkieletowej usług](service-fabric-diagnostics-event-generation-perf.md) dla listy wydajności liczniki, które firma Microsoft zaleca zbierania.
  
Jeśli używasz zbiornika usługi Application Insights zgodnie z opisem w poniższej sekcji i chcesz tych metryk wyświetlani w usłudze Application Insights, następnie upewnij się, że Dodaj nazwę obiektu sink w sekcji "sink", jak pokazano powyżej. Liczniki wydajności, które są konfigurowane osobno automatycznie zostanie wysłane do zasobu usługi Application Insights.


## <a name="send-logs-to-application-insights"></a>Wysyłanie dzienników do usługi Application Insights

Wysyłanie danych monitorowania i diagnostyki Insights aplikacji (AI) może zostać wykonane jako część konfiguracji WAD. Jeśli zdecydujesz się używać AI do analizy zdarzeń i wizualizacji, przeczytaj [analiza zdarzeń i wizualizacji z usługą Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) ustanowienie zbiornika AI jako część programu "WadCfg".

## <a name="next-steps"></a>Kolejne kroki

Po diagnostyki Azure zostały prawidłowo skonfigurowane, zostanie wyświetlona dane w tabelach magazynu z dzienników zdarzeń systemu Windows i EventSource. Jeśli wybierzesz OMS, Kibana lub dowolnej innych danych analizy i wizualizacji platformie, która nie jest bezpośrednio skonfigurowany w szablonie usługi Resource Manager, upewnij się skonfigurować platformę wybór można odczytać danych z tych tabel do przechowywania. W ten sposób dla OMS jest względnie proste i zostało wyjaśnione w dokumencie [zdarzeń i dziennika analizy za pośrednictwem OMS](service-fabric-diagnostics-event-analysis-oms.md). Usługa Application Insights jest nieco szczególnych przypadkach, w tym sensie, ponieważ może być skonfigurowana jako część konfiguracji rozszerzenia diagnostyki, aby zapoznać się [odpowiedniego artykułu](service-fabric-diagnostics-event-analysis-appinsights.md) Jeśli wybierzesz AI.

>[!NOTE]
>Nie ma można filtrować lub czyścić zdarzenia, które są wysyłane do tabeli. Nie można wdrożyć proces Usuń zdarzenia z tabeli, będzie nadal wzrostu tabeli. Obecnie jest przykładem usługi pielęgnacji danych w [próbki programu alarmowego](https://github.com/Azure-Samples/service-fabric-watchdog-service), i zaleca zapisu jedną dla siebie również, chyba że powody do przechowywania dzienników poza przedział czasu dnia 30 lub 90.

* [Dowiedz się, jak zbierania liczników wydajności lub dzienniki przy użyciu rozszerzenia diagnostyki](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Analiza zdarzeń i wizualizacji z usługą Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Analiza zdarzeń i wizualizacji z OMS](service-fabric-diagnostics-event-analysis-oms.md)
