---
title: Zarządzanie kopiami zapasowymi przy użyciu kontroli dostępu opartej na rolach na platformie Azure "
description: Zarządzanie dostępem do operacji zarządzania kopiami zapasowymi w magazynie usługi Recovery Services za pomocą kontroli dostępu opartej na rolach.
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 7/11/2018
ms.author: trinadhk
ms.openlocfilehash: 855b75652fca421df12766f7711152d1e3ca2aeb
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009252"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Kontrola dostępu oparta na rolach umożliwia zarządzanie punktami odzyskiwania usługi Azure Backup
Kontrola dostępu oparta na rolach (Role-Based Access Control, RBAC) na platformie Azure umożliwia precyzyjne zarządzanie dostępem dla platformy Azure. Przy użyciu kontroli dostępu opartej na rolach można przeprowadzić segregowanie zadań w ramach zespołu i nadać użytkownikom tylko takie uprawnienia dostępu, które są im niezbędne do wykonywania zadań.

> [!IMPORTANT]
> Udostępnione przez usługę Azure Backup ról są ograniczone do akcji, które mogą być wykonywane w witrynie Azure portal lub poleceń cmdlet programu PowerShell magazyn usługi Recovery Services. Akcje wykonywane w kopii zapasowej agenta klienta w interfejsie użytkownika lub systemu Centrum Azure interfejsu użytkownika programu Data Protection Manager lub interfejsu użytkownika usługi Azure Backup Server znajdą się poza kontrolę nad tych ról.

Usługa Azure Backup udostępnia 3 role wbudowane, umożliwiające kontrolowanie operacji zarządzania kopiami zapasowymi. Aby dowiedzieć się więcej, zobacz [Wbudowane role RBAC na platformie Azure](../role-based-access-control/built-in-roles.md).

* [Wykonaj kopię zapasową Współautor](../role-based-access-control/built-in-roles.md#backup-contributor) — ta rola ma wszystkie uprawnienia do tworzenia i obsługi kopii zapasowych z wyjątkiem tworzenia magazynu usługi Recovery Services i przyznawania dostępu innym osobom. Wyobraź sobie tej roli jako administratora zarządzania kopiami zapasowymi, która może zajmować się każdej operacji zarządzania kopiami zapasowymi.
* [Wykonaj kopię zapasową Operator](../role-based-access-control/built-in-roles.md#backup-operator) — ta rola ma uprawnienia do wszystkiego, współautor, z wyjątkiem usuwania kopii zapasowych i zarządzanie zasadami tworzenia kopii zapasowych. Ta rola jest odpowiednikiem współautora, z wyjątkiem nie można wykonać operacji destrukcyjne, takie jak zatrzymywanie tworzenia kopii zapasowych usunąć dane i Usuń rejestrację zasobów lokalnych.
* [Wykonaj kopię zapasową czytnika](../role-based-access-control/built-in-roles.md#backup-reader) — ta rola ma uprawnienia do wyświetlania wszystkich operacji zarządzania kopiami zapasowymi. Wyobraź sobie tej roli, do monitorowania osoby.

Jeśli chcesz zdefiniować własne role, aby uzyskać większą kontrolę, zobacz temat jak [Tworzenie niestandardowych ról](../role-based-access-control/custom-roles.md) w RBAC platformy Azure.



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mapowanie kopii zapasowej wbudowane role do akcji zarządzania kopiami zapasowymi
Poniższa tabela przedstawia akcje z zakresu zarządzania kopii zapasowej i odpowiedniego rolę RBAC minimalnych wymaganych do wykonania tej operacji.

| Operacja zarządzania | Minimalna rola RBAC wymagane |
| --- | --- |
| Utwórz magazyn usługi Recovery Services | Współautor dla grupy zasobów magazynu |
| Włącz wykonywanie kopii zapasowej maszyn wirtualnych platformy Azure | Operator kopii zapasowych zdefiniowany w zakresie grupy zasobów zawierające magazynu, współautor maszyny wirtualnej na maszynach wirtualnych |
| Na żądanie kopii zapasowej maszyny Wirtualnej | Operator kopii zapasowych |
| Przywracanie maszyny Wirtualnej | Operator kopii zapasowych, współautor grupy zasobów, w którym maszyna wirtualna ma wdrożony, przeczytaj w sieci wirtualnej i Dołącz do podsieć wybrana |
| Przywróć dyski i poszczególnych plików z kopii zapasowej maszyny Wirtualnej | Operator kopii zapasowych, współautor maszyny wirtualnej na maszynach wirtualnych |
| Tworzenie zasad kopii zapasowych dla kopii zapasowej maszyny Wirtualnej platformy Azure | Współautor kopii zapasowych |
| Modyfikowanie zasad tworzenia kopii zapasowej, kopii zapasowej maszyny Wirtualnej platformy Azure | Współautor kopii zapasowych |
| Usuwanie zasad kopii zapasowych kopii zapasowej maszyny Wirtualnej platformy Azure | Współautor kopii zapasowych |
| Zatrzymaj kopię zapasową (z opcją zachowania danych lub usunięcie danych) w kopii zapasowej maszyny Wirtualnej | Współautor kopii zapasowych |
| Zarejestruj się w środowisku lokalnym systemu Windows Server/klienta/serwera SCDPM lub serwera usługi Azure Backup | Operator kopii zapasowych |
| Usuń zarejestrowane lokalnie systemu Windows Server/klienta/serwera SCDPM lub serwera usługi Azure Backup | Współautor kopii zapasowych |

## <a name="next-steps"></a>Kolejne kroki
* [Kontroli dostępu opartej na rolach](../role-based-access-control/role-assignments-portal.md): wprowadzenie do funkcji RBAC w witrynie Azure portal.
* Dowiedz się, jak zarządzanie dostępem przy użyciu:
  * [Program PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Interfejs wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md)
  * [Interfejs API REST](../role-based-access-control/role-assignments-rest.md)
* [Rozwiązywanie kontroli dostępu opartej na rolach](../role-based-access-control/troubleshooting.md): Pobierz sugestie dotyczące rozwiązywania typowych problemów.
