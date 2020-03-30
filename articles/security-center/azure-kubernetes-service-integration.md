---
title: Usługa Azure Security Center i usługa Azure Kubernetes
description: Dowiedz się więcej o integracji usługi Azure Security Center z usługami Azure Kubernetes
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: d1cd4691586b27282d221a19c5fb7a1af034ed6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125159"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Integracja usług Kubernetes z usługą Azure z usługą Security Center

Usługa Azure Kubernetes Service (AKS) to usługa zarządzana przez firmę Microsoft do tworzenia, wdrażania i zarządzania aplikacjami konteneryzowanymi. 

Użyj usługi AKS wraz ze standardową warstwą usługi Azure Security Center (zobacz [cennik),](security-center-pricing.md)aby uzyskać lepszy wgląd w węzły usługi AKS, ruch w chmurze i mechanizmy kontroli zabezpieczeń.

Usługa Security Center przynosi korzyści w zakresie zabezpieczeń klastrom usługi AKS przy użyciu danych już zebranych przez węzeł główny usługi AKS. 

![Omówienie wysokiego poziomu usługi Azure Security Center i usługi Azure Kubernetes (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Razem te dwa narzędzia tworzą najlepszą ofertę zabezpieczeń Kubernetes natywną dla chmury. 

## <a name="benefits-of-integration"></a>Korzyści z integracji

Korzystanie z tych dwóch usług razem zapewnia:

* **Zalecenia dotyczące zabezpieczeń** — usługa Security Center identyfikuje zasoby usługi AKS i kategoryzuje je: od klastrów po poszczególne maszyny wirtualne. Następnie można wyświetlić zalecenia dotyczące zabezpieczeń dla zasobu. Aby uzyskać więcej informacji, zobacz zalecenia dotyczące kontenerów na [liście zaleceń referencyjnych](recommendations-reference.md#recs-computeapp). 

* **Wzmacnianie środowiska** — usługa Security Center stale monitoruje konfigurację klastrów kubernetes i konfiguracji platformy Docker. Następnie generuje zalecenia dotyczące zabezpieczeń, które odzwierciedlają standardy branżowe.

* **Ochrona w czasie wykonywania** — dzięki ciągłej analizie następujących źródeł usługi AKS Usługa Security Center ostrzega o zagrożeniach i złośliwych działaniach wykrytych na poziomie hosta *i* klastra AKS:
    * Surowe zdarzenia zabezpieczeń, takie jak dane sieciowe i tworzenie procesów
    * Dziennik inspekcji Kubernetes

    Aby uzyskać więcej informacji, zobacz [ochrona przed zagrożeniami dla kontenerów platformy Azure](threat-protection.md#azure-containers)

    Aby uzyskać listę możliwych alertów, zobacz te sekcje w tabeli odwołania alertów: [Alerty poziomu klastra AKS](alerts-reference.md#alerts-akscluster) i [alerty na poziomie hosta kontenera](alerts-reference.md#alerts-containerhost).  

![Usługi Azure Security Center i Azure Kubernetes Service (AKS) bardziej szczegółowo](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Niektóre dane skanowane przez usługę Azure Security Center ze środowiska usługi Kubernetes mogą zawierać poufne informacje.


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o funkcjach zabezpieczeń kontenerów usługi Security Center, zobacz:

* [Usługa Azure Security Center i zabezpieczenia kontenerów](container-security.md)

* [Integracja z usługą Azure Container Registry](azure-container-registry-integration.md)

* [Zarządzanie danymi w firmie Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) — w tym zasady dotyczące danych usług firmy Microsoft (w tym platformy Azure, usługi Intune i usługi Office 365), szczegółowe informacje na temat zarządzania danymi firmy Microsoft oraz zasady przechowywania, które mają wpływ na dane użytkownika