---
title: Monitorowanie kondycji usługi Azure Kubernetes Service (AKS) (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak łatwo można przejrzeć wydajności kontenera AKS do szybkiego zrozumienia wykorzystania hostowanym środowiskiem Kubernetes.
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
ms.date: 07/16/2018
ms.author: magoedte
ms.openlocfilehash: 1fd5ac0f9994a4dbf4365c21ac4f31ba0eccbb15
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069155"
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>Monitorowanie kondycji kontenera usługi Azure Kubernetes Service (AKS) (wersja zapoznawcza)

W tym artykule opisano sposób konfigurowania i używania usługi Azure Monitor kondycji kontenera w celu monitorowania wydajności obciążeń wdrożonych do rozwiązania Kubernetes środowisk hostowanych w usłudze Azure Kubernetes Service (AKS).  Monitorowanie klastra i kontenerów usługi Kubernetes ma krytyczne znaczenie, szczególnie w przypadku korzystania z klastra produkcyjnego o dużej skali i z wieloma aplikacjami.

Kondycji kontenera zapewnia możliwość przez zbieranie pamięci i procesora metryki kontrolery, węzły i dostępnych w usłudze Kubernetes za pomocą interfejsu API metryki kontenerów do monitorowania wydajności.  Po włączeniu kondycji kontenera te metryki są automatycznie zbierane przy użyciu konteneryzowanych wersję agenta pakietu OMS dla systemu Linux i przechowywane w swojej [usługi Log Analytics](../log-analytics/log-analytics-overview.md) obszaru roboczego.  Wstępnie zdefiniowanych widoków, dostępnych Pokaż przechowywanych obciążeń kontenerów i co ma wpływ na wydajności kondycji klastra usługi Kubernetes, aby umożliwić poznanie:  

* Jakie kontenery są uruchomione na węźle i ich średnie wykorzystanie procesora i pamięci do identyfikowania wąskich gardeł zasobów
* Określenie, której kontenera znajduje się w kontrolerze i/lub zasobników, aby wyświetlić ogólną wydajność dla kontrolera lub zasobników 
* Sprawdź wykorzystanie zasobów obciążenia uruchomione na hoście niezwiązanych ze sobą do standardowych procesów obsługi zasobnik
* Zrozumienie zachowania klastra pod obciążeniem średnią i największym, aby pomóc w zidentyfikowaniu potrzeby związane z pojemnością i określenia maksymalnego obciążenia, który może kontynuować działanie 

Jeśli interesuje Cię monitorowania i zarządzania platformy Docker i Windows hostach kontenerów, Wyświetl konfigurację, inspekcji i wykorzystanie zasobów, zobacz [rozwiązanie do monitorowania kontenerów](../log-analytics/log-analytics-containers.md).

## <a name="requirements"></a>Wymagania 
Przed rozpoczęciem, przejrzyj następujące informacje, aby umożliwić poznanie obsługiwanych wymagań wstępnych.

- Nowego lub istniejącego klastra AKS
- Konteneryzowane agenta pakietu OMS dla systemu Linux w wersji microsoft / oms:ciprod04202018 i nowszych. Numer wersji jest reprezentowany przez wartość typu date zgodnie z formatem — *mmddyyyy*.  Jest on instalowany automatycznie zestawu dokumentacji podczas dołączania kondycji kontenera.  
- Obszar roboczy usługi Log Analytics.  Można tworzyć, gdy monitorowanie nowego klastra AKS, lub możesz je utworzyć za pomocą [usługi Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), lub z [witryny Azure portal](../log-analytics/log-analytics-quick-create-workspace.md).
- Członek roli Współautor usługi Log Analytics, aby włączyć monitorowanie kontenerów.  Aby uzyskać więcej informacji na temat kontrolowania dostępu do obszaru roboczego usługi Log Analytics, zobacz [możesz zarządzać obszarami roboczymi](../log-analytics/log-analytics-manage-access.md).

## <a name="components"></a>Składniki 

Ta funkcja opiera się na konteneryzowanych agenta pakietu OMS dla systemu Linux na zbieranie danych zdarzeń i wydajności ze wszystkich węzłów w klastrze.  Agent automatycznie wdrożeniu i zarejestrowaniu z określonym obszarem roboczym usługi Log Analytics, po włączeniu monitorowanie kontenerów. 

>[!NOTE] 
>Jeśli masz już wdrożone w klastrze AKS, zostanie włączone monitorowanie za pomocą podanego szablonu Azure Resource Manager, jak pokazano w dalszej części tego artykułu. Nie można użyć `kubectl` do uaktualnienia, Usuń, Wdróż ponownie lub wdrożyć agenta.  
>

## <a name="sign-in-to-azure-portal"></a>Zaloguj się w witrynie Azure Portal
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com). 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>Włącz monitorowanie kondycji kontenera dla nowego klastra
Aby umożliwić monitorowanie nowy klaster AKS podczas wdrażania w witrynie Azure portal.  Postępuj zgodnie z instrukcjami w artykule przewodnika Szybki Start [wdrażanie klastra usługi Azure Kubernetes Service (AKS)](../aks/kubernetes-walkthrough-portal.md).  Jeśli korzystasz z **monitorowanie** wybierz opcję **tak** opcji **Włącz monitorowanie** można włączyć, a następnie wybierz istniejącą lub Utwórz nowy obszar roboczy usługi Log Analytics.  

