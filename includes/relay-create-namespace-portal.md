---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 3e3617f68d58e51f24affaae7d69812195b72fd0
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71210302"
---
1. Zaloguj się w witrynie [Azure Portal][Azure portal].
1. Wybierz pozycję **Utwórz zasób**. Następnie wybierz pozycję **Integracja** > **Relay**. Jeśli nie widzisz pozycji **Relay** na liście, wybierz pozycję **Zobacz wszystko** w prawym górnym rogu.
1. Wybierz pozycję **Utwórz**, a następnie wprowadź nazwę przestrzeni nazw w polu **Nazwa** . Azure Portal sprawdza, czy nazwa jest dostępna.
1. Wybierz subskrypcję platformy Azure, w której chcesz utworzyć przestrzeń nazw.
1. W obszarze [Grupa zasobów](../articles/azure-resource-manager/manage-resource-groups-portal.md)wybierz istniejącą grupę zasobów, w której ma zostać umieszczona przestrzeń nazw, lub Utwórz nową.  
1. Wybierz kraj lub region, w którym powinna być hostowana przestrzeń nazw.

    ![Tworzenie przestrzeni nazw][create-namespace]

1. Wybierz pozycję **Utwórz**. Azure Portal tworzy przestrzeń nazw i włącza ją. Po kilku minutach system aprowizuje zasoby dla Twojego konta.

### <a name="get-management-credentials"></a>Uzyskiwanie poświadczeń zarządzania

1. Wybierz pozycję **wszystkie zasoby**, a następnie wybierz nowo utworzoną nazwę przestrzeni nazw.
1. Wybierz pozycję **zasady dostępu współdzielonego**.  
1. W obszarze **Zasady dostępu współdzielonego** wybierz pozycję **RootManageSharedAccessKey**.
1. W **obszarze zasady SAS: RootManageSharedAccessKey**wybierz przycisk **Kopiuj** obok pozycji **podstawowe parametry połączenia**. Ta akcja powoduje skopiowanie parametrów połączenia do Schowka w celu późniejszego użycia. Wklej tę wartość do Notatnika lub innej tymczasowej lokalizacji.
1. Powtórz poprzedni krok w celu skopiowania i wklejenia wartości pozycji **Klucz podstawowy** w lokalizacji tymczasowej do późniejszego użycia.  

    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
