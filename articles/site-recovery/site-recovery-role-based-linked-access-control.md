---
title: Zarządzanie kontrolą dostępu opartą na rolach platformy Azure w usłudze Azure Site Recovery
description: W tym artykule opisano sposób stosowania kontroli dostępu opartej na rolach (RBAC) do zarządzania dostępem usługi Azure Site Recovery.
ms.service: site-recovery
ms.date: 04/08/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: ce389f9281b02662f87353f00c9bca92cdf86937
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257578"
---
# <a name="manage-site-recovery-access-with-role-based-access-control-rbac"></a>Zarządzanie dostępem do usługi Site Recovery za pomocą kontroli dostępu opartej na rolach (RBAC)

Kontrola dostępu oparta na rolach platformy Azure (RBAC) umożliwia szczegółowe zarządzanie dostępem dla platformy Azure. Za pomocą RBAC, można segregować obowiązki w zespole i udzielić tylko określonych uprawnień dostępu dla użytkowników, zgodnie z potrzebami do wykonywania określonych zadań.

Usługa Azure Site Recovery udostępnia 3 wbudowane role do kontrolowania operacji zarządzania odzyskiwaniem witryn. Aby dowiedzieć się więcej, zobacz [Wbudowane role RBAC na platformie Azure](../role-based-access-control/built-in-roles.md).

* [Współautor usługi Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-contributor) — ta rola ma wszystkie uprawnienia wymagane do zarządzania operacjami usługi Azure Site Recovery w magazynie usługi Recovery Services. Użytkownik z tą rolą nie może jednak tworzyć ani usuwać magazynu usługi Recovery Services, ani przypisywać praw dostępu innym użytkownikom. Ta rola jest najlepiej nadaje się dla administratorów odzyskiwania po awarii, którzy mogą włączyć i zarządzać odzyskiwanie po awarii dla aplikacji lub całych organizacji, w zależności od przypadku.
* [Operator usługi Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-operator) — ta rola ma uprawnienia do uruchamiania operacji trybu failover i powrotu po awarii oraz zarządzania nimi. Użytkownik z tą rolą nie może włączać ani wyłączać replikacji, tworzyć ani usuwać przechowalni, rejestrować nowej infrastruktury ani przypisywać praw dostępu innym użytkownikom. Ta rola jest najlepiej nadaje się dla operatora odzyskiwania po awarii, który może pracy awaryjnej maszyn wirtualnych lub aplikacji, gdy jest poinstruowany przez właścicieli aplikacji i administratorów IT w sytuacji rzeczywistej lub symulowanej awarii, takich jak dreder odzyskiwania po awarii. Po rozwiązaniu awarii operator odzyskiwania po awarii może ponownie chronić i po awarii maszyn wirtualnych.
* [Czytelnik usługi Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-reader) — ta rola ma uprawnienia do wyświetlania wszystkich operacji zarządzania usługą Site Recovery. Ta rola jest najlepiej nadaje się dla it monitorowania wykonawczego, który może monitorować bieżący stan ochrony i podnieść bilety pomocy technicznej w razie potrzeby.

Jeśli chcesz zdefiniować własne role, aby uzyskać jeszcze większą kontrolę, zobacz, jak [tworzyć role niestandardowe](../role-based-access-control/custom-roles.md) na platformie Azure.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Uprawnienia wymagane do włączenia replikacji dla nowych maszyn wirtualnych
Gdy nowa maszyna wirtualna jest replikowana na platformę Azure przy użyciu usługi Azure Site Recovery, poziomy dostępu skojarzonego użytkownika są sprawdzane, aby upewnić się, że użytkownik ma wymagane uprawnienia do korzystania z zasobów platformy Azure dostarczonych do usługi Site Recovery.

Aby włączyć replikację dla nowej maszyny wirtualnej, użytkownik musi mieć:
* Uprawnienie do tworzenia maszyny wirtualnej w wybranej grupie zasobów
* Uprawnienie do tworzenia maszyny wirtualnej w wybranej sieci wirtualnej
* Uprawnienie do zapisu na wybranym koncie Magazynu

Użytkownik potrzebuje następujących uprawnień, aby zakończyć replikację nowej maszyny wirtualnej.

> [!IMPORTANT]
>Upewnij się, że odpowiednie uprawnienia są dodawane dla modelu wdrażania (Menedżer zasobów/ Klasyczny) używanego do wdrażania zasobów.

> [!NOTE]
> Jeśli włączasz replikację dla maszyny Wirtualnej platformy Azure i chcesz zezwolić funkcji Site Recovery na zarządzanie aktualizacjami, a następnie podczas włączania replikacji można również utworzyć nowe konto automatyzacji, w którym to przypadku potrzebne jest uprawnienie do utworzenia konta automatyzacji w tym samym subskrypcji jako skarbca.

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
| Network (Sieć) | Resource Manager | Microsoft.Network/networkInterfaces/read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft.Network/networkInterfaces/delete |
|  |  | Sieć Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft.Network/virtualNetworks/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | Wdrożenie klasyczne | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| Magazyn | Resource Manager | Magazyn/magazyn microsoftKonsowania/odczytu |
|  |  | Magazyn/magazyn microsoftKonserwuje/klucze listy/akcja |
|  | Wdrożenie klasyczne | Microsoft.ClassicStorage/storageKonta/odczyt |
|  |  | Microsoft.ClassicStorage/storageKonta/listKeys/akcja |
| Grupa zasobów | Resource Manager | Zasoby/wdrożenia firmy Microsoft/* |
|  |  | Zasoby firmy Microsoft/subskrypcje/zasobyGrupy/odczyt |

Należy rozważyć użycie "Współautor maszyny wirtualnej" i "Classic Virtual Machine Contributor" [wbudowane role](../role-based-access-control/built-in-roles.md) dla Menedżera zasobów i klasycznych modeli wdrażania odpowiednio.

## <a name="next-steps"></a>Następne kroki
* [Kontrola dostępu oparta na rolach:](../role-based-access-control/role-assignments-portal.md)Wprowadzenie do funkcji RBAC w witrynie Azure portal.
* Dowiedz się, jak zarządzać dostępem za pomocą:
  * [Powershell](../role-based-access-control/role-assignments-powershell.md)
  * [Interfejs wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md)
  * [INTERFEJS API ODPOCZYNKU](../role-based-access-control/role-assignments-rest.md)
* [Rozwiązywanie problemów z kontrolą dostępu opartą na rolach:](../role-based-access-control/troubleshooting.md)uzyskaj sugestie dotyczące rozwiązywania typowych problemów.
