---
title: Jak włączyć Azure Monitor dla kontenerów | Microsoft Docs
description: W tym artykule opisano sposób włączania i konfigurowania Azure Monitor dla kontenerów, dzięki czemu można zrozumieć, jak działa kontener i jakie problemy związane z wydajnością zostały zidentyfikowane.
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
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: 25be8f166fec8a311fdc2ed1fa3fca6339185e94
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2019
ms.locfileid: "67867533"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Jak włączyć Azure Monitor dla kontenerów

Ten artykuł zawiera omówienie opcji dostępnych w celu skonfigurowania Azure Monitor dla kontenerów w celu monitorowania wydajności obciążeń wdrożonych w środowiskach Kubernetes i hostowanych w [usłudze Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/).

Usługa Azure Monitor dla kontenerów można włączyć dla nowych lub obsługiwane przez jedną lub więcej istniejących wdrożeń usługi AKS przy użyciu następujących metod:

* Z Azure Portal, Azure PowerShell lub z interfejsem wiersza polecenia platformy Azure
* Za pomocą [narzędzia Terraform i AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że dysponujesz następującymi elementami:

* **Obszar roboczy Log Analytics.**

    Azure Monitor dla kontenerów obsługuje obszar roboczy Log Analytics w regionach wymienionych w produktach platformy Azure [według regionów](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor), z wyłączeniem regionu **US gov Wirginia**.

    Można utworzyć obszar roboczy po włączeniu monitorowania nowego klastra usługi AKS lub umożliwieniu funkcji dołączania tworzenie domyślnego obszaru roboczego w domyślnej grupie zasobów subskrypcji klastra AKS. Jeśli chcesz utworzyć samodzielnie, możesz je utworzyć za pomocą [usługi Azure Resource Manager](../platform/template-workspace-configuration.md)za pośrednictwem [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), lub [witryny Azure portal](../learn/quick-create-workspace.md). Aby uzyskać listę obsługiwanych par mapowania używanych dla domyślnego obszaru roboczego, zobacz [Mapowanie regionów dla Azure monitor kontenerów](container-insights-region-mapping.md).

* Musisz być członkiem **roli współautor log Analytics** , aby umożliwić monitorowanie kontenerów. Aby uzyskać więcej informacji na temat kontrolowania dostępu do obszaru roboczego usługi Log Analytics, zobacz [możesz zarządzać obszarami roboczymi](../platform/manage-access.md).

* Jesteś członkiem roli **[właściciela](../../role-based-access-control/built-in-roles.md#owner)** w zasobie klastra AKS.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* Metryki Prometheus nie są domyślnie zbierane. Przed [skonfigurowaniem agenta](container-insights-agent-config.md) do zbierania danych należy zapoznać się z [dokumentacją](https://prometheus.io/) Prometheus, aby zrozumieć, co można zdefiniować.

## <a name="components"></a>Składniki

Możliwość monitorowania wydajności zależy od konteneryzowanych agenta usługi Log Analytics dla opracowane specjalnie dla usługi Azure Monitor, dla kontenerów systemu Linux. To wyspecjalizowane agenta służy do zbierania danych dotyczących zdarzeń i wydajności ze wszystkich węzłów w klastrze, a agent automatycznie wdrożeniu i zarejestrowaniu z określonym obszarem roboczym usługi Log Analytics podczas wdrażania. Wersja agenta jest microsoft / oms:ciprod04202018 lub nowszej, a jest reprezentowane przez datę w następującym formacie: *mmddyyyy*.

>[!NOTE]
>W wersji zapoznawczej obsługi systemu Windows Server dla programu AKS klaster AKS z węzłami systemu Windows Server nie ma zainstalowanego agenta do zbierania danych i przesyłania dalej do Azure Monitor. Zamiast tego węzeł systemu Linux jest automatycznie wdrażany w klastrze w ramach standardowego wdrożenia zbiera dane i przekazuje je do Azure Monitor w imieniu wszystkich węzłów Windows w klastrze.  
>

Po wydaniu nowej wersji agenta jest on automatycznie uaktualniany na zarządzanych klastrów Kubernetes hostowanych na platformie Azure Kubernetes Service (AKS). Aby skorzystać z wersji wydanych, zobacz [anonse wersji agenta](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

>[!NOTE]
>Jeśli masz już wdrożone w klastrze AKS, Włącz monitorowanie przy użyciu wiersza polecenia platformy Azure lub podanego szablonu Azure Resource Manager, jak pokazano w dalszej części tego artykułu. Nie można użyć `kubectl` do uaktualnienia, Usuń, Wdróż ponownie lub wdrożyć agenta.
>Szablon musi zostać wdrożony w tej samej grupie zasobów co klaster.

Azure Monitor dla kontenerów można włączyć za pomocą jednej z następujących metod opisanych w poniższej tabeli.

| Stan wdrożenia | Metoda | Opis |
|------------------|--------|-------------|
| Nowy klaster AKS | [Tworzenie klastra przy użyciu interfejsu wiersza polecenia platformy Azure](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Można włączyć monitorowanie nowego klastra AKS utworzonego za pomocą interfejsu wiersza polecenia platformy Azure. |
| | [Tworzenie klastra przy użyciu Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Można włączyć monitorowanie nowego klastra AKS utworzonego za pomocą narzędzia typu open source Terraform. |
| Istniejący klaster AKS | [Włączanie przy użyciu interfejsu wiersza polecenia platformy Azure](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Możesz włączyć monitorowanie klastra AKS już wdrożonego za pomocą interfejsu wiersza polecenia platformy Azure. |
| |[Włącz korzystanie z Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Można włączyć monitorowanie klastra AKS już wdrożonego za pomocą narzędzia typu "open source" Terraform. |
| | [Włącz z Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Można włączyć monitorowanie jednego lub więcej klastrów AKS już wdrożonych ze strony AKS wielu klastrów w Azure Monitor. |
| | [Włącz z klastra AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Monitorowanie można włączyć bezpośrednio z klastra AKS w Azure Portal. |
| | [Włączanie przy użyciu szablonu Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Można włączyć monitorowanie klastra AKS przy użyciu wstępnie skonfigurowanego szablonu Azure Resource Manager. |

## <a name="next-steps"></a>Następne kroki

* Przy użyciu funkcji monitorowania, włączyć przechwytywania metryk kondycji dla węzłów klastra usługi AKS i zasobników, te metryki kondycji są dostępne w witrynie Azure portal. Aby dowiedzieć się, jak używać usługi Azure Monitor dla kontenerów, zobacz [widok usługi Azure Kubernetes Service health](container-insights-analyze.md).
