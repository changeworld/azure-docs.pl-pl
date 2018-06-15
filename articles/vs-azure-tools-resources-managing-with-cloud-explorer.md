---
title: Zarządzanie zasobami Azure za pomocą Eksplorator chmury | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać Eksploratora chmury do przeglądania i zarządzania zasobami Azure w programie Visual Studio.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 6347dc53-f497-49d5-b29b-e8b9f0e939d7
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/25/2017
ms.author: ghogen
ms.openlocfilehash: 9d8ae465e80ab38c1d7b0f6082865c110c0ff06b
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31794076"
---
# <a name="manage-the-resources-associated-with-your-azure-accounts-in-visual-studio-cloud-explorer"></a>Zarządzanie zasobami skojarzone z konta platformy Azure w Visual Studio Cloud Explorer
Eksplorator chmury umożliwia wyświetlanie Twoich zasobów platformy Azure i grup zasobów, sprawdzić ich właściwości i akcje developer klucza diagnostycznych z poziomu programu Visual Studio. 

Podobnie jak [portalu Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), Eksplorator chmury jest oparty na stosie usługi Azure Resource Manager. W związku z tym Eksplorator chmury rozumie zasobów, takich jak grupy zasobów platformy Azure i usług Azure, takich jak aplikacje logiki i interfejsu API apps i obsługuje [kontroli dostępu opartej na rolach](role-based-access-control/role-assignments-portal.md) (RBAC). 

