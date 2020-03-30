---
title: Zarządzanie kopiami zapasowymi za pomocą kontroli dostępu opartej na rolach
description: Użyj kontroli dostępu opartej na rolach, aby zarządzać dostępem do operacji zarządzania kopiami zapasowymi w magazynie usług odzyskiwania.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: e2e32ac6981635e3b9885119fdf397783ac32cc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273204"
---
# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Zarządzanie punktami odzyskiwania kopii zapasowej usługi Azure za pomocą kontroli dostępu opartej na rolach

Kontrola dostępu oparta na rolach (Role-Based Access Control, RBAC) na platformie Azure umożliwia precyzyjne zarządzanie dostępem dla platformy Azure. Przy użyciu kontroli dostępu opartej na rolach można przeprowadzić segregowanie zadań w ramach zespołu i nadać użytkownikom tylko takie uprawnienia dostępu, które są im niezbędne do wykonywania zadań.

> [!IMPORTANT]
> Role udostępniane przez usługę Azure Backup są ograniczone do akcji, które można wykonać w witrynie Azure portal lub za pośrednictwem interfejsu API REST lub usług odzyskiwania w vault PowerShell lub polecenia cmdlet interfejsu wiersza polecenia. Akcje wykonywane w interfejsie użytkownika klienta agenta usługi Azure lub interfejsie użytkownika usługi Data Protection Manager lub ui usługi Azure Backup Server są poza kontrolą tych ról.

Usługa Azure Backup udostępnia trzy wbudowane role do kontrolowania operacji zarządzania kopiami zapasowymi. Aby dowiedzieć się więcej, zobacz [Wbudowane role RBAC na platformie Azure](../role-based-access-control/built-in-roles.md).

