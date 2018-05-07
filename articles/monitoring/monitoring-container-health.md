---
title: Monitorowanie kondycji usługi Kubernetes Azure (AKS) (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak łatwo można przejrzeć wydajność sieci kontener AKS, aby szybko poznać użycie środowiska Kubernetes hostowanej.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: magoedte
ms.openlocfilehash: f0501d4404375ee44b96ae4514c15e69b616d38a
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>Monitorowanie kondycji kontenera Azure Kubernetes usługi (AKS) (wersja zapoznawcza)

W tym artykule opisano sposób konfigurowania i używania kondycji kontenera Azure Monitor do monitorowania wydajności obciążeń wdrożonych w środowiskach Kubernetes hostowanej na platformie Azure usługa Kubernetes (AKS).  Monitorowanie klastra i kontenerów usługi Kubernetes ma krytyczne znaczenie, szczególnie w przypadku korzystania z klastra produkcyjnego o dużej skali i z wieloma aplikacjami.

Kontener kondycji umożliwia monitorowanie możliwości na zbieranie pamięci i procesora metryki z kontrolerów, węzły i kontenery dostępne w Kubernetes za pośrednictwem interfejsu API metryki wydajności.  Po włączeniu kondycji kontenera te metryki są automatycznie pobierane przy użyciu konteneryzowanych wersję agenta pakietu OMS dla systemu Linux i przechowywane w Twojej [analizy dzienników](../log-analytics/log-analytics-overview.md) obszaru roboczego.  Wstępnie zdefiniowanych widoków dołączonych Pokaż przechowywanych obciążeń kontenera i co jest wpływające na kondycję wydajności klastra Kubernetes, więc można zrozumieć:  

* Jakie kontenery są uruchomione w węźle i ich średnie wykorzystanie procesora i pamięci do identyfikowania wąskich gardeł w zasobach
* Określenie, której kontenera znajduje się w kontrolerze i/lub stanowiskami, aby wyświetlić ogólną wydajność dla kontrolera lub pod 
* Sprawdź wykorzystanie zasobów obciążeń uruchomionych na hoście niezwiązanych ze sobą na standardowe procedury obsługi pod
* Zrozumienie zachowania klastra w obszarze średni i największe obciążenia do identyfikacji wymagań wydajności i określić maksymalne obciążenie, który może kontynuować działanie 

Jeśli interesuje Cię w monitorowaniu i zarządzania systemu Windows i Docker hostów kontenera konfiguracji widoku, inspekcji i wykorzystania zasobów, zobacz [rozwiązanie monitorowanie kontenera](../log-analytics/log-analytics-containers.md).

## <a name="requirements"></a>Wymagania 
Przed rozpoczęciem, przejrzyj następujące informacje, aby zrozumieć, obsługiwane wymagania wstępne.

- Obsługiwane są następujące wersje klastra AKS: 1.7.7 pytanie 1.9.6.
- Konteneryzowanych agent pakietu OMS dla wersji systemu Linux microsoft / oms:ciprod04202018 i nowszych. Ten agent jest instalowany automatycznie podczas dołączania kondycji kontenera.  
- Obszar roboczy analizy dzienników.  Można tworzyć, gdy monitorowanie nowego klastra AKS lub można go utworzyć za pomocą [usługi Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), lub z [portalu Azure](../log-analytics/log-analytics-quick-create-workspace.md).


## <a name="components"></a>Składniki 

Ta funkcja opiera się na konteneryzowanych Agent pakietu OMS dla systemu Linux w celu zbierania danych dotyczących zdarzeń i wydajności ze wszystkich węzłów w klastrze.  Agent automatycznie wdrożeniu i zarejestrowaniu z określonego obszaru roboczego analizy dzienników po włączeniu monitorowania kontenera. 

>[!NOTE] 
>Jeśli zostały już wdrożone AKS klastra, zostanie włączone monitorowanie za pomocą podanego szablonu Azure Resource Manager, jak pokazano w dalszej części tego artykułu. Nie można użyć `kubectl` do uaktualnienia, Usuń, Wdróż ponownie lub wdrożyć agenta programu.  
>

## <a name="log-in-to-azure-portal"></a>Logowanie do witryny Azure Portal
Zaloguj się do witryny Azure Portal na stronie [https://portal.azure.com](https://portal.azure.com). 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>Włącz monitorowanie kondycji kontener dla nowego klastra
Można włączyć tylko monitorowanie AKS klastra podczas jego wdrażania przy użyciu portalu Azure.  Wykonaj kroki opisane w artykule szybkiego startu [wdrażanie klastra usługi Kubernetes Azure (AKS)](../aks/kubernetes-walkthrough-portal.md).  Jeśli jesteś na **monitorowanie** wybierz pozycję **tak** opcji **Włącz monitorowanie** można włączyć, a następnie wybierz istniejącą lub Utwórz nowy obszar roboczy analizy dzienników.  

Po włączeniu monitorowania wszystkie zadania konfiguracji zostały zakończone pomyślnie, można monitorować wydajność klastra z jednym z dwóch sposobów:

1. Bezpośrednio z klastra AKS, wybierając **kondycji** w lewym okienku.<br><br> 
2. Klikając **monitorowanie kondycji kontenera** kafelka na stronie AKS klastra dla wybranego klastra.  W monitorze Azure, wybierz **kondycji** w lewym okienku.  

![Opcje, aby wybrać kondycji kontenera w AKS](./media/monitoring-container-health/container-performance-and-health-select-01.png)

Po włączeniu monitorowania może potrwać około 15 minut, zanim będzie można wyświetlić danych operacyjnych dla klastra.  

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>Włącz monitorowanie kondycji kontener dla istniejących klastrów zarządzanych
Włączanie monitorowania z kontenera AKS już wdrożona, nie można dokonać z portalu, tylko można zainstalować, za pomocą polecenia cmdlet programu PowerShell przy użyciu podanego szablonu Azure Resource Manager **New-AzureRmResourceGroupDeployment** lub interfejsu wiersza polecenia platformy Azure.  Jeden szablon JSON Określa konfigurację, aby włączyć monitorowanie i szablonie JSON zawiera wartości parametrów, które określają następujące czynności:

* Identyfikator zasobu kontenera AKS 
* Grupa zasobów klastra jest wdrażany w 
* Obszar roboczy analizy dzienników i regionie można utworzyć obszaru roboczego w 

Obszar roboczy analizy dzienników ma zostać utworzone ręcznie.  Aby utworzyć obszaru roboczego, można skonfigurować jeden za pośrednictwem [usługi Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), z [portalu Azure](../log-analytics/log-analytics-quick-create-workspace.md).

Jeśli nie znasz pojęcia związane z wdrażaniem zasobów przy użyciu programu PowerShell przy użyciu szablonu z, zobacz [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)lub interfejsu wiersza polecenia Azure, zobacz [wdrożenie zasobów z Szablony Menedżera zasobów i interfejsu wiersza polecenia Azure](../azure-resource-manager/resource-group-template-deploy-cli.md).

Jeśli wybrano opcję Użyj interfejsu wiersza polecenia Azure, należy najpierw zainstalować i używać interfejsu wiersza polecenia lokalnie.  Jest to wymagane używasz interfejsu wiersza polecenia Azure w wersji 2.0.27 lub nowszej. Uruchom `az --version` do identyfikowania wersji. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-and-execute-template"></a>Tworzenie i wykonywanie szablonu

1. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "aksResourceId": {
        "type": "string",
        "metadata": {
           "description": "AKS Cluster resource id"
        }
    },
    "aksResourceLocation": {
      "type": "string",
      "metadata": {
        "description": "Location of the AKS resource e.g. \"East US\""
        }
      },
      "workspaceId": {
        "type": "string",
        "metadata": {
          "description": "Azure Monitor Log Analytics resource id"
        }
      },
      "workspaceRegion": {
        "type": "string",
        "metadata": {
          "description": "Azure Monitor Log Analytics workspace region"
        }
      }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": true,
              "config": {
                "logAnalyticsWorkspaceResourceID": "[parameters('workspaceId')]"
              }
            }
          }
        }
      },
      {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceId'),'/')[8], ')')]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
         }
      ]
    }
    ```

2. Zapisz ten plik jako **existingClusterOnboarding.json** do folderu lokalnego.
3. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "East US"
        },
        "workspaceId": {
          "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
        },
        "workspaceRegion": {
          "value": "eastus"
        }
      }
    }
    ```

