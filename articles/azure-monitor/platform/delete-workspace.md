---
title: Usuwanie i odzyskiwanie obszaru roboczego usługi Azure Log Analytics | Dokumenty firmy Microsoft
description: Dowiedz się, jak usunąć obszar roboczy usługi Log Analytics, jeśli został utworzony w ramach subskrypcji osobistej lub zrestrukturyzowanie modelu obszaru roboczego.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/14/2020
ms.openlocfilehash: 1dceb3db4572ecdaf504745dba1099a5eccead43
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80395782"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Usuwanie i odzyskiwanie obszaru roboczego usługi Azure Log Analytics

W tym artykule opisano pojęcie usuwania programowego obszaru roboczego usługi Azure Log Analytics i sposobu odzyskiwania usuniętego obszaru roboczego. 

## <a name="considerations-when-deleting-a-workspace"></a>Zagadnienia dotyczące usuwania obszaru roboczego

Po usunięciu obszaru roboczego usługi Log Analytics wykonywana jest operacja usuwania nietrwałego, aby umożliwić odzyskanie obszaru roboczego, w tym jego danych i połączonych agentów w ciągu 14 dni, niezależnie od tego, czy usunięcie było przypadkowe, czy zamierzone. Po okresie usuwania nietrwałego zasób obszaru roboczego i jego dane nie można odzyskać — jego dane są umieszczane w kolejce do trwałego usunięcia i całkowicie czyszczące w ciągu 30 dni. Nazwa obszaru roboczego jest "zwolniona" i można jej użyć do utworzenia nowego obszaru roboczego.