* [Współautor kopii zapasowej](../role-based-access-control/built-in-roles.md#backup-contributor) — ta rola ma wszystkie uprawnienia do tworzenia kopii zapasowych i zarządzania nimi, z wyjątkiem usuwania magazynu usług odzyskiwania i udzielania dostępu innym osobom. Wyobraź sobie tę rolę jako administrator zarządzania kopiami zapasowymi, który może wykonać każdą operację zarządzania kopiami zapasowymi.
* [Operator kopii zapasowej](../role-based-access-control/built-in-roles.md#backup-operator) — ta rola ma uprawnienia do wszystkiego, co robi współautor, z wyjątkiem usuwania kopii zapasowych i zarządzania zasadami tworzenia kopii zapasowych. Ta rola jest odpowiednikiem współautora, z wyjątkiem nie można wykonywać destrukcyjnych operacji, takich jak zatrzymanie tworzenia kopii zapasowej z usunąć dane lub usunąć rejestrację zasobów lokalnych.
* [Czytnik kopii zapasowych](../role-based-access-control/built-in-roles.md#backup-reader) — ta rola ma uprawnienia do wyświetlania wszystkich operacji zarządzania kopiami zapasowymi. Wyobraź sobie tę rolę, aby być osobą monitorującą.

Jeśli chcesz zdefiniować własne role dla jeszcze większej kontroli, zobacz jak [tworzyć role niestandardowe](../role-based-access-control/custom-roles.md) w usłudze Azure RBAC.

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>Mapowanie wbudowanych ról kopii zapasowej do akcji zarządzania kopiami zapasowymi

W poniższej tabeli przechwytuje akcje zarządzania kopiami zapasowymi i odpowiadające mu minimalne zadanie RBAC wymagane do wykonania tej operacji.

| Operacja zarządzania | Wymagana minimalna rola RBAC | Wymagany zakres |
| --- | --- | --- |
| Tworzenie magazynu usługi Recovery Services | Współautor kopii zapasowej | Grupa zasobów zawierająca przechowalnię |
| Włączanie tworzenia kopii zapasowych maszyn wirtualnych platformy Azure | Operator kopii zapasowej | Grupa zasobów zawierająca przechowalnię |
| | Współautor maszyny wirtualnej | Zasób maszyny Wirtualnej |
| Kopia zapasowa maszyny wirtualnej na żądanie | Operator kopii zapasowej | Magazyn usługi Recovery Services |
| Przywracanie maszyny wirtualnej | Operator kopii zapasowej | Magazyn usługi Recovery Services |
| | Współautor | Grupa zasobów, w której zostanie wdrożona maszyna wirtualna |
| | Współautor maszyny wirtualnej | Źródło maszyny Wirtualnej, która została utworzona kopia zapasową |
| Przywracanie kopii zapasowej dysków niezarządzanych maszyn wirtualnych | Operator kopii zapasowej | Magazyn usługi Recovery Services |
| | Współautor maszyny wirtualnej | Źródło maszyny Wirtualnej, która została utworzona kopia zapasową |
| | Współautor konta magazynu | Zasób konta magazynu, w którym dyski mają zostać przywrócone |
| Przywracanie dysków zarządzanych z kopii zapasowej maszyny Wirtualnej | Operator kopii zapasowej | Magazyn usługi Recovery Services |
| | Współautor maszyny wirtualnej | Źródło maszyny Wirtualnej, która została utworzona kopia zapasową |
| | Współautor konta magazynu | Konto magazynu tymczasowego wybrane jako część przywracania do przechowywania danych z przechowalni przed przekonwertowaniem ich na dyski zarządzane |
| | Współautor | Grupa zasobów, do której zostaną przywrócone zarządzane dyski |
| Przywracanie pojedynczych plików z kopii zapasowej maszyny Wirtualnej | Operator kopii zapasowej | Magazyn usługi Recovery Services |
| | Współautor maszyny wirtualnej | Źródło maszyny Wirtualnej, która została utworzona kopia zapasową |
| Tworzenie zasad tworzenia kopii zapasowych dla kopii zapasowej maszyny Wirtualnej platformy Azure | Współautor kopii zapasowej | Magazyn usługi Recovery Services |
| Modyfikowanie zasad tworzenia kopii zapasowych kopii zapasowej maszyny Wirtualnej platformy Azure | Współautor kopii zapasowej | Magazyn usługi Recovery Services |
| Usuwanie zasad tworzenia kopii zapasowych kopii zapasowej maszyny Wirtualnej platformy Azure | Współautor kopii zapasowej | Magazyn usługi Recovery Services |
| Zatrzymaj tworzenie kopii zapasowych (z zachowaniem danych lub usuń dane) na kopii zapasowej maszyny Wirtualnej | Współautor kopii zapasowej | Magazyn usługi Recovery Services |
| Rejestracja lokalnego systemu Windows Server/client/SCDPM lub usługi Azure Backup Server | Operator kopii zapasowej | Magazyn usługi Recovery Services |
| Usuwanie zarejestrowanych lokalnych systemów Windows Server/client/SCDPM lub Usługi Azure Backup Server | Współautor kopii zapasowej | Magazyn usługi Recovery Services |

> [!IMPORTANT]
> Jeśli określisz współautora maszyny Wirtualnej w zakresie zasobów maszyny Wirtualnej i klikniesz kopię zapasową jako część ustawień maszyny Wirtualnej, otworzy ekran "Włącz kopię zapasową", nawet jeśli maszyna wirtualna jest już kopią zapasową, ponieważ wywołanie w celu zweryfikowania stanu kopii zapasowej działa tylko na poziomie subskrypcji. Aby tego uniknąć, przejdź do magazynu i otwórz widok elementu kopii zapasowej maszyny Wirtualnej lub określ rolę współautora maszyny Wirtualnej na poziomie subskrypcji.

## <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Minimalne wymagania dotyczące roli dla kopii zapasowej udziału plików platformy Azure

W poniższej tabeli przechwytuje akcje zarządzania kopiami zapasowymi i odpowiadającą mu rolę wymaganą do wykonania operacji udziału plików azure.

| Operacja zarządzania | Wymagana rola | Resources |
| --- | --- | --- |
| Włączanie tworzenia kopii zapasowych udziałów plików platformy Azure | Współautor kopii zapasowej |Magazyn usługi Recovery Services |
| |Konto magazynu | Zasób konta magazynu współautora |
| Kopia zapasowa maszyny wirtualnej na żądanie | Operator kopii zapasowej | Magazyn usługi Recovery Services |
| Przywróć udział plików | Operator kopii zapasowej | Magazyn usługi Recovery Services |
| | Współautor konta magazynu | Zasoby konta magazynu, w których znajdują się zasoby źródłowe przywracania i docelowe |
| Przywracanie pojedynczych plików | Operator kopii zapasowej | Magazyn usługi Recovery Services |
| |Współautor konta magazynu|Zasoby konta magazynu, w których znajdują się zasoby źródłowe przywracania i docelowe |
| Zatrzymywanie ochrony |Współautor kopii zapasowej | Magazyn usługi Recovery Services |
| Wyrejestrowanie konta magazynu z magazynu |Współautor kopii zapasowej | Magazyn usługi Recovery Services |
| |Współautor konta magazynu | Zasób konta magazynu|

## <a name="next-steps"></a>Następne kroki

* [Kontrola dostępu oparta na rolach:](../role-based-access-control/role-assignments-portal.md)Wprowadzenie do funkcji RBAC w witrynie Azure portal.
* Dowiedz się, jak zarządzać dostępem za pomocą:
  * [Powershell](../role-based-access-control/role-assignments-powershell.md)
  * [Interfejs wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md)
  * [INTERFEJS API ODPOCZYNKU](../role-based-access-control/role-assignments-rest.md)
* [Rozwiązywanie problemów z kontrolą dostępu opartą na rolach:](../role-based-access-control/troubleshooting.md)uzyskaj sugestie dotyczące rozwiązywania typowych problemów.