4. Edytuj wartość **aksResourceId**, **aksResourceLocation** z wartościami, które można znaleźć w **omówienie AKS** strony AKS klastra.  Wartość **workspaceId** powinna być nazwą obszaru roboczego analizy dzienników i określ lokalizację dla utworzono w obszarze roboczym **workspaceRegion**.    
5. Zapisz ten plik jako **existingClusterParam.json** do folderu lokalnego.
6. Wszystko jest teraz gotowe do wdrożenia tego szablonu. 

    * Użyj następujących poleceń programu PowerShell z folderu zawierającego szablon:

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        Zmiana konfiguracji może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat o podobne do poniższych opcji obejmujących wynik:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Aby uruchomić następujące polecenie z wiersza polecenia platformy Azure w systemie Linux:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        Zmiana konfiguracji może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat o podobne do poniższych opcji obejmujących wynik:

        ```azurecli
        provisioningState       : Succeeded
        ```
Po włączeniu monitorowania może potrwać około 15 minut, zanim będzie można wyświetlić danych operacyjnych dla klastra.  

## <a name="verify-agent-deployed-successfully"></a>Sprawdź pomyślnie wdrożony agent
Aby sprawdzić, agent pakietu OMS prawidłowo wdrożony, uruchom następujące polecenie: ` kubectl get ds omsagent -—namespace=kube-system`.

