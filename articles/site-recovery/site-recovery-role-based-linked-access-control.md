---
title: Za pomocą kontroli dostępu opartej na rolach do zarządzania usługi Azure Site Recovery | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób zastosowania i zarządzanie wdrożeniami usługi Azure Site Recovery przy użyciu kontroli dostępu opartej na rolach (RBAC)
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 4618d449410656f742cff40cf3cc190a0ce9c5e4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="use-role-based-access-control-to-manage-azure-site-recovery-deployments"></a>Kontrola dostępu oparta na rolach umożliwia zarządzanie wdrożenia usługi Azure Site Recovery

Kontrola dostępu oparta na rolach (Role-Based Access Control, RBAC) na platformie Azure umożliwia precyzyjne zarządzanie dostępem dla platformy Azure. Przy użyciu funkcji RBAC, można rozdzielenie obowiązków w obrębie organizacji i udzielić tylko określonym dostępu uprawnień użytkownikom do wykonywania określonych zadań.

Usługa Azure Site Recovery zapewnia 3 wbudowane role do kontrolowania operacji zarządzania usługi Site Recovery. Aby dowiedzieć się więcej, zobacz [Wbudowane role RBAC na platformie Azure](../role-based-access-control/built-in-roles.md).

* [Współautor usługi Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-contributor) — ta rola ma wszystkie uprawnienia wymagane do zarządzania operacjami usługi Azure Site Recovery w magazynie usługi Recovery Services. Użytkownik z tą rolą nie może jednak tworzyć ani usuwać magazynu usługi Recovery Services, ani przypisywać praw dostępu innym użytkownikom. Ta rola jest najbardziej odpowiednie dla administratorów odzyskiwania po awarii można włączyć i zarządzać w przypadku odzyskiwania po awarii dla aplikacji lub w całej organizacji.
* [Operator odzyskiwania lokacji](../role-based-access-control/built-in-roles.md#site-recovery-operator) — ta rola ma uprawnienia do wykonania operacji trybu Failover i powrotu po awarii. Użytkownika z tą rolą nie Włącz lub Wyłącz replikację, utworzyć lub usunąć magazynów, zarejestrować nową infrastrukturę lub przypisywanie praw dostępu do innych użytkowników. Ta rola jest najbardziej odpowiednie dla operatora odzyskiwania po awarii, który można trybu failover maszyny wirtualnej lub przejść do szczegółów aplikacji zaleceniami właściciele aplikacji i Administratorzy systemów informatycznych w sytuacji rzeczywista lub symulowane po awarii, takich jak odzyskiwania po awarii. Post rozpoznawanie po awarii, operator odzyskiwania po awarii można ponownie włączyć ochronę i powrotu po awarii maszyn wirtualnych.
* [Czytelnik usługi Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-reader) — ta rola ma uprawnienia do wyświetlania wszystkich operacji zarządzania usługą Site Recovery. Ta rola jest najbardziej odpowiednie dla zarządu monitorowania IT, który można monitorować bieżący stan ochrony i podnieść biletami pomocy technicznej, jeśli jest to wymagane.

Jeśli szukasz definiować własne role, aby uzyskać większą kontrolę, zobacz porady [Tworzenie niestandardowych ról](../role-based-access-control/custom-roles.md) na platformie Azure.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Uprawnienia wymagane do włączenia replikacji dla nowych maszyn wirtualnych
Podczas nowej maszyny wirtualnej są replikowane do platformy Azure przy użyciu usługi Azure Site Recovery, poziomy dostępu skojarzonego użytkownika jest sprawdzana poprawność, aby upewnić się, że użytkownik ma uprawnienia wymagane do użycia zasobów platformy Azure do usługi Site Recovery.

Aby włączyć replikację dla nowej maszyny wirtualnej, użytkownik musi mieć:
* Uprawnienia do tworzenia maszyny wirtualnej w wybranej grupy zasobów
* Uprawnienia do tworzenia maszyny wirtualnej w wybranej sieci wirtualnej
* Uprawnienia do zapisu do wybranego konta magazynu

Użytkownik wymaga następujących uprawnień do ukończenia replikacji nowej maszyny wirtualnej.

> [!IMPORTANT]
>Upewnij się, że odpowiednie uprawnienia są dodawane na model wdrażania (Resource Manager / klasycznego) używany do wdrażania zasobów.

| **Typ zasobu** | **Model wdrażania** | **Uprawnienia** |
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

Rozważ użycie "Współautora maszyny wirtualnej" i "Klasycznego współautora maszyny wirtualnej" [wbudowane role](../role-based-access-control/built-in-roles.md) wdrożenia usługi Resource Manager i Model Klasyczny odpowiednio modeli.

## <a name="next-steps"></a>Kolejne kroki
* [Kontrola dostępu oparta na rolach](../role-based-access-control/role-assignments-portal.md): rozpoczynanie pracy z RBAC w portalu Azure.
* Dowiedz się, jak Zarządzaj dostępem za pomocą:
  * [Program PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Interfejs wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md)
  * [Interfejs API REST](../role-based-access-control/role-assignments-rest.md)
* [Rozwiązywanie kontroli dostępu opartej na rolach](../role-based-access-control/troubleshooting.md): Pobierz sugestie dotyczące rozwiązywania typowych problemów.