Po włączeniu monitorowania wszystkich zadań konfiguracji zostaną ukończone pomyślnie, można monitorować wydajność klastra z jednego z dwóch sposobów:

1. Bezpośrednio z poziomu klastra AKS, wybierając **kondycji** z okienka po lewej stronie.<br><br> 
2. Klikając **monitorowania kondycji kontenera** kafelka na stronie klastra AKS dla wybranego klastra.  W usłudze Azure Monitor, wybierz **kondycji** z okienka po lewej stronie.  

![Opcje, aby wybrać kondycji kontenera w usłudze AKS](./media/monitoring-container-health/container-performance-and-health-select-01.png)

Po włączeniu monitorowania może potrwać około 15 minut, zanim będzie możliwe wyświetlić dane operacyjne dla klastra.  

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>Włącz monitorowanie kondycji kontenera dla istniejących zarządzane klastry
Aby umożliwić monitorowanie klastra usługi AKS już wdrożone z witryny Azure portal lub za pomocą podanego szablonu Azure Resource Manager przy użyciu polecenia cmdlet programu PowerShell **New-AzureRmResourceGroupDeployment** lub wiersza polecenia platformy Azure.  


### <a name="enable-from-azure-portal"></a>Korzystanie z witryny Azure portal
Wykonaj poniższe kroki, aby włączyć monitorowanie kontenera usługi AKS w witrynie Azure portal.

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**. Na liście zasobów wpisz **kontenery**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **usługi Kubernetes**.<br><br> ![Azure Portal](./media/monitoring-container-health/azure-portal-01.png)<br><br>  
2. Na liście kontenerów Wybierz kontener.
3. Na stronie Przegląd kontenera wybierz **monitorowania kondycji kontenera** i **dołączenie do kondycji kontenera i dzienniki** zostanie wyświetlona strona.
4. Na **dołączenie do kondycji kontenera i dzienniki** strony, jeśli masz istniejące usługi Log Analytics obszaru roboczego w tej samej subskrypcji co klaster, wybierz ją z listy rozwijanej.  Listy preselects domyślnego obszaru roboczego i lokalizację kontenera w usłudze AKS jest wdrożona w ramach subskrypcji.<br><br> ![Włącz monitorowanie kondycji kontenera w usłudze AKS](./media/monitoring-container-health/container-health-enable-brownfield-02.png) 

>[!NOTE]
>Jeśli chcesz utworzyć nowy obszar roboczy usługi Log Analytics do przechowywania danych monitorowania z klastra, wykonaj kroki opisane w [Cretae obszar roboczy usługi Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md) i należy utworzyć obszar roboczy w tą samą subskrypcją, w której kontenerów AKS wdrażane.  
>
 
Po włączeniu monitorowania może potrwać około 15 minut, zanim będzie możliwe wyświetlić dane operacyjne dla klastra. 

### <a name="enable-using-azure-resource-manager-template"></a>Włącz przy użyciu szablonu usługi Azure Resource Manager
Ta metoda obejmuje dwa szablony JSON, jeden szablon Określa konfigurację, aby włączyć monitorowanie i szablon JSON zawiera wartości parametrów, które można skonfigurować w celu określ następujące ustawienia:

* Identyfikator zasobu kontenerów AKS 
* Grupa zasobów klastra jest wdrożony w 
* Obszar roboczy usługi log Analytics i region, aby utworzyć obszar roboczy w 

Obszar roboczy usługi Log Analytics ma zostać utworzone ręcznie.  Aby utworzyć obszar roboczy, możesz skonfigurować jedną za pośrednictwem [usługi Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), z [witryny Azure portal](../log-analytics/log-analytics-quick-create-workspace.md).

Jeśli nie znasz pojęć dotyczących wdrażania zasobów przy użyciu szablonu przy użyciu programu PowerShell, zobacz [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)lub wiersza polecenia platformy Azure, zobacz temat [wdrażanie zasobów przy użyciu Szablony usługi Resource Manager i interfejsu wiersza polecenia Azure](../azure-resource-manager/resource-group-template-deploy-cli.md).

