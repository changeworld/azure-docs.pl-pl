---
title: Kontrola zabezpieczeń platformy Azure — Zarządzanie zapasami i zasobami
description: Spis kontroli zabezpieczeń i zarządzanie zasobami
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 95c1834e997caae13e650df5b588df898a1e361d
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564181"
---
# <a name="security-control-inventory-and-asset-management"></a>Kontrola zabezpieczeń: Zarządzanie zapasami i zasobami

Rekomendacje dotyczące spisu i zarządzania zasobami koncentrują się na rozwiązywaniu problemów związanych z aktywnie zarządzaniem (spisem, śledzeniem i korygowaniem) wszystkich zasobów platformy Azure, dzięki czemu tylko autoryzowane zasoby mają dostęp, a nieautoryzowane i niezarządzane zasoby są zidentyfikowane i usunięte.

## <a name="61-use-azure-asset-discovery"></a>6,1: Użyj odnajdywania zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 6.1 | 1,1, 1,2, 1,3, 1,4, 9,1, 12,1 | Klient |

Korzystając z grafu zasobów platformy Azure, można wysyłać zapytania/odnajdywać wszystkie zasoby (takie jak obliczenia, magazyn, Sieć, porty i protokoły itp.) w ramach subskrypcji.  Upewnij się, że masz odpowiednie uprawnienia (odczyt) w dzierżawie i Wylicz wszystkie subskrypcje platformy Azure oraz zasoby w ramach subskrypcji.

Mimo że klasyczne zasoby platformy Azure mogą zostać odnalezione za pośrednictwem grafu zasobów, zdecydowanie zaleca się tworzenie i używanie Azure Resource Manager zasobów do przodu.

Jak tworzyć zapytania przy użyciu grafu zasobów platformy Azure:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Jak wyświetlić subskrypcje platformy Azure:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Informacje o kontroli RBAC platformy Azure:

https://docs.microsoft.com/azure/role-based-access-control/overview

## <a name="62-maintain-asset-metadata"></a>6,2: Konserwowanie metadanych zasobów

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 6.2 | 1.5 | Klient |

Zastosowanie tagów do zasobów platformy Azure, dzięki czemu metadane są logicznie zorganizowane w taksonomię.

Tworzenie i używanie tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="63-delete-unauthorized-azure-resources"></a>6,3: Usuń nieautoryzowane zasoby platformy Azure

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 6.3 | 1.6 | Klient |

W razie potrzeby można używać tagowania, grup zarządzania i oddzielnych subskrypcji, aby organizować i śledzić zasoby. Regularnie Uzgadniaj spis i zapewnij, że nieautoryzowane zasoby są usuwane z subskrypcji w odpowiednim czasie.

Jak utworzyć dodatkowe subskrypcje platformy Azure:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Jak utworzyć Grupy zarządzania:

https://docs.microsoft.com/azure/governance/management-groups/create

Tworzenie i używanie tagów:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: przechowywanie spisu zatwierdzonych zasobów platformy Azure i tytułów oprogramowania

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 6.4 | 2.1 | Klient |

Zdefiniuj zatwierdzone zasoby platformy Azure i zatwierdzone oprogramowanie dla zasobów obliczeniowych.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: Monitoruj niezatwierdzone zasoby platformy Azure

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 6.5 | 2,3, 2,4 | Klient |

Użyj Azure Policy, aby umieścić ograniczenia dotyczące typu zasobów, które można utworzyć w ramach subskrypcji.

Użyj grafu zasobów platformy Azure do wykonywania zapytań/odnajdywania zasobów w ramach subskrypcji. &nbsp;upewnić się, że wszystkie zasoby platformy Azure obecne w środowisku są zatwierdzone.

Jak skonfigurować Azure Policy i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak tworzyć zapytania za pomocą usługi Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: Monitoruj niezatwierdzone aplikacje oprogramowania w ramach zasobów obliczeniowych

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 6.6 | 2.3/2.4 | Klient |

Użyj spisu maszyn wirtualnych platformy Azure, aby zautomatyzować zbieranie informacji o całym oprogramowaniu na Virtual Machines. W Azure Portal są dostępne nazwy, wersje, Wydawca i czas odświeżania oprogramowania. Aby uzyskać dostęp do daty instalacji i innych informacji, Włącz diagnostykę na poziomie gościa i Przenieś dzienniki zdarzeń systemu Windows do obszaru roboczego Log Analytics.