Dane wyjściowe powinno przypominać następujące wskazujący, który został wdrożony prawidłowo:

```
User@aksuser:~$ kubectl get ds omsagent -—namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-performance-utilization"></a>Widok wydajności użycia
Po otwarciu kondycji kontener strony natychmiast przedstawia użycie wydajności przez węzły klastra.  Wyświetlanie informacji o klastrze AKS jest podzielone na trzy perspektyw:

- Węzły 
- Kontrolery  
- Containers

Hierarchia wiersza następuje modelu obiektu Kubernetes, począwszy od węzła w klastrze.  Rozwiń węzeł i zobaczysz jednego lub więcej stanowiskami uruchomione w węźle, a jeśli istnieje więcej niż jeden kontener grupowane pod, są wyświetlane jako ostatni wiersz w hierarchii.<br><br> ![Przykładowa hierarchia Kubernetes węzeł w widoku wydajności](./media/monitoring-container-health/container-performance-and-health-view-03.png)

Możesz wybrać kontrolerów lub kontenerów w górnej części strony i sprawdź stan i użycia zasobów dla tych obiektów.  Aby filtrować według przestrzeni nazw usługi i węzła, użyj pola listy rozwijanej w górnej części ekranu. Jeśli zamiast tego chcesz przejrzeć wykorzystanie pamięci, z **Metryka** listy rozwijanej wybierz **RSS pamięci** lub **zestaw roboczy pamięci**.  **Pamięć RSS** jest obsługiwana tylko dla Kubernetes wersji 1.8 i nowszych. W przeciwnym razie zobacz wartości **AVG %** przedstawiający jako *NaN %*, która jest wartością typu danych liczbowych, reprezentujący wartość niezdefiniowane lub unrepresentable. 

![Widok wydajności węzłów wydajności kontenera](./media/monitoring-container-health/container-performance-and-health-view-04.png)

Domyślnie dane wydajności są oparte na ostatnich sześciu godzin, ale można zmienić okna z **zakres czasu** znaleziono listy rozwijanej w prawym górnym rogu strony. W tej chwili strony nie automatycznego odświeżania, dlatego należy go ręcznie odświeżyć. 

W poniższym przykładzie, można zauważyć dla węzła *aks-obiektu agentpool-3402399-0*, wartość **kontenery** to 10, który jest to wartość zbiorcza z całkowitej liczby kontenery wdrożone.<br><br> ![Pakiet zbiorczy kontenerów, na przykład węzła](./media/monitoring-container-health/container-performance-and-health-view-07.png)<br><br> Ułatwia to szybkie ustalenie, czy nie ma właściwej równowagi kontenery między węzłami w klastrze.  

W poniższej tabeli opisano informacje podane podczas wyświetlania węzłów.

| Kolumna | Opis | 
|--------|-------------|
| Name (Nazwa) | Nazwa hosta |
| Stan | Widok stanu węzła Kubernetes |
| % ŚR. | Średni procent węzła oparte na wybrana Metryka czas wybrana wartość czasu trwania. |
| ŚREDNIA | Średnia węzły na podstawie wartości rzeczywistej wybrany Metryka wybrana wartość czasu trwania.  Średnia wartość jest mierzony z Procesora/pamięci limit ustawiony dla węzła; stanowiskami i kontenerów jest średnia wartość zgłaszaną przez hosta. |
| Containers | Liczba kontenerów. |
| Czas pracy | Reprezentuje czas, ponieważ węzeł uruchomiony lub został ponownie uruchomiony. |
| Zasobnik | Tylko dla kontenerów. Przedstawia on, które pods go znajdującej się. |
| Kontrolery | Tylko w przypadku kontenerów i stanowiskami. Przedstawia on kontrolera, który jest znajdującej się. Nie wszystkie stanowiskami będzie w kontrolerze, więc niektóre mogą być wyświetlane n/d. | 
| Trend AVG % | Trend wykres słupkowy w oparciu metryki % avg kontenera i węzła. |


Selektor, wybrać **kontrolerów**.<br><br> ![Wybierz kontrolerów widoku](./media/monitoring-container-health/container-performance-and-health-view-08.png)

Tutaj można zobaczyć kondycję wydajności kontrolerów.<br><br> ![Widok wydajności kontrolerów < nazwa >](./media/monitoring-container-health/container-performance-and-health-view-05.png)

Hierarchia wiersz rozpoczyna się od kontrolera i rozwija kontrolera i zobaczysz jednego lub więcej stanowiskami lub kontenerach.  Rozwiń pod i kontener grupowane pod Pokaż ostatni wiersz.  

W poniższej tabeli opisano informacje podane podczas wyświetlania kontrolerów.

| Kolumna | Opis | 
|--------|-------------|
| Name (Nazwa) | Nazwa kontrolera|
| Stan | Stan kontenery po ukończeniu uruchomione o stanie, takie jak *zwolniony*, ** *zatrzymane*, lub *Paused*. Jeśli kontener jest uruchomiona, ale stan był niepoprawnie przedstawione lub nie została pobrana przez agenta nie odpowiedział ponad 30 minut, stan będzie *nieznany*. |
| % ŚR. | Przedstawia średnią średni procent każdej jednostki dla wybranego metryki. |
| ŚREDNIA | Przedstawia średnią millicore lub pamięci wydajność Procesora kontenera.  Średnia wartość jest podawana z Procesora/pamięci limit ustawiony dla pod. |
| Containers | Całkowita liczba kontenery pod lub kontrolera. |
| Ponowne uruchomienie | Rzutowanie liczby ponowne uruchomienie z kontenerów. |
| Czas pracy | Reprezentuje czas od momentu uruchomienia kontenera. |
| Zasobnik | Tylko dla kontenerów. Przedstawia on, które pods go znajdującej się. |
| Węzeł | Tylko w przypadku kontenerów i stanowiskami. Przedstawia on kontrolera, który jest znajdującej się. | 
| Trend AVG % | Trend wykres słupkowy przedstawiający średni procent metryki kontenera. |

Selektor, wybrać **kontenery**.<br><br> ![Wybierz kontenery widoku](./media/monitoring-container-health/container-performance-and-health-view-09.png)

W tym miejscu możemy sprawdzić kondycję wydajności kontenerów.<br><br> ![Widok wydajności kontrolerów < nazwa >](./media/monitoring-container-health/container-performance-and-health-view-06.png)

W poniższej tabeli opisano informacje podane podczas wyświetlania kontenerów.

| Kolumna | Opis | 
|--------|-------------|
| Name (Nazwa) | Nazwa kontrolera|
| Stan | Zbiorczy stan kontenerów, jeśli istnieje. |
| % ŚR. | Przedstawia średnią średni procent każdej jednostki dla wybranego metryki. |
| ŚREDNIA | Przedstawia średnią millicore lub pamięci wydajność Procesora kontenera. Średnia wartość jest podawana z Procesora/pamięci limit ustawiony dla pod. |
| Containers | Całkowita liczba kontenerów dla kontrolera.|
| Ponowne uruchomienie | Reprezentuje czas od momentu uruchomienia kontenera. |
| Czas pracy | Reprezentuje czas, ponieważ kontener został uruchomiony lub ponownego uruchomienia. |
| Zasobnik | Informacje, w którym znajduje się pod. |
| Węzeł |  Węzeł, w którym znajduje się kontener.  | 
| Trend AVG % | Trend wykres słupkowy przedstawiający średni procent metryki kontenera. |

## <a name="container-data-collection-details"></a>Szczegóły pobierania danych kontenera
Kontener kondycji służy do zbierania różnych metryki i dziennika danych z hostów kontenera i kontenery. Dane są zbierane co trzy minuty.

### <a name="container-records"></a>Rejestruje kontenera

W poniższej tabeli przedstawiono przykłady rekordów zebrane przez kontener kondycji i typy danych, które są wyświetlane w wynikach wyszukiwania dziennika.

| Typ danych | Typ danych w dzienniku wyszukiwania | Pola |
| --- | --- | --- |
| Wydajność dla hostów i kontenerów | `Perf` | Komputer, nazwa obiektu, CounterName &#40;czas procesora (%), dysk odczytuje MB, dysku zapisuje MB, użycie pamięć (MB), sieci odbieranie bajtów, sieci wysyłania w bajtach, procesor s użycia, sieć&#41;, równowartości, TimeGenerated, Ścieżka_licznika, SourceSystem |
| Kontener magazynu | `ContainerInventory` | TimeGenerated, komputera, nazwę kontenera, ContainerHostname, obraz, ImageTag, ContainerState, ExitCode, EnvironmentVar, polecenia, CreatedTime, StartedTime, FinishedTime, SourceSystem, identyfikatora kontenera, ImageID |
| Kontener magazynu obrazu | `ContainerImageInventory` | TimeGenerated, komputer, obraz, ImageTag, ImageSize, VirtualSize, działa wstrzymana, zatrzymana, nie powiodło się, SourceSystem, ImageID, TotalContainer |
| Kontener dziennika | `ContainerLog` | TimeGenerated, komputer, identyfikator obrazu, nazwy kontenera, LogEntrySource, LogEntry, SourceSystem, identyfikatora kontenera |
| Dziennik usługi kontenera | `ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |
| Kontener węzła magazynu | `ContainerNodeInventory_CL`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Proces kontenera | `ContainerProcess_CL` | TimeGenerated, Computer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Spis stanowiskami w klastrze Kubernetes | `KubePodInventory` | TimeGenerated, komputer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus, Identyfikatora kontenera, ContainerName, nazwa, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Magazynu węzłami częścią klastra Kubernetes | `KubeNodeInventory` | TimeGenerated, komputera, ClusterName, ClusterId, LastTransitionTimeReady, etykiet, stan, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Zdarzenia Kubernetes | `KubeEvents_CL` | TimeGenerated, komputer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, wiadomości, SourceSystem | 
| Usługi w klastrze Kubernetes | `KubeServices_CL` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Metryki wydajności dla węzłów częścią klastra Kubernetes | Wydajności &#124; gdzie ObjectName == "K8SNode" | cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, networkRxBytes, networkTxBytes, restartTimeEpoch, networkRxBytesPerSec, networkTxBytesPerSec, cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes | 
| Metryki wydajności dla kontenerów częścią klastra Kubernetes | Wydajności &#124; gdzie ObjectName == "K8SContainer" | cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, restartTimeEpoch, cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryLimitBytes | 

