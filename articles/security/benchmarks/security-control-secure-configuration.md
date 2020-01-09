---
title: Kontrola zabezpieczeń Azure — bezpieczna konfiguracja
description: Bezpieczna konfiguracja kontroli zabezpieczeń
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 08458777d8a8c5c74ac20a63245135ffaf0a5c04
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564116"
---
# <a name="security-control-secure-configuration"></a>Kontrola zabezpieczeń: Konfiguracja zabezpieczona

Ustanów, Implementuj i aktywnie Zarządzaj (śledź, Przetwarzaj, Popraw) konfigurację zabezpieczeń zasobów platformy Azure, aby uniemożliwić osobom atakującym wykorzystanie zagrożonych usług i ustawień.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: Ustanów bezpieczne konfiguracje dla wszystkich zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 7.1 | 5.1 | Klient |

Użyj Azure Policy lub Azure Security Center, aby zachować konfiguracje zabezpieczeń dla wszystkich zasobów platformy Azure.

Jak skonfigurować Azure Policy i zarządzać nim: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="72-establish-secure-operating-system-configurations"></a>7,2: Ustanów bezpieczne konfiguracje systemów operacyjnych

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 7.2 | 5.1 | Klient |

Użyj Azure Security Center rekomendacja &quot;Koryguj luki w zabezpieczeniach w konfiguracjach zabezpieczeń na Virtual Machines&quot;, aby zachować konfiguracje zabezpieczeń we wszystkich zasobach obliczeniowych.

Jak monitorować zalecenia dotyczące Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-recommendations

Jak skorygować Azure Security Center zalecenia:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: obsługa bezpiecznych konfiguracji zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 7.3 | 5.2 | Klient |

Użyj zasad platformy Azure [Odmów] i [Wdróż, jeśli nie istnieje], aby wymusić bezpieczne ustawienia dla zasobów platformy Azure.

Jak skonfigurować Azure Policy i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Zrozumienie Azure Policy efektów:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

## <a name="74-maintain-secure-operating-system-configurations"></a>7,4: Zachowaj konfiguracje bezpiecznego systemu operacyjnego

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 7.4 | 5.2 | Współdzielona |

Podstawowe obrazy systemu operacyjnego są zarządzane i obsługiwane przez firmę Microsoft.

Można jednak zastosować ustawienia zabezpieczeń wymagane przez organizację przy użyciu szablonów Azure Resource Manager i/lub konfiguracji żądanego stanu.

Jak utworzyć maszynę wirtualną platformy Azure przy użyciu szablonu Azure Resource Manager:

https://docs.microsoft.com/azure/virtual-machines/windows/ps-template

Zapoznaj się z konfiguracją żądanego stanu dla Virtual Machines platformy Azure:

https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview

## <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: bezpiecznie przechowuj konfigurację zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 7.5 | 5.3 | Klient |

Jeśli używasz niestandardowych definicji zasad platformy Azure, Użyj usługi Azure DevOps lub Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

Jak przechowywać kod w usłudze Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentacja Azure Repos:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

## <a name="76-securely-store-custom-operating-system-images"></a>7,6: bezpieczne przechowywanie niestandardowych obrazów systemu operacyjnego

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 7.6 | 5.3 | Klient |

Jeśli używasz obrazów niestandardowych, użyj funkcji RBAC, aby zapewnić dostęp do obrazów tylko autoryzowanym użytkownikom. W przypadku obrazów kontenerów Zapisz je w Azure Container Registry i Skorzystaj z RBAC, aby zapewnić dostęp do obrazów tylko autoryzowanym użytkownikom.

Opis RBAC na platformie Azure:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Opis RBAC dla Container Registry:

https://docs.microsoft.com/azure/container-registry/container-registry-roles

Jak skonfigurować RBAC na platformie Azure:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

## <a name="77-deploy-system-configuration-management-tools"></a>7,7: Wdrażanie narzędzi do zarządzania konfiguracją systemu

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 7.7 | 5.4 | Klient |

Użyj Azure Policy, aby otrzymywać alerty, inspekcje i wymuszać konfiguracje systemu. Dodatkowo opracowuj proces i potok na potrzeby zarządzania wyjątkami zasad.

Jak skonfigurować Azure Policy i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 7.8 | 5.4 | Klient |

Użyj rozszerzeń obliczeniowych platformy Azure, takich jak Konfiguracja żądanego stanu programu PowerShell dla rozszerzenia Windows COMPUTE lub Linux Chef dla systemu Linux.

Jak zainstalować rozszerzenia maszyny wirtualnej na platformie Azure:

https://docs.microsoft.com/azure/virtual-machines/extensions/overview#how-can-i-install-an-extension

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: Zaimplementuj automatyczne monitorowanie konfiguracji dla usług platformy Azure

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 7.9 | 5.5 | Klient |

Użyj Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla zasobów platformy Azure

Jak skorygować zalecenia w Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: Zaimplementuj automatyczne monitorowanie konfiguracji dla systemów operacyjnych

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 7.1 | 5.5 | Klient |

Użyj Azure Security Center, aby przeprowadzić skanowanie linii bazowej dla ustawień systemu operacyjnego i platformy Docker dla kontenerów.

Informacje na temat zaleceń dotyczących kontenera Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-container-recommendations

## <a name="711-manage-azure-secrets-securely"></a>7,11: bezpieczne zarządzanie wpisami tajnymi platformy Azure 

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 7.11 | 13,1 | Klient |

Użyj tożsamość usługi zarządzanej w połączeniu z Azure Key Vault, aby uprościć i zabezpieczyć tajne Zarządzanie aplikacjami w chmurze.

Jak przeprowadzić integrację z tożsamościami zarządzanymi przez platformę Azure: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Jak utworzyć Key Vault: https://docs.microsoft.com/azure/key-vault/quick-create-portal

Jak zapewnić uwierzytelnianie Key Vault przy użyciu tożsamości zarządzanej: https://docs.microsoft.com/azure/key-vault/managed-identity

## <a name="712-manage-identities-securely-and-automatically"></a>7,12: bezpieczne i automatyczne zarządzanie tożsamościami

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 7,12 | 4.1 | Klient |

Użyj tożsamości zarządzanych, aby zapewnić usługom platformy Azure automatyczną tożsamość zarządzaną w usłudze Azure AD. Tożsamości zarządzane umożliwiają uwierzytelnianie w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Key Vault, bez żadnych poświadczeń w kodzie.

Jak skonfigurować tożsamości zarządzane: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

## <a name="713-eliminate-unintended-credential-exposure"></a>7,13: eliminowanie nieprzewidzianego narażenia na poświadczenia

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 7,13 | 13,3 | Klient |

Implementuj skaner poświadczeń, aby identyfikować poświadczenia w kodzie. Skaner poświadczeń zachęca również do przemieszczania odnalezionych poświadczeń do bardziej bezpiecznych lokalizacji, takich jak Azure Key Vault. 

Jak skonfigurować skaner poświadczeń: https://secdevtools.azurewebsites.net/helpcredscan.html

## <a name="next-steps"></a>Następne kroki

Zobacz następną kontrolę zabezpieczeń: [obrona złośliwego oprogramowania](security-control-malware-defense.md)
