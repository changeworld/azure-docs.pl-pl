---
title: Przykłady i szablony usługi Azure Lighthouse
description: Te przykłady i szablony usługi Azure Resource Manager przedstawiają, jak dołączać klientów na potrzeby zarządzania zasobami delegowanymi na platformie Azure i obsługi scenariuszy usługi Azure Lighthouse.
author: JnHs
manager: carmonm
ms.service: lighthouse
ms.topic: sample
ms.date: 10/17/2019
ms.author: jenhayes
ms.openlocfilehash: 6d47534026b6fe815f9756a74ba3438dc67a8e02
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553687"
---
# <a name="azure-lighthouse-samples"></a>Przykłady usługi Azure Lighthouse

Poniższa tabela zawiera linki do kluczowych szablonów usługi Azure Resource Manager dla usługi Azure Lighthouse. Te i inne pliki można także znaleźć w [repozytorium przykładów usługi Azure Lighthouse](https://github.com/Azure/Azure-Lighthouse-samples/).

## <a name="onboarding-customers-to-azure-delegated-resource-management"></a>Dołączanie klientów do zarządzania zasobami delegowanymi na platformie Azure

Udostępniamy różne szablony dla określonych scenariuszy dołączania. Wybierz najlepiej działającą opcję i pamiętaj, aby zmodyfikować plik parametrów w celu odzwierciedlenia środowiska. Aby uzyskać więcej informacji o sposobach korzystania z tych plików we wdrożeniu, zobacz [Dołączanie klienta do zarządzania zasobami delegowanymi na platformie Azure](../how-to/onboard-customer.md).

| **Szablon** | **Opis** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Dołączanie subskrypcji klienta do zarządzania zasobami delegowanymi na platformie Azure. Dla każdej subskrypcji należy wykonać oddzielne wdrożenie. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Dołączanie jednej lub większej liczby grup zasobów klienta do zarządzania zasobami delegowanymi na platformie Azure. Użyj właściwości **rgDelegatedResourceManagement** dla pojedynczej grupy zasobów lub właściwości **multipleRgDelegatedResourceManagement**, aby dołączyć wiele grup zasobów w tej samej subskrypcji. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | Jeśli [opublikowano ofertę usługi zarządzanej w witrynie Azure Marketplace](../how-to/publish-managed-services-offers.md), możesz opcjonalnie użyć tego szablonu do dołączenia zasobów dla klientów, którzy zaakceptowali ofertę. Wartości z witryny Marketplace w pliku parametrów muszą być zgodne z wartościami użytymi podczas publikowania oferty. |

Zazwyczaj potrzebne jest osobne wdrożenie dla każdej dołączanej subskrypcji, ale możesz także wdrożyć szablony w wielu subskrypcjach.

| **Szablon** | **Opis** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Wdrażanie szablonów usługi Azure Resource Manager w wielu subskrypcjach. |

## <a name="azure-policy"></a>Azure Policy

Te przykłady pokazują, jak używać usługi Azure Policy z subskrypcjami, które zostały dołączone na potrzeby zarządzania zasobami delegowanymi na platformie Azure.

| **Szablon** | **Opis** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) | Przypisuje zasady, które dodają tag do subskrypcji delegowanej lub go z niej usuwają (za pomocą efektu modify). Aby uzyskać więcej informacji, zobacz [Wdrażanie zasad, które można skorygować w ramach subskrypcji delegowanej](../how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-audit-delegation) | Przypisuje zasady, które będą przeprowadzać inspekcję przypisań delegacji. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring) | Przypisuje zasady, które umożliwiają diagnostykę zasobów usługi Azure Key Vault w subskrypcjach delegowanych (przy użyciu efektu deployIfNotExists). Aby uzyskać więcej informacji, zobacz [Wdrażanie zasad, które można skorygować w ramach subskrypcji delegowanej](../how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-sub-monitoring) | Przypisuje kilka zasad w celu włączenia diagnostyki w subskrypcji delegowanej oraz łączy wszystkie maszyny wirtualne z systemem Windows i Linux z obszarem roboczym usługi Log Analytics utworzonym przez te zasady. Aby uzyskać więcej informacji, zobacz [Wdrażanie zasad, które można skorygować w ramach subskrypcji delegowanej](../how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-initiative) | Stosuje inicjatywę (wiele powiązanych definicji zasad) do subskrypcji delegowanej. |

## <a name="azure-monitor"></a>Azure Monitor

Te przykłady pokazują, jak za pomocą usługi Azure Monitor tworzyć alerty dla subskrypcji, które zostały dołączone na potrzeby zarządzania zasobami delegowanymi na platformie Azure.

| **Szablon** | **Opis** |
|---------|---------|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | Ten szablon tworzy alert platformy Azure i nawiązuje połączenie z istniejącą grupą akcji.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | Tworzy wiele alertów dziennika opartych na zapytaniach języka Kusto.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | Wdraża alert w dzierżawie, gdy użytkownik deleguje subskrypcję do dzierżawy zarządzającej.|

## <a name="additional-cross-tenant-scenarios"></a>Dodatkowe scenariusze dla wielu dzierżaw

Te przykłady ilustrują różne zadania, które można wykonywać w scenariuszach zarządzania wieloma dzierżawami.

| **Szablon** | **Opis** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | Wdraża konta magazynu w dwóch różnych grupach zasobów.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | Tworzy usługi zarządzania platformą Azure, łączy je i wdraża dodatkowe rozwiązania. Aby uzyskać kompleksowe wdrożenie, użyj szablonu **rgWithAzureMgmt.json**. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | Włącza i konfiguruje usługę Azure Security Center w docelowej subskrypcji platformy Azure. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-sentinel) | Wdraża i włącza rozwiązanie Azure Sentinel w istniejącym obszarze roboczym usługi Log Analytics w subskrypcji delegowanej. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-log-analytics-vm-extensions) | Te szablony umożliwiają wdrożenie rozszerzeń maszyn wirtualnych usługi Log Analytics na maszynach wirtualnych z systemem Windows i Linux, łącząc je ze wskazanym obszarem roboczym usługi Log Analytics |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zarządzaniu zasobami delegowanymi na platformie Azure](../concepts/azure-delegated-resource-management.md).
- Zapoznaj się z [Repozytorium przykładów usługi Azure Lighthouse](https://github.com/Azure/Azure-Lighthouse-samples/).