## <a name="search-logs-to-analyze-data"></a>Dzienniki wyszukiwania do analizowania danych
Analiza dzienników ułatwiają obserwować trendy, diagnozowanie wąskich gardeł, prognozy lub korelowanie danych, które mogą pomóc ustalić, czy bieżąca konfiguracja klastra działa optymalnie.  Wstępnie zdefiniowane dziennik wyszukiwania podano aby od razu rozpocząć korzystanie z lub dostosować w celu zwracania informacji w taki sposób, który ma. 

W obszarze roboczym można wykonać analizy interakcyjnej danych, wybierając **znajdują się w dzienniku** opcja jest dostępna w prawej po rozwinięciu kontenera.  **Zaloguj się wyszukiwania** zostanie wyświetlona strona prawo powyżej strony był w portalu.<br><br> ![Analizowanie danych analizy dzienników](./media/monitoring-container-health/container-performance-and-health-view-logs-01.png)   

Dane wyjściowe dzienników kontenera przekazywane do analizy dzienników są STDOUT i STDERR. Ponieważ Azure Kubernetes zarządzanych (AKS) jest monitorowanie kondycji kontenera, Kube systemu nie są zbierane dzisiaj z powodu dużej ilości danych wygenerowanych.     

### <a name="example-log-search-queries"></a>Przykładowe zapytania wyszukiwania dziennika
Często jest przydatne do tworzenia zapytań w programie przykład lub dwóch, a następnie modyfikując je zgodnie z własnymi wymaganiami. Możesz eksperymentować z następujące przykładowe zapytania do tworzenia bardziej zaawansowanych zapytań.

