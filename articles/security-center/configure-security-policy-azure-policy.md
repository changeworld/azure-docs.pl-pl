---
title: Tworzenie i edytowanie zasad zabezpieczeń usługi Azure Policy przy użyciu interfejsu API REST
description: Dowiedz się więcej o zarządzaniu zasadami usługi Azure za pośrednictwem interfejsu API REST.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: c218b5dc8ca3bfa0358a9b6a0d4867696762a8d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77430945"
---
# <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Konfigurowanie zasad zabezpieczeń w usłudze Azure Policy przy użyciu interfejsu API REST

W ramach natywnej integracji z usługą Azure Policy usługa Azure Security Center umożliwia korzystanie z interfejsu API REST usługi Azure Policy w celu tworzenia przypisań zasad. Poniższe instrukcje umożliwiają tworzenie przypisań zasad, a także dostosowywanie istniejących przydziałów. 

Ważne pojęcia w zasadach platformy Azure: 

- **Definicja zasad** jest regułą 

- **Inicjatywa** jest zbiorem definicji zasad (reguł) 

- **Przypisanie** jest zastosowaniem inicjatywy lub zasady do określonego zakresu (grupa zarządzania, subskrypcja itp.) 

Usługa Security Center ma wbudowaną inicjatywę, która obejmuje wszystkie jego zasady zabezpieczeń. Aby ocenić zasady usługi Security Center w zasobach platformy Azure, należy utworzyć przypisanie w grupie zarządzania lub subskrypcji, którą chcesz ocenić.

Wbudowana inicjatywa ma domyślnie włączone wszystkie zasady usługi Security Center. Można wyłączyć niektóre zasady z wbudowanej inicjatywy. Na przykład, aby zastosować wszystkie zasady usługi Security Center z wyjątkiem **zapory aplikacji sieci web,** zmień wartość parametru efektu zasad na **Wyłączone**. 

## <a name="api-examples"></a>Przykłady dotyczące interfejsu API

W poniższych przykładach zastąp te zmienne:

- **{scope}** wprowadź nazwę grupy zarządzania lub subskrypcji, do której stosujesz zasady.
- **{policyAssignmentName}** wprowadź [nazwę odpowiedniego przypisania zasad](#policy-names).
- **{name}** wprowadź swoje imię i nazwisko lub nazwę administratora, który zatwierdził zmianę zasad.

W tym przykładzie pokazano, jak przypisać wbudowaną inicjatywę usługi Security Center w ramach subskrypcji lub grupy zarządzania
 
 ```
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
 ```

W tym przykładzie pokazano, jak przypisać wbudowaną inicjatywę Centrum zabezpieczeń w ramach subskrypcji, z wyłączonymi następującymi zasadami: 

- Aktualizacje systemu ("systemUpdatesMonitoringEffect") 

- Konfiguracje zabezpieczeń ("systemConfigurationsMonitoringEffect") 

- Ochrona punktów końcowych ("endpointProtectionMonitoringEffect") 

 ```
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
 ```
W tym przykładzie pokazano, jak usunąć przypisanie:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

## <a name="policy-names-reference"></a>Odwołanie do nazw zasad<a name="policy-names"></a>

|Nazwa zasad w Centrum zabezpieczeń|Nazwa zasad wyświetlana w usłudze Azure Policy |Nazwa parametru efektu zasad|
|----|----|----|
|Szyfrowanie SQL |Monitorowanie niezaszyfrowanej bazy danych SQL w usłudze Azure Security Center |sqlEncryptionMonitoringEffect| 
|Inspekcja SQL |Monitorowanie niezbadanych baz danych SQL w usłudze Azure Security Center |sqlAuditingMonitoringEffect|
|Aktualizacje systemu |Monitorowanie brakujących aktualizacji systemu w usłudze Azure Security Center |systemUpdatesMonitoringEffect|
|Szyfrowanie w usłudze Storage |Należy wykonać inspekcję pod kątem braku szyfrowania obiektów blob dla kont magazynu |storageEncryptionMonitoringEffect|
|Dostęp do sieci JIT |Monitorowanie możliwego dostępu do sieci just-in-time (JIT) w usłudze Azure Security Center |jitNetworkAccessMonitoringEfekt |
|Funkcje adaptacyjnego sterowania aplikacjami |Monitorowanie białej listy aplikacji w usłudze Azure Security Center |adaptiveApplicationControlsMonitoringEffect|
|Grupy zabezpieczeń sieci |Monitorowanie dopuszczalnego dostępu do sieci w usłudze Azure Security Center |networkSecurityGroupsMonitoringEfekt| 
|Konfiguracje zabezpieczeń |Monitorowanie luk w zabezpieczeniach systemu operacyjnego w usłudze Azure Security Center |systemConfigurationsMonitoringEffect| 
|Ochrona punktów końcowych |Monitorowanie braku ochrony punktów końcowych w usłudze Azure Security Center |endpointProtectionMonitoringEffect |
|Szyfrowanie dysków |Monitorowanie niezaszyfrowanych dysków maszyn wirtualnych w usłudze Azure Security Center |diskEncryptionMonitoringEffect|
|Ocena luk w zabezpieczeniach |Monitorowanie luk w zabezpieczeniach maszyny Wirtualnej w usłudze Azure Security Center |vulnerabilityAssessmentMonitoringEffect|
|Zapora aplikacji internetowej |Monitorowanie niechronionej aplikacji sieci Web w usłudze Azure Security Center |webApplicationFirewallMonitoringEffect |
|Zapora nowej generacji |Monitorowanie niechronionych punktów końcowych sieci w usłudze Azure Security Center| |


## <a name="next-steps"></a>Następne kroki

W przypadku innych powiązanych materiałów zobacz następujące artykuły: 

- [Niestandardowe zasady zabezpieczeń](custom-security-policies.md)
- [Omówienie zasad zabezpieczeń](tutorial-security-policy.md)