Jeśli została wybrana opcja używania wiersza polecenia platformy Azure, należy najpierw zainstalować i korzystać z interfejsu wiersza polecenia lokalnie.  Jest to wymagane czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Uruchom `az --version` do identyfikowania wersji. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-template"></a>Tworzenie i wykonywanie szablonu

1. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "aksResourceId": {
        "type": "string",
        "metadata": {
           "description": "AKS Cluster Resource ID"
           }
    },
    "aksResourceLocation": {
    "type": "string",
     "metadata": {
        "description": "Location of the AKS resource e.g. \"East US\""
       }
    },
    "workspaceResourceId": {
      "type": "string",
      "metadata": {
         "description": "Azure Monitor Log Analytics Resource ID"
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
            "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
          }
         }
       }
      }
     },
    {
        "type": "Microsoft.Resources/deployments",
        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
        "apiVersion": "2017-05-10",
        "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
        "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
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
                        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                        "properties": {
                            "workspaceResourceId": "[parameters('workspaceResourceId')]"
                        },
                        "plan": {
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
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
       "$schema": "https://schema.management.azure.com/  schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscroptiopnId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "East US"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       },
       "workspaceRegion": {
         "value": "eastus"
       }
     }
    }
    ```

4. Edytuj wartość dla **aksResourceId**, **aksResourceLocation** z wartościami, które można znaleźć na **Omówienie usługi AKS** strony dla klastra usługi AKS.  Wartość **workspaceResourceId** jest pełny identyfikator zasobu obszaru roboczego usługi Log Analytics, która zawiera nazwę obszaru roboczego.  Również określić lokalizację, obszar roboczy jest w dla **workspaceRegion**.    
5. Zapisz ten plik jako **existingClusterParam.json** do folderu lokalnego.
6. Wszystko jest teraz gotowe do wdrożenia tego szablonu. 

    * Użyj następujących poleceń programu PowerShell z poziomu folderu zawierającego szablon:

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        Zmiana konfiguracji może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat podobny do poniższego, który zawiera wynik:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Aby uruchomić następujące polecenie z wiersza polecenia platformy Azure w systemie Linux:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        Zmiana konfiguracji może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat podobny do poniższego, który zawiera wynik:

        ```azurecli
        provisioningState       : Succeeded
        ```
Po włączeniu monitorowania może potrwać około 15 minut, zanim będzie możliwe wyświetlić dane operacyjne dla klastra.  

## <a name="verify-agent-and-solution-deployment"></a>Sprawdź wdrożenie agenta i rozwiązania
Za pomocą wersji agenta *06072018* i wyższej, jesteś w stanie sprawdzić, czy agent i rozwiązania zostały pomyślnie wdrożone.  W starszych wersjach programu agent tylko można zweryfikować wdrożenie agenta.

### <a name="agent-version-06072018-and-higher"></a>Wersja agenta 06072018 lub nowszy
Uruchom następujące polecenie, aby sprawdzić, czy agent zostanie wdrożony pomyślnie.   

```
kubectl get ds omsagent --namespace=kube-system
```

Wynik powinien przypominać następujące wskazujący, który został wdrożony prawidłowo:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Aby zweryfikować wdrożenie rozwiązania, uruchom następujące polecenie:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Wynik powinien przypominać następujące wskazujący, który został wdrożony prawidłowo:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Agent w wersji wcześniejszej niż 06072018

Aby sprawdzić wersję agenta pakietu OMS wydanych przed *06072018* jest wdrażany prawidłowo, uruchom następujące polecenie:  

```
kubectl get ds omsagent --namespace=kube-system
```

Wynik powinien przypominać następujące wskazujący, który został wdrożony prawidłowo:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-performance-utilization"></a>Widok wydajności użycia
Po otwarciu kondycji kontenera, strony natychmiast przedstawia informacje o wykorzystaniu wydajność całego klastra.  Wyświetlanie informacji na temat klastra usługi AKS jest podzielony na cztery perspektywy:

- Klaster
- Węzły 
- Kontrolery  
- Containers

Na karcie klastra wykresy wydajności liniowe pokazują kluczowe metryki wydajności klastra.  

![Przykładowe wykresy wydajności na karcie klastra](./media/monitoring-container-health/container-health-cluster-perfview.png)

Poniżej przedstawiono podział prezentowane metryki wydajności:

- Procesor CPU % wykorzystania — tego wykresu reprezentuje węzeł perspektywy zagregowane wykorzystanie procesora CPU dla całego klastra.  Wyniki zakresu czasu można filtrować, wybierając *Avg*, *Min*, *Max*, *50.*, *90*, i *95* z selektora percentyle nad wykresem, albo indywidualnie lub w połączeniu. 
- % Wykorzystania pamięci węzła — tego wykresu reprezentuje perspektywy zagregowane wykorzystanie pamięci dla całego klastra.  Wyniki zakresu czasu można filtrować, wybierając *Avg*, *Min*, *Max*, *50.*, *90*, i *95* z selektora percentyle nad wykresem, albo indywidualnie lub w połączeniu. 
- Liczba węzłów — ten wykres przedstawia liczbę węzłów i stan z zakresu od Kubernetes.  Stan reprezentowany węzły klastra są *wszystkich*, *gotowe*, i *niegotowe* i może być filtrowana indywidualnie lub w połączeniu z selektora powyżej wykresu.    
- Liczba zasobników działań — ten wykres przedstawia stan i Liczba zasobników z zakresu od rozwiązania Kubernetes.  Stan zasobników reprezentowane to *wszystkich*, *oczekujące*, *systemem*, i *nieznany* i może być filtrowana indywidualnie lub w połączeniu z Selektor powyżej wykresu.  

Przełączenie na karcie węzłów, hierarchia wiersz poniżej model obiektów usługi Kubernetes, począwszy od węzła w klastrze.  Rozwiń węzeł i zostanie wyświetlona co najmniej jeden zasobników, uruchomione w węźle, a jeśli istnieje więcej niż jednego kontenera grupowania zasobnik, są wyświetlane jako ostatni wiersz w hierarchii. Jesteś w stanie zobaczyć, jak wiele powiązanych obciążeniach-pod są uruchomione na hoście, w przypadku, gdy host ma procesor lub dużego wykorzystania pamięci.

![Przykładowa hierarchia Kubernetes węzeł w widoku wydajności](./media/monitoring-container-health/container-health-nodes-view.png)

Można wybrać kontrolery lub kontenerów w górnej części strony i sprawdź stan i użycia zasobów dla tych obiektów.  Użyj pola listy rozwijanej w górnej części ekranu, aby filtrować według przestrzeni nazw, usługi i języka node. Jeśli zamiast tego chcesz przejrzeć wykorzystanie pamięci, od **metryki** listy rozwijanej wybierz **RSS pamięci** lub **zestaw roboczy pamięci**.  **Pamięć RSS** jest obsługiwana tylko w przypadku rozwiązania Kubernetes w wersji 1.8 i nowszych. W przeciwnym razie zostaną wyświetlone wartości dla **MIN %** wyświetlane jako *NaN %*, która jest wartością typu dane liczbowe, reprezentującą wartość niezdefiniowana lub wyniku. 

![Widok wydajności węzłów kontenerów](./media/monitoring-container-health/container-health-node-metric-dropdown.png)

Domyślnie dane dotyczące wydajności opiera się na ostatnie 6 godzin, ale można zmienić okno z **zakres czasu** odnaleźć listy rozwijanej w prawym górnym rogu strony. W tej chwili strony nie automatycznego odświeżania, więc musisz ręcznie odświeżyć je. Można również filtrować wyniki do zakresu, wybierając *Avg*, *Min*, *Max*, *50.*, *90*, i *95* z selektora percentyl. 

![Wybór percentyl filtrowanie danych](./media/monitoring-container-health/container-health-metric-percentile-filter.png)

W poniższym przykładzie można zauważyć dla węzła *3977305-aks-nodepool*, wartość **kontenery** wynosi 5, która stanowi pakiet zbiorczy całkowita liczba kontenerów wdrożonych.

![Pakiet zbiorczy kontenerów na przykład węzeł](./media/monitoring-container-health/container-health-nodes-containerstotal.png)

Ułatwia ona szybkie ustalenie, czy nie ma właściwej równowagi kontenerów między węzłami w klastrze.  

W poniższej tabeli opisano informacje znajdujące się po wyświetleniu węzłów.

| Kolumna | Opis | 
|--------|-------------|
| Name (Nazwa) | Nazwa hosta |
| Stan | Widok stanu węzła usługi Kubernetes |
| ŚREDNI %, MIN %, MAX %, 50%, 90% | Średni procent węzła, w oparciu o procentowy podczas tego okresu czasu wybrany. |
| AVG, MIN, MAX, 50., 90 | Węzły średnia wartość rzeczywista oparciu o procentowy podczas tego okresu czasu wybrany.  Średnia wartość jest mierzony od limitu Procesora/pamięci dla węzła; dla zasobników i kontenerów to wartość średnia zgłaszaną przez hosta. |
| Containers | Liczba kontenerów. |
| Czas pracy | Reprezentuje czas, ponieważ węzeł uruchomiony lub został ponownie uruchomiony. |
| Kontrolery | Tylko w przypadku kontenerów i zasobników. Przedstawia on kontrolera, który jest znajdującej się. Nie wszystkie zasobników będą w kontrolerze, więc niektóre z nich mogą być wyświetlane n/d. | 
| Trend AVG %, MIN %, MAX %, 50%, 90% | Trend wykres słupkowy prezentowanie percentyla metryk % kontrolera. |


W obszarze wyboru wybierz **kontrolerów**.

![Wybierz kontrolerów widoku](./media/monitoring-container-health/container-health-controllers-tab.png)

Tutaj można zobaczyć kondycję wydajności kontrolerów.

![Widok wydajności kontrolerów < nazwa >](./media/monitoring-container-health/container-health-controllers-view.png)

Hierarchia wiersz rozpoczyna się od kontrolera i rozwija kontrolera i zostanie wyświetlony jeden lub co najmniej jeden kontener.  Rozwiń zasobnik i ostatni wiersz Pokaż kontener grupowania zasobnik.  

W poniższej tabeli opisano informacje znajdujące się po wyświetleniu kontrolerów.

| Kolumna | Opis | 
|--------|-------------|
| Name (Nazwa) | Nazwa kontrolera|
| Stan | Pakiet zbiorczy stan kontenery po ukończeniu uruchomione o stanie, takie jak *OK*, *zwolniony*, *niepowodzenie* *zatrzymane*, lub  *Wstrzymano*. Jeśli kontener jest uruchomiony, ale stan był albo nie zostało prawidłowo prezentowane lub nie została pobrana przez agenta i nie odpowiedział ponad 30 minut, stan jest *nieznany*. W poniższej tabeli znajdują się dodatkowe szczegóły ikonę stanu.|
| ŚREDNI %, MIN %, MAX %, 50%, 90% | Przedstawia średnią średni procent każdej jednostki dla wybranej metryki i percentyl. |
| AVG, MIN, MAX, 50., 90  | Zbiorczy średni Procesora pamięci lub millicore wydajności kontenera dla wybranych percentyl.  Średnia wartość jest mierzony od limitu Procesora/pamięci dla zasobnik. |
| Containers | Łączna liczba kontenerów dla kontrolera lub zasobników. |
| Ponowne uruchomienie | Rzutowanie liczby ponowne uruchomienie z kontenerów. |
| Czas pracy | Reprezentuje czas od momentu uruchomienia kontenera. |
| Węzeł | Tylko w przypadku kontenerów i zasobników. Przedstawia on kontrolera, który jest znajdującej się. | 
| Trend AVG %, MIN %, MAX %, 50%, 90%| Trend wykresu słupkowego, reprezentujący metryki percentyl kontrolera. |

W polu Stan wskazują stan online kontenerów:
 
| Ikona | Stan | 
|--------|-------------|
| ![Gotowe uruchomionej ikona stanu](./media/monitoring-container-health/container-health-ready-icon.png) | Uruchamianie (gotowe)|
| ![Ikona stanu oczekuje lub wstrzymana](./media/monitoring-container-health/container-health-waiting-icon.png) | Oczekuje lub wstrzymana|
| ![Ikona stanu uruchomiona po raz ostatni informowało](./media/monitoring-container-health/container-health-grey-icon.png) | Ostatnie zgłaszane uruchomiona, ale nie odpowiedział w ponad 30 minut|
| ![Ikona stanu zakończone](./media/monitoring-container-health/container-health-green-icon.png) | Pomyślnie zatrzymano lub nie można zatrzymać|

Ikona stanu pokazuje liczbę oparte na zasobnik udostępnia. Pokazuje niższa dwóch stanów i po umieszczeniu stanu przedstawia zbiorcze informacje o stanie ze wszystkich zasobników w kontenerze.  Jeśli nie ma stanu gotowości, zostaną wyświetlone wartości stanu **(0)**.  

W obszarze wyboru wybierz **kontenery**.

![Wybierz kontenery widoku](./media/monitoring-container-health/container-health-containers-tab.png)

Tutaj widzimy kondycji wydajności kontenerów.

![Widok wydajności kontrolerów < nazwa >](./media/monitoring-container-health/container-health-containers-view.png)

W poniższej tabeli opisano informacje znajdujące się po wyświetleniu kontenerów.

| Kolumna | Opis | 
|--------|-------------|
| Name (Nazwa) | Nazwa kontrolera|
| Stan | Stan kontenerów, jeśli istnieje. W poniższej tabeli znajdują się dodatkowe szczegóły ikonę stanu.|
| ŚREDNI %, MIN %, MAX %, 50%, 90% | Przedstawia średnią średni procent każdej jednostki dla wybranej metryki i percentyl. |
| AVG, MIN, MAX, 50., 90  | Zbiorczy średni Procesora pamięci lub millicore wydajności kontenera dla wybranych percentyl.  Średnia wartość jest mierzony od limitu Procesora/pamięci dla zasobnik. |
| Zasobnik | Kontener, w którym znajduje się pod.| 
| Węzeł |  Węzeł, w którym znajduje się kontener. | 
| Ponowne uruchomienie | Reprezentuje czas od momentu uruchomienia kontenera. |
| Czas pracy | Reprezentuje czas, ponieważ kontener został uruchomiony lub ponownie uruchomiony. |
| Trend AVG %, MIN %, MAX %, 50%, 90% | Trend wykresu słupkowego, reprezentujący średni procent metryki kontenera. |

W polu Stan wskazują stan online zasobników:
 
| Ikona | Stan | 
|--------|-------------|
| ![Gotowe uruchomionej ikona stanu](./media/monitoring-container-health/container-health-ready-icon.png) | Uruchamianie (gotowe)|
| ![Ikona stanu oczekuje lub wstrzymana](./media/monitoring-container-health/container-health-waiting-icon.png) | Oczekuje lub wstrzymana|
| ![Ikona stanu uruchomiona po raz ostatni informowało](./media/monitoring-container-health/container-health-grey-icon.png) | Ostatnie zgłaszane uruchomiona, ale nie odpowiedział w ponad 30 minut|
| ![Ikona stanu zakończone](./media/monitoring-container-health/container-health-terminated-icon.png) | Pomyślnie zatrzymano lub nie można zatrzymać|
| ![Ikona stanu nie powiodło się](./media/monitoring-container-health/container-health-failed-icon.png) | Stan niepowodzenia |

## <a name="container-data-collection-details"></a>Szczegóły zbierania danych kontenera
Kondycji kontenera zbiera różne dane metryk i dzienników wydajności hostach kontenerów i kontenery. Dane są gromadzone co trzy minuty.

### <a name="container-records"></a>Rekordów kontenera

W poniższej tabeli przedstawiono przykłady rekordów zbieranych przez kondycji kontenera i typy danych, które są wyświetlane w wynikach wyszukiwania w dzienniku.

| Typ danych | Typ danych podczas wyszukiwania dziennika | Pola |
| --- | --- | --- |
| Wydajność dla hostów i kontenerów | `Perf` | CounterName komputera, nazwa obiektu, &#40;czas procesora (%), dysk odczytuje MB, dysku zapisuje MB, MB użycia pamięci, sieci odbierania bajtów, sieci wysyłania w bajtach, procesor s użycia, sieć&#41;, CounterValue, TimeGenerated, Ścieżka_licznika, system źródłowy |
| Kontener magazynu | `ContainerInventory` | TimeGenerated, komputera, nazwa kontenera, ContainerHostname, obraz, ImageTag, ContainerState, ExitCode, EnvironmentVar, polecenia, wartością CreatedTime, StartedTime, FinishedTime, system źródłowy, ContainerID, ImageID |
| Spis obrazów kontenera | `ContainerImageInventory` | TimeGenerated, komputer, obraz, ImageTag, ImageSize, VirtualSize, uruchamianie, wstrzymana, zatrzymana, nie powiodło się, system źródłowy, ImageID, TotalContainer |
| Dziennik kontenera | `ContainerLog` | TimeGenerated, komputer, identyfikator obrazu, pola Nazwa kontenera LogEntrySource, LogEntry, system źródłowy, ContainerID |
| Dziennik usługi kontenera | `ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |
| Spis węzłów kontenerów | `ContainerNodeInventory_CL`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Proces kontenera | `ContainerProcess_CL` | TimeGenerated, Computer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Spis zasobników w klastrze Kubernetes | `KubePodInventory` | TimeGenerated, komputer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, element ControllerName, ContainerStatus, ContainerID, ContainerName, PodLabel, adresem IP zasobnika, Namespace, ClusterName, nazwa, PodStatus, system źródłowy |
| Spis węzłów będących częścią klastra Kubernetes | `KubeNodeInventory` | TimeGenerated, komputera, ClusterName, ClusterId, LastTransitionTimeReady, etykiety, stan, KubeletVersion, KubeProxyVersion, CreationTimeStamp, system źródłowy | 
| Zdarzenia Kubernetes | `KubeEvents_CL` | TimeGenerated, komputera, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, komunikat o błędzie, system źródłowy | 
| Usługi w klastrze Kubernetes | `KubeServices_CL` | TimeGenerated, ServiceName_s, SelectorLabels_s, ServiceType_s, ClusterId_s, ClusterIP_s, Namespace_s, ClusterName_s, system źródłowy | 
| Metryki wydajności dla części węzłów klastra Kubernetes | Perf &#124; gdzie ObjectName == "K8SNode" | CounterName komputera, nazwa obiektu, &#40;cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, networkRxBytes, networkTxBytes, restartTimeEpoch, networkRxBytesPerSec, networkTxBytesPerSec, cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes&#41;, CounterValue, TimeGenerated, Ścieżka_licznika, system źródłowy | 
| Metryki wydajności dla kontenerów częścią klastra Kubernetes | Perf &#124; gdzie ObjectName == "K8SContainer" | CounterName &#40;cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, restartTimeEpoch, cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryLimitBytes&#41;, CounterValue, TimeGenerated, Ścieżka_licznika, System źródłowy | 