| Zapytanie | Opis | 
|-------|-------------|
| ContainerInventory<br> &#124;Projekt komputera, nazwa, obraz, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124;Renderowanie tabeli | Lista cyklu życia kontenera wszystkich informacji| 
| KubeEvents_CL<br> &#124;gdzie not(isempty(Namespace_s))<br> &#124;Sortuj według TimeGenerated desc<br> &#124;Renderowanie tabeli | Zdarzenia Kubernetes|
| ContainerImageInventory<br> &#124;Podsumuj AggregatedValue = count() przez obraz, ImageTag, uruchamianie | Obraz magazynu | 
| **Zaawansowane analizy wybierz wykresy liniowe**:<br> Wydajności<br> &#124;Gdzie ObjectName == i CounterName "Kontener" == "% czasu procesora"<br> &#124;Podsumuj AvgCPUPercent = avg(CounterValue) przez bin (TimeGenerated, 30m), InstanceName | Kontener Procesora | 
| **Zaawansowane analizy wybierz wykresy liniowe**:<br> Wydajności &#124; gdzie ObjectName == i CounterName "Kontener" == "Pamięć użycia (MB)"<br> &#124;Podsumuj AvgUsedMemory = avg(CounterValue) przez bin (TimeGenerated, 30m), InstanceName | Kontener pamięci |

