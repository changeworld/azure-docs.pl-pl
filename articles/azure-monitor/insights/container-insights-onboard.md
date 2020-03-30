---
title: Jak włączyć usługę Azure Monitor dla kontenerów | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób włączania i konfigurowania usługi Azure Monitor dla kontenerów, dzięki czemu można zrozumieć, jak kontener działa i jakie problemy związane z wydajnością zostały zidentyfikowane.
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: 7aad7e7dd5ec2569377f9276c2e4793c7afd631a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275310"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Jak włączyć usługę Azure Monitor dla kontenerów

Ten artykuł zawiera omówienie opcji dostępnych do skonfigurowania usługi Azure Monitor dla kontenerów w celu monitorowania wydajności obciążeń, które są wdrażane w środowiskach Kubernetes i hostowane w:

- [Usługa Azure Kubernetes](https://docs.microsoft.com/azure/aks/) (AKS)

- Samodzielnie zarządzane klastry Kubernetes hostowane na platformie Azure przy użyciu [aparatu AKS](https://github.com/Azure/aks-engine).

- Samodzielnie zarządzane klastry Kubernetes hostowane w [usłudze Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) lub lokalnie przy użyciu aparatu AKS.

- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

Usługa Azure Monitor dla kontenerów może być włączona dla nowych lub co najmniej jednego istniejącego wdrożenia usługi Kubernetes przy użyciu następujących obsługiwanych metod:

- Z witryny Azure Portal, usługi Azure PowerShell lub z platformą Azure CLI

- Korzystanie z [Terraform i AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące elementy:

- **Obszar roboczy usługi Log Analytics.**

    Usługa Azure Monitor dla kontenerów obsługuje obszar roboczy usługi Log Analytics w regionach wymienionych w usłudze Azure [Products według regionów.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)

    Obszar roboczy można utworzyć po włączeniu monitorowania nowego klastra AKS lub umożliwieniu środowiska dołączania utworzenia domyślnego obszaru roboczego w domyślnej grupie zasobów subskrypcji klastra AKS. Jeśli zdecydujesz się utworzyć go samodzielnie, możesz go utworzyć za pośrednictwem [usługi Azure Resource Manager](../platform/template-workspace-configuration.md), za pośrednictwem programu [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)lub w [witrynie Azure portal](../learn/quick-create-workspace.md). Aby uzyskać listę obsługiwanych par mapowania używanych dla domyślnego obszaru roboczego, zobacz [Mapowanie regionu dla usługi Azure Monitor dla kontenerów](container-insights-region-mapping.md).

- Jesteś członkiem **roli współautora usługi Log Analytics,** aby włączyć monitorowanie kontenera. Aby uzyskać więcej informacji na temat kontrolowania dostępu do obszaru roboczego usługi Log Analytics, zobacz [Zarządzanie obszarami roboczymi](../platform/manage-access.md).

- Jesteś członkiem roli **[Właściciel](../../role-based-access-control/built-in-roles.md#owner)** zasobu klastra AKS.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* Metryki Prometheus nie są zbierane domyślnie. Przed [skonfigurowaniem agenta](container-insights-prometheus-integration.md) do ich zbierania, ważne jest, aby przejrzeć [dokumentację](https://prometheus.io/) Prometheus, aby zrozumieć, co można zeskrobać i metody obsługiwane.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje

Następujące są oficjalnie obsługiwane w usłudze Azure Monitor dla kontenerów.

- Środowiska: Azure Red Hat OpenShift, Kubernetes lokalnie i aparat AKS na platformie Azure i usłudze Azure Stack. Aby uzyskać więcej informacji, zobacz [Aparat AKS w usłudze Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- Wersje usługi Kubernetes i zasady pomocy technicznej są takie same jak wersje [obsługiwanych przez program AKS](../../aks/supported-kubernetes-versions.md). 

## <a name="network-firewall-requirements"></a>Wymagania dotyczące zapory sieciowej

Informacje w poniższej tabeli zawiera listę informacji o konfiguracji serwera proxy i zapory wymagane dla konteneryzowanego agenta do komunikowania się z usługą Azure Monitor dla kontenerów. Cały ruch sieciowy z agenta jest wychodzący do usługi Azure Monitor.

|Zasób agenta|Porty |
|--------------|------|
| *.ods.opinsights.azure.com | 443 |  
| *.oms.opinsights.azure.com | 443 | 
| *.blob.core.windows.net | 443 |
| dc.services.visualstudio.com | 443 |
| *.microsoftonline.com | 443 |
| *.monitoring.azure.com | 443 |
| login.microsoftonline.com | 443 |

Informacje zawarte w poniższej tabeli zawierają informacje o konfiguracji serwera proxy i zapory dla platformy Azure w Chinach.

|Zasób agenta|Porty |Opis | 
|--------------|------|-------------|
| *.ods.opinsights.azure.cn | 443 | Wprowadzanie danych |
| *.oms.opinsights.azure.cn | 443 | Dołączanie do oms |
| *.blob.core.windows.net | 443 | Służy do monitorowania połączeń wychodzących. |
| microsoft.com | 80 | Używany do łączności sieciowej. Jest to wymagane tylko wtedy, gdy wersja obrazu agenta jest ciprod09262019 lub wcześniej. |
| dc.services.visualstudio.com | 443 | Dla danych telemetrycznych agenta przy użyciu usługi Azure Public Cloud Application Insights. |

Informacje zawarte w poniższej tabeli zawierają informacje o konfiguracji serwera proxy i zapory dla platformy Azure US Government.

|Zasób agenta|Porty |Opis | 
|--------------|------|-------------|
| *.ods.opinsights.azure.us | 443 | Wprowadzanie danych |
| *.oms.opinsights.azure.us | 443 | Dołączanie do oms |
| *.blob.core.windows.net | 443 | Służy do monitorowania połączeń wychodzących. |
| microsoft.com | 80 | Używany do łączności sieciowej. Jest to wymagane tylko wtedy, gdy wersja obrazu agenta jest ciprod09262019 lub wcześniej. |
| dc.services.visualstudio.com | 443 | Dla danych telemetrycznych agenta przy użyciu usługi Azure Public Cloud Application Insights. |

## <a name="components"></a>Składniki

Możliwość monitorowania wydajności zależy od konteneryzowanego agenta analizy dzienników dla systemu Linux opracowanego specjalnie dla usługi Azure Monitor dla kontenerów. Ten wyspecjalizowany agent zbiera dane o wydajności i zdarzenia ze wszystkich węzłów w klastrze, a agent jest automatycznie wdrażany i rejestrowany w określonym obszarze roboczym usługi Log Analytics podczas wdrażania. Wersja agenta to microsoft/oms:ciprod04202018 lub nowsza i jest reprezentowana przez datę w następującym formacie: *mmddyyyyyy .*

>[!NOTE]
>Po wydaniu wersji zapoznawczej obsługi systemu Windows Server dla usługi AKS klaster AKS z węzłami systemu Windows Server nie ma agenta zainstalowanego do zbierania danych i przesyłania dalej do usługi Azure Monitor. Zamiast tego węzeł systemu Linux automatycznie wdrożony w klastrze w ramach wdrożenia standardowego zbiera i przekazuje dane do usługi Azure Monitor w imieniu wszystkich węzłów systemu Windows w klastrze.  
>

Po wydaniu nowej wersji agenta jest ona automatycznie uaktualniana do zarządzanych klastrów kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS). Aby śledzić wydane wersje, zobacz [anonse wydania agenta](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

>[!NOTE]
>Jeśli już wdrożono klaster AKS, można włączyć monitorowanie przy użyciu interfejsu wiersza polecenia platformy Azure lub dostarczonego szablonu usługi Azure Resource Manager, jak pokazano w dalszej części tego artykułu. Nie można `kubectl` użyć do uaktualnienia, usunięcia, ponownego wdrożenia lub wdrożenia agenta.
>Szablon musi zostać wdrożony w tej samej grupie zasobów co klaster.

Włącz usługę Azure Monitor dla kontenerów przy użyciu jednej z następujących metod opisanych w poniższej tabeli.

| Stan wdrożenia | Metoda | Opis |
|------------------|--------|-------------|
| Nowy klaster AKS Kubernetes | [Tworzenie klastra AKS przy użyciu interfejsu wiersza polecenia platformy Azure](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Można włączyć monitorowanie nowego klastra AKS, który można utworzyć za pomocą interfejsu wiersza polecenia platformy Azure. |
| | [Tworzenie klastra AKS przy użyciu terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Można włączyć monitorowanie nowego klastra AKS, który tworzysz za pomocą narzędzia open source Terraform. |
| | [Tworzenie klastra OpenShift przy użyciu szablonu usługi Azure Resource Manager](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | Można włączyć monitorowanie nowego klastra OpenShift utworzonego za pomocą wstępnie skonfigurowanego szablonu usługi Azure Resource Manager. |
| | [Tworzenie klastra OpenShift przy użyciu interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) | Monitorowanie można włączyć podczas wdrażania nowego klastra OpenShift przy użyciu interfejsu wiersza polecenia platformy Azure. |
| Istniejący klaster AKS Kubernetes | [Włącz klaster AKS przy użyciu interfejsu wiersza polecenia platformy Azure](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Można włączyć monitorowanie klastra AKS już wdrożony przy użyciu interfejsu wiersza polecenia platformy Azure. |
| |[Włącz klaster AKS przy użyciu terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Monitorowanie klastra AKS już wdrożonego przy użyciu narzędzia open source Terraform. |
| | [Włącz klaster AKS z usługi Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Można włączyć monitorowanie jednego lub więcej klastrów AKS już wdrożonych na stronie wielu klastrów w usłudze Azure Monitor. |
| | [Włącz z klastra AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Monitorowanie można włączyć bezpośrednio z klastra AKS w witrynie Azure portal. |
| | [Włącz klaster AKS przy użyciu szablonu usługi Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Monitorowanie klastra usługi AKS można włączyć za pomocą wstępnie skonfigurowanego szablonu usługi Azure Resource Manager. |
| | [Włącz hybrydowy klaster Kubernetes](container-insights-hybrid-setup.md) | Można włączyć monitorowanie aparatu AKS hostowane w usłudze Azure Stack lub dla kubernetes hostowane lokalnie. |
| | [Włącz klaster OpenShift przy użyciu szablonu usługi Azure Resource Manager](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | Monitorowanie istniejącego klastra OpenShift można włączyć za pomocą wstępnie skonfigurowanego szablonu usługi Azure Resource Manager. |
| | [Włącz klaster OpenShift z usługi Azure Monitor](container-insights-azure-redhat-setup.md#from-the-azure-portal) | Można włączyć monitorowanie jednego lub więcej klastrów OpenShift już wdrożonych na stronie wielu klastrów w usłudze Azure Monitor. |

## <a name="next-steps"></a>Następne kroki

- Po włączeniu monitorowania można rozpocząć analizowanie wydajności klastrów kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS), usłudze Azure Stack lub innym środowisku. Aby dowiedzieć się, jak używać usługi Azure Monitor dla kontenerów, zobacz [Wyświetlanie wydajności klastra Kubernetes](container-insights-analyze.md).
