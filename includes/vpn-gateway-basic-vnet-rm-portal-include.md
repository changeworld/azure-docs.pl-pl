---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 02297a45c9560f867e97d7024862ea0de5d3f7b2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467681"
---
Możesz utworzyć sieć wirtualną w modelu wdrożenia usługi Resource Manager w witrynie Azure Portal, wykonując poniższe kroki. Aby uzyskać więcej informacji na temat sieci wirtualnych, zobacz [Virtual network overview](../articles/virtual-network/virtual-networks-overview.md) (Omówienie sieci wirtualnych).

>[!NOTE]
>Aby ta sieć wirtualna nawiązywała połączenie z lokalizacją lokalną, musisz zapewnić koordynację z administratorem sieci lokalnej w celu wyznaczenia zakresu adresów IP, którego będzie można używać w szczególności dla tej sieci wirtualnej. Jeśli po obu stronach połączenia sieci VPN będzie istnieć powielony zakres adresów, ruch będzie przekierowywany w nieoczekiwany sposób. Ponadto jeśli chcesz połączyć tę sieć wirtualną z inną siecią wirtualną, przestrzeń adresowa nie może pokrywać się z inną siecią wirtualną. Dlatego należy odpowiednio zaplanować konfigurację sieci.
>
>

1. Zaloguj się do [portalu Azure](https://portal.azure.com).  W menu Azure Portal lub na stronie **głównej** , a następnie wybierz pozycję **Utwórz zasób**. Zostanie otwarta strona **Nowy**.

2. W obszarze **Wyszukaj w portalu Marketplace**wprowadź *sieć wirtualną* i wybierz **Virtual Network** z wyników.

   ![Lokalizowanie strony zasobów Virtual Network](./media/vpn-gateway-basic-vnet-rm-portal-include/search-marketplace-for-virtual-network.png "Strona lokalizowanie zasobu sieci wirtualnej")

   Zostanie otwarta strona **Sieć wirtualna**.

3. Z listy **Wybierz model wdrożenia** w dolnej części strony wybierz pozycję **Resource Manager**, a następnie kliknij polecenie **Utwórz**. Spowoduje to otwarcie strony **Tworzenie sieci wirtualnej**.

   ![Strona tworzenie sieci wirtualnej](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet.png "Strona Tworzenie sieci wirtualnej")

4. Na stronie **Tworzenie sieci wirtualnej** skonfiguruj ustawienia sieci wirtualnej. Po wypełnieniu pól czerwony wykrzyknik zmieni się na zielony znacznik wyboru, jeśli znaki wprowadzone w polu są prawidłowe. Niektóre wartości są wypełnione automatycznie — możesz je zastąpić własnymi wartościami:

   - **Nazwa**: Wprowadź nazwę sieci wirtualnej.

   - **Przestrzeń adresowa**: Wprowadź przestrzeń adresową. Jeśli masz wiele przestrzeni adresowych do dodania, tutaj wprowadź pierwszą przestrzeń adresową. Możesz dodać dodatkowe przestrzenie adresowe później, po utworzeniu sieci wirtualnej.

   - **Subskrypcja**: Sprawdź, czy jest wyświetlana prawidłowa subskrypcja. Subskrypcje można zmieniać, korzystając z listy rozwijanej.

   - **Grupa zasobów**: wybierz istniejącą grupę zasobów lub Utwórz nową, wprowadzając nazwę nowej grupy zasobów. Jeśli tworzysz nową grupę, nadaj jej nazwę odpowiadającą wartościom planowanej konfiguracji. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../articles/azure-resource-manager/management/overview.md#resource-groups).

   - **Lokalizacja**: Wybierz lokalizację sieci wirtualnej. Lokalizacja określa miejsce, w którym zostaną umieszczone zasoby wdrażane w tej sieci wirtualnej.

   - **Podsieć**: Dodaj **nazwę** podsieci i **zakres adresów**podsieci. Możesz dodać dodatkowe podsieci później, po utworzeniu sieci wirtualnej. 
     
5. Wybierz pozycję **Utwórz**.
