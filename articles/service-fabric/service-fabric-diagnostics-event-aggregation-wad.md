---
title: Agregacja zdarzeń za pomocą diagnostyki systemu Windows Azure
description: Dowiedz się więcej o agregowaniu i zbieraniu zdarzeń przy użyciu wad do monitorowania i diagnostyki klastrów sieci szkieletowej usług Azure.
author: srrengar
ms.topic: conceptual
ms.date: 04/03/2018
ms.author: srrengar
ms.openlocfilehash: b9a448ff41c66fa3a38c124f7acde062bacbe9ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282499"
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Agregacja i zbieranie zdarzeń przy użyciu diagnostyki systemu Windows Azure
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Podczas uruchamiania klastra sieci szkieletowej usług Azure, warto zbierać dzienniki ze wszystkich węzłów w centralnej lokalizacji. Posiadanie dzienników w centralnej lokalizacji ułatwia analizowanie i rozwiązywanie problemów w klastrze lub problemów w aplikacjach i usługach uruchomionych w tym klastrze.

Jednym ze sposobów przekazywania i zbierania dzienników jest użycie rozszerzenia diagnostyki systemu Windows Azure (WAD), które przekazuje dzienniki do usługi Azure Storage, a także ma możliwość wysyłania dzienników do usługi Azure Application Insights lub Event Hubs. Można również użyć procesu zewnętrznego, aby odczytać zdarzenia z magazynu i umieścić je w produkcie platformy analizy, takich jak [dzienniki usługi Azure Monitor](../log-analytics/log-analytics-service-fabric.md) lub innego rozwiązania do analizy dzienników.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule używane są następujące narzędzia:

