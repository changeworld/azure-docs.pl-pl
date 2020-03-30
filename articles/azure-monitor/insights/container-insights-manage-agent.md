---
title: Jak zarządzać usługą Azure Monitor dla kontenerów agenta | Dokumenty firmy Microsoft
description: W tym artykule opisano zarządzanie najbardziej typowymi zadaniami konserwacji za pomocą konteneryzowanego agenta usługi Log Analytics używanego przez usługę Azure Monitor dla kontenerów.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 1a1f8d690979a846dbf5041999180221752acc0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275323"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Jak zarządzać usługą Azure Monitor dla agenta kontenerów

Usługa Azure Monitor dla kontenerów używa konteneryzowanej wersji agenta usługi Log Analytics dla systemu Linux. Po początkowym wdrożeniu istnieją rutynowe lub opcjonalne zadania, które mogą być konieczne do wykonania w trakcie jego cyklu życia. W tym artykule opisano sposób ręcznego uaktualniania agenta i wyłączania kolekcji zmiennych środowiskowych z określonego kontenera. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Jak uaktualnić agenta usługi Azure Monitor dla kontenerów

Usługa Azure Monitor dla kontenerów używa konteneryzowanej wersji agenta usługi Log Analytics dla systemu Linux. Po wydaniu nowej wersji agenta agent jest automatycznie uaktualniany w zarządzanych klastrach kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS) i Azure Red Hat OpenShift. W przypadku [hybrydowego klastra kubernetes](container-insights-hybrid-setup.md) agent nie jest zarządzany i należy ręcznie uaktualnić agenta.

Jeśli uaktualnienie agenta nie powiedzie się dla klastra hostowanego w u usługi AKS, w tym artykule opisano również proces ręcznego uaktualniania agenta. Aby śledzić wydane wersje, zobacz [anonse wydania agenta](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

### <a name="upgrade-agent-on-monitored-kubernetes-cluster"></a>Agent uaktualniania w monitorowanym klastrze kubernetes

Proces uaktualniania agenta w klastrach, innych niż Azure Red Hat OpenShift, składa się z dwóch prostych kroków do przodu. Pierwszym krokiem jest wyłączenie monitorowania za pomocą usługi Azure Monitor dla kontenerów przy użyciu interfejsu wiersza polecenia platformy Azure. Wykonaj kroki opisane w [artykule Wyłącz monitorowanie.](container-insights-optout.md?#azure-cli) Za pomocą narzędzia Azure CLI pozwala nam usunąć agenta z węzłów w klastrze bez wpływu na rozwiązanie i odpowiednie dane, które są przechowywane w obszarze roboczym. 

>[!NOTE]
>Podczas wykonywania tego działania konserwacji węzły w klastrze nie są przekazywania zebranych danych, a widoki wydajności nie będą wyświetlać danych między czasem usunięcia agenta i zainstalowania nowej wersji. 
>

Aby zainstalować nową wersję agenta, wykonaj kroki opisane w [enable monitorowania przy użyciu interfejsu wiersza polecenia platformy Azure](container-insights-enable-new-cluster.md#enable-using-azure-cli), aby zakończyć ten proces.  

Po ponownym włączeniu monitorowania może upłynąć około 15 minut, zanim będzie można wyświetlić zaktualizowane metryki kondycji klastra. Aby pomyślnie zweryfikować uaktualnienie agenta, uruchom polecenie:`kubectl logs omsagent-484hw --namespace=kube-system`

Stan powinien przypominać poniższy przykład, w którym wartość *omi* i *omsagent* powinna być zgodna z najnowszą wersją określoną w [historii wydań agenta.](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)  

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

## <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>Agent uaktualniania w hybrydowym klastrze kubernetes

Proces uaktualniania agenta w klastrze Kubernetes hostowany lokalnie, Aparat AKS na platformie Azure i Azure Stack można ukończyć, uruchamiając następujące polecenie:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Jeśli obszar roboczy usługi Log Analytics znajduje się w Chinach platformy Azure, uruchom następujące polecenie:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Jeśli obszar roboczy usługi Log Analytics znajduje się na platformie Azure US GOVERNMENT, uruchom następujące polecenie:

```
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Jak wyłączyć zbieranie zmiennych środowiskowych w kontenerze

Usługa Azure Monitor dla kontenerów zbiera zmienne środowiskowe z kontenerów uruchomionych w zasobniku i przedstawia je w okienku właściwości wybranego kontenera w widoku **Kontenery.** To zachowanie można kontrolować, wyłączając kolekcję dla określonego kontenera podczas wdrażania klastra Kubernetes lub po ustawieniu zmiennej środowiskowej *AZMON_COLLECT_ENV*. Ta funkcja jest dostępna w wersji agenta — ciprod11292018 i nowszej.  

Aby wyłączyć zbieranie zmiennych środowiskowych w nowym lub istniejącym kontenerze, należy ustawić zmienną **AZMON_COLLECT_ENV** o wartości **False** w pliku konfiguracji yaml wdrożenia kubernetes. 

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Uruchom następujące polecenie, aby zastosować zmianę do klastrów kubernetes `kubectl apply -f  <path to yaml file>`innych niż Azure Red Hat OpenShift): . Aby edytować configMap i zastosować tę zmianę dla klastrów OpenShift Usługi Azure Red Hat, uruchom polecenie:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Spowoduje to otwarcie domyślnego edytora tekstu. Po ustawieniu zmiennej zapisz plik w edytorze.

Aby sprawdzić, czy zmiana konfiguracji została zaaranżena, wybierz kontener w widoku **Kontenery** w usłudze Azure Monitor dla kontenerów, a następnie w panelu właściwości rozwiń pozycję **Zmienne środowiskowe**.  Sekcja powinna pokazywać tylko zmienną utworzoną wcześniej - **AZMON_COLLECT_ENV=FALSE**. Dla wszystkich innych kontenerów zmienne środowiskowe sekcja powinna wyświetlić listę wszystkich wykrytych zmiennych środowiskowych.

Aby ponownie włączyć odnajdowanie zmiennych środowiskowych, zastosuj ten sam proces wcześniej i `kubectl` zmień wartość z **False** na **True**, a następnie uruchom ponownie polecenie, aby zaktualizować kontener.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Następne kroki

Jeśli wystąpią problemy podczas uaktualniania agenta, zapoznaj się z [przewodnikiem rozwiązywania problemów, aby](container-insights-troubleshoot.md) uzyskać pomoc techniczną.
