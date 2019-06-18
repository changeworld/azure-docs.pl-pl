---
title: Zarządzanie kopiami zapasowymi przy użyciu kontroli dostępu opartej na rolach na platformie Azure "
description: Zarządzanie dostępem do operacji zarządzania kopiami zapasowymi w magazynie usługi Recovery Services za pomocą kontroli dostępu opartej na rolach.
services: backup
author: trinadhk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: trinadhk
ms.openlocfilehash: ed3797183e13a00d2c5381fa6449c111c3bc9ab9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60253725"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Kontrola dostępu oparta na rolach umożliwia zarządzanie punktami odzyskiwania usługi Azure Backup
Kontrola dostępu oparta na rolach (Role-Based Access Control, RBAC) na platformie Azure umożliwia precyzyjne zarządzanie dostępem dla platformy Azure. Przy użyciu kontroli dostępu opartej na rolach można przeprowadzić segregowanie zadań w ramach zespołu i nadać użytkownikom tylko takie uprawnienia dostępu, które są im niezbędne do wykonywania zadań.

> [!IMPORTANT]
> Udostępnione przez usługę Azure Backup ról są ograniczone do akcji, które mogą być wykonywane w witrynie Azure portal lub za pośrednictwem interfejsu API REST lub polecenia cmdlet programu PowerShell lub interfejsu wiersza polecenia magazyn usługi Recovery Services. Akcje wykonywane w kopii zapasowej agenta klienta w interfejsie użytkownika lub systemu Centrum Azure interfejsu użytkownika programu Data Protection Manager lub interfejsu użytkownika usługi Azure Backup Server znajdą się poza kontrolę nad tych ról.

Usługa Azure Backup udostępnia trzy role wbudowane, umożliwiające kontrolowanie operacji zarządzania kopiami zapasowymi. Aby dowiedzieć się więcej, zobacz [Wbudowane role RBAC na platformie Azure](../role-based-access-control/built-in-roles.md).

