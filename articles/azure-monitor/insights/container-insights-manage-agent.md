---
title: Sposób zarządzania usługi Azure Monitor dla agenta kontenerów | Dokumentacja firmy Microsoft
description: W tym artykule opisano zarządzanie najbardziej typowych zadań konserwacji przy użyciu agenta usługi Log Analytics konteneryzowanych używane przez usługi Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 1a1f8d690979a846dbf5041999180221752acc0b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275323"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Jak zarządzać usługi Azure Monitor dla agenta kontenerów

Usługa Azure Monitor dla kontenerów używa konteneryzowanych wersję agenta usługi Log Analytics dla systemu Linux. Po początkowym wdrożeniu istnieją procedury lub opcjonalnych zadań, który może być konieczne do wykonania podczas jej cyklu projektowania. Ten artykuł zawiera o tym, jak ręcznie uaktualnić agenta i wyłączyć kolekcji zmiennych środowiskowych w określonym kontenerze. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Jak uaktualnić usługi Azure Monitor dla agenta kontenerów

Usługa Azure Monitor dla kontenerów używa konteneryzowanych wersję agenta usługi Log Analytics dla systemu Linux. Po wydaniu nowej wersji agenta Agent jest automatycznie uaktualniany do zarządzanych klastrów Kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS) i na platformie Azure Red Hat OpenShift. W przypadku [hybrydowego klastra Kubernetes](container-insights-hybrid-setup.md) Agent nie jest zarządzany i należy ręcznie uaktualnić agenta.

Jeśli uaktualnienie agenta nie powiedzie się w klastrze hostowanym na AKS, w tym artykule opisano również proces ręcznego uaktualniania agenta. Aby postępować zgodnie z wydaną wersją, zobacz [anonse dotyczące wersji agentów](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

### <a name="upgrade-agent-on-monitored-kubernetes-cluster"></a>Uaktualnij agenta w monitorowanym klastrze Kubernetes

Proces uaktualniania agenta w klastrach, innym niż Azure Red Hat OpenShift, składa się z dwóch prostych kroków do przodu. Pierwszym krokiem jest wyłączyć monitorowanie za pomocą usługi Azure Monitor dla kontenerów za pomocą narzędzia wiersza polecenia platformy Azure. Wykonaj kroki opisane w artykule [wyłączanie monitorowania](container-insights-optout.md?#azure-cli) . Przy użyciu wiersza polecenia platformy Azure pozwala usunąć agenta z węzłów w klastrze bez wywierania wpływu na rozwiązanie i odpowiadające im dane, które są przechowywane w obszarze roboczym. 

>[!NOTE]
>Podczas wykonywania związanych z konserwacją, węzły w klastrze nie przekazuje dalej zebranych danych i widokach wydajności nie będą wyświetlane dane w czasie między usunąć agenta i zainstalowanie nowej wersji. 
>

Aby zainstalować nową wersję agenta, wykonaj kroki opisane w temacie [Włączanie monitorowania przy użyciu interfejsu wiersza polecenia platformy Azure](container-insights-enable-new-cluster.md#enable-using-azure-cli), aby ukończyć ten proces.  

Po ponownym włączeniu monitorowania może upłynąć około 15 minut, zanim będzie możliwe wyświetlenie zaktualizowanych metryk kondycji klastra. Aby sprawdzić, czy Agent został uaktualniony pomyślnie, uruchom polecenie: `kubectl logs omsagent-484hw --namespace=kube-system`

Stan powinien wyglądać podobnie do poniższego przykładu, gdzie wartość parametru *OMI* i *omsagent* powinna być zgodna z najnowszą wersją określoną w [historii wydania agenta](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).  

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
    omi 1.4.2.5
    omsagent 1.6.0-163
    docker-cimprov 1.0.0.31

## <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>Uaktualnij agenta w hybrydowym klastrze Kubernetes

Proces uaktualniania agenta do klastra Kubernetes hostowanego lokalnie, aparatu AKS na platformie Azure i Azure Stack można wykonać, uruchamiając następujące polecenie:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Jeśli obszar roboczy Log Analytics jest w Chinach platformy Azure, uruchom następujące polecenie:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Jeśli obszar roboczy Log Analytics jest w instytucji rządowych USA platformy Azure, uruchom następujące polecenie:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Jak wyłączyć kolekcji zmiennych środowiskowych w kontenerze

Azure Monitor dla kontenerów zbiera zmienne środowiskowe z kontenerów uruchomionych w obszarze pod i wyświetla je w okienku właściwości wybranego kontenera w widoku **kontenerów** . Możesz kontrolować to zachowanie, wyłączając kolekcję dla określonego kontenera albo podczas wdrażania klastra Kubernetes, albo po ustawieniu zmiennej środowiskowej *AZMON_COLLECT_ENV*. Ta funkcja jest dostępna w wersji agenta — ciprod11292018 lub nowszy.  

Aby wyłączyć zbieranie zmiennych środowiskowych w nowym lub istniejącym kontenerze, należy ustawić dla zmiennej **AZMON_COLLECT_ENV** wartość **false** w pliku konfiguracji YAML wdrożenia Kubernetes. 

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Uruchom następujące polecenie, aby zastosować zmianę do klastrów Kubernetes innych niż Azure Red Hat OpenShift): `kubectl apply -f  <path to yaml file>`. Aby edytować ConfigMap i zastosować tę zmianę dla klastrów Red Hat OpenShift platformy Azure, uruchom polecenie:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Spowoduje to otwarcie domyślnego edytora tekstu. Po ustawieniu zmiennej Zapisz plik w edytorze.

Aby sprawdzić, czy zmiana konfiguracji zadziałała, wybierz kontener w widoku **kontenery** w Azure monitor dla kontenerów, a następnie w panelu właściwości rozwiń pozycję **zmienne środowiskowe**.  Sekcja powinna zawierać tylko utworzoną wcześniej zmienną- **AZMON_COLLECT_ENV = false**. Dla innych kontenerów w sekcji zmiennych środowiskowych powinien zawierać wszystkie zmienne środowiskowe odnalezione.

Aby ponownie włączyć odnajdywanie zmiennych środowiskowych, Zastosuj ten sam proces wcześniej i zmień wartość z **false** na **true**, a następnie ponownie uruchom polecenie `kubectl` w celu zaktualizowania kontenera.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Następne kroki

W przypadku wystąpienia problemów podczas uaktualniania agenta zapoznaj się z [przewodnikiem rozwiązywania problemów](container-insights-troubleshoot.md) , aby uzyskać pomoc techniczną.
