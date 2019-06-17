---
title: Zarządzanie dostępem do usługi Azure Site Recovery, przy użyciu kontroli dostępu opartej na rolach (RBAC) | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób stosowania kontroli dostępu opartej na rolach (RBAC) w celu zarządzania dostępem do usługi Azure Site Recovery.
ms.service: site-recovery
ms.date: 04/08/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 33fc2cd19152fb6cbbffb106aa058948d39555f9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61471438"
---
# <a name="manage-site-recovery-access-with-role-based-access-control-rbac"></a>Zarządzanie dostępem Site Recovery przy użyciu kontroli dostępu opartej na rolach (RBAC)

Kontrola dostępu oparta na rolach (Role-Based Access Control, RBAC) na platformie Azure umożliwia precyzyjne zarządzanie dostępem dla platformy Azure. Korzystając z modelu RBAC, można segregować obowiązki w ramach zespołu i udzielić tylko uprawnienia dostępu do określonych użytkowników, zgodnie z potrzebami do wykonywania określonych zadań.

Usługa Azure Site Recovery zapewnia 3 role wbudowane, umożliwiające kontrolowanie operacji zarządzania usługą Site Recovery. Aby dowiedzieć się więcej, zobacz [Wbudowane role RBAC na platformie Azure](../role-based-access-control/built-in-roles.md).

* [Współautor usługi Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-contributor) — ta rola ma wszystkie uprawnienia wymagane do zarządzania operacjami usługi Azure Site Recovery w magazynie usługi Recovery Services. Użytkownik z tą rolą nie może jednak tworzyć ani usuwać magazynu usługi Recovery Services, ani przypisywać praw dostępu innym użytkownikom. Ta rola jest najbardziej odpowiednia dla administratorów odzyskiwania po awarii, którzy można włączyć i zarządzać w przypadku odzyskiwania po awarii dla aplikacji lub całej organizacji.
* [Operator usługi Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-operator) — ta rola ma uprawnienia do uruchamiania operacji trybu failover i powrotu po awarii oraz zarządzania nimi. Użytkownik z tą rolą nie można włączyć lub wyłączyć replikację, Utwórz lub usuwać magazynów, rejestrować nowej infrastruktury ani przypisywać praw dostępu innym użytkownikom. Ta rola jest najbardziej odpowiednia dla operatora odzyskiwania po awarii, który można trybu failover maszyny wirtualne lub przejść do aplikacji po uzyskaniu odpowiednich instrukcji od właścicieli aplikacji i administratorów IT w sytuacji rzeczywistych lub symulowanych po awarii, takich jak odzyskiwanie po awarii. Po usunięciu, operatora odzyskiwania po awarii można ponownie włączyć ochronę i powrotu po awarii maszyn wirtualnych.
* [Czytelnik usługi Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-reader) — ta rola ma uprawnienia do wyświetlania wszystkich operacji zarządzania usługą Site Recovery. Ta rola jest najbardziej odpowiednie dla IT zarządzający monitorowania, kto może monitorowania ich bieżącego stanu ochrony i podnieść biletami pomocy technicznej, jeśli jest to wymagane.

Jeśli chcesz zdefiniować własne role, aby uzyskać większą kontrolę, zobacz temat jak [Tworzenie niestandardowych ról](../role-based-access-control/custom-roles.md) na platformie Azure.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Uprawnienia wymagane do włączenia replikacji dla nowych maszyn wirtualnych
Gdy nowa maszyna wirtualna jest replikowana do platformy Azure przy użyciu usługi Azure Site Recovery, poziomy dostępu skojarzonego użytkownika są weryfikowane aby upewnić się, że użytkownik ma uprawnienia wymagane do użycia zasobów platformy Azure, pod warunkiem usługą Site Recovery.

Aby włączyć replikację dla nowej maszyny wirtualnej, użytkownik musi mieć:
* Uprawnienie do tworzenia maszyny wirtualnej w wybranej grupie zasobów
* Uprawnienie do tworzenia maszyny wirtualnej w wybranej sieci wirtualnej
* Uprawnienia do zapisu do wybranego konta magazynu

Użytkownik wymaga następujących uprawnień do ukończenia replikacji nowej maszyny wirtualnej.

> [!IMPORTANT]
>Upewnij się, że odpowiednie uprawnienia są dodawane na modelu wdrażania (Resource Manager / klasycznym) używany do wdrażania zasobów.

| **Typ zasobu** | **Model wdrażania** | **Uprawnienie** |
| --- | --- | --- |
| Wystąpienia obliczeniowe | Resource Manager | Microsoft.Compute/availabilitySets/read |
|  |  | Microsoft.Compute/virtualMachines/read |
|  |  | Microsoft.Compute/virtualMachines/write |
|  |  | Microsoft.Compute/virtualMachines/delete |
|  | Wdrożenie klasyczne | Microsoft.ClassicCompute/domainNames/read |
|  |  | Microsoft.ClassicCompute/domainNames/write |
|  |  | Microsoft.ClassicCompute/domainNames/delete |
|  |  | Microsoft.ClassicCompute/virtualMachines/read |
|  |  | Microsoft.ClassicCompute/virtualMachines/write |
|  |  | Microsoft.ClassicCompute/virtualMachines/delete |
| Sieć | Resource Manager | Microsoft.Network/networkInterfaces/read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft.Network/networkInterfaces/delete |
|  |  | Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft.Network/virtualNetworks/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | Wdrożenie klasyczne | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| Magazyn | Resource Manager | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | Wdrożenie klasyczne | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Grupa zasobów | Resource Manager | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

Rozważ użycie "Współautor maszyny wirtualnej" i "Współautora klasycznej maszyny wirtualnej" [wbudowane role](../role-based-access-control/built-in-roles.md) dla wdrożenia usługi Resource Manager i Model Klasyczny odpowiednio modeli.

## <a name="next-steps"></a>Kolejne kroki
* [Kontrola dostępu oparta na rolach](../role-based-access-control/role-assignments-portal.md): Wprowadzenie do funkcji RBAC w witrynie Azure portal.
* Dowiedz się, jak zarządzanie dostępem przy użyciu:
  * [Program PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Interfejs wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md)
  * [Interfejs API REST](../role-based-access-control/role-assignments-rest.md)
* [Rozwiązywanie kontroli dostępu opartej na rolach](../role-based-access-control/troubleshooting.md): Pobierz sugestie dotyczące rozwiązywania typowych problemów.