## <a name="prerequisites"></a>Wymagania wstępne
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) z **obciążenia Azure** zaznaczone, lub starszej wersji programu Visual Studio z [Microsoft Azure SDK dla programu .NET 2.9](https://www.microsoft.com/en-us/download/details.aspx?id=51657).
- Konto Microsoft Azure — Jeśli nie masz konta, możesz [utworzyć konto bezpłatnej wersji próbnej](http://go.microsoft.com/fwlink/?LinkId=623901) lub [aktywować korzyści dla subskrybentów programu Visual Studio](http://go.microsoft.com/fwlink/?LinkId=623901).

> [!NOTE]
> Zaznacz, aby wyświetlić Eksplorator chmury **widoku** > **Eksplorator chmury** na pasku menu.   
> 
> 

## <a name="add-an-azure-account-to-cloud-explorer"></a>Dodaj Azure konto Eksplorator chmury
Aby wyświetlić zasoby skojarzone z kontem platformy Azure, najpierw należy dodać konto do Eksplorator chmury. 

1. W **Eksplorator chmury**, wybierz pozycję **ustawienia konta Azure**.

    ![Ikona ustawienia konta Azure Eksplorator chmury](media/vs-azure-tools-resources-managing-with-cloud-explorer/azure-account-settings.png)

1. Wybierz **Dodaj nowe konto**. 

    ![Łącze Eksploratora Dodaj konto w chmurze](media/vs-azure-tools-resources-managing-with-cloud-explorer/add-account-link.png)

1. Zaloguj się do konta platformy Azure zasobów, których chcesz przeglądać. 

1. Po zalogowaniu się do konta platformy Azure, wyświetlanie subskrypcji skojarzonych z tym kontem. Zaznacz pola wyboru dla subskrypcji konta, aby przejrzeć i wybrać pozycję **Zastosuj**. 
 
    ![Eksplorator chmury: Wybierz subskrypcje platformy Azure, aby wyświetlić](media/vs-azure-tools-resources-managing-with-cloud-explorer/select-subscriptions.png)

1. Po wybraniu subskrypcji zasobów, których chcesz przeglądać, te subskrypcje i zasoby wyświetlić w Eksploratorze chmury.

    ![Eksplorator zasobów dla konta platformy Azure w chmurze](media/vs-azure-tools-resources-managing-with-cloud-explorer/resources-listed.png)

## <a name="remove-an-azure-account-from-cloud-explorer"></a>Usuń konto platformy Azure w Eksploratorze chmury 

1. W **Eksplorator chmury**, wybierz pozycję **ustawienia konta Azure**.

    ![Ikona ustawienia konta Azure Eksplorator chmury](media/vs-azure-tools-resources-managing-with-cloud-explorer/azure-account-settings.png)

1. Obok konto, którego chcesz usunąć, wybierz **Usuń**.

    ![Ikona ustawienia konta Azure Eksplorator chmury](media/vs-azure-tools-resources-managing-with-cloud-explorer/remove-account.png)

## <a name="view-resource-types-or-resource-groups"></a>Wyświetlanie typów zasobów lub grupy zasobów
Aby wyświetlić zasobów platformy Azure, można wybrać opcję **typów zasobów** lub **grup zasobów** widoku.

1. W **Eksplorator chmury**, wybierz z listy rozwijanej widoku zasobów.

    ![Lista rozwijana Eksplorator chmury do wybierz widok żądanych zasobów](media/vs-azure-tools-resources-managing-with-cloud-explorer/resources-view-dropdown.png)

1. Z menu kontekstowego wybierz żądany widok: 

    - **Typy zasobów** view - wspólnej widok używany na [portalu Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), pokazuje zasobów platformy Azure podzielone według typu, takich jak aplikacje sieci web, konta magazynu i maszyn wirtualnych. 
    - **Grupy zasobów** view - zasobów Azure kategoryzuje przez grupę zasobów platformy Azure, z którym są powiązane. Grupa zasobów to pakiet zasobów platformy Azure, zwykle używanych przez określoną aplikację. Aby dowiedzieć się więcej na temat grup zasobów platformy Azure, zobacz [Omówienie usługi Azure Resource Manager](./azure-resource-manager/resource-group-overview.md).

    Na poniższej ilustracji przedstawiono porównanie zasobów dwa widoki:

    ![Eksplorator zasobów widoków porównania w chmurze](media/vs-azure-tools-resources-managing-with-cloud-explorer/resource-views-comparison.png)

## <a name="view-and-navigate-resources-in-cloud-explorer"></a>Wyświetlanie i przejdź do zasobów w Eksploratorze chmury
Aby przejść do zasobów platformy Azure i wyświetlić informacje o nim w Eksploratorze chmury, rozwiń element typu lub grupy zasobów, a następnie wybierz zasób. Po wybraniu zasobu informacje są wyświetlane w dwóch kartach - **akcje** i **właściwości** — u dołu Eksplorator chmury. 

- **Akcje** karcie — zawiera listę akcji może zająć w Eksploratorze chmury dla wybranego zasobu. Te opcje można również wyświetlić, klikając prawym przyciskiem myszy zasób, aby wyświetlić jego menu kontekstowego.

- **Właściwości** karcie - Wyświetla właściwości zasobów, takich jak grupy jego typu, ustawienia regionalne i zasobów, z którym jest skojarzona.

Na poniższej ilustracji przedstawiono przykład porównanie informacje wyświetlane na każdej karcie usługi aplikacji:

![](./media/vs-azure-tools-resources-managing-with-cloud-explorer/actions-and-properties.png)

Każdy zasób podejmuje działanie **Otwórz w portalu**. Po wybraniu tej akcji, Eksplorator chmury Wyświetla wybranego zasobu w [portalu Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). **Otwórz w portalu** funkcja jest przydatna do nawigowania do głęboko zagnieżdżone zasoby.

Dodatkowe akcje i wartości właściwości mogą również wyświetlane w zależności od zasobów platformy Azure. Na przykład aplikacje sieci web i aplikacje logiki również mieć akcje **Otwórz w przeglądarce** i **dołączyć debuger** oprócz **Otwórz w portalu**. Operacje, aby otworzyć edytory są wyświetlane po wybraniu obiektu blob z konta magazynu, kolejki lub tabeli. Aplikacje platformy Azure ma **adres URL** i **stan** właściwości, gdy zasobów magazynu ma właściwości parametrów połączenia i klucza.

## <a name="find-resources-in-cloud-explorer"></a>Znajdź zasoby w Eksploratorze chmury
Aby znaleźć zasoby o określonej nazwie w subskrypcji konto platformy Azure, wprowadź nazwę w **wyszukiwania** pole w Eksploratorze chmury.

![Znajdowanie zasobów w Eksploratorze chmury](./media/vs-azure-tools-resources-managing-with-cloud-explorer/search-for-resources.png)

Podczas wpisywania znaków w **wyszukiwania** okno, tylko tych zasobów, które odpowiada te znaki są wyświetlane w drzewie zasobów.
