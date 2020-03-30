---
title: Monitorowanie nowego klastra usługi Azure Kubernetes (AKS) | Dokumenty firmy Microsoft
description: Dowiedz się, jak włączyć monitorowanie nowego klastra usługi Azure Kubernetes (AKS) za pomocą usługi Azure Monitor dla subskrypcji kontenerów.
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: c731826f2780c45358730f9ce20d6a6151f6f259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275440"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Włączanie monitorowania nowego klastra usługi Azure Kubernetes (AKS)

W tym artykule opisano sposób konfigurowania usługi Azure Monitor dla kontenerów w celu monitorowania zarządzanego klastra kubernetes hostowanego w [usłudze Azure Kubernetes,](https://docs.microsoft.com/azure/aks/) który przygotowujesz do wdrożenia w ramach subskrypcji.

Monitorowanie klastra usługi AKS można włączyć przy użyciu jednej z obsługiwanych metod:

* Interfejs wiersza polecenia platformy Azure
* Terraform

## <a name="enable-using-azure-cli"></a>Włącz korzystanie z interfejsu wiersza polecenia platformy Azure

Aby włączyć monitorowanie nowego klastra AKS utworzonego za pomocą interfejsu wiersza polecenia platformy Azure, wykonaj krok w artykule szybki start w sekcji [Tworzenie klastra AKS](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Jeśli zdecydujesz się korzystać z interfejsu wiersza polecenia platformy Azure, najpierw należy zainstalować i używać interfejsu wiersza polecenia lokalnie. Musi być uruchomiony interfejsu wiersza polecenia platformy Azure w wersji 2.0.74 lub nowszej. Aby zidentyfikować swoją `az --version`wersję, uruchom polecenie . Jeśli chcesz zainstalować lub uaktualnić platformę Azure CLI, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Jeśli zainstalowano rozszerzenie interfejsu wiersza polecenia aks-preview w wersji 0.4.12 lub nowszej, usuń wszelkie zmiany wprowadzone w celu włączenia rozszerzenia w wersji zapoznawczej, ponieważ może ono zastąpić domyślne zachowanie interfejsu wiersza polecenia platformy Azure, ponieważ funkcje AKS Preview nie są dostępne w chmurze Azure US Governmnet.

## <a name="enable-using-terraform"></a>Włącz korzystanie z terraform

W przypadku [wdrażania nowego klastra AKS przy użyciu programu Terraform](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)należy określić argumenty wymagane w profilu [do utworzenia obszaru roboczego usługi Log Analytics,](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) jeśli nie zostanie określony istniejący. 

>[!NOTE]
>Jeśli zdecydujesz się korzystać z terraform, musisz uruchomić terraform Azure Rm Provider w wersji 1.17.0 lub wyższej.

Aby dodać usługę Azure Monitor dla kontenerów do obszaru roboczego, zobacz [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) i wypełnij profil, dołączając [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) i określ **oms_agent**. 

Po włączeniu monitorowania i pomyślnym zakończeniu wszystkich zadań konfiguracyjnych można monitorować wydajność klastra na dwa sposoby:

* Bezpośrednio w klastrze AKS, wybierając **health** w lewym okienku.
* Wybierając kafelek **Szczegółowe informacje o kontenerze monitoru** na stronie klastra AKS dla wybranego klastra. W usłudze Azure Monitor w lewym okienku wybierz pozycję **Zdrowie**. 

  ![Opcje wybierania usługi Azure Monitor dla kontenerów w aks](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Po włączeniu monitorowania może upłynąć około 15 minut, zanim będzie można wyświetlić metryki kondycji klastra. 

## <a name="verify-agent-and-solution-deployment"></a>Weryfikowanie wdrażania agenta i rozwiązania
W wersji agenta *06072018* lub nowszej można sprawdzić, czy agent i rozwiązanie zostały pomyślnie wdrożone. We wcześniejszych wersjach agenta można zweryfikować tylko wdrożenie agenta.

### <a name="agent-version-06072018-or-later"></a>Wersja agenta 06072018 lub nowsza
Uruchom następujące polecenie, aby sprawdzić, czy agent został pomyślnie wdrożony. 

```
kubectl get ds omsagent --namespace=kube-system
```

Dane wyjściowe powinny przypominać następujące, co wskazuje, że został wdrożony poprawnie:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Aby zweryfikować wdrożenie rozwiązania, uruchom następujące polecenie:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Dane wyjściowe powinny przypominać następujące, co wskazuje, że został wdrożony poprawnie:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Wersja agenta wcześniejsza niż 06072018

Aby sprawdzić, czy wersja agenta usługi Log Analytics wydana przed *06072018* jest poprawnie wdrożona, uruchom następujące polecenie:  

```
kubectl get ds omsagent --namespace=kube-system
```

Dane wyjściowe powinny przypominać następujące, co wskazuje, że został wdrożony poprawnie:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Wyświetlanie konfiguracji za pomocą interfejsu wiersza polecenia
Użyj `aks show` polecenia, aby uzyskać szczegółowe informacje, takie jak jest włączone rozwiązanie lub nie, co to jest log analytics obszar roboczy resourceID i szczegóły podsumowania o klastrze.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Po kilku minutach polecenie kończy i zwraca informacje o rozwiązaniu w formacie JSON.  Wyniki polecenia powinny wyświetlać profil dodatku monitorowania i przypomina następujące przykładowe dane wyjściowe:

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

## <a name="next-steps"></a>Następne kroki

* Jeśli podczas próby wprowadzenia rozwiązania wystąpią problemy, zapoznaj się z [przewodnikiem rozwiązywania problemów](container-insights-troubleshoot.md)

* Dzięki możliwości monitorowania włączonemu do zbierania kondycji i wykorzystania zasobów klastra AKS i uruchomionych na nich obciążeń dowiedz się, [jak używać](container-insights-analyze.md) usługi Azure Monitor dla kontenerów.
