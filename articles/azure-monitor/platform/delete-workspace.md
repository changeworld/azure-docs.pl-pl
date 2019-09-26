---
title: Usuwanie i przywracanie obszaru roboczego usługi Azure Log Analytics | Microsoft Docs
description: Dowiedz się, jak usunąć obszar roboczy Log Analytics, jeśli został on utworzony w prywatnej subskrypcji lub zmienić strukturę modelu obszaru roboczego.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: magoedte
ms.openlocfilehash: f8dcab1a7a46d518b752e48f9886b60a37d8ec4c
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299538"
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
> Zainstalowane rozwiązania i połączone usługi, takie jak konto usługi Automation, są trwale usuwane z obszaru roboczego w czasie usuwania i nie można ich odzyskać. Te zmiany należy skonfigurować ponownie po wykonaniu operacji odzyskiwania w celu przełączenia obszaru roboczego do jego poprzedniej funkcjonalności. 

Obszar roboczy można usunąć przy użyciu [programu PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), [interfejsu API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)lub w [Azure Portal](https://portal.azure.com).

### <a name="delete-workspace-in-azure-portal"></a>Usuwanie obszaru roboczego w Azure Portal
1. Aby się zalogować, przejdź do [Azure Portal](https://portal.azure.com). 
2. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **log Analytics obszary robocze**.
3. Na liście obszarów roboczych Log Analytics wybierz obszar roboczy, a następnie kliknij przycisk **Usuń** w górnej części środkowego okienka.
   ![Opcja usuwania z okienka właściwości obszaru roboczego](media/delete-workspace/log-analytics-delete-workspace.png)
4. Gdy zostanie wyświetlone okno komunikatu potwierdzenia z prośbą o potwierdzenie usunięcia obszaru roboczego, kliknij przycisk **tak**.
   ![Potwierdzenie usunięcia obszaru roboczego](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

## <a name="recover-workspace"></a>Odzyskiwanie obszaru roboczego
Jeśli masz uprawnienia współautora do subskrypcji i grupy zasobów do lokalizacji obszaru roboczego przed operacją usuwania nietrwałego, możesz odzyskać ją w okresie usuwania nietrwałego, łącznie z danymi, konfiguracją i połączonymi agentami. Po okresie usuwania nietrwałego obszar roboczy nie jest możliwy do odzyskania i zostaje przypisany do trwałego usunięcia.

Obszar roboczy można odzyskać przez ponowne utworzenie obszaru roboczego przy użyciu dowolnej z obsługiwanych metod tworzenia: Program PowerShell, interfejs wiersza polecenia platformy Azure lub z Azure Portal tak długo, jak te właściwości są wypełniane informacjami o usuniętym obszarze roboczym, w tym:
1.  Identyfikator subskrypcji
2.  Nazwa grupy zasobów
3.  Nazwa obszaru roboczego
4.  Region

> [!NOTE]
> Nazwy usuniętych obszarów roboczych są zachowywane dla okresu usuwania nietrwałego i nie mogą być używane podczas tworzenia nowego obszaru roboczego. Nazwy obszarów roboczych są *udostępniane i dostępne* do użycia podczas tworzenia nowego obszaru roboczego po upływie okresu usuwania nietrwałego.