## <a name="search-logs-to-analyze-data"></a>Dzienniki wyszukiwania do analizy danych
Usługa log Analytics może pomóc wyszukiwania trendach, diagnozowanie wąskich gardeł, prognozy lub korelowanie danych, które mogą pomóc ustalić, czy bieżąca konfiguracja klastra działa optymalnie.  Wstępnie zdefiniowane wyszukiwań w dziennikach znajdują się od razu rozpocząć korzystanie z lub dostosować w celu zwracania informacji w żądany sposób. 

W obszarze roboczym można wykonywać interakcyjne analizy danych, zaznaczając **Wyświetl dziennik** opcja jest dostępna na końcu po prawej stronie po rozwinięciu kontrolera lub kontenera.  **Wyszukiwania w dzienniku** prawo powyżej strony wcześniej w portalu zostanie wyświetlona strona.

![Analizowanie danych w usłudze Log Analytics](./media/monitoring-container-health/container-health-view-logs.png)   

Dane wyjściowe dzienników kontenera, które zostały przekazane do usługi Log Analytics to STDOUT i STDERR. Ponieważ usługa Azure Kubernetes zarządzanych (AKS) jest monitorowanie kondycji kontenera, systemu Kubernetes nie są zbierane już dziś z powodu dużej ilości danych wygenerowanych.     

