---
title: Sposób zarządzania usługi Azure Monitor dla agenta kontenerów | Dokumentacja firmy Microsoft
description: W tym artykule opisano zarządzanie najbardziej typowych zadań konserwacji przy użyciu agenta usługi Log Analytics konteneryzowanych używane przez usługi Azure Monitor dla kontenerów.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2018
ms.author: magoedte
ms.openlocfilehash: e1d47be159d4721aed4b055a51acf675688b855e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65071801"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>Jak zarządzać usługi Azure Monitor dla agenta kontenerów
Usługa Azure Monitor dla kontenerów używa konteneryzowanych wersję agenta usługi Log Analytics dla systemu Linux. Po początkowym wdrożeniu istnieją procedury lub opcjonalnych zadań, który może być konieczne do wykonania podczas jej cyklu projektowania. Ten artykuł zawiera o tym, jak ręcznie uaktualnić agenta i wyłączyć kolekcji zmiennych środowiskowych w określonym kontenerze. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>Jak uaktualnić usługi Azure Monitor dla agenta kontenerów
Usługa Azure Monitor dla kontenerów używa konteneryzowanych wersję agenta usługi Log Analytics dla systemu Linux. Po udostępnieniu nowej wersji agenta, agent jest automatycznie uaktualniany na zarządzanych klastrów Kubernetes hostowanych na platformie Azure Kubernetes Service (AKS).  

Jeśli uaktualnienie agenta nie powiedzie się, w tym artykule opisano proces, aby ręcznie uaktualnić agenta. Aby skorzystać z wersji wydanych, zobacz [anonse wersji agenta](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).   

### <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Uaktualnienie agenta na monitorowanych klastra Kubernetes
Proces uaktualniania agenta składa się z dwa proste kroki. Pierwszym krokiem jest wyłączyć monitorowanie za pomocą usługi Azure Monitor dla kontenerów za pomocą narzędzia wiersza polecenia platformy Azure.  Wykonaj kroki opisane w [Wyłącz monitorowanie](container-insights-optout.md?#azure-cli) artykułu. Przy użyciu wiersza polecenia platformy Azure pozwala usunąć agenta z węzłów w klastrze bez wywierania wpływu na rozwiązanie i odpowiadające im dane, które są przechowywane w obszarze roboczym. 

>[!NOTE]
>Podczas wykonywania związanych z konserwacją, węzły w klastrze nie przekazuje dalej zebranych danych i widokach wydajności nie będą wyświetlane dane w czasie między usunąć agenta i zainstalowanie nowej wersji. 
>

Aby zainstalować nową wersję agenta, wykonaj czynności opisane w [Włącz monitorowanie przy użyciu wiersza polecenia platformy Azure](container-insights-enable-new-cluster.md#enable-using-azure-cli), aby ukończyć ten proces.  

Po ponownym włączeniu, monitorowania, może upłynąć około 15 minut, zanim będzie można wyświetlić metryki kondycji zaktualizowane dla klastra. Aby sprawdzić, czy agent pomyślnie uaktualnione, uruchom polecenie: `kubectl logs omsagent-484hw --namespace=kube-system`

Stan powinny być podobne do następującego przykładu, gdzie wartość *omi* i *omsagent* powinien być zgodny z najnowszej wersji, które są określone w [historię wersji agenta](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).  

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

## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Jak wyłączyć kolekcji zmiennych środowiskowych w kontenerze
Usługa Azure Monitor dla kontenerów umożliwia zbieranie informacji o zmiennych środowiskowych z kontenery działające w zasobniku i przedstawia je w okienku właściwości wybranego kontenera w **kontenery** widoku. To zachowanie można kontrolować, wyłączając zbieranie dla określonego kontenera albo podczas wdrażania klastra AKS, lub po przez ustawienie zmiennej środowiskowej *AZMON_COLLECT_ENV*. Ta funkcja jest dostępna w wersji agenta — ciprod11292018 lub nowszy.  

Aby wyłączyć zbieranie zmienne środowiskowe do nowego lub istniejącego kontenera, ustaw zmienną **AZMON_COLLECT_ENV** o wartości **False** w pliku konfiguracyjnym yaml wdrożenia rozwiązania Kubernetes.   

```  
- name: AZMON_COLLECT_ENV  
  value: "False"  
```  

Uruchom następujące polecenie, aby zastosować zmianę na kontenerze usługi AKS: `kubectl apply -f  <path to yaml file>`.

Aby sprawdzić, czy zmiana konfiguracji weszło w życie, wybierz kontener w **kontenery** Wyświetl w usłudze Azure Monitor dla kontenerów i na panelu Właściwości, rozwiń **zmienne środowiskowe**.  Sekcja powinny być widoczne tylko zmiennej utworzonej wcześniej — **AZMON_COLLECT_ENV = FALSE**. Dla innych kontenerów w sekcji zmiennych środowiskowych powinien zawierać wszystkie zmienne środowiskowe odnalezione.   

Aby ponownie włączyć odnajdywanie zmiennych środowiskowych, zastosuj ten sam proces wcześniej, a następnie zmień wartość z **False** do **True**, a następnie ponownie uruchom `kubectl` polecenia do aktualizacji kontenera.  

```  
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Kolejne kroki
Jeśli występują problemy podczas uaktualniania agenta, zapoznaj się z [przewodnik rozwiązywania problemów z](container-insights-troubleshoot.md) pomocy technicznej.