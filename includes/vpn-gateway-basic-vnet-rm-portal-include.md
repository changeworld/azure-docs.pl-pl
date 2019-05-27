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
ms.openlocfilehash: 835f23f98ebe56e0b19081f07dc3302ef93b27b9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66171589"
---
Możesz utworzyć sieć wirtualną w modelu wdrożenia usługi Resource Manager w witrynie Azure Portal, wykonując poniższe kroki. Aby uzyskać więcej informacji na temat sieci wirtualnych, zobacz [Virtual network overview](../articles/virtual-network/virtual-networks-overview.md) (Omówienie sieci wirtualnych).

>[!NOTE]
>Aby ta sieć wirtualna nawiązywała połączenie z lokalizacją lokalną, musisz zapewnić koordynację z administratorem sieci lokalnej w celu wyznaczenia zakresu adresów IP, którego będzie można używać w szczególności dla tej sieci wirtualnej. Jeśli po obu stronach połączenia sieci VPN będzie istnieć powielony zakres adresów, ruch będzie przekierowywany w nieoczekiwany sposób. Ponadto jeśli chcesz połączyć tę sieć wirtualną z inną siecią wirtualną, przestrzeń adresowa nie może pokrywać się z inną siecią wirtualną. Dlatego należy odpowiednio zaplanować konfigurację sieci.
>
>

1. Zaloguj się do witryny [Azure Portal](http://portal.azure.com) i wybierz pozycję **Utwórz zasób**. Zostanie otwarta strona **Nowy**.

2. W polu **Wyszukaj w witrynie Marketplace** wpisz *sieć wirtualna* i wybierz pozycję **Sieć wirtualna** na liście wyników. Zostanie otwarta strona **Sieć wirtualna**.

   ![Znajdowanie strony zasobów sieci wirtualnej](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "Znajdowanie strony zasobów sieci wirtualnej")

3. Z listy **Wybierz model wdrożenia** w dolnej części strony wybierz pozycję **Resource Manager**, a następnie kliknij polecenie **Utwórz**. Spowoduje to otwarcie strony **Tworzenie sieci wirtualnej**.

   ![Strona Tworzenie sieci wirtualnej](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet.png "Strona Tworzenie sieci wirtualnej")

4. Na stronie **Tworzenie sieci wirtualnej** skonfiguruj ustawienia sieci wirtualnej. Po wypełnieniu pól czerwony wykrzyknik zmieni się na zielony znacznik wyboru, jeśli znaki wprowadzone w polu są prawidłowe. Niektóre wartości są wypełnione automatycznie — możesz je zastąpić własnymi wartościami:

   - **Nazwa**: Wprowadź nazwę sieci wirtualnej.

   - **Przestrzeń adresowa**: Wprowadź przestrzeń adresową. Jeśli masz wiele przestrzeni adresowych do dodania, tutaj wprowadź pierwszą przestrzeń adresową. Możesz dodać dodatkowe przestrzenie adresowe później, po utworzeniu sieci wirtualnej.

   - **Subskrypcja**: Sprawdź, czy wyświetlana jest prawidłowa subskrypcja. Subskrypcje można zmieniać, korzystając z listy rozwijanej.

   - **Grupa zasobów**: Wybierz istniejącą grupę zasobów lub utwórz nową, wpisując nazwę nowej grupy zasobów. Jeśli tworzysz nową grupę, nadaj jej nazwę odpowiadającą wartościom planowanej konfiguracji. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).

   - **Lokalizacja**: Wybierz lokalizację sieci wirtualnej. Lokalizacja określa miejsce, w którym zostaną umieszczone zasoby wdrażane w tej sieci wirtualnej.

   - **Podsieć**: Dodaj **nazwę** podsieci i **zakres adresów** podsieci. Możesz dodać dodatkowe podsieci później, po utworzeniu sieci wirtualnej. 
     
5. Wybierz pozycję **Utwórz**.