* [Azure Resource Manager](../azure-resource-manager/management/overview.md)
* [Azure PowerShell](/powershell/azure/overview)
* [Szablon usługi Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-platform-events"></a>Zdarzenia platformy sieci szkieletowej usług
Sieci szkieletowej usług ustawia się z kilku [gotowych kanałów rejestrowania](service-fabric-diagnostics-event-generation-infra.md), z których następujące kanały są wstępnie skonfigurowane z rozszerzeniem do wysyłania danych monitorowania i diagnostyki do tabeli magazynu lub w innym miejscu:
  * [Zdarzenia operacyjne:](service-fabric-diagnostics-event-generation-operational.md)operacje wyższego poziomu, które wykonuje platforma sieci szkieletowej usług. Przykłady obejmują tworzenie aplikacji i usług, zmiany stanu węzła i informacje o uaktualnieniu. Są one emitowane jako dzienniki śledzenia zdarzeń dla systemu Windows (ETW)
  * [Reliable Actors modelowanie zdarzeń modelu](service-fabric-reliable-actors-diagnostics.md)
  * [Zdarzenia modelu programowania niezawodnych usług](service-fabric-reliable-services-diagnostics.md)

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>Wdrażanie rozszerzenia diagnostyki za pośrednictwem portalu
Pierwszym krokiem w zbieraniu dzienników jest wdrożenie rozszerzenia diagnostyki w węzłach zestawu skalowania maszyny wirtualnej w klastrze sieci szkieletowej usług. Rozszerzenie diagnostyki zbiera dzienniki na każdej maszynie Wirtualnej i przekazuje je do konta magazynu, które określisz. W poniższych krokach opisano, jak to zrobić dla nowych i istniejących klastrów za pośrednictwem witryny Azure portal i szablonów usługi Azure Resource Manager.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Wdrażanie rozszerzenia diagnostyki w ramach tworzenia klastra za pośrednictwem witryny Azure Portal
Podczas tworzenia klastra w kroku konfiguracji klastra rozwiń ustawienia opcjonalne i upewnij się, że diagnostyka jest ustawiona **na Włączone** (ustawienie domyślne).

![Ustawienia diagnostyki platformy Azure w portalu do tworzenia klastra](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

Zdecydowanie zalecamy pobranie szablonu **przed kliknięciem przycisku Utwórz** w ostatnim kroku. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie klastra sieci szkieletowej usług przy użyciu szablonu Usługi Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Szablon jest potrzebny do wprowadzania zmian w kanałach (wymienionych powyżej), aby zbierać dane.

![Szablon klastra](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

Teraz, gdy agregujesz zdarzenia w usłudze Azure Storage, [skonfiguruj dzienniki usługi Azure Monitor,](service-fabric-diagnostics-oms-setup.md) aby uzyskać szczegółowe informacje i zbadać je w portalu dzienników usługi Azure Monitor

>[!NOTE]
>Obecnie nie ma możliwości filtrowania lub oczyszczenia zdarzeń, które są wysyłane do tabel. Jeśli nie zaimplementujesz procesu usuwania zdarzeń z tabeli, tabela będzie nadal rosnąć (domyślny limit wynosi 50 GB). Instrukcje dotyczące zmiany tego są [dalsze w tym artykule](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota). Ponadto istnieje przykład usługi pielęgnacji danych uruchomionej w [przykładzie watchdog](https://github.com/Azure-Samples/service-fabric-watchdog-service)i zaleca się, aby napisać jeden dla siebie, jak również, chyba że istnieje dobry powód, aby przechowywać dzienniki poza 30 lub 90 dni ramy czasowe.



## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>Wdrażanie rozszerzenia diagnostyki za pośrednictwem usługi Azure Resource Manager

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>Tworzenie klastra z rozszerzeniem diagnostyki
Aby utworzyć klaster przy użyciu Menedżera zasobów, należy dodać konfigurację diagnostyki JSON do pełnego szablonu Menedżera zasobów. Udostępniamy przykładowy szablon Menedżera zasobów klastra pięciu maszyn wirtualnych z konfiguracją diagnostyki dodaną do niego jako część naszych przykładów szablonów Menedżera zasobów. Można go zobaczyć w tej lokalizacji w galerii przykłady platformy Azure: [Klaster pięciu węzłów z przykładowym szablonem Menedżera zasobów diagnostyki](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

Aby wyświetlić ustawienie Diagnostyka w szablonie Menedżer zasobów, otwórz plik azuredeploy.json i wyszukaj **iaaSdiagnostics**. Aby utworzyć klaster przy użyciu tego szablonu, wybierz przycisk Wdrażanie na **platformie Azure** dostępny w poprzednim łączu.

Alternatywnie można pobrać przykład Menedżera zasobów, wprowadzić zmiany do niego i utworzyć klaster `New-AzResourceGroupDeployment` z zmodyfikowanym szablonem przy użyciu polecenia w oknie programu Azure PowerShell. Zobacz następujący kod parametrów, które można przekazać do polecenia. Aby uzyskać szczegółowe informacje dotyczące wdrażania grupy zasobów przy użyciu programu PowerShell, zobacz artykuł [Wdrażanie grupy zasobów z szablonem usługi Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>Dodawanie rozszerzenia diagnostyki do istniejącego klastra
Jeśli masz istniejący klaster, który nie ma wdrożony diagnostyki, można dodać lub zaktualizować go za pośrednictwem szablonu klastra. Zmodyfikuj szablon Menedżera zasobów, który jest używany do tworzenia istniejącego klastra lub pobierz szablon z portalu, jak opisano wcześniej. Zmodyfikuj plik template.json, wykonując następujące zadania:

Dodaj nowy zasób magazynu do szablonu, dodając go do sekcji zasobów.

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

 Następnie dodaj do sekcji parametry tuż po definicjach `supportLogStorageAccountName`kont magazynu, między . Zastąp *nazwę konta magazynu* tekstu zastępczego idzie tutaj z nazwą konta magazynu, które chcesz.

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
Następnie zaktualizuj sekcję `VirtualMachineProfile` pliku template.json, dodając następujący kod w tablicy rozszerzeń. Pamiętaj, aby dodać przecinek na początku lub na końcu, w zależności od tego, gdzie jest wstawiony.

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
                },
                {
                    "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
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

Po zmodyfikowaniu pliku template.json zgodnie z opisem ponownie opublikuj szablon Menedżera zasobów. Jeśli szablon został wyeksportowany, uruchomienie pliku deploy.ps1 ponownie publikuje szablon. Po wdrożeniu upewnij się, że **apowizjno** jest **powodem.**

> [!TIP]
> Jeśli zamierzasz wdrożyć kontenery w klastrze, włącz wad odebrać statystyki docker, dodając to do **wadcfg > DiagnosticMonitorConfiguration** sekcji.
>
>```json
>"DockerSources": {
>    "Stats": {
>        "enabled": true,
>        "sampleRate": "PT1M"
>    }
>},
>```

### <a name="update-storage-quota"></a>Aktualizowanie przydziału magazynu

Ponieważ tabele wypełniane przez rozszerzenie rośnie, dopóki przydział zostanie trafiony, można rozważyć zmniejszenie rozmiaru przydziału. Wartość domyślna wynosi 50 GB i można `overallQuotaInMB` go konfigurować w szablonie pod polem`DiagnosticMonitorConfiguration`

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>Konfiguracje kolekcji dzienników
Dzienniki z dodatkowych kanałów są również dostępne do zbierania, oto niektóre z najbardziej typowych konfiguracji, które można wprowadzić w szablonie dla klastrów uruchomionych na platformie Azure.

* Kanał operacyjny — baza: domyślnie włączone operacje wysokiego poziomu wykonywane przez sieci szkieletowe usług i klaster, w tym zdarzenia dla nadchodzącego węzła, wdrażana nowa aplikacja lub wycofywanie uaktualnień itp. Lista zdarzeń znajduje się w [części Zdarzenia kanału operacyjnego](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-operational).
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* Kanał operacyjny — szczegółowe informacje: obejmuje to raporty dotyczące kondycji i decyzje dotyczące równoważenia obciążenia, a także wszystko w podstawowym kanale operacyjnym. Zdarzenia te są generowane przez system lub kod przy użyciu interfejsów API raportowania kondycji lub obciążenia, takich jak [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) lub [ReportLoad.](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx) Aby wyświetlić te zdarzenia w Podglądzie zdarzeń diagnostycznych programu Visual Studio, dodaj "Microsoft-ServiceFabric:4:0x40000000000000000008" do listy dostawców ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Data and Messaging Channel — baza: Krytyczne dzienniki i zdarzenia generowane w wiadomościach (obecnie tylko ReverseProxy) i ścieżka danych, oprócz szczegółowych dzienników kanałów operacyjnych. Te zdarzenia są żądania przetwarzania błędów i innych krytycznych problemów w ReverseProxy, jak również żądania przetwarzane. **Jest to nasza rekomendacja dla kompleksowego rejestrowania.** Aby wyświetlić te zdarzenia w Podglądzie zdarzeń diagnostycznych programu Visual Studio, dodaj "Microsoft-ServiceFabric:4:0x4000000000000001010" do listy dostawców ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Data & Messaging Channel — szczegółowe: pełny kanał, który zawiera wszystkie dzienniki niekrytyczne z danych i wiadomości w klastrze i szczegółowy kanał operacyjny. Szczegółowe informacje na temat rozwiązywania problemów ze wszystkimi zdarzeniami odwrotnego serwera proxy można znaleźć w [przewodniku diagnostyki odwrotnego serwera proxy](service-fabric-reverse-proxy-diagnostics.md).  Aby wyświetlić te zdarzenia w przeglądarce zdarzeń diagnostycznych programu Visual Studio, dodaj "Microsoft-ServiceFabric:4:0x400000000000002020" do listy dostawców ETW.

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Ten kanał ma bardzo dużą liczbę zdarzeń, włączając zbieranie zdarzeń z tego szczegółowego kanału powoduje, że wiele śladów jest generowanych szybko i może zużywać pojemność magazynu. Włącz to tylko wtedy, gdy jest to absolutnie konieczne.


Aby włączyć **podstawowy kanał operacyjny** nasze zalecenie dotyczące kompleksowego rejestrowania z najmniejszą ilością hałasu, w `EtwManifestProviderConfiguration` `WadCfg` szablonie będzie wyglądać następująco:

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
              },
              {
                "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
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

## <a name="collect-from-new-eventsource-channels"></a>Zbieraj z nowych kanałów EventSource

Aby zaktualizować diagnostyki do zbierania dzienników z nowych kanałów EventSource, które reprezentują nową aplikację, którą zamierzasz wdrożyć, wykonaj te same kroki, jak wcześniej opisane dla instalacji diagnostyki dla istniejącego klastra.

Zaktualizuj sekcję `EtwEventSourceProviderConfiguration` w pliku template.json, aby dodać wpisy dla nowych `New-AzResourceGroupDeployment` kanałów EventSource przed zastosowaniem aktualizacji konfiguracji za pomocą polecenia programu PowerShell. Nazwa źródła zdarzeń jest zdefiniowana jako część kodu w pliku ServiceEventSource.cs wygenerowanym przez program Visual Studio.

Na przykład jeśli źródło zdarzenia ma nazwę My-Eventsource, dodaj następujący kod, aby umieścić zdarzenia z my-eventsource w tabeli o nazwie MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Aby zebrać liczniki wydajności lub dzienniki zdarzeń, zmodyfikuj szablon Menedżera zasobów przy użyciu przykładów podanych w [obszarze Tworzenie maszyny wirtualnej systemu Windows z monitorowaniem i diagnostyką przy użyciu szablonu Usługi Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Następnie ponownie opublikuj szablon Menedżera zasobów.

## <a name="collect-performance-counters"></a>Zbieranie liczników wydajności

Aby zebrać metryki wydajności z klastra, dodaj liczniki wydajności do "WadCfg > DiagnosticMonitorConfiguration" w szablonie Menedżera zasobów dla klastra. Zobacz [monitorowanie wydajności z wad](service-fabric-diagnostics-perf-wad.md) kroki `WadCfg` dotyczące modyfikowania liczników wydajności do zbierania określonych liczników wydajności. Odwołaj się do [liczników wydajności sieci szkieletowej](service-fabric-diagnostics-event-generation-perf.md) usług dla listy liczników wydajności, które zaleca się zbieranie.
  
Jeśli używasz ujścia usługi Application Insights, zgodnie z opisem w poniższej sekcji i chcesz, aby te metryki były wyświetlane w usłudze Application Insights, upewnij się, że dodaj nazwę ujścia w sekcji "pochłaniacze", jak pokazano powyżej. Spowoduje to automatyczne wysłanie liczników wydajności, które są indywidualnie skonfigurowane do zasobu usługi Application Insights.


## <a name="send-logs-to-application-insights"></a>Wysyłanie dzienników do usługi Application Insights

### <a name="configuring-application-insights-with-wad"></a>Konfigurowanie usługi Application Insights za pomocą wad

>[!NOTE]
>Ma to zastosowanie tylko do klastrów systemu Windows w tej chwili.

Istnieją dwa podstawowe sposoby wysyłania danych z wad do usługi Azure Application Insights, który jest osiągany przez dodanie ujścia usługi Application Insights do konfiguracji WAD, za pośrednictwem witryny Azure portal lub za pośrednictwem szablonu usługi Azure Resource Manager.

#### <a name="add-an-application-insights-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Dodawanie klucza instrumentacji usługi Application Insights podczas tworzenia klastra w witrynie Azure portal

![Dodawanie klawisza AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Podczas tworzenia klastra, jeśli diagnostyka jest włączona " Włącz", zostanie wyświetlenie opcjonalnego pola do wprowadzenia klucza instrumentacji usługi Application Insights. Jeśli wklejasz klucz usługi Application Insights w tym miejscu, usługa Application Insights jest automatycznie konfigurowana w szablonie Menedżera zasobów używanym do wdrażania klastra.

#### <a name="add-the-application-insights-sink-to-the-resource-manager-template"></a>Dodawanie ujścia usługi Application Insights do szablonu Menedżera zasobów

W "WadCfg" szablonu Menedżera zasobów, dodaj "Sink", dołączając następujące dwie zmiany:

1. Dodaj konfigurację ujścia bezpośrednio po `DiagnosticMonitorConfiguration` zakończeniu deklarowania jest:

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

2. Dołącz sink w `DiagnosticMonitorConfiguration` przez dodanie następującego `DiagnosticMonitorConfiguration` wiersza w `WadCfg` `EtwProviders` (tuż przed są zadeklarowane):

    ```json
    "sinks": "applicationInsights"
    ```

W obu poprzednich fragmentów kodu nazwa "applicationInsights" została użyta do opisania ujścia. Nie jest to wymagane i tak długo, jak nazwa ujścia jest zawarta w "sinks", można ustawić nazwę na dowolny ciąg.

Obecnie dzienniki z klastra są wyświetlane jako ślady w przeglądarce **dzienników** usługi Application Insights. Ponieważ większość śladów pochodzących z platformy są na poziomie "Informational", można również rozważyć zmianę konfiguracji ujścia tylko wysłać dzienniki typu "Ostrzeżenie" lub "Błąd". Można to zrobić, dodając "Kanały" do zlewu, jak pokazano w [tym artykule](../azure-monitor/platform/diagnostics-extension-to-application-insights.md).

>[!NOTE]
>Jeśli używasz niepoprawnego klucza usługi Application Insights w portalu lub w szablonie Menedżera zasobów, musisz ręcznie zmienić klucz i zaktualizować klaster / ponownie go wdrożyć.

## <a name="next-steps"></a>Następne kroki

Po poprawnej konfiguracji diagnostyki platformy Azure zobaczysz dane w tabelach magazynu z dzienników ETW i EventSource. Jeśli zdecydujesz się korzystać z dzienników usługi Azure Monitor, Kibana lub innej platformy analizy i wizualizacji danych, która nie jest bezpośrednio skonfigurowana w szablonie Menedżera zasobów, upewnij się, że skonfigurujesz wybraną platformę do odczytu danych z tych tabel magazynu. W ten sposób dla dzienników usługi Azure Monitor jest stosunkowo trywialne i jest wyjaśnione w [analizie zdarzeń i dzienników.](service-fabric-diagnostics-event-analysis-oms.md) Usługa Application Insights jest nieco szczególnym przypadkiem w tym sensie, ponieważ można go skonfigurować jako część konfiguracji rozszerzenia diagnostyki, więc zapoznaj się z [odpowiednim artykułem,](service-fabric-diagnostics-event-analysis-appinsights.md) jeśli zdecydujesz się użyć ai.

>[!NOTE]
>Obecnie nie ma możliwości filtrowania lub oczyszczenia zdarzeń, które są wysyłane do tabeli. Jeśli nie zaimplementujesz procesu usuwania zdarzeń z tabeli, tabela będzie nadal rosnąć. Obecnie istnieje przykład usługi pielęgnacji danych uruchomionej w [przykładzie watchdog](https://github.com/Azure-Samples/service-fabric-watchdog-service)i zaleca się, aby napisać jeden dla siebie, jak również, chyba że istnieje dobry powód, aby przechowywać dzienniki poza 30 lub 90 dni ramy czasowe.

* [Dowiedz się, jak zbierać liczniki wydajności lub dzienniki przy użyciu rozszerzenia Diagnostyka](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Analiza i wizualizacja zdarzeń za pomocą usługi Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Analiza zdarzeń i wizualizacja za pomocą dzienników usługi Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)
* [Analiza i wizualizacja zdarzeń za pomocą usługi Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Analiza zdarzeń i wizualizacja za pomocą dzienników usługi Azure Monitor](service-fabric-diagnostics-event-analysis-oms.md)
