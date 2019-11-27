---
title: Usuwanie i odzyskiwanie obszaru roboczego usługi Azure Log Analytics | Microsoft Docs
description: Dowiedz się, jak usunąć obszar roboczy Log Analytics, jeśli został on utworzony w prywatnej subskrypcji lub zmienić strukturę modelu obszaru roboczego.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 10/28/2019
ms.openlocfilehash: b8fdefb5e8555e90b5c9065672f4593e5bf98e06
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326509"
---
# <a name="delete-and-restore-azure-log-analytics-workspace"></a>Usuwanie i przywracanie obszaru roboczego usługi Azure Log Analytics

W tym artykule wyjaśniono koncepcję nietrwałego usunięcia obszaru roboczego usługi Azure Log Analytics i odzyskiwania usuniętego obszaru roboczego. 

## <a name="considerations-when-deleting-a-workspace"></a>Zagadnienia dotyczące usuwania obszaru roboczego

Po usunięciu obszaru roboczego Log Analytics jest wykonywana operacja usuwania nietrwałego umożliwiająca odzyskanie obszaru roboczego, w tym jego danych i podłączonych agentów w ciągu 14 dni, bez względu na to, czy usunięcie było przypadkowe czy celowe. Po okresie usuwania nieodzyskiwalnego obszar roboczy i jego dane nie są możliwe do odzyskania — dane są umieszczane w kolejce do trwałego usunięcia w ciągu 30 dni, a nazwa obszaru roboczego jest dostępna i można jej użyć do utworzenia nowego obszaru roboczego.

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

Obszar roboczy można odzyskać przez ponowne utworzenie go przy użyciu następujących metod tworzenia obszaru roboczego: program [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) lub [interfejs API REST]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) , o ile następujące właściwości są wypełniane informacjami o usuniętych obszarach roboczych:

* Identyfikator subskrypcji
* Nazwa grupy zasobów
* Nazwa obszaru roboczego
* Region

Obszar roboczy i wszystkie jego dane zostaną przywrócone po operacji odzyskiwania. Rozwiązania i połączone usługi zostały trwale usunięte z obszaru roboczego, gdy zostało usunięte, i należy je ponownie skonfigurować w celu przeniesienia obszaru roboczego do wcześniej skonfigurowanego stanu. Niektóre dane mogą nie być dostępne dla zapytań po odzyskaniu obszaru roboczego do momentu ponownego zainstalowania skojarzonych rozwiązań, a ich schematy zostaną dodane do obszaru roboczego.

> [!NOTE]
> * Odzyskiwanie obszaru roboczego nie jest obsługiwane w [Azure Portal](https://portal.azure.com). 
> * Ponowne tworzenie obszaru roboczego w okresie usuwania nietrwałego wskazuje, że ta nazwa obszaru roboczego jest już używana. 
> 
