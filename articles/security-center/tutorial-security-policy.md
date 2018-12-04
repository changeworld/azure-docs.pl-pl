---
title: Edytowanie zasad zabezpieczeń usługa Azure Policy | Dokumentacja firmy Microsoft
description: Edytowanie zasad zabezpieczeń usługa Azure Policy.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: rkarlin
ms.openlocfilehash: d6cc216f71efcd3b3973cd37349dd5145237f02f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52839334"
---
# <a name="edit-security-policies-in-azure-policy"></a>Edytowanie zasad zabezpieczeń usługa Azure Policy
Centrum zabezpieczeń ułatwia wyświetlanie stanu zasad zabezpieczeń i jak są one stosowane w różnych obciążeń. Usługa Azure Security Center automatycznie przypisuje jej [zasad wbudowanych rozwiązań zabezpieczeń](security-center-policy-definitions.md) w każdej subskrypcji, która jest dołączona. Można skonfigurować je w [usługi Azure Policy](../azure-policy/azure-policy-introduction.md), lub za pomocą interfejsu API REST, który umożliwia także ustawić zasady w grupach zarządzania i w ramach wielu subskrypcji. Aby uzyskać więcej informacji, zobacz temat [Integracja zasad zabezpieczeń w usłudze Security Center z usługą Azure Policy](security-center-azure-policy.md). Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie zasad zabezpieczeń przy użyciu interfejsu API REST
> * Ocenianie zabezpieczeń zasobów

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Wymagania wstępne
Aby wypróbować funkcje omówione w tym samouczku, musisz korzystać ze standardowej warstwy cenowej usługi Security Center. Możesz spróbować standardowej usługi Security Center bez ponoszenia kosztów. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/security-center/). Przewodnik szybkiego startu [Dołączanie subskrypcji platformy Azure do standardowej usługi Security Center](security-center-get-started.md) przeprowadzi Cię przez procedurę uaktualniania do warstwy standardowej.

## <a name="configure-a-security-policy-using-the-rest-api"></a>Konfigurowanie zasad zabezpieczeń przy użyciu interfejsu API REST

W ramach natywnej integracji z usługą Azure Policy Azure Security Center pozwala na przejęcie interfejsu API REST zaletą usługi Azure Policy w celu utworzenia przypisania zasad. Poniższe instrukcje opisują Tworzenie przypisania zasad, a także dostosowywania istniejących przypisań. 

Ważne pojęcia dotyczące usługi Azure Policy: 

- A **definicji zasad** jest regułą 

-  **Inicjatywy** to kolekcja definicji zasad (zasad) 

-  **Przypisania** stosowania zasad lub inicjatywy do określonego zakresu (grupy zarządzania, subskrypcji, itp.) 

Usługa Security Center ma wbudowane inicjatywy, która obejmuje wszystkie jego zasadami zabezpieczeń. Aby ocenić zasady Centrum zabezpieczeń na zasoby platformy Azure, należy utworzyć przypisania na grupę zarządzania lub subskrypcji, którą chcesz ocenić.  

Wbudowane inicjatywy zawiera wszystkie zasady Centrum zabezpieczeń, domyślnie włączone. Użytkownik może wyłączyć określonych zasad z wbudowaną inicjatywy, na przykład można zastosować wszystkie zasady Centrum zabezpieczeń, z wyjątkiem **zapory aplikacji sieci web**, zmieniając wartość parametru efekt zasad  **Wyłączone**. 

### <a name="api-examples"></a>Przykłady interfejsu API

W poniższych przykładach należy zastąpić następujące zmienne:

