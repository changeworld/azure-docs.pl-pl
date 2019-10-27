---
title: Usuwanie i przywracanie obszaru roboczego usługi Azure Log Analytics | Microsoft Docs
description: Dowiedz się, jak usunąć obszar roboczy Log Analytics, jeśli został on utworzony w prywatnej subskrypcji lub zmienić strukturę modelu obszaru roboczego.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 10/11/2019
ms.openlocfilehash: f15e9c2a5980c8fb6d98f7bf9187b030e6910523
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932369"
---
# <a name="delete-and-restore-azure-log-analytics-workspace"></a>Usuwanie i przywracanie obszaru roboczego usługi Azure Log Analytics

W tym artykule wyjaśniono koncepcję nietrwałego usunięcia obszaru roboczego usługi Azure Log Analytics i odzyskiwania usuniętego obszaru roboczego. 

## <a name="considerations-when-deleting-a-workspace"></a>Zagadnienia dotyczące usuwania obszaru roboczego

Po usunięciu obszaru roboczego Log Analytics jest wykonywana operacja usuwania nietrwałego umożliwiająca odzyskanie obszaru roboczego, w tym jego danych i podłączonych agentów w ciągu 14 dni, bez względu na to, czy usunięcie było przypadkowe czy celowe. Po okresie usuwania nietrwałego obszar roboczy i jego dane nie są odzyskiwalne i są umieszczane w kolejce do trwałego usunięcia w ciągu 30 dni.

Należy zachować ostrożność podczas usuwania obszaru roboczego, ponieważ mogą istnieć ważne dane i konfiguracja, które mogą negatywnie wpłynąć na działanie usługi. Zapoznaj się z agentami, rozwiązaniami i innymi usługami i źródłami platformy Azure, które przechowują dane w Log Analytics, na przykład:

* Rozwiązania do zarządzania
* Azure Automation
* Agenci działający na maszynach wirtualnych z systemem Windows i Linux
* Agenci działający na komputerach z systemem Windows i Linux w danym środowisku
* System Center Operations Manager

Operacja usuwania nietrwałego usuwa zasób obszaru roboczego, a wszystkie powiązane uprawnienia użytkowników są zerwane. Jeśli użytkownicy są powiązani z innymi obszarami roboczymi, mogą nadal korzystać z Log Analytics z tymi innymi obszarami roboczymi.

## <a name="soft-delete-behavior"></a>Zachowanie nietrwałego usuwania

Operacja usuwania obszaru roboczego służy do usuwania obszaru roboczego Menedżer zasobów zasobu, ale jego konfiguracja i dane będą przechowywane przez 14 dni, a jednocześnie zostanie wyświetlony wygląd obszaru roboczego. Wszyscy agenci i System Center Operations Manager grupy zarządzania skonfigurowane do raportowania do obszaru roboczego pozostają w stanie oddzielonym w okresie usuwania nietrwałego. Usługa zapewnia Dodatkowo mechanizm odzyskiwania usuniętego obszaru roboczego, w tym jego danych i połączonych zasobów, głównie cofając operację usuwania.

> [!NOTE] 
> Zainstalowane rozwiązania i połączone usługi, takie jak konto Azure Automation, zostaną trwale usunięte z obszaru roboczego w czasie usuwania i nie można ich odzyskać. Te zmiany należy skonfigurować ponownie po operacji odzyskiwania, aby przełączyć obszar roboczy do wcześniej skonfigurowanego stanu.

Obszar roboczy można usunąć przy użyciu [programu PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), [interfejsu API REST](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)lub w [Azure Portal](https://portal.azure.com).

### <a name="delete-workspace-in-azure-portal"></a>Usuwanie obszaru roboczego w Azure Portal

1. Aby się zalogować, przejdź do [Azure Portal](https://portal.azure.com). 
2. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **log Analytics obszary robocze**.
3. Na liście obszarów roboczych Log Analytics wybierz obszar roboczy, a następnie kliknij przycisk **Usuń** w górnej części środkowego okienka.
   Opcja usuwania ![z okienka właściwości obszaru roboczego](media/delete-workspace/log-analytics-delete-workspace.png)
4. Gdy zostanie wyświetlone okno komunikatu potwierdzenia z prośbą o potwierdzenie usunięcia obszaru roboczego, kliknij przycisk **tak**.
   ![potwierdzić usunięcie obszaru roboczego](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

## <a name="recover-workspace"></a>Odzyskiwanie obszaru roboczego

Jeśli masz uprawnienia współautora do subskrypcji i grupy zasobów, do której został skojarzony obszar roboczy przed operacją usuwania nietrwałego, możesz odzyskać ją w okresie usuwania nietrwałego, w tym jego dane, konfigurację i połączone agenci. Po okresie usuwania nietrwałego obszar roboczy nie jest możliwy do odzyskania i zostaje przypisany do trwałego usunięcia. Nazwy usuniętych obszarów roboczych są zachowywane w okresie usuwania nietrwałego i nie mogą być używane podczas próby utworzenia nowego obszaru roboczego.  

Obszar roboczy można odzyskać przez ponowne utworzenie obszaru roboczego przy użyciu metod tworzenia obszaru roboczego [programu PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) lub [interfejsu API REST]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) , o ile te właściwości zostaną wypełnione informacjami o usuniętych obszarach roboczych, takich jak:

* Identyfikator subskrypcji
* Nazwa grupy zasobów
* Nazwa obszaru roboczego
* Region

> [!NOTE]
> * Odzyskiwanie obszaru roboczego nie jest obsługiwane w [Azure Portal](https://portal.azure.com). 
> * Ponowne tworzenie obszaru roboczego w okresie usuwania nietrwałego wskazuje, że ta nazwa obszaru roboczego jest już używana. 
> 
