---
title: Kontrola zabezpieczeń platformy Azure — bezpieczna konfiguracja
description: Bezpieczna konfiguracja kontroli zabezpieczeń
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 03564effeee36ddb3316d48329ccab8ccfce75b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934292"
---
# <a name="security-control-secure-configuration"></a>Kontrola bezpieczeństwa: bezpieczna konfiguracja

Ustanawiaj, wdrażaj i aktywnie zarządzaj (śledź, raportuj, popraw) konfigurację zabezpieczeń zasobów platformy Azure, aby uniemożliwić osobom atakującym wykorzystywanie zagrożonych usług i ustawień.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Ustanawianie bezpiecznych konfiguracji dla wszystkich zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7.1 | 5.1 | Klient |

Użyj usługi Azure Policy lub Usługi Azure Security Center, aby zachować konfiguracje zabezpieczeń dla wszystkich zasobów platformy Azure.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: Ustanawianie bezpiecznych konfiguracji systemów operacyjnych

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7.2 | 5.1 | Klient |

Użyj zalecenia &quot;usługi Azure Security Center Korygowanie luk&quot; w zabezpieczeniach konfiguracji zabezpieczeń na maszynach wirtualnych, aby zachować konfiguracje zabezpieczeń na wszystkich zasobach obliczeniowych.

Jak monitorować zalecenia usługi Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-recommendations

Jak skorygować zalecenia usługi Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7.3 | 5.2 | Klient |

Użyj zasad platformy Azure [odmów] i [wdrożyć, jeśli nie istnieje], aby wymusić bezpieczne ustawienia w zasobach platformy Azure.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Poznaj efekty zasad platformy Azure:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Obsługa bezpiecznych konfiguracji systemów operacyjnych

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7.4 | 5.2 | Udostępnione |

Podstawowe obrazy systemu operacyjnego są zarządzane i obsługiwane przez firmę Microsoft.

Można jednak zastosować ustawienia zabezpieczeń wymagane przez organizację przy użyciu szablonów usługi Azure Resource Manager i/lub żądanej konfiguracji stanu.

Jak utworzyć maszynę wirtualną platformy Azure na podstawie szablonu usługi Azure Resource Manager:

https://docs.microsoft.com/azure/virtual-machines/windows/ps-template

Zrozumienie konfiguracji żądanego stanu dla maszyn wirtualnych platformy Azure:

https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-overview

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7,5 | 5.3 | Klient |

Jeśli używasz niestandardowych definicji zasad platformy Azure, użyj usługi Azure DevOps lub Azure Repos, aby bezpiecznie przechowywać kod i zarządzać nim.

Jak przechowywać kod w usłudze Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Dokumentacja repozytorium platformy Azure:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpieczne przechowywanie obrazów niestandardowych systemów operacyjnych

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7.6 | 5.3 | Klient |

Jeśli używasz obrazów niestandardowych, użyj RBAC, aby upewnić się, że tylko autoryzowani użytkownicy mogą uzyskać dostęp do obrazów. W przypadku obrazów kontenerów należy przechowywać je w rejestrze kontenerów platformy Azure i korzystać z narzędzia RBAC, aby upewnić się, że tylko autoryzowani użytkownicy mogą uzyskiwać dostęp do obrazów.

Poznaj rbac na platformie Azure:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Zrozumienie RBAC dla rejestru kontenerów:

https://docs.microsoft.com/azure/container-registry/container-registry-roles

Jak skonfigurować rbac na platformie Azure:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

## <a name="77-deploy-system-configuration-management-tools"></a>7.7: Wdrażanie narzędzi do zarządzania konfiguracją systemu

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7.7 | 5.4 | Klient |

Użyj usługi Azure Policy, aby ostrzegać, przeprowadzać inspekcje i wymuszać konfiguracje systemu. Ponadto opracuj proces i potok do zarządzania wyjątkami zasad.

Jak skonfigurować zasady platformy Azure i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

## <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Wdrażanie narzędzi do zarządzania konfiguracją systemu dla systemów operacyjnych

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7.8 | 5.4 | Klient |

Użyj rozszerzenia obliczeniowe platformy Azure, takie jak Konfiguracja żądanego stanu programu PowerShell dla obliczeń systemu Windows lub rozszerzenie linux chef dla systemu Linux.

Jak zainstalować rozszerzenia maszyn wirtualnych na platformie Azure:

https://docs.microsoft.com/azure/virtual-machines/extensions/overview#how-can-i-install-an-extension

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Wdrażanie automatycznego monitorowania konfiguracji usług platformy Azure

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7.9 | 5.5 | Klient |

Korzystanie z usługi Azure Security Center w celu wykonywania skanowania według planu bazowego zasobów platformy Azure

Jak skorygować zalecenia w usłudze Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Wdrożenie automatycznego monitorowania konfiguracji systemów operacyjnych

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7.1 | 5.5 | Klient |

Usługa Azure Security Center służy do skanowania według planu bazowego dla systemu operacyjnego i ustawień platformy Docker dla kontenerów.

Poznaj zalecenia dotyczące kontenerów usługi Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-container-recommendations

## <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure 

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7.11 | 13.1 | Klient |

Użyj tożsamości usługi zarządzanej w połączeniu z usługą Azure Key Vault, aby uprościć i zabezpieczyć zarządzanie tajne dla aplikacji w chmurze.

Jak zintegrować się z tożsamościami zarządzanymi platformy Azure:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Jak utworzyć przechowalnię kluczy:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Jak zapewnić uwierzytelnianie usługi Key Vault z tożsamością zarządzaną:

https://docs.microsoft.com/azure/key-vault/managed-identity

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7.12 | 4.1 | Klient |

Użyj tożsamości zarządzanych, aby zapewnić usługi platformy Azure z automatycznie zarządzaną tożsamością w usłudze Azure AD. Tożsamości zarządzane umożliwia uwierzytelnianie do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD, w tym Usługi Key Vault, bez żadnych poświadczeń w kodzie.

Jak skonfigurować tożsamości zarządzane:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Wyeliminuj niezamierzone narażenie na poświadczenia

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7.13 | 13.3 | Klient |

Zaimplementuj skaner poświadczeń w celu zidentyfikowania poświadczeń w kodzie. Skaner poświadczeń będzie również zachęcać do przenoszenia wykrytych poświadczeń do bezpieczniejszych lokalizacji, takich jak Usługa Azure Key Vault. 

Jak skonfigurować Skaner poświadczeń:

https://secdevtools.azurewebsites.net/helpcredscan.html

## <a name="next-steps"></a>Następne kroki

Zobacz następną kontrolę zabezpieczeń: [Ochrona przed złośliwym oprogramowaniem](security-control-malware-defense.md)
