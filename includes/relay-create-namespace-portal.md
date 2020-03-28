---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 500db9f6d299ea81b1f1b1be864df5d91ba1eae7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76021183"
---
1. Zaloguj się do [Portalu Azure][Azure portal].
1. Wybierz pozycję **Utwórz zasób**. Następnie wybierz polecenie > **Przekaźnik** **integracji**. Jeśli nie widzisz pozycji **Relay** na liście, wybierz pozycję **Zobacz wszystko** w prawym górnym rogu.
1. Wybierz **pozycję Utwórz**i wprowadź nazwę obszaru nazw w polu **Nazwa.** Usługa Azure portal sprawdza, czy nazwa jest dostępna.
1. Wybierz subskrypcję platformy Azure, w której chcesz utworzyć obszar nazw.
1. W przypadku [grupy zasobów](../articles/azure-resource-manager/management/manage-resource-groups-portal.md)wybierz istniejącą grupę zasobów, w której ma być umieszczana przestrzeń nazw, lub utwórz nową.  
1. Wybierz kraj lub region, w którym ma być hostowany obszar nazw.

    ![Create namespace][create-namespace]

1. Wybierz **pozycję Utwórz**. Portal Azure tworzy obszar nazw i włącza go. Po kilku minutach system aprowizuje zasoby dla Twojego konta.

### <a name="get-management-credentials"></a>Uzyskiwanie poświadczeń zarządzania

1. Wybierz **pozycję Wszystkie zasoby**, a następnie wybierz nowo utworzoną nazwę obszaru nazw.
1. Wybierz pozycję **Zasady dostępu współdzielonego**.  
1. W obszarze **Zasady dostępu współdzielonego** wybierz pozycję **RootManageSharedAccessKey**.
1. W obszarze **Zasady sygnatury dostępu współdzielonego: RootManageSharedAccessKey**wybierz przycisk **Kopiuj** obok **pozycji Podstawowy ciąg połączenia**. Ta akcja powoduje skopiowanie ciągu połączenia do schowka w celu późniejszego użycia. Wklej tę wartość do Notatnika lub innej tymczasowej lokalizacji.
1. Powtórz poprzedni krok w celu skopiowania i wklejenia wartości pozycji **Klucz podstawowy** w lokalizacji tymczasowej do późniejszego użycia.  

    ![połączenie — parametry][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
