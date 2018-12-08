---
title: Jak uaktualnić usługi Azure Monitor dla agenta kontenery (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak uaktualnić agenta usługi Log Analytics, używane przez usługi Azure Monitor dla kontenerów.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: magoedte
ms.openlocfilehash: 026bdd6a59dc84220e7e52707cee3c1971fba838
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104322"
---
# <a name="how-to-upgrade-the-azure-monitor-for-containers-preview-agent"></a>Jak uaktualnić usługi Azure Monitor dla agenta kontenery (wersja zapoznawcza)
Usługa Azure Monitor dla kontenerów używa konteneryzowanych wersję agenta usługi Log Analytics dla systemu Linux. Po udostępnieniu nowej wersji agenta, agent jest automatycznie uaktualniany na zarządzanych klastrów Kubernetes hostowanych na platformie Azure Kubernetes Service (AKS).  

Jeśli uaktualnienie agenta nie powiedzie się, w tym artykule opisano proces, aby ręcznie uaktualnić agenta. Aby skorzystać z wersji wydanych, zobacz [anonse wersji agenta](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).   

## <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Uaktualnienie agenta na monitorowanych klastra Kubernetes
Proces uaktualniania agenta składa się z dwa proste kroki. Pierwszym krokiem jest wyłączyć monitorowanie za pomocą usługi Azure Monitor dla kontenerów za pomocą narzędzia wiersza polecenia platformy Azure.  Wykonaj kroki opisane w [Wyłącz monitorowanie](container-insights-optout.md?toc=%2fazure%2fmonitoring%2ftoc.json#azure-cli) artykułu. Przy użyciu wiersza polecenia platformy Azure pozwala usunąć agenta z węzłów w klastrze bez wywierania wpływu na rozwiązanie i odpowiadające im dane, które są przechowywane w obszarze roboczym. 

>[!NOTE]
>Podczas wykonywania związanych z konserwacją, węzły w klastrze nie przekazuje dalej zebranych danych i widokach wydajności nie będą wyświetlane dane w czasie między usunąć agenta i zainstalowanie nowej wersji. 
>

Aby zainstalować nową wersję agenta, wykonaj czynności opisane w [dołączanie monitorowania](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-using-azure-cli) artykuł przy użyciu wiersza polecenia platformy Azure, aby ukończyć ten proces.  

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

## <a name="next-steps"></a>Kolejne kroki
Jeśli występują problemy podczas uaktualniania agenta, zapoznaj się z [przewodnik rozwiązywania problemów z](container-insights-troubleshoot.md) pomocy technicznej.