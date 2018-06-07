---
title: Zarządzanie kopiami zapasowymi przy użyciu kontroli dostępu opartej na rolach na platformie Azure "
description: Kontrola dostępu oparta na rolach umożliwia zarządzanie dostępem do operacji tworzenia kopii zapasowej zarządzania w magazynie usług odzyskiwania.
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 8/22/2017
ms.author: trinadhk
ms.openlocfilehash: 37240b211e055682c4ce93c9057c4b266bba49e3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34607450"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Umożliwia zarządzanie punktami odzyskiwania kopia zapasowa Azure opartej na rolach kontrola dostępu
Kontrola dostępu oparta na rolach (Role-Based Access Control, RBAC) na platformie Azure umożliwia precyzyjne zarządzanie dostępem dla platformy Azure. Przy użyciu funkcji RBAC, można rozdzielenie obowiązków w obrębie organizacji i udzielić tylko takiego dostępu dla użytkowników, które są niezbędne do wykonywania swoich zadań.

> [!IMPORTANT]
> Role udostępniane przez usługi Kopia zapasowa Azure są ograniczone do działania, które mogą być wykonywane w portalu Azure lub poleceń cmdlet programu PowerShell magazyn usług odzyskiwania. Akcje wykonywane w Azure kopii zapasowej center interfejs użytkownika klienta usługi agenta lub systemu interfejs programu Data Protection Manager lub interfejsu użytkownika serwera kopii zapasowej Azure są poza kontrolą tych ról.

Kopia zapasowa Azure stanowi 3 wbudowane role do kontrolowania operacji zarządzania kopiami zapasowymi. Aby dowiedzieć się więcej, zobacz [Wbudowane role RBAC na platformie Azure](../role-based-access-control/built-in-roles.md).

* [Wykonaj kopię zapasową współautora](../role-based-access-control/built-in-roles.md#backup-contributor) — ta rola ma wszystkie uprawnienia do tworzenia i zarządzanie kopiami zapasowymi, z wyjątkiem Tworzenie magazynu usług odzyskiwania i zapewnieniu dostępu do innych użytkowników. Wyobraź sobie tej roli administratora zarządzania kopiami zapasowymi, który można wykonać każdej operacji zarządzania kopiami zapasowymi.
* [Wykonaj kopię zapasową Operator](../role-based-access-control/built-in-roles.md#backup-operator) — ta rola ma uprawnienia do wszystkich współautora z wyjątkiem usuwanie kopii zapasowej i zarządzanie nimi zasad tworzenia kopii zapasowych. Ta rola jest odpowiednikiem współautora, z wyjątkiem nie można wykonać operacji destrukcyjnego, takich jak tworzenie kopii zapasowej Zatrzymaj z usuwania danych lub usunąć rejestrację zasobów lokalnych.
* [Wykonaj kopię zapasową czytnika](../role-based-access-control/built-in-roles.md#backup-reader) — ta rola ma uprawnienia do wyświetlania wszystkich operacji zarządzania kopiami zapasowymi. Wyobraź sobie tej roli, do monitorowania osoby.

Jeśli szukasz definiować własne role, aby uzyskać większą kontrolę, zobacz porady [Tworzenie niestandardowych ról](../role-based-access-control/custom-roles.md) w Azure RBAC.



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mapowanie wbudowane role kopii zapasowej do akcji zarządzania kopiami zapasowymi
Poniższa tabela zawiera akcje zarządzania kopii zapasowej i odpowiedniego roli RBAC minimalnych wymaganych do wykonania tej operacji.

| Operacja zarządzania | Minimalna roli RBAC wymagane |
| --- | --- |
| Tworzenie magazynu usług odzyskiwania | Współautor grupy zasobów magazynu |
| Włączenia kopii zapasowej maszyn wirtualnych Azure | Operator kopii zapasowych w magazynie, współautora maszyny wirtualnej na maszynach wirtualnych |
| Na żądanie kopii zapasowej maszyny Wirtualnej | Operator kopii zapasowych |
| Przywracanie maszyny Wirtualnej | Operator kopii zapasowych, w którym maszyny Wirtualnej i sieci wirtualne mają być wdrożony Współautor grupy zasobów |
| Przywracanie dysków, pojedyncze pliki z kopii zapasowej maszyny Wirtualnej | Operator kopii zapasowych, współautora maszyny wirtualnej na maszynach wirtualnych |
| Tworzenie zasad tworzenia kopii zapasowej dla kopii zapasowej maszyny Wirtualnej Azure | Współautor kopii zapasowej |
| Modyfikowanie zasad tworzenia kopii zapasowej kopii zapasowej maszyny Wirtualnej Azure | Współautor kopii zapasowej |
| Usuń zasady tworzenia kopii zapasowej kopii zapasowej maszyny Wirtualnej Azure | Współautor kopii zapasowej |
| Zatrzymaj kopii zapasowej (z opcją zachowania danych lub usunięcie danych) w kopii zapasowej maszyny Wirtualnej | Współautor kopii zapasowej |
| Zarejestruj lokalnego systemu Windows serwera/klienta/SCDPM lub serwer kopii zapasowej systemu Azure | Operator kopii zapasowych |
| Usuń zarejestrowane lokalnie systemu Windows serwera/klienta/SCDPM lub serwer kopii zapasowej systemu Azure | Współautor kopii zapasowej |

## <a name="next-steps"></a>Kolejne kroki
* [Kontroli dostępu opartej na rolach](../role-based-access-control/role-assignments-portal.md): rozpoczynanie pracy z RBAC w portalu Azure.
* Dowiedz się, jak Zarządzaj dostępem za pomocą:
  * [Program PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Interfejs wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md)
  * [Interfejs API REST](../role-based-access-control/role-assignments-rest.md)
* [Rozwiązywanie kontroli dostępu opartej na rolach](../role-based-access-control/troubleshooting.md): Pobierz sugestie dotyczące rozwiązywania typowych problemów.
