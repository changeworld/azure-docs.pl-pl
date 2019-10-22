---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3d9826e3eae2a60b217df1406d26d83c78fbdefb
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "67673462"
---
Aby utworzyć sieć wirtualną w modelu wdrożenia usługi Resource Manager w witrynie Azure Portal, wykonaj poniższe kroki. Zamieszczone zrzuty ekranu są przykładowe. Przedstawione wartości należy zastąpić własnymi. Aby uzyskać więcej informacji dotyczących pracy z sieciami wirtualnymi, zobacz temat [Omówienie sieci wirtualnych](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Jeśli chcesz, aby ta sieć wirtualna nawiązywała połączenie z lokalizacją lokalną (poza tworzeniem konfiguracji P2S), musisz zapewnić koordynację z administratorem sieci lokalnej w celu wyznaczenia zakresu adresów IP, którego będzie można używać w szczególności dla tej sieci wirtualnej. Jeśli po obu stronach połączenia sieci VPN istnieje powielony zakres adresów, ruch nie będzie przekierowywany w oczekiwany sposób. Ponadto jeśli chcesz połączyć tę sieć wirtualną z inną siecią wirtualną, przestrzeń adresowa nie może pokrywać się z inną siecią wirtualną. Dlatego należy odpowiednio zaplanować konfigurację sieci.
>
>

1. W przeglądarce przejdź do witryny [Azure Portal](https://portal.azure.com) i, jeśli to konieczne, zaloguj się przy użyciu konta platformy Azure.
2. Kliknij pozycję **+** . W polu **Szukaj w witrynie Marketplace** wpisz „Sieć wirtualna”. Znajdź pozycję **Sieć wirtualna** na liście wyników i kliknij, aby otworzyć stronę **Sieć wirtualna**.

   ![Lokalizowanie strony zasobów Virtual Network](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/newvnetportal700.png "Strona lokalizowanie zasobu sieci wirtualnej")
3. Z listy **Wybierz model wdrożenia** w dolnej części strony Sieć wirtualna wybierz pozycję **Menedżer zasobów**, a następnie kliknij przycisk **Utwórz**.

   ![Wybierz Menedżer zasobów](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/resourcemanager250.png "Wybór opcji Menedżer zasobów")
4. Na stronie **Tworzenie sieci wirtualnej** skonfiguruj ustawienia sieci wirtualnej. Po wypełnieniu pól czerwony wykrzyknik zmieni się na zielony znacznik wyboru, jeśli znaki wprowadzone w polu są prawidłowe. Niektóre wartości mogą być wypełnione automatycznie. W takim przypadku należy zastąpić je własnymi wartościami. Strona **Tworzenie sieci wirtualnej** wygląda podobnie do przedstawionej w poniższym przykładzie:

   ![Sprawdzanie poprawności pola](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/vnetp2s.png "Sprawdzanie poprawności pól")
5. **Nazwa**: Wprowadź nazwę sieci wirtualnej.
6. **Przestrzeń adresowa**: Wprowadź przestrzeń adresową. Jeśli masz wiele przestrzeni adresowych do dodania, dodaj pierwszą przestrzeń adresową. Możesz dodać dodatkowe przestrzenie adresowe później, po utworzeniu sieci wirtualnej.
7. **Subskrypcja**: Sprawdź, czy wyświetlana jest prawidłowa subskrypcja. Subskrypcje można zmieniać, korzystając z listy rozwijanej.
8. **Grupa zasobów**: Wybierz istniejącą grupę zasobów lub utwórz nową, wpisując nazwę nowej grupy zasobów. Jeśli tworzysz nową grupę, nadaj jej nazwę odpowiadającą wartościom planowanej konfiguracji. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
9. **Lokalizacja**: Wybierz lokalizację sieci wirtualnej. Lokalizacja określa miejsce, w którym zostaną umieszczone zasoby wdrażane w tej sieci wirtualnej.
10. **Podsieć**: Dodaj nazwę podsieci i zakres adresów podsieci. Możesz dodać dodatkowe podsieci później, po utworzeniu sieci wirtualnej.
11. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**, jeśli chcesz, aby Twoją sieć wirtualną można było łatwo znaleźć na pulpicie nawigacyjnym, a następnie kliknij przycisk **Utwórz**.

    ![Przypnij do pulpitu nawigacyjnego](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/pintodashboard150.png "Przypnij do pulpitu nawigacyjnego")
12. Po kliknięciu przycisku **Utwórz** na pulpicie nawigacyjnym zostanie umieszczony kafelek, który będzie odzwierciedlać postęp Twojej sieci wirtualnej. Wygląd kafelka zmienia się w trakcie tworzenia sieci wirtualnej.

    ![Tworzenie kafelka sieci wirtualnej](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/deploying150.png "Kafelek tworzenia sieci wirtualnej")