## <a name="how-to-stop-monitoring-with-container-health"></a>Jak zatrzymać monitorowanie z kontenera kondycji
Po włączeniu monitorowania z kontenera AKS okażą się nie chcą, aby ją monitorować, możesz *zrezygnować* za pomocą podanego szablonów usługi Azure Resource Manager za pomocą polecenia cmdlet programu PowerShell  **Nowy AzureRmResourceGroupDeployment** lub interfejsu wiersza polecenia platformy Azure.  Jeden szablon JSON Określa konfigurację do *zrezygnować* i szablonie JSON zawiera wartości parametrów, które można skonfigurować w celu określenia AKS grupy zasobów klastra identyfikator i zasobów klastra jest wdrażana w.  Jeśli nie znasz pojęcia związane z wdrażaniem zasobów przy użyciu programu PowerShell przy użyciu szablonu z, zobacz [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) lub interfejsu wiersza polecenia Azure, zobacz [wdrożenie zasobów z Szablony Menedżera zasobów i interfejsu wiersza polecenia Azure](../azure-resource-manager/resource-group-template-deploy-cli.md).

Jeśli wybrano opcję Użyj interfejsu wiersza polecenia Azure, należy najpierw zainstalować i używać interfejsu wiersza polecenia lokalnie.  Jest to wymagane używasz interfejsu wiersza polecenia Azure w wersji 2.0.27 lub nowszej. Uruchom `az --version` do identyfikowania wersji. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-and-execute-template"></a>Tworzenie i wykonywanie szablonu

1. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster resource id"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": {
                "logAnalyticsWorkspaceResourceID": null
              }
            }
           }
         }
       }
      ]
    }
    ```

2. Zapisz ten plik jako **OptOutTemplate.json** do folderu lokalnego.
3. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "aksResourceId": {
         "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
      },
      "aksResourceLocation": {
        "value": "<aksClusterRegion>"
        }
      }
    }
    ```

4. Edytuj wartość **aksResourceId** i **aksResourceLocation** wartościami AKS klastra, który można znaleźć na **właściwości** strony dla wybranego klastra.<br><br> ![Strona właściwości kontenera](./media/monitoring-container-health/container-properties-page.png)<br>

    Podczas **właściwości** pozycję także skopiować **identyfikator zasobu obszaru roboczego**.  Ta wartość jest wymagana, jeśli zdecydujesz, że chcesz usunąć obszar roboczy analizy dzienników później, który nie jest wykonywane w ramach tego procesu.  

5. Zapisz ten plik jako **OptOutParam.json** do folderu lokalnego.
6. Wszystko jest teraz gotowe do wdrożenia tego szablonu. 

    * Aby użyć następujących poleceń programu PowerShell z folderu zawierającego szablon:

        ```powershell
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
        New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
        ```

        Zmiana konfiguracji może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat o podobne do poniższych opcji obejmujących wynik:

        ```powershell
        ProvisioningState       : Succeeded
        ```

    * Aby uruchomić następujące polecenie z wiersza polecenia platformy Azure w systemie Linux:

        ```azurecli
        az login   
        az account set --subscription "Subscription Name" 
        az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
        ```

        Zmiana konfiguracji może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat o podobne do poniższych opcji obejmujących wynik:

        ```azurecli
        ProvisioningState       : Succeeded
        ```

Jeśli utworzono obszar roboczy tylko do obsługi monitorowania klastra i nie jest już potrzebne, należy ręcznie je usunąć. Jeśli nie masz doświadczenia w obsłudze jak usunąć obszar roboczy, zobacz [usunąć obszarem roboczym usługi Analiza dzienników Azure przy użyciu portalu Azure](../log-analytics/log-analytics-manage-del-workspace.md).  Nie zapomnij o **identyfikator zasobu obszaru roboczego** możemy skopiowany wcześniej w kroku 4, który należy zacząć.  

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Ta sekcja zawiera informacje ułatwiające rozwiązywanie problemów z kontenera kondycji.

Jeśli kondycja kontenera została pomyślnie włączona i skonfigurowana, ale są nie zobaczysz informacje o stanie lub powoduje analizy dzienników przy wyszukiwaniu dziennika, można wykonać następujące kroki, aby pomóc w zdiagnozowaniu problemu.   

1. Sprawdź stan agenta, uruchamiając następujące polecenie: `kubectl get ds omsagent --namespace=kube-system`

    Dane wyjściowe powinny przypominać następujące wskazujący, które agent jest uruchomiony na wszystkich węzłów w klastrze.  Na przykład ten klaster zawiera dwa węzły i należy spodziewać się wartości równej liczby węzłów.  

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. Sprawdź stan pod, aby sprawdzić, czy jest uruchomiona lub nie, uruchamiając następujące polecenie: `kubectl get pods --namespace=kube-system`

    Dane wyjściowe powinny przypominać następujące — z stan *systemem* dla omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

3. Sprawdź dziennik agenta. Po wdrożeniu agenta konteneryzowanych pobiera uruchamia szybkie sprawdzenie przez uruchomienie poleceń OMI i jest wyświetlana wersja agenta i dostawcy Docker. Aby sprawdzić, czy agent został został załadowany z pomyślnie, uruchom następujące polecenie: `kubectl logs omsagent-484hw --namespace=kube-system`

    Stan powinien wyglądać w następujący sposób:

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="next-steps"></a>Kolejne kroki

[Wyszukaj dzienniki](../log-analytics/log-analytics-log-search.md) Aby wyświetlić szczegółowe kontenera kondycji i aplikacji informacje o wydajności.  