### <a name="example-log-search-queries"></a>Przykład zapytania wyszukiwania w Dzienniku
Często jest to przydatne do tworzenia zapytań, począwszy od przykładem lub dwóch, a następnie modyfikować ją zgodnie z wymaganiami. Możesz eksperymentować z następujące przykładowe zapytania w celu ułatwienia tworzenia bardziej zaawansowanych zapytań.

| Zapytanie | Opis | 
|-------|-------------|
| ContainerInventory<br> &#124;Projekt komputera, nazwa, obraz, ImageTag, ContainerState, wartością CreatedTime, StartedTime, FinishedTime<br> &#124;Renderowanie tabeli | Listę wszystkich kontenerów informacje o cyklu życia| 
| KubeEvents_CL<br> &#124;gdzie not(isempty(Namespace_s))<br> &#124;Sortuj według malejącej TimeGenerated<br> &#124;Renderowanie tabeli | Zdarzenia Kubernetes|
| ContainerImageInventory<br> &#124;summarize AggregatedValue = count() by obrazu, ImageTag, działa | Spis obrazów | 
| **Advanced Analytics wybierz wykresów liniowych**:<br> Wydajności<br> &#124;Gdzie ObjectName == "Container" i CounterName == "% czasu procesora"<br> &#124;Podsumowanie AvgCPUPercent avg(CounterValue) przez bin (TimeGenerated, 30 min), InstanceName = | Procesora CPU kontenera | 
| **Advanced Analytics wybierz wykresów liniowych**:<br> Perf &#124; gdzie ObjectName == "Container" i CounterName == "MB użycia pamięci"<br> &#124;Podsumowanie AvgUsedMemory avg(CounterValue) przez bin (TimeGenerated, 30 min), InstanceName = | Pamięci kontenera |