> [!NOTE]
> Jeśli chcesz zastąpić zachowanie usuwania nietrwałego i trwale usunąć obszar roboczy, wykonaj kroki opisane w [obszarze Trwałe usuwanie obszaru roboczego](#permanent-workspace-delete).

Należy zachować ostrożność podczas usuwania obszaru roboczego, ponieważ mogą istnieć ważne dane i konfiguracja, które mogą negatywnie wpłynąć na działanie usługi. Sprawdź, jakie agenty, rozwiązania i inne usługi i źródła platformy Azure, które przechowują swoje dane w usłudze Log Analytics, takie jak:

* Rozwiązania do zarządzania
* Azure Automation
* Agenci działający na maszynach wirtualnych z systemem Windows i Linux
* Agenci działający na komputerach z systemem Windows i Linux w twoim środowisku
* System Center Operations Manager

Operacja usuwania nietrwałego usuwa zasób obszaru roboczego, a wszelkie uprawnienia skojarzonych użytkowników są przerywane. Jeśli użytkownicy są skojarzone z innymi obszarami roboczymi, a następnie mogą kontynuować korzystanie z usługi Log Analytics z tych innych obszarów roboczych.

## <a name="soft-delete-behavior"></a>Zachowanie usuwania nietrwałego

Operacja usuwania obszaru roboczego usuwa zasób Menedżera zasobów obszaru roboczego, ale jego konfiguracja i dane są przechowywane przez 14 dni, jednocześnie dając wrażenie, że obszar roboczy jest usuwany. Wszyscy agenci i grupy zarządzania programu System Center Operations Manager skonfigurowane do raportowania do obszaru roboczego pozostają w stanie osieroconym w okresie usuwania nietrwałego. Usługa dodatkowo zapewnia mechanizm odzyskiwania usuniętego obszaru roboczego, w tym jego danych i połączonych zasobów, zasadniczo cofając usunięcie.

> [!NOTE] 
> Zainstalowane rozwiązania i połączone usługi, takie jak konto usługi Azure Automation, są trwale usuwane z obszaru roboczego w czasie usuwania i nie można ich odzyskać. Należy je ponownie skonfigurować po operacji odzyskiwania, aby przenieść obszar roboczy do wcześniej skonfigurowanym stanu.

Obszar roboczy można usunąć za pomocą programu [PowerShell,](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0) [interfejsu API REST](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)lub w portalu [Azure.](https://portal.azure.com)

### <a name="azure-portal"></a>Azure Portal

1. Aby się zalogować, przejdź do [witryny Azure portal](https://portal.azure.com). 
2. W witrynie Azure portal wybierz pozycję **Wszystkie usługi**. Na liście zasobów wpisz **Log Analytics**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Wybierz **obszary robocze usługi Log Analytics**.
3. Na liście obszarów roboczych usługi Log Analytics wybierz obszar roboczy, a następnie kliknij przycisk **Usuń** u góry środkowego okienka.
   ![Opcja Usuń z okienka właściwości obszaru roboczego](media/delete-workspace/log-analytics-delete-workspace.png)
4. Gdy pojawi się okno komunikatu potwierdzającego z prośbą o potwierdzenie usunięcia obszaru roboczego, kliknij przycisk **Tak**.
   ![Potwierdź usunięcie obszaru roboczego](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Aby usunąć obszar roboczy usługi Log Analytics, musisz mieć uprawnienia "Log Analytics Contributor".<br>
Jeśli podczas tworzenia obszaru roboczego zostanie wyświetlony komunikat o błędzie "*Ta nazwa obszaru roboczego jest już używana,może*to być:
* Nazwa obszaru roboczego nie jest dostępna i jest używana przez inną osobę w organizacji lub przez innego klienta.
* Obszar roboczy został usunięty w ciągu ostatnich 14 dni, a jego nazwa była zarezerwowana dla okresu usuwania nietrwałego. Aby zastąpić usuwanie programów i natychmiast usunąć obszar roboczy i utworzyć nowy obszar roboczy o tej samej nazwie, wykonaj następujące kroki, aby najpierw odzyskać obszar roboczy i wykonać trwałe usunięcie:<br>
   1. [Odzyskaj](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#recover-workspace) swój obszar roboczy.
   2. [Trwałe usunięcie](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#permanent-workspace-delete) obszaru roboczego.
   3. Utwórz nowy obszar roboczy o tej samej nazwie obszaru roboczego.


## <a name="permanent-workspace-delete"></a>Trwałe usuwanie obszaru roboczego
Metoda usuwania nietrwałego może nie mieścić się w niektórych scenariuszach, takich jak programowanie i testowanie, gdzie należy powtórzyć wdrożenie z tymi samymi ustawieniami i nazwą obszaru roboczego. W takich przypadkach można trwale usunąć obszar roboczy i "zastąpić" okres usuwania nietrwałego. Operacja usuwania stałego obszaru roboczego zwalnia nazwę obszaru roboczego i można utworzyć nowy obszar roboczy o tej samej nazwie.


> [!IMPORTANT]
> Użyj operacji usuwania stałego obszaru roboczego z ostrożnością, ponieważ jest ona nieodwracalna i nie będzie można odzyskać obszaru roboczego i jego danych.

Trwałe usuwanie obszaru roboczego można obecnie wykonać za pośrednictwem interfejsu API REST.

> [!NOTE]
> Każde żądanie interfejsu API musi zawierać token autoryzacji na okaziciela w nagłówku żądania.
>
> Token można uzyskać za pomocą:
> - [Rejestracje aplikacji](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
> - Przejdź do witryny Azure portal przy użyciu konsoli dewelopera (F12) w przeglądarce. Poszukaj w jednej z **instancji partii?** dla ciągu uwierzytelniania w obszarze **Nagłówki żądań**. Będzie to w *autoryzacji wzoru: <token>Okaziciel *. Skopiuj i dodaj to do wywołania interfejsu API, jak pokazano w przykładach.
> - Przejdź do witryny dokumentacji usługi Azure REST. naciśnij **przycisk Wypróbuj go** na dowolnym interfejsie API, skopiuj token na okaziciela i dodaj go do wywołania interfejsu API.
Aby trwale usunąć obszar roboczy, użyj wywołania interfejsu API [Workspaces - Delete REST]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) za pomocą znacznika force:
>
> ```rst
> DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview&force=true
> Authorization: Bearer eyJ0eXAiOiJKV1Qi….
> ```
Gdzie 'eyJ0eXAiOiJKV1Qi...". reprezentuje token pełnej autoryzacji.

## <a name="recover-workspace"></a>Odzyskiwanie obszaru roboczego

Jeśli masz uprawnienia współautora do grupy subskrypcji i zasobów, w której obszar roboczy został skojarzony przed operacją usuwania nietrwałego, możesz go odzyskać podczas okresu usuwania nietrwałego, w tym danych, konfiguracji i połączonych agentów. Po okresie usuwania nietrwałego obszar roboczy jest nie do odzyskania i przypisany do trwałego usunięcia. Nazwy usuniętych obszarów roboczych są zachowywane w okresie usuwania nietrwałego i nie mogą być używane podczas próby utworzenia nowego obszaru roboczego.  

Obszar roboczy można odzyskać, ponownie tworząc go przy użyciu następujących metod tworzenia obszaru roboczego: [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) lub [REST API,]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) o ile następujące właściwości są wypełniane szczegółami usuniętego obszaru roboczego:

* Identyfikator subskrypcji
* Nazwa grupy zasobów
* Nazwa obszaru roboczego
* Region

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

Obszar roboczy i wszystkie jego dane są przywracane po operacji odzyskiwania. Rozwiązania i połączone usługi zostały trwale usunięte z obszaru roboczego, gdy został usunięty i te powinny zostać ponownie skonfigurowane, aby przenieść obszar roboczy do wcześniej skonfigurowanego stanu. Niektóre dane mogą nie być dostępne dla kwerendy po odzyskaniu obszaru roboczego, dopóki skojarzone rozwiązania nie zostaną ponownie zainstalowane, a ich schematy zostaną dodane do obszaru roboczego.

> [!NOTE]
> * Odzyskiwanie obszaru roboczego nie jest obsługiwane w [witrynie Azure portal.](https://portal.azure.com) 
> * Ponowne utworzenie obszaru roboczego w okresie usuwania nietrwałego wskazuje, że ta nazwa obszaru roboczego jest już używana. 
> 