- **{zakresu}**  wprowadź nazwę grupy zarządzania lub subskrypcji są stosowane zasady.
- **{poicyAssignmentName}**  wprowadź [nazwa przypisania zasad odpowiednich](#policy-names).
- **{name}**  wprowadź swoją nazwę lub nazwę administratora, który zatwierdzone zmiany zasad.

W tym przykładzie pokazano, jak przypisać wbudowanych inicjatywy Centrum zabezpieczeń dla subskrypcji lub grupy zarządzania
 
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 

W tym przykładzie pokazano, jak przypisać wbudowanych inicjatywy Centrum zabezpieczeń w ramach subskrypcji za pomocą następujących zasad, które są wyłączone: 

- Aktualizacje systemu ("systemUpdatesMonitoringEffect") 

- Konfiguracje zabezpieczeń ("systemConfigurationsMonitoringEffect") 

- Program Endpoint protection ("endpointProtectionMonitoringEffect") 

 
      PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

      Request Body (JSON) 

      { 

        "properties":{ 

      "displayName":"Enable Monitoring in Azure Security Center", 

      "metadata":{ 

      "assignedBy":"{Name}" 

      }, 

      "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

      "parameters":{ 

      "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 

      "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 

      "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 

      }, 

       } 

      } 

W tym przykładzie pokazano, jak usunąć przypisanie:

    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 


## Odwołanie do nazwy zasad <a name="policy-names"></a>

|Nazwa zasad w usłudze Security Center|Nazwa zasad wyświetlana w usługi Azure Policy |Nazwa parametru efekt zasad|
|----|----|----|
|Szyfrowanie SQL |Monitoruj nieszyfrowaną bazę danych SQL w usłudze Azure Security Center |sqlEncryptionMonitoringEffect| 
|Inspekcja SQL |Monitorowanie bazy danych SQL bez inspekcji w usłudze Azure Security Center |sqlAuditingMonitoringEffect|
|Aktualizacje systemu |Monitoruj brakujące aktualizacje systemu w usłudze Azure Security Center |systemUpdatesMonitoringEffect|
|Szyfrowanie w usłudze Storage |Przeprowadź inspekcję brakującego szyfrowania elementu blob dla kont magazynu |storageEncryptionMonitoringEffect|
|Dostęp do sieci JIT |Monitoruj ewentualny dostęp tylko w czas (JIT) w usłudze Azure Security Center |jitNetworkAccessMonitoringEffect |
|Funkcje adaptacyjnego sterowania aplikacjami |Monitor możliwe listę dozwolonych aplikacji w usłudze Azure Security Center |adaptiveApplicationControlsMonitoringEffect|
|Grupy zabezpieczeń sieci |Monitoruj liberalny dostęp do sieci w Centrum zabezpieczeń Azure |networkSecurityGroupsMonitoringEffect| 
|Konfiguracje zabezpieczeń |Monitorowanie luk w zabezpieczeniach systemu operacyjnego, w usłudze Azure Security Center |systemConfigurationsMonitoringEffect| 
|Ochrona punktów końcowych |Monitorowanie brakującej ochrony punktów końcowych w usłudze Azure Security Center |endpointProtectionMonitoringEffect |
|Szyfrowanie dysków |Monitoruj nieszyfrowane dyski maszyn wirtualnych w usłudze Azure Security Center |diskEncryptionMonitoringEffect|
|Ocena luk w zabezpieczeniach |Monitorowanie maszyn wirtualnych luk w zabezpieczeniach w usłudze Azure Security Center |vulnerabilityAssesmentMonitoringEffect|
|Zapora aplikacji internetowej |Monitoruj niechronione aplikacje internetowe w usłudze Azure Security Center |webApplicationFirewallMonitoringEffect |
|Zapora nowej generacji |Monitoruj niechronione punkty końcowe sieci w Centrum zabezpieczeń Azure| |





## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób edytowanie zasad zabezpieczeń usługa Azure Policy. Aby dowiedzieć się więcej na temat usługi Security Center, zobacz następujące artykuły:

* [Przewodnik planowania i obsługi usługi Azure Security Center](security-center-planning-and-operations-guide.md) — informacje na temat planowania i zagadnień projektowych podczas wdrażania usługi Azure Security Center.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Uzyskiwanie wglądu na poziomie dzierżawy w usłudze Azure Security Center](security-center-management-groups.md) — informacje na temat sposobu konfigurowania grup zarządzania dla usługi Azure Security Center.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.

Aby dowiedzieć się więcej na temat usługi Azure Policy, zobacz [Co to jest Azure Policy?](../azure-policy/azure-policy-introduction.md)