## <a name="how-to-stop-monitoring-with-container-health"></a>Jak zatrzymać monitorowanie za pomocą programu health kontenera
Po włączeniu monitorowania kontenera AKS okażą się już nie chcesz go monitorować, możesz *zrezygnować* przy użyciu dostarczonych szablonów usługi Azure Resource Manager za pomocą polecenia cmdlet programu PowerShell  **Nowe AzureRmResourceGroupDeployment** lub wiersza polecenia platformy Azure.  Jeden szablon JSON Określa konfigurację *zrezygnować* i szablon JSON zawiera wartości parametrów, które można skonfigurować w celu określenia AKS grupę zasobów klastra identyfikator i zasobów w klastrze jest wdrożony.  Jeśli nie znasz pojęć dotyczących wdrażania zasobów przy użyciu szablonu przy użyciu programu PowerShell, zobacz [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) lub wiersza polecenia platformy Azure, zobacz temat [wdrażanie zasobów przy użyciu Szablony usługi Resource Manager i interfejsu wiersza polecenia Azure](../azure-resource-manager/resource-group-template-deploy-cli.md).

Jeśli została wybrana opcja używania wiersza polecenia platformy Azure, należy najpierw zainstalować i korzystać z interfejsu wiersza polecenia lokalnie.  Jest to wymagane czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Uruchom `az --version` do identyfikowania wersji. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

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
             "description": "AKS Cluster Resource ID"
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

