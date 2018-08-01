---
title: Monitorowanie kondycji usługi Azure Kubernetes Service (AKS) (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak łatwo można przejrzeć wydajności kontenera AKS do szybkiego zrozumienia wykorzystania hostowanym środowiskiem Kubernetes.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2018
ms.author: magoedte
ms.openlocfilehash: f84452af9c2c731d69d5805961266c46351a7687
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366100"
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>Monitorowanie kondycji kontenera usługi Azure Kubernetes Service (AKS) (wersja zapoznawcza)

W tym artykule opisano sposób konfigurowania i używania usługi Azure Monitor kondycji kontenera w celu monitorowania wydajności obciążeń, które są wdrażane do środowisk Kubernetes i hostowanej na platformie Azure Kubernetes Service (AKS). Monitorowanie klastra Kubernetes i kontenerów usługi ma krytyczne znaczenie, szczególnie w przypadku korzystania z klastra produkcyjnego na dużą skalę z wieloma aplikacjami.

Kondycji kontenera zapewnia możliwość przez zbieranie pamięci i procesora metryki kontrolery, węzły i kontenerów, które są dostępne w usłudze Kubernetes za pomocą interfejsu API metryki monitorowania wydajności. Po włączeniu kondycji kontenera, te metryki są automatycznie zbierane dla Ciebie za pośrednictwem konteneryzowanych wersję agenta Operations Management Suite (OMS) dla systemu Linux i przechowywane w swojej [usługi Log Analytics](../log-analytics/log-analytics-overview.md) obszaru roboczego. Dołączone wstępnie zdefiniowanych widoków wyświetlić przechowywanych obciążeń kontenerów i co wpływa na kondycji przedstawiający klaster Kubernetes wydajność tak aby można było przeprowadzić:  

* Określ kontenery, które są uruchomione na węźle i ich średnie wykorzystanie procesora i pamięci. Ta wiedza może pomóc w identyfikacji wąskich gardeł zasobów.
* Określ, gdzie kontenera znajduje się w kontrolerze lub zasobnik. Ta wiedza może ułatwić wyświetlanie kontrolera lub zasobnika na ogólną wydajność. 
* Sprawdź wykorzystanie zasobów obciążeń uruchomionych na hoście, które są powiązane z standardowych procesów, które obsługują zasobnik.
* Poznanie zachowań klastra, średnia i największym obciążeniach. Ta wiedza może pomóc zidentyfikować potrzeby związane z pojemnością i określić maksymalnego obciążenia, które klaster może wytrzymać. 

Jeśli interesuje Cię monitorowania i zarządzania platformy Docker i Windows hostach kontenerów, Wyświetl konfigurację, inspekcji i wykorzystanie zasobów, zobacz [rozwiązanie do monitorowania kontenerów](../log-analytics/log-analytics-containers.md).

## <a name="prerequisites"></a>Wymagania wstępne 
Przed rozpoczęciem upewnij się, że dysponujesz następującymi elementami:

- Nowy lub istniejący klaster usługi AKS.
- A kontenerowych nimi agenta pakietu OMS dla systemu Linux w wersji microsoft / oms:ciprod04202018 lub nowszej. Numer wersji jest reprezentowany przez datę w następującym formacie: *mmddyyyy*. Agent jest instalowany automatycznie podczas dołączania kondycji kontenera. 
- Obszar roboczy usługi Log Analytics. Włącz monitorowanie dla nowego klastra AKS lub pozwolić proces dołączania, Utwórz domyślny obszar roboczy w domyślnej grupie zasobów subskrypcji klastra AKS możesz go utworzyć. Jeśli chcesz utworzyć samodzielnie, możesz je utworzyć za pomocą [usługi Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md)za pośrednictwem [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), lub [witryny Azure portal](../log-analytics/log-analytics-quick-create-workspace.md).
- Usługa Log Analytics roli Współautor, Włącz monitorowanie kontenera. Aby uzyskać więcej informacji na temat kontrolowania dostępu do obszaru roboczego usługi Log Analytics, zobacz [możesz zarządzać obszarami roboczymi](../log-analytics/log-analytics-manage-access.md).

## <a name="components"></a>Składniki 

Możliwość monitorowania wydajności opiera się na konteneryzowanych agenta pakietu OMS dla systemu Linux, który służy do zbierania danych zdarzeń i wydajności ze wszystkich węzłów w klastrze. Agent automatycznie wdrożeniu i zarejestrowaniu z określonym obszarem roboczym usługi Log Analytics, po włączeniu monitorowanie kontenerów. 

>[!NOTE] 
>Jeśli masz już wdrożone w klastrze AKS, Włącz monitorowanie przy użyciu wiersza polecenia platformy Azure lub podanego szablonu Azure Resource Manager, jak pokazano w dalszej części tego artykułu. Nie można użyć `kubectl` do uaktualnienia, Usuń, Wdróż ponownie lub wdrożyć agenta. 
>

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal
Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>Włącz monitorowanie kondycji kontenera dla nowego klastra
Podczas wdrażania możesz włączyć monitorowanie nowy klaster AKS w witrynie Azure portal lub za pomocą wiersza polecenia platformy Azure. Postępuj zgodnie z instrukcjami w artykule przewodnika Szybki Start [wdrażanie klastra usługi Azure Kubernetes Service (AKS)](../aks/kubernetes-walkthrough-portal.md) Jeśli chcesz włączyć z poziomu portalu. Na **monitorowanie** strony dla **Włącz monitorowanie** wybierz **tak**, a następnie wybierz istniejący obszar roboczy usługi Log Analytics lub Utwórz nową. 

Aby włączyć monitorowanie nowy klaster AKS utworzone przy użyciu wiersza polecenia platformy Azure, przejdź do kroku w artykule przewodnika Szybki Start w sekcji [klastra AKS tworzenie](../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Jeśli zdecydujesz się użyć wiersza polecenia platformy Azure, należy najpierw zainstalować i korzystać z interfejsu wiersza polecenia lokalnie. Musi być uruchomiona wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Aby zidentyfikować wersję, uruchom `az --version`. Jeśli musisz zainstalować lub uaktualnić wiersza polecenia platformy Azure, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 
>

Po włączeniu monitorowania i wszystkie zadania konfiguracji zostaną ukończone pomyślnie, można monitorować wydajność klastra w jeden z dwóch sposobów:

* Bezpośrednio w klastrze AKS, wybierając **kondycji** w okienku po lewej stronie.
* Wybierając **monitorowania kondycji kontenera** kafelka na stronie klastra AKS dla wybranego klastra. W usłudze Azure Monitor w okienku po lewej stronie wybierz **kondycji**. 

  ![Opcje dotyczące wybierania kondycji kontenera w usłudze AKS](./media/monitoring-container-health/container-performance-and-health-select-01.png)

Po włączeniu monitorowania może potrwać około 15 minut, zanim będzie można wyświetlić danych operacyjnych dla klastra. 

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>Włącz monitorowanie kondycji kontenera dla istniejących zarządzane klastry
Aby umożliwić monitorowanie klastra usługi AKS, który jest już wdrożony za pomocą wiersza polecenia platformy Azure z poziomu portalu lub za pomocą podanego szablonu Azure Resource Manager za pomocą polecenia cmdlet programu PowerShell `New-AzureRmResourceGroupDeployment`. 

### <a name="enable-monitoring-using-azure-cli"></a>Włącz monitorowanie przy użyciu wiersza polecenia platformy Azure
Następny krok umożliwia monitorowanie klastra usługi AKS przy użyciu wiersza polecenia platformy Azure. W tym przykładzie nie należy utworzyć na lub określić istniejący obszar roboczy. To polecenie upraszcza ten proces dla Ciebie przez utworzenie domyślnego obszaru roboczego w domyślnej grupie zasobów subskrypcji klastra AKS, jeśli jeden nie istnieje w tym regionie.  Domyślny obszar roboczy utworzony podobny format *DefaultWorkspace -<GUID>-<Region>*.  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

Dane wyjściowe będą wyglądać w następujący sposób:

```azurecli
provisioningState       : Succeeded
```

### <a name="enable-monitoring-in-the-azure-portal"></a>Aby włączyć monitorowanie w witrynie Azure portal
Aby włączyć monitorowanie kontenera usługi AKS w witrynie Azure portal, wykonaj następujące czynności:

1. W witrynie Azure portal wybierz **wszystkich usług**. 
2. Na liście zasobów wpisz **kontenery**.  
    Listy jest filtrowana w oparciu o wpisywane dane. 
3. Wybierz **usługi Kubernetes**.  

    ![Link usługi Kubernetes](./media/monitoring-container-health/azure-portal-01.png)

4. Na liście kontenerów Wybierz kontener.
5. Na stronie Przegląd kontenera wybierz **monitorowania kondycji kontenera**.  
6. Na **dołączenie do kondycji kontenera i dzienniki** strony, jeśli masz istniejące usługi Log Analytics obszaru roboczego w tej samej subskrypcji co klaster, wybierz go na liście rozwijanej.  
    Lista preselects domyślnego obszaru roboczego i lokalizacji, który kontenerów AKS jest wdrażany w ramach subskrypcji. 

    ![Włącz monitorowanie kondycji kontenera w usłudze AKS](./media/monitoring-container-health/container-health-enable-brownfield-02.png)

>[!NOTE]
>Jeśli chcesz utworzyć nowy obszar roboczy usługi Log Analytics do przechowywania danych monitorowania z klastra, postępuj zgodnie z instrukcjami [Utwórz obszar roboczy usługi Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). Pamiętaj utworzyć obszar roboczy w tej samej subskrypcji, który kontenerów AKS jest wdrażany. 
 
Po włączeniu monitorowania może potrwać około 15 minut, zanim będzie można wyświetlić danych operacyjnych dla klastra. 

### <a name="enable-monitoring-by-using-an-azure-resource-manager-template"></a>Aby włączyć monitorowanie za pomocą szablonu usługi Azure Resource Manager
Ta metoda obejmuje dwa szablony JSON. Jeden szablon Określa konfigurację, aby włączyć monitorowanie, a drugi zawiera wartości parametrów, które można skonfigurować w celu określ następujące ustawienia:

* Identyfikator zasobu kontenera usługi AKS. 
* Grupa zasobów, który jest wdrażany klaster w.
* Obszar roboczy usługi Log Analytics i region, aby utworzyć obszar roboczy w. 

Obszar roboczy usługi Log Analytics ma zostać utworzone ręcznie. Aby utworzyć obszar roboczy, możesz skonfigurować go za pośrednictwem [usługi Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md)za pośrednictwem [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), lub [witryny Azure portal](../log-analytics/log-analytics-quick-create-workspace.md).

Jeśli znasz koncepcji wdrażanie zasobów za pomocą szablonu, zobacz:
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../azure-resource-manager/resource-group-template-deploy.md)
* [Wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)

Jeśli zdecydujesz się użyć wiersza polecenia platformy Azure, należy najpierw zainstalować i korzystać z interfejsu wiersza polecenia lokalnie. Musi być uruchomiona wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Aby zidentyfikować wersję, uruchom `az --version`. Jeśli musisz zainstalować lub uaktualnić wiersza polecenia platformy Azure, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-a-template"></a>Tworzenie i wykonywanie szablonu

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
3. Wklej następującą składnię JSON do pliku:

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

4. Edytuj wartości **aksResourceId** i **aksResourceLocation** przy użyciu wartości na **Omówienie usługi AKS** strony dla klastra usługi AKS. Wartość **workspaceResourceId** jest pełny identyfikator zasobu obszaru roboczego usługi Log Analytics, która zawiera nazwę obszaru roboczego. Też określić miejsce, w obszarze roboczym **workspaceRegion**. 
5. Zapisz ten plik jako **existingClusterParam.json** do folderu lokalnego.
6. Wszystko jest teraz gotowe do wdrożenia tego szablonu. 

    * Użyj następujących poleceń programu PowerShell w folderze, który zawiera szablon:

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        Zmiana konfiguracji może potrwać kilka minut. Po jej zakończeniu zostanie wyświetlony komunikat podobny do poniższego, która zawiera wynik:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Aby za pomocą wiersza polecenia platformy Azure w systemie Linux, należy uruchomić następujące polecenie:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        Zmiana konfiguracji może potrwać kilka minut. Po jej zakończeniu zostanie wyświetlony komunikat podobny do poniższego, która zawiera wynik:

        ```azurecli
        provisioningState       : Succeeded
        ```
Po włączeniu monitorowania może potrwać około 15 minut, zanim będzie można wyświetlić danych operacyjnych dla klastra. 

## <a name="verify-agent-and-solution-deployment"></a>Sprawdź wdrożenie agenta i rozwiązania
Za pomocą wersji agenta *06072018* lub później, sprawdź, czy agent i rozwiązania zostały pomyślnie wdrożone. W starszych wersjach programu agent można sprawdzić tylko na wdrożenie agenta.

### <a name="agent-version-06072018-or-later"></a>Wersja agenta 06072018 lub nowszy
Uruchom następujące polecenie, aby sprawdzić, czy agent zostanie wdrożony pomyślnie. 

```
kubectl get ds omsagent --namespace=kube-system
```

Dane wyjściowe powinien przypominać poniższe polecenie, co oznacza, że została prawidłowo wdrożona:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Aby zweryfikować wdrożenie rozwiązania, uruchom następujące polecenie:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Dane wyjściowe powinien przypominać poniższe polecenie, co oznacza, że została prawidłowo wdrożona:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Agent w wersji wcześniejszej niż 06072018

Aby sprawdzić, czy wersja agenta pakietu OMS wydanych przed *06072018* jest wdrażany prawidłowo, uruchom następujące polecenie:  

```
kubectl get ds omsagent --namespace=kube-system
```

Dane wyjściowe powinien przypominać poniższe polecenie, co oznacza, że została prawidłowo wdrożona:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Wyświetl konfigurację przy użyciu interfejsu wiersza polecenia
Użyj `aks show` polecenia w celu uzyskania szczegółowych informacji takich, w jakim są rozwiązania włączony, czy nie, co to jest identyfikator zasobu obszaru roboczego usługi Log Analytics i podsumowanie szczegółowych informacji o klastrze.  

```azurecli
az aks show -g <resoourceGroupofAKSCluster> -n <nameofAksCluster>
```

Po kilku minutach polecenie kończy i zwraca sformatowanego JSON informacji na temat rozwiązania.  Wyniki polecenia powinien być wyświetlony profilu monitorowania dodatku i podobne do następujących przykładowych danych wyjściowych:

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="view-performance-utilization"></a>Widok wydajności użycia
Po otwarciu kondycji kontenera, strony natychmiast przedstawia informacje o wykorzystaniu wydajność całego klastra. Wyświetlanie informacji na temat klastra usługi AKS jest podzielony na cztery perspektywy:

- Klaster
- Węzły 
- Kontrolery  
- Containers

Na **klastra** karcie cztery wykresy wydajności wiersza zawierają kluczowe metryki wydajności klastra. 

![Przykładowe wykresy wydajności na karcie klastra](./media/monitoring-container-health/container-health-cluster-perfview.png)

Wykres wydajności przedstawia cztery metryki wydajności:

- **Wykorzystanie procesora CPU w węźle&nbsp;%**: perspektywy zagregowane wykorzystanie procesora CPU dla całego klastra. Wyniki zakresu czasu można filtrować, wybierając **Avg**, **Min**, **Max**, **50.**, **90**, i **95** w selektorze percentyle nad wykresem, albo indywidualnie lub w połączeniu. 
- **Wykorzystanie pamięci węzła&nbsp;%**: perspektywy zagregowane wykorzystanie pamięci dla całego klastra. Wyniki zakresu czasu można filtrować, wybierając **Avg**, **Min**, **Max**, **50.**, **90**, i **95** w selektorze percentyle nad wykresem, albo indywidualnie lub w połączeniu. 
- **Liczba węzłów**: liczba węzłów i stan z usługi Kubernetes. Są Stany węzłów klastra, reprezentowany *wszystkich*, *gotowe*, i *niegotowe* i może być filtrowana indywidualnie lub w połączeniu w selektorze powyżej wykresu. 
- **Liczba zasobników działań**: Liczba zasobników i stan z usługi Kubernetes. Są Stany zasobników reprezentowane *wszystkich*, *oczekujące*, *systemem*, i *nieznany* i może być filtrowana indywidualnie lub w Selektor powyżej wykresu. 

Po przełączeniu do **węzłów** karcie hierarchii wiersz poniżej model obiektów usługi Kubernetes, począwszy od węzła w klastrze. Rozwiń węzeł i można wyświetlić co najmniej jeden zasobników, uruchomione w węźle. Jeśli więcej niż jednego kontenera jest zgrupowany do zasobnik, są wyświetlane jako ostatni wiersz w hierarchii. Można również wyświetlić, jak wiele powiązanych obciążeniach-pod są uruchomione na hoście, jeśli host ma procesor lub dużego wykorzystania pamięci.

![Przykładowa hierarchia Kubernetes węzeł w widoku wydajności](./media/monitoring-container-health/container-health-nodes-view.png)

Można wybrać kontrolery lub kontenerów w górnej części strony i sprawdź stan i użycia zasobów dla tych obiektów. Użyj pola listy rozwijanej w górnej filtrującą dane według przestrzeni nazw, usługi i języka node. Jeśli zamiast tego chcesz przejrzeć wykorzystanie pamięci w **metryki** listy rozwijanej wybierz **RSS pamięci** lub **zestaw roboczy pamięci**. **Pamięć RSS** jest obsługiwany tylko w przypadku rozwiązania Kubernetes w wersji 1.8 i nowszych. W przeciwnym razie do wyświetlania wartości **Min&nbsp; %**  jako *NaN&nbsp;%*, który jest wartość typu danych liczbowych, który reprezentuje niezdefiniowany lub wartość wyniku. 

![Widok wydajności węzłów kontenerów](./media/monitoring-container-health/container-health-node-metric-dropdown.png)

Domyślnie dane dotyczące wydajności opiera się na ostatnie 6 godzin, ale okno można zmienić za pomocą **zakres czasu** listy rozwijanej w prawym górnym rogu. W tej chwili strony nie automatycznego odświeżania, więc musisz ręcznie odświeżyć je. Można również filtrować wyniki do zakresu, wybierając **Avg**, **Min**, **Max**, **50.**, **90**, i **95** w selektorze percentyl. 

![Wybór percentyl filtrowanie danych](./media/monitoring-container-health/container-health-metric-percentile-filter.png)

W poniższym przykładzie należy zauważyć, dla węzła *3977305-aks-nodepool*, wartość **kontenery** wynosi 5, czyli zbiorcze całkowita liczba kontenerów wdrożonych.

![Zbiorczy kontenerów na przykład węzeł](./media/monitoring-container-health/container-health-nodes-containerstotal.png)

Ułatwia ona szybko ustalić, czy masz kompromisu kontenerów między węzłami w klastrze. 

W poniższej tabeli opisano informacje, które są prezentowane podczas wyświetlania węzłów:

| Kolumna | Opis | 
|--------|-------------|
| Name (Nazwa) | Nazwa hosta. |
| Stan | Kubernetes widok stanu węzła. |
| Średnia liczba&nbsp;%, minimalnej&nbsp;%, Max&nbsp;%, 50.&nbsp;%, 90.&nbsp;% | Średni procent węzła, w oparciu o procentowy podczas wybranego okresu. |
| Avg, Min, Max, 50., 90 | Węzły średnia wartość rzeczywista oparciu o procentowy podczas tego okresu czasu wybrany. Średnia wartość jest mierzony od limitu Procesora/pamięci dla węzła; dla zasobników i kontenerów to wartość średnia zgłaszaną przez hosta. |
| Containers | Liczba kontenerów. |
| Czas pracy | Reprezentuje czas, ponieważ węzeł uruchomiony lub został ponownie uruchomiony. |
| Kontrolery | Tylko w przypadku kontenerów i zasobników. Przedstawia on kontrolera, który jest we wszystkich. Nie wszystkie zasobników są w kontrolerze, więc niektóre może być wyświetlany **n/d**. | 
| Trend Avg&nbsp;%, minimalnej&nbsp;%, Max&nbsp;%, 50.&nbsp;%, 90.&nbsp;% | Trend wykres słupkowy prezentowanie percentyla metryk wartość procentowa kontrolera. |


W selektorze, wybierz **kontrolerów**.

![Wybierz kontrolerów widoku](./media/monitoring-container-health/container-health-controllers-tab.png)

W tym miejscu można wyświetlić kondycję wydajności kontrolerów.

![Widok wydajności kontrolerów < nazwa >](./media/monitoring-container-health/container-health-controllers-view.png)

Hierarchia wiersz rozpoczyna się od kontrolera i rozwija kontrolera. Możesz wyświetlić co najmniej jeden kontener. Rozwiń zasobnik, a ostatni wiersz wyświetla kontenera grupowania zasobnik. 

W poniższej tabeli opisano informacje, które jest wyświetlane, gdy wyświetlanie kontrolerów:

| Kolumna | Opis | 
|--------|-------------|
| Name (Nazwa) | Nazwa kontrolera.|
| Stan | Stan rozwinięcia kontenery po ukończeniu uruchomione o stanie, takie jak *OK*, *zwolniony*, *niepowodzenie* *zatrzymane*, lub *Wstrzymana*. Jeśli kontener jest uruchomiony, ale stan był albo nie zostało prawidłowo wyświetlane lub nie została pobrana przez agenta i nie odpowiedział ponad 30 minut, stan jest *nieznany*. W poniższej tabeli znajdują się dodatkowe szczegóły ikonę stanu.|
| Średnia liczba&nbsp;%, minimalnej&nbsp;%, Max&nbsp;%, 50.&nbsp;%, 90.&nbsp;% | Średnia zbiorczy średnią wartość procentową poszczególnych jednostek dla wybranej metryki i percentyl. |
| Avg, Min, Max, 50., 90  | Zbiorcze średni wydajność użycia CPU millicore lub pamięci przez kontener dla wybranych percentyl. Średnia wartość jest mierzony od limitu Procesora/pamięci dla zasobnik. |
| Containers | Łączna liczba kontenerów dla kontrolera lub zasobników. |
| Ponowne uruchomienie | Zbiorcza liczba ponowne uruchomienie z kontenerów. |
| Czas pracy | Reprezentuje czas od momentu uruchomienia kontenera. |
| Węzeł | Tylko w przypadku kontenerów i zasobników. Przedstawia on kontrolera, który jest znajdującej się. | 
| Trend Avg&nbsp;%, minimalnej&nbsp;%, Max&nbsp;%, 50.&nbsp;%, 90.&nbsp;%| Trend wykresu słupkowego, reprezentujący metryki percentyl kontrolera. |

Ikony w polu Stan wskazują stan online kontenerów:
 
| Ikona | Stan | 
|--------|-------------|
| ![Gotowe uruchomionej ikona stanu](./media/monitoring-container-health/container-health-ready-icon.png) | Uruchamianie (gotowe)|
| ![Ikona stanu oczekuje lub wstrzymana](./media/monitoring-container-health/container-health-waiting-icon.png) | Oczekuje lub wstrzymana|
| ![Ikona stanu uruchomiona po raz ostatni informowało](./media/monitoring-container-health/container-health-grey-icon.png) | Ostatnie zgłaszane uruchomiona, ale nie odpowiedział w ponad 30 minut|
| ![Ikona stanu powodzenia](./media/monitoring-container-health/container-health-green-icon.png) | Pomyślnie zatrzymano lub nie można zatrzymać|

Ikona stanu przedstawia liczbę oparte na zasobnik udostępnia. Pokazuje dwa najgorsze Stany i po najechaniu kursorem na stan, wyświetla stan zbiorcze z wszystkich zasobników w kontenerze. Jeśli nie ma stanu gotowości, wyświetlana jest wartość stanu **(0)**. 

W selektorze, wybierz **kontenery**.

![Wybierz kontenery widoku](./media/monitoring-container-health/container-health-containers-tab.png)

W tym miejscu można wyświetlić kondycję wydajności kontenerów.

![Widok wydajności kontrolerów < nazwa >](./media/monitoring-container-health/container-health-containers-view.png)

W poniższej tabeli opisano informacje, który jest wyświetlany podczas przeglądania kontenerów:

| Kolumna | Opis | 
|--------|-------------|
| Name (Nazwa) | Nazwa kontrolera.|
| Stan | Stan kontenerów, jeśli istnieje. Dodatkowe szczegóły ikona stanu znajdują się w następnej tabeli.|
| Średnia liczba&nbsp;%, minimalnej&nbsp;%, Max&nbsp;%, 50.&nbsp;%, 90.&nbsp;% | Roll-up średnią wartość procentową poszczególnych jednostek dla wybranej metryki i percentyl. |
| Avg, Min, Max, 50., 90  | Zbiorczy średni Procesora pamięci lub millicore wydajności kontenera dla wybranych percentyl. Średnia wartość jest mierzony od limitu Procesora/pamięci dla zasobnik. |
| Zasobnik | Kontener, w którym znajduje się pod.| 
| Węzeł |  Węzeł, w którym znajduje się kontener. | 
| Ponowne uruchomienie | Reprezentuje czas od momentu uruchomienia kontenera. |
| Czas pracy | Reprezentuje czas, ponieważ kontener został uruchomiony lub ponownie uruchomiony. |
| Trend Avg&nbsp;%, minimalnej&nbsp;%, Max&nbsp;%, 50.&nbsp;%, 90.&nbsp;% | Trend wykresu słupkowego, reprezentująca średnią wartość procentową metryki kontenera. |

Ikony w polu Stan wskazują online stany zasobników, zgodnie z opisem w poniższej tabeli:
 
| Ikona | Stan | 
|--------|-------------|
| ![Gotowe uruchomionej ikona stanu](./media/monitoring-container-health/container-health-ready-icon.png) | Uruchamianie (gotowe)|
| ![Ikona stanu oczekuje lub wstrzymana](./media/monitoring-container-health/container-health-waiting-icon.png) | Oczekuje lub wstrzymana|
| ![Ikona stanu uruchomiona po raz ostatni informowało](./media/monitoring-container-health/container-health-grey-icon.png) | Ostatnie zgłaszane uruchomiona, ale nie odpowiedział w ponad 30 minut|
| ![Ikona stanu zakończone](./media/monitoring-container-health/container-health-terminated-icon.png) | Pomyślnie zatrzymano lub nie można zatrzymać|
| ![Ikona stanu nie powiodło się](./media/monitoring-container-health/container-health-failed-icon.png) | Stan niepowodzenia |

## <a name="container-data-collection-details"></a>Szczegóły dotyczące zbierania danych kontenera
Kondycji kontenera zbiera różne dane metryk i dzienników wydajności hostach kontenerów i kontenery. Dane są gromadzone co trzy minuty.

### <a name="container-records"></a>Rekordów kontenera

Przykłady rekordy, które są zbierane przez kondycji kontenera i typy danych, które są wyświetlane w wynikach wyszukiwania w dzienniku są wyświetlane w poniższej tabeli:

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
Usługa log Analytics może pomóc wyszukiwania trendach, diagnozowanie wąskich gardeł, prognozy lub korelowanie danych, które mogą pomóc ustalić, czy bieżąca konfiguracja klastra działa optymalnie. Wstępnie zdefiniowane wyszukiwań w dziennikach znajdują się za Ciebie, aby od razu rozpocząć korzystanie z lub dostosować do zwracania informacji w żądany sposób. 

W obszarze roboczym można wykonywać interakcyjne analizy danych, zaznaczając **Wyświetl dziennik** opcja jest dostępna na końcu po prawej stronie po rozwinięciu kontrolera lub kontenera. **Wyszukiwanie w dzienniku** powyżej stronie portalu platformy Azure były na zostanie wyświetlona strona.

![Analizowanie danych w usłudze Log Analytics](./media/monitoring-container-health/container-health-view-logs.png)   

Dane wyjściowe dzienników kontenera, który jest przekazywany do usługi Log Analytics to STDOUT i STDERR. Ponieważ usługi Azure managed Kubernetes (AKS) jest monitorowanie kondycji kontenera, systemu Kubernetes nie są zbierane już dziś ze względu na duże obciążenie wygenerowane dane. 

### <a name="example-log-search-queries"></a>Przykład zapytania wyszukiwania w Dzienniku
Często jest to przydatne do tworzenia zapytań, które zaczynać się przykładem lub dwa, a następnie zmodyfikuj je zgodnie z wymaganiami. Aby ułatwić tworzenie bardziej zaawansowanych zapytań, możesz eksperymentować z następujące przykładowe zapytania:

| Zapytanie | Opis | 
|-------|-------------|
| ContainerInventory<br> &#124;Projekt komputera, nazwa, obraz, ImageTag, ContainerState, wartością CreatedTime, StartedTime, FinishedTime<br> &#124;Renderowanie tabeli | Wyświetlić listę wszystkich informacji o cyklu życia kontenera| 
| KubeEvents_CL<br> &#124;gdzie not(isempty(Namespace_s))<br> &#124;Sortuj według malejącej TimeGenerated<br> &#124;Renderowanie tabeli | Zdarzenia Kubernetes|
| ContainerImageInventory<br> &#124;summarize AggregatedValue = count() by obrazu, ImageTag, działa | Spis obrazów | 
| **Advanced Analytics wybierz wykresów liniowych**:<br> Wydajności<br> &#124;Gdzie ObjectName == "Container" i CounterName == "% czasu procesora"<br> &#124;Podsumowanie AvgCPUPercent avg(CounterValue) przez bin (TimeGenerated, 30 min), InstanceName = | Procesora CPU kontenera | 
| **Advanced Analytics wybierz wykresów liniowych**:<br> Perf &#124; gdzie ObjectName == "Container" i CounterName == "MB użycia pamięci"<br> &#124;Podsumowanie AvgUsedMemory avg(CounterValue) przez bin (TimeGenerated, 30 min), InstanceName = | Pamięci kontenera |

## <a name="how-to-stop-monitoring-with-container-health"></a>Jak zatrzymać monitorowanie za pomocą programu health kontenera
Jeśli po włączeniu monitorowania kontenera usługi AKS, okażą się już nie chcesz, aby ją monitorować, możesz *zrezygnować* przy użyciu dostarczonych szablonów usługi Azure Resource Manager za pomocą polecenia cmdlet programu PowerShell  **Nowe AzureRmResourceGroupDeployment** lub wiersza polecenia platformy Azure. Jeden szablon JSON Określa konfigurację *zrezygnować*. Drugi zawiera wartości parametrów, które można skonfigurować w celu określenia AKS grupę zasobów klastra identyfikator i zasobów wdrożoną w klastrze. 

Jeśli znasz koncepcji wdrażanie zasobów za pomocą szablonu, zobacz:
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../azure-resource-manager/resource-group-template-deploy.md)
* [Wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)