* [Wykonaj kopię zapasową Współautor](../role-based-access-control/built-in-roles.md#backup-contributor) — ta rola ma wszystkie uprawnienia do tworzenia i obsługi kopii zapasowych z wyjątkiem tworzenia magazynu usługi Recovery Services i przyznawania dostępu innym osobom. Wyobraź sobie tej roli jako administratora zarządzania kopiami zapasowymi, która może zajmować się każdej operacji zarządzania kopiami zapasowymi.
* [Wykonaj kopię zapasową Operator](../role-based-access-control/built-in-roles.md#backup-operator) — ta rola ma uprawnienia do wszystkiego, współautor, z wyjątkiem usuwania kopii zapasowych i zarządzanie zasadami tworzenia kopii zapasowych. Ta rola jest odpowiednikiem współautora, z wyjątkiem nie można wykonać operacji destrukcyjne, takie jak zatrzymywanie tworzenia kopii zapasowych usunąć dane i Usuń rejestrację zasobów lokalnych.
* [Wykonaj kopię zapasową czytnika](../role-based-access-control/built-in-roles.md#backup-reader) — ta rola ma uprawnienia do wyświetlania wszystkich operacji zarządzania kopiami zapasowymi. Wyobraź sobie tej roli, do monitorowania osoby.

Jeśli chcesz zdefiniować własne role, aby uzyskać większą kontrolę, zobacz temat jak [Tworzenie niestandardowych ról](../role-based-access-control/custom-roles.md) w RBAC platformy Azure.



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mapowanie kopii zapasowej wbudowane role do akcji zarządzania kopiami zapasowymi
Poniższa tabela przedstawia akcje z zakresu zarządzania kopii zapasowej i odpowiedniego rolę RBAC minimalnych wymaganych do wykonania tej operacji.

| Operacja zarządzania | Minimalna rola RBAC wymagane | Wymagany zakres |
| --- | --- | --- |
| Tworzenie magazynu usługi Recovery Services | Współautor | Grupa zasobów zawierająca magazynu |
| Włącz wykonywanie kopii zapasowej maszyn wirtualnych platformy Azure | Operator kopii zapasowych | Grupa zasobów zawierająca magazynu |
| | Współautor maszyny wirtualnej | Zasób maszyny Wirtualnej |
| Na żądanie kopii zapasowej maszyny Wirtualnej | Operator kopii zapasowych | Zasób magazynu odzyskiwania |
| Przywracanie maszyny Wirtualnej | Operator kopii zapasowych | Magazyn usługi Recovery Services |
| | Współautor | Grupa zasobów, w którym maszyna wirtualna zostanie wdrożona |
| | Współautor maszyny wirtualnej | Źródłowa maszyna wirtualna, której kopia zapasowa |
| Przywracanie kopii zapasowych maszyn wirtualnych z dyskami niezarządzanymi | Operator kopii zapasowych | Zasób magazynu odzyskiwania |
| | Współautor maszyny wirtualnej | Źródłowa maszyna wirtualna, której kopia zapasowa |
| | Współautor konta magazynu | Zasób konta magazynu, gdzie dysków do przywrócenia |
| Przywracanie dysków zarządzanych z kopii zapasowej maszyny Wirtualnej | Operator kopii zapasowych | Zasób magazynu odzyskiwania |
| | Współautor maszyny wirtualnej | Źródłowa maszyna wirtualna, której kopia zapasowa |
| | Współautor konta magazynu | Tymczasowe konto magazynu wybrane w ramach przywracania do przechowywania danych z magazynu przed przekonwertowaniem je do usługi managed disks |
| | Współautor | Grupa zasobów, do którego zostanie przywrócona następująca liczba dysków zarządzanych |
| Przywrócić pojedyncze pliki z kopii zapasowych maszyn wirtualnych | Operator kopii zapasowych | Zasób magazynu odzyskiwania |
| | Współautor maszyny wirtualnej | Źródłowa maszyna wirtualna, której kopia zapasowa |
| Tworzenie zasad kopii zapasowych dla kopii zapasowej maszyny Wirtualnej platformy Azure | Współautor kopii zapasowych | Zasób magazynu odzyskiwania |
| Modyfikowanie zasad tworzenia kopii zapasowej, kopii zapasowej maszyny Wirtualnej platformy Azure | Współautor kopii zapasowych | Zasób magazynu odzyskiwania |
| Usuwanie zasad kopii zapasowych kopii zapasowej maszyny Wirtualnej platformy Azure | Współautor kopii zapasowych | Zasób magazynu odzyskiwania |
| Zatrzymaj kopię zapasową (z opcją zachowania danych lub usunięcie danych) w kopii zapasowej maszyny Wirtualnej | Współautor kopii zapasowych | Zasób magazynu odzyskiwania |
| Zarejestruj się w środowisku lokalnym systemu Windows Server/klienta/serwera SCDPM lub serwera usługi Azure Backup | Operator kopii zapasowych | Zasób magazynu odzyskiwania |
| Usuń zarejestrowane lokalnie systemu Windows Server/klienta/serwera SCDPM lub serwera usługi Azure Backup | Współautor kopii zapasowych | Zasób magazynu odzyskiwania |

> [!IMPORTANT]
> Określ Współautor maszyny Wirtualnej w zakresie zasobów maszyny Wirtualnej, kliknij pozycję Kopia zapasowa jako część ustawień maszyny Wirtualnej zostanie otwarty ekran Włącz kopię zapasową, mimo, że maszyna wirtualna ma już kopię zapasową co wywołanie, aby sprawdzić stan kopii zapasowej działa tylko na poziomie subskrypcji. Aby tego uniknąć, należy albo przejdź do magazynu i otwórz widok elementu kopii zapasowej maszyny wirtualnej lub określ roli współautora maszyny Wirtualnej na poziomie subskrypcji.

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Rola minimalne wymagania dotyczące tworzenia kopii zapasowej udziału plików platformy Azure
Poniższa tabela przedstawia akcje z zakresu zarządzania kopii zapasowej i odpowiednich ról wymaganych do wykonania operacji udziału plików platformy Azure.

| Operacja zarządzania | Rolę wymaganą | Zasoby |
| --- | --- | --- |
| Włącz wykonywanie kopii zapasowej udziałów plików platformy Azure | Współautor kopii zapasowych | Magazyn usługi Recovery Services |
| | Konto magazynu | Współautor zasobów konta usługi Storage |
| Na żądanie kopii zapasowej maszyny Wirtualnej | Operator kopii zapasowych | Magazyn usługi Recovery Services |
| Przywróć udział plików | Operator kopii zapasowych | Magazyn usługi Recovery Services |
| | Współautor konta magazynu | Zasobów konta magazynu, w którym są obecne przywracania źródłowy i docelowy udziałów plików |
| Przywracanie pojedynczych plików | Operator kopii zapasowych | Magazyn usługi Recovery Services |
| | Współautor konta magazynu |   Zasobów konta magazynu, w którym są obecne przywracania źródłowy i docelowy udziałów plików |
| Zatrzymaj ochronę | Współautor kopii zapasowych | Magazyn usługi Recovery Services |      
| Wyrejestruj konto magazynu z magazynu |   Współautor kopii zapasowych | Magazyn usługi Recovery Services |
| | Współautor konta magazynu | Zasób konta magazynu|


## <a name="next-steps"></a>Kolejne kroki
* [Kontrola dostępu oparta na rolach](../role-based-access-control/role-assignments-portal.md): Wprowadzenie do funkcji RBAC w witrynie Azure portal.
* Dowiedz się, jak zarządzanie dostępem przy użyciu:
  * [Program PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Interfejs wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md)
  * [Interfejs API REST](../role-based-access-control/role-assignments-rest.md)
* [Rozwiązywanie kontroli dostępu opartej na rolach](../role-based-access-control/troubleshooting.md): Pobierz sugestie dotyczące rozwiązywania typowych problemów.
