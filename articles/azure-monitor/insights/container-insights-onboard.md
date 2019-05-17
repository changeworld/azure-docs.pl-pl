---
title: Jak włączyć usługi Azure Monitor dla kontenerów | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak włączyć i skonfigurować usługi Azure Monitor dla kontenerów, dzięki czemu można zrozumieć, jaka jest wydajność kontenera oraz zostały zidentyfikowane problemów związanych z wydajnością.
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
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: 5e149fa96e0a62656804906b52adf10273321d17
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521895"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Jak włączyć usługi Azure Monitor dla kontenerów  

Ten artykuł zawiera omówienie dostępne opcje Aby skonfigurować usługi Azure Monitor dla kontenerów w celu monitorowania wydajności obciążeń, które są wdrażane do środowisk Kubernetes i hostowanych na [usługi Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/).

Usługa Azure Monitor dla kontenerów można włączyć dla nowych lub obsługiwane przez jedną lub więcej istniejących wdrożeń usługi AKS przy użyciu następujących metod:

* W witrynie Azure portal, programu Azure PowerShell lub za pomocą wiersza polecenia platformy Azure
* Za pomocą [narzędzia Terraform i AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne 
Przed rozpoczęciem upewnij się, że dysponujesz następującymi elementami:

- **Obszar roboczy usługi Log Analytics.** Włącz monitorowanie dla nowego klastra AKS lub pozwolić proces dołączania, Utwórz domyślny obszar roboczy w domyślnej grupie zasobów subskrypcji klastra AKS możesz go utworzyć. Jeśli chcesz utworzyć samodzielnie, możesz je utworzyć za pomocą [usługi Azure Resource Manager](../platform/template-workspace-configuration.md)za pośrednictwem [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), lub [witryny Azure portal](../learn/quick-create-workspace.md).
- Jesteś członkiem **roli Współautor usługi Log Analytics** umożliwiające monitorowanie kontenerów. Aby uzyskać więcej informacji na temat kontrolowania dostępu do obszaru roboczego usługi Log Analytics, zobacz [możesz zarządzać obszarami roboczymi](../platform/manage-access.md).
- Jesteś członkiem **[właściciela](../../role-based-access-control/built-in-roles.md#owner)** roli dla zasobu klastra usługi AKS. 

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>Składniki 

Możliwość monitorowania wydajności zależy od konteneryzowanych agenta usługi Log Analytics dla opracowane specjalnie dla usługi Azure Monitor, dla kontenerów systemu Linux. To wyspecjalizowane agenta służy do zbierania danych dotyczących zdarzeń i wydajności ze wszystkich węzłów w klastrze, a agent automatycznie wdrożeniu i zarejestrowaniu z określonym obszarem roboczym usługi Log Analytics podczas wdrażania. Wersja agenta jest microsoft / oms:ciprod04202018 lub nowszej, a jest reprezentowane przez datę w następującym formacie: *mmddyyyy*. 

>[!NOTE]
>Za pomocą obsługi systemu Windows Server dla usługi AKS w wersji zapoznawczej klastra usługi AKS w taki sposób, w przypadku węzłów systemu Windows Server nie zainstalowano agenta do zbierania danych i przekazywać je do usługi Azure Monitor. Zamiast tego węzła systemu Linux, automatycznie wdrożone w klastrze jako część wdrożenia standardowego zbiera i przesyła dane do usługi Azure Monitor w imieniu wszystkich węzłach klastra Windows.  
>

Po wydaniu nowej wersji agenta jest on automatycznie uaktualniany na zarządzanych klastrów Kubernetes hostowanych na platformie Azure Kubernetes Service (AKS). Aby skorzystać z wersji wydanych, zobacz [anonse wersji agenta](https://github.com/microsoft/docker-provider/tree/ci_feature_prod). 

>[!NOTE] 
>Jeśli masz już wdrożone w klastrze AKS, Włącz monitorowanie przy użyciu wiersza polecenia platformy Azure lub podanego szablonu Azure Resource Manager, jak pokazano w dalszej części tego artykułu. Nie można użyć `kubectl` do uaktualnienia, Usuń, Wdróż ponownie lub wdrożyć agenta. Szablon musi zostać wdrożony w tej samej grupie zasobów co klaster.

Można włączyć usługi Azure Monitor kontenerów przy użyciu jednej z następujących metod opisanych w poniższej tabeli.

| Stan wdrożenia | Metoda | Opis | 
|------------------|--------|-------------| 
| Nowy klaster AKS | [Tworzenie klastra przy użyciu wiersza polecenia platformy Azure](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Aby umożliwić monitorowanie nowego klastra AKS utworzonego za pomocą wiersza polecenia platformy Azure. | 
| | [Tworzenie klastra przy użyciu programu Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Aby umożliwić monitorowanie nowy klaster AKS za pomocą narzędzia typu open-source programu Terraform utworzyć. | 
| Istniejący klaster usługi AKS | [Włącz przy użyciu wiersza polecenia platformy Azure](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Aby umożliwić monitorowanie klastra usługi AKS już wdrożenie przy użyciu wiersza polecenia platformy Azure. | 
| |[Włącz przy użyciu programu Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Aby umożliwić monitorowanie klastra usługi AKS już wdrożony za pomocą narzędzia typu open-source Terraform. | 
| | [Korzystanie z usługi Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Aby umożliwić monitorowanie co najmniej jeden klaster AKS został już wdrożony ze strony wielu klastra AKS w usłudze Azure Monitor. | 
| | [Korzystanie z klastra AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Aby umożliwić monitorowanie bezpośrednio z klastra usługi AKS w witrynie Azure portal. | 
| | [Włącz przy użyciu szablonu usługi Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Aby umożliwić monitorowanie klastra usługi AKS przy użyciu wstępnie skonfigurowanego szablonu usługi Azure Resource Manager. | 

## <a name="next-steps"></a>Kolejne kroki

* Przy użyciu funkcji monitorowania, włączyć przechwytywania metryk kondycji dla węzłów klastra usługi AKS i zasobników, te metryki kondycji są dostępne w witrynie Azure portal. Aby dowiedzieć się, jak używać usługi Azure Monitor dla kontenerów, zobacz [widok usługi Azure Kubernetes Service health](container-insights-analyze.md).
