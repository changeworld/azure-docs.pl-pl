---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/25/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 872ba86c9e43b1f6642331908eb829605f6c19bd
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77619719"
---
Możesz utworzyć sieć wirtualną w modelu wdrożenia usługi Resource Manager w witrynie Azure Portal, wykonując poniższe kroki. Aby uzyskać więcej informacji na temat sieci wirtualnych, zobacz [Virtual network overview](../articles/virtual-network/virtual-networks-overview.md) (Omówienie sieci wirtualnych).

>[!NOTE]
>Aby ta sieć wirtualna nawiązywała połączenie z lokalizacją lokalną, musisz zapewnić koordynację z administratorem sieci lokalnej w celu wyznaczenia zakresu adresów IP, którego będzie można używać w szczególności dla tej sieci wirtualnej. Jeśli po obu stronach połączenia sieci VPN będzie istnieć powielony zakres adresów, ruch będzie przekierowywany w nieoczekiwany sposób. Ponadto jeśli chcesz połączyć tę sieć wirtualną z inną siecią wirtualną, przestrzeń adresowa nie może pokrywać się z inną siecią wirtualną. Dlatego należy odpowiednio zaplanować konfigurację sieci.
>
>

1. Zaloguj się do [Azure portal](https://portal.azure.com).
1. W obszarze **Wyszukaj zasoby, usługi i dokumenty (G +/)** wpisz *sieć wirtualną*.

   ![Lokalizowanie strony zasobów Virtual Network](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "Strona lokalizowanie zasobu sieci wirtualnej")
1. Wybierz **Virtual Network** z wyników z **witryny Marketplace** .

   ![Wybierz sieć wirtualną](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "Strona lokalizowanie zasobu sieci wirtualnej")
1. Na stronie **Virtual Network** kliknij przycisk **Utwórz**.

   ![Strona sieci wirtualnej](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Kliknięcie pozycji Utwórz")
1. Po kliknięciu przycisku Utwórz zostanie otwarta strona **Tworzenie sieci wirtualnej** .

   ![Strona tworzenie sieci wirtualnej](./media/vpn-gateway-basic-vnet-rm-portal-include/create-virtual-network-page.png "Strona Tworzenie sieci wirtualnej")
1. Na stronie **Tworzenie sieci wirtualnej** skonfiguruj ustawienia sieci wirtualnej. Po wypełnieniu pól czerwony wykrzyknik zmieni się na zielony znacznik wyboru, jeśli znaki wprowadzone w polu są prawidłowe. Niektóre wartości są wypełnione automatycznie — możesz je zastąpić własnymi wartościami:

   - **Nazwa**: Wprowadź nazwę sieci wirtualnej.
   - **Przestrzeń adresowa**: Wprowadź przestrzeń adresową. Jeśli masz wiele przestrzeni adresowych do dodania, tutaj wprowadź pierwszą przestrzeń adresową. Możesz dodać dodatkowe przestrzenie adresowe później, po utworzeniu sieci wirtualnej. Jeśli konfiguracja wymaga przestrzeni adresów IPv6, zaznacz pole wyboru, aby wprowadzić te informacje.
   - **Subskrypcja**: Sprawdź, czy jest wyświetlana prawidłowa subskrypcja. Subskrypcje można zmieniać, korzystając z listy rozwijanej.
   - **Grupa zasobów**: wybierz istniejącą grupę zasobów lub Utwórz nową, wprowadzając nazwę nowej grupy zasobów. Jeśli tworzysz nową grupę, nadaj jej nazwę odpowiadającą wartościom planowanej konfiguracji. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../articles/azure-resource-manager/management/overview.md#resource-groups).
   - **Lokalizacja**: Wybierz lokalizację sieci wirtualnej. Lokalizacja określa miejsce, w którym zostaną umieszczone zasoby wdrażane w tej sieci wirtualnej.
   - **Podsieć**: Dodaj **nazwę** podsieci i **zakres adresów**podsieci. Możesz dodać dodatkowe podsieci później, po utworzeniu sieci wirtualnej.
   - **Ochrona DDoS**: wybierz pozycję **podstawowa**, chyba że chcesz używać usługi standardowej.
   - **Punkty końcowe usługi**: to ustawienie można pozostawić jako **wyłączone**, chyba że konfiguracja określi to ustawienie.
   - **Zapora**: to ustawienie można pozostawić jako **wyłączone**, chyba że konfiguracja określi to ustawienie.
1. Kliknij przycisk **Utwórz** , aby rozpocząć wdrażanie sieci wirtualnej.