Jeśli zdecydujesz się użyć wiersza polecenia platformy Azure, należy najpierw zainstalować i korzystać z interfejsu wiersza polecenia lokalnie. Musi być uruchomiona wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Aby zidentyfikować wersję, uruchom `az --version`. Jeśli musisz zainstalować lub uaktualnić wiersza polecenia platformy Azure, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

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
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. Zapisz ten plik jako **OptOutTemplate.json** do folderu lokalnego.
3. Wklej następującą składnię JSON do pliku:

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

4. Edytuj wartości **aksResourceId** i **aksResourceLocation** przy użyciu wartości klastra AKS, który można znaleźć na **właściwości** strona dla wybranego klastra .

    ![Strona właściwości kontenera](./media/monitoring-container-health/container-properties-page.png)

    Gdy jesteś w **właściwości** strony, również skopiować **identyfikator zasobu obszaru roboczego**. Ta wartość jest wymagana, jeśli zdecydujesz, że chcesz później usunąć obszar roboczy usługi Log Analytics. Trwa usuwanie obszaru roboczego usługi Log Analytics nie jest wykonywane jako część tego procesu. 

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

Jeśli obszar roboczy został utworzony tylko do obsługi monitorowania klastra i nie jest już potrzebny, należy ręcznie je usunąć. Jeśli nie znasz sposób usuwania obszaru roboczego, zobacz [usunąć obszar roboczy usługi Azure Log Analytics w witrynie Azure portal](../log-analytics/log-analytics-manage-del-workspace.md). Nie zapomnij o **identyfikator zasobu obszaru roboczego** możemy skopiowany wcześniej w kroku 4, możesz zacząć potrzebna. 

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Ta sekcja zawiera informacje ułatwiające rozwiązywanie problemów z usługą kondycji kontenera.