4. Edytuj wartość dla **aksResourceId** i **aksResourceLocation** przy użyciu wartości klastra AKS, który można znaleźć na **właściwości** strona dla wybranego klastra.

    ![Strona właściwości kontenera](./media/monitoring-container-health/container-properties-page.png)

    Gdy jesteś w **właściwości** strony, również skopiować **identyfikator zasobu obszaru roboczego**.  Ta wartość jest wymagana, jeśli zdecydujesz, że chcesz usunąć obszar roboczy analizy dzienników później, który nie jest wykonywane jako część tego procesu.  

5. Zapisz ten plik jako **OptOutParam.json** do folderu lokalnego.
6. Wszystko jest teraz gotowe do wdrożenia tego szablonu. 

    * Aby użyć następujących poleceń programu PowerShell z folderu zawierającego szablon:

        ```powershell
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
        New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
        ```

        Zmiana konfiguracji może potrwać kilka minut. Po ukończeniu, jest zwracany komunikat podobny do poniższego, który zawiera wynik:

        ```powershell
        ProvisioningState       : Succeeded
        ```

    * Aby uruchomić następujące polecenie z wiersza polecenia platformy Azure w systemie Linux:

        ```azurecli
        az login   
        az account set --subscription "Subscription Name" 
        az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
        ```

        Zmiana konfiguracji może potrwać kilka minut. Po ukończeniu, jest zwracany komunikat podobny do poniższego, który zawiera wynik:

        ```azurecli
        ProvisioningState       : Succeeded
        ```