Jak włączyć Spis maszyn wirtualnych platformy Azure:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: Usuń niezatwierdzone zasoby platformy Azure i aplikacje oprogramowania

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 6.7 | 2.5 | Klient |

Użyj Azure Security Center monitorowania integralności plików (Change Tracking) i spisu maszyn wirtualnych, aby zidentyfikować całe oprogramowanie zainstalowane w Virtual Machines. Możesz zaimplementować własny proces usuwania nieautoryzowanego oprogramowania. Do zidentyfikowania niezatwierdzonego oprogramowania można również użyć rozwiązania innej firmy.

Jak używać monitorowania integralności plików:

https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring#using-file-integrity-monitoring

Informacje na temat Change Tracking platformy Azure:

https://docs.microsoft.com/azure/automation/change-tracking

Jak włączyć Spis maszyn wirtualnych platformy Azure:

https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software

## <a name="68-use-only-approved-applications"></a>6,8: Używaj tylko zatwierdzonych aplikacji

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 6.8 | 2,6 | Klient |

Użyj Azure Security Center adaptacyjnych kontroli aplikacji, aby upewnić się, że tylko autoryzowane oprogramowanie i wszystkie nieautoryzowane oprogramowanie zostało zablokowane na platformie Azure Virtual Machines.

Jak używać Azure Security Center adaptacyjnych kontroli aplikacji:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="69-use-only-approved-azure-services"></a>6,9: Używaj tylko zatwierdzonych usług platformy Azure

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 6.9 | 2,6 | Klient |

Użyj Azure Policy, aby ograniczyć usługi, które możesz udostępnić w danym środowisku.

Jak skonfigurować Azure Policy i zarządzać nimi:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Jak odmówić określonego typu zasobu Azure Policy:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

## <a name="610-implement-approved-application-list"></a>6,10: Zaimplementuj listę zatwierdzonych aplikacji

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 6.1 | 2.7 | Klient |

Użyj Azure Security Center adaptacyjnych kontrolek aplikacji, aby określić typy plików, których może dotyczyć reguła lub które nie mogą być stosowane.

Zaimplementuj rozwiązanie innych firm, jeśli nie spełnia ono wymagań.

Jak używać Azure Security Center adaptacyjnych kontroli aplikacji:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-application

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6,11: Ogranicz możliwość korzystania przez użytkowników z Azure Resource Manager za pośrednictwem skryptów

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 6,11 | 2.8 | Klient |

Użyj dostępu warunkowego platformy Azure, aby ograniczyć możliwość współpracy użytkowników z Azure Resource Manager przez skonfigurowanie &quot;bloku dostępu&quot; dla aplikacji &quot;Microsoft Azure Management&quot;.

Jak skonfigurować dostęp warunkowy w celu blokowania dostępu do Azure Resource Manager:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: Ogranicz możliwość wykonywania skryptów w zasobach obliczeniowych przez użytkowników

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 6,12 | 2.8 | Klient |

Użyj określonych konfiguracji systemu operacyjnego lub zasobów innych firm, aby ograniczyć możliwość wykonywania skryptów w ramach zasobów obliczeniowych platformy Azure.

Na przykład Sterowanie wykonywaniem skryptu programu PowerShell w środowiskach systemu Windows: https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizyczne lub logiczne rozdzielenie aplikacji wysokiego ryzyka

| Identyfikator platformy Azure | Identyfikatory WNP | Odpowiedzialność |
|--|--|--|
| 6,13 | 2.9 | Klient |

Oprogramowanie wymagane do działania biznesowego, ale może powodować większe ryzyko dla organizacji, powinno być odizolowane w ramach własnej maszyny wirtualnej i/lub sieci wirtualnej i dostatecznie zabezpieczone za pomocą zapory platformy Azure lub sieciowej grupy zabezpieczeń.

Jak utworzyć sieć wirtualną:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Jak utworzyć sieciowej grupy zabezpieczeń z konfiguracją zabezpieczeń:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="next-steps"></a>Następne kroki

Zobacz następną kontrolę zabezpieczeń: [bezpieczna konfiguracja](security-control-secure-configuration.md)