Jeśli kondycji kontenera, została pomyślnie włączona i skonfigurowana, ale nie można wyświetlić informacje o stanie lub nie zwróciło wyników w usłudze Log Analytics podczas wykonywania przeszukiwania dzienników, możesz pomóc zdiagnozować problem, wykonując następujące czynności: 

1. Sprawdź stan agenta, uruchamiając następujące polecenie: 

    `kubectl get ds omsagent --namespace=kube-system`

    Dane wyjściowe powinien przypominać poniższe polecenie, co oznacza, że została prawidłowo wdrożona:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Sprawdź stan wdrożenia rozwiązania z wersją agenta *06072018* lub nowszej, uruchamiając następujące polecenie:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Dane wyjściowe powinien przypominać poniższe polecenie, co oznacza, że została prawidłowo wdrożona:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Sprawdź stan zasobników, aby sprawdzić, czy jest uruchomiona przez uruchomienie następującego polecenia: `kubectl get pods --namespace=kube-system`

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

4. Sprawdź dzienniki agenta. Gdy agent konteneryzowanych zostanie wdrożona, uruchamia szybkie sprawdzenie, uruchamiając polecenia OMI i wyświetla wersję agenta i dostawcy. 

5. Aby sprawdzić, czy agent zakończy się powodzeniem dołączone, uruchom następujące polecenie: `kubectl logs omsagent-484hw --namespace=kube-system`

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

Aby wyświetlić szczegółowe kontenera kondycji i aplikacji informacje o wydajności, zobacz [przeszukiwanie dzienników](../log-analytics/log-analytics-log-search.md). 