Jeśli obszar roboczy został utworzony tylko do obsługi monitorowania klastra i nie jest już potrzebny, należy ręcznie je usunąć. Jeśli nie znasz sposób usuwania obszaru roboczego, zobacz [usunąć obszar roboczy usługi Azure Log Analytics w witrynie Azure portal](../log-analytics/log-analytics-manage-del-workspace.md).  Nie zapomnij o **identyfikator zasobu obszaru roboczego** możemy skopiowany wcześniej w kroku 4, możesz zacząć potrzebna.  

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Ta sekcja zawiera informacje ułatwiające rozwiązywanie problemów z usługą kondycji kontenera.

Jeśli kondycji kontenera, została pomyślnie włączona i skonfigurowana, ale są nie widzisz, informacje o stanie lub wyniki w usłudze Log Analytics podczas wykonywania przeszukiwania dzienników, można wykonać poniższe kroki, aby pomóc w zdiagnozowaniu problemu.   

1. Sprawdź stan agenta, uruchamiając następujące polecenie: 

    `kubectl get ds omsagent --namespace=kube-system`

    Wynik powinien przypominać następujące wskazujący, który został wdrożony prawidłowo:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Sprawdź stan wdrożenia rozwiązania z wersją agenta *06072018* lub nowszej, uruchamiając następujące polecenie:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Wynik powinien przypominać następujące wskazujący, który został wdrożony prawidłowo:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Sprawdź stan zasobników, aby sprawdzić, czy jest uruchomiona lub nie przez uruchomienie następującego polecenia: `kubectl get pods --namespace=kube-system`

    Dane wyjściowe powinny wyglądać podobnie do poniższego ze stanem *systemem* dla omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Sprawdź dzienniki agenta. Gdy konteneryzowanych agent zostanie wdrożona, jest ono wykonywane szybkie sprawdzanie przez uruchomienie OMI polecenia i wyświetla wersję agenta i 
5.  Dostawca. Aby sprawdzić, czy agent zakończy się powodzeniem dołączone, uruchom następujące polecenie: `kubectl logs omsagent-484hw --namespace=kube-system`

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

[Przeszukiwanie dzienników](../log-analytics/log-analytics-log-search.md) Aby wyświetlić szczegółowe kontenera kondycji i aplikacji informacje o wydajności.  