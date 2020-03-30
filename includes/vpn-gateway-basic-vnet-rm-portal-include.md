---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/03/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d2cf1a2e2ab9cf2d6e35aa12b5b0f8ddc04ad0e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301956"
---
Możesz utworzyć sieć wirtualną w modelu wdrożenia usługi Resource Manager w witrynie Azure Portal, wykonując poniższe kroki. Aby uzyskać więcej informacji na temat sieci wirtualnych, zobacz [Virtual network overview](../articles/virtual-network/virtual-networks-overview.md) (Omówienie sieci wirtualnych).

>[!NOTE]
>Korzystając z sieci wirtualnej jako części architektury międzylokalnej, należy koordynować z administratorem sieci lokalnej, aby wyrzeźbić zakres adresów IP, którego można użyć specjalnie dla tej sieci wirtualnej. Jeśli po obu stronach połączenia sieci VPN będzie istnieć powielony zakres adresów, ruch będzie przekierowywany w nieoczekiwany sposób. Ponadto jeśli chcesz połączyć tę sieć wirtualną z inną siecią wirtualną, przestrzeń adresowa nie może pokrywać się z inną siecią wirtualną. Dlatego należy odpowiednio zaplanować konfigurację sieci.
>
>

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. W **polu Zasoby wyszukiwania, usługa i dokumenty (G+/)** wpisz polecenie *Sieć wirtualna*.

   ![Strona zlokalizuj zasoby sieci wirtualnej](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "Znajdź stronę zasobów sieci wirtualnej")
1. Wybierz **pozycję Sieć wirtualna** z wyników **portalu Marketplace.**

   ![Wybierz sieć wirtualną](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "Znajdź stronę zasobów sieci wirtualnej")
1. Na stronie **Sieć wirtualna** wybierz pozycję **Utwórz**.

   ![strona sieci wirtualnej](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Wybierz pozycję Utwórz")
1. Po wybraniu opcji **Utwórz**zostanie otwarta strona **Utwórz sieć wirtualną.**
1. Na karcie **Podstawy** skonfiguruj **szczegóły projektu** i ustawienia sieci wirtualnej **szczegółów wystąpienia.**

   ![Karta Podstawowe informacje](./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png "Karta Podstawowe") Po wypełnieniu pól zostanie wyświetlony zielony znacznik wyboru po sprawdzeniu poprawności znaków wprowadzonych w polu. Niektóre wartości są wypełnione automatycznie — możesz je zastąpić własnymi wartościami:

   - **Subskrypcja**: Sprawdź, czy wymieniona subskrypcja jest poprawna. Subskrypcje można zmieniać, korzystając z listy rozwijanej.
   - **Grupa zasobów**: Wybierz istniejącą grupę zasobów lub kliknij przycisk **Utwórz nowy,** aby utworzyć nową. Aby uzyskać więcej informacji na temat grup zasobów, zobacz [Omówienie usługi Azure Resource Manager](../articles/azure-resource-manager/management/overview.md#resource-groups).
   - **Nazwa**: Wprowadź nazwę sieci wirtualnej.
   - **Region:** Wybierz lokalizację sieci wirtualnej. Lokalizacja określa miejsce, w którym zostaną umieszczone zasoby wdrażane w tej sieci wirtualnej.

1. Na karcie **Adresy IP** skonfiguruj wartości. Wartości pokazane w poniższych przykładach służą do celów demonstracyjnych. Dostosuj te wartości zgodnie z wymaganymi ustawieniami.

   ![Karta Adresy IP](./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png "Karta Adresy IP")  
   - **Przestrzeń adresowa IPv4**: Domyślnie przestrzeń adresowa jest tworzona automatycznie. Możesz kliknąć przestrzeń adresową, aby dostosować ją do własnych wartości. Można również dodać dodatkowe przestrzenie adresowe.
   - **IPv6**: Jeśli konfiguracja wymaga przestrzeni adresowej IPv6, wybierz pole **Dodaj przestrzeń adresową IPv6,** aby wprowadzić te informacje.
   - **Podsieć**: Jeśli używasz domyślnej przestrzeni adresowej, domyślna podsieć jest tworzona automatycznie. Jeśli zmienisz przestrzeń adresową, musisz dodać podsieć. Wybierz **+ Dodaj podsieć,** aby otworzyć okno **Dodaj podsieć.** Skonfiguruj następujące ustawienia, a następnie wybierz pozycję **Dodaj,** aby dodać wartości:
      - **Nazwa podsieci**: W tym przykładzie nazwaliśmy podsieć "FrontEnd".
      - **Zakres adresów podsieci:** Zakres adresów dla tej podsieci.

1. Na karcie **Zabezpieczenia** w tej chwili pozostaw wartości domyślne:

   - **Ochrona przed atakami DDos**: Podstawowa
   - **Firewall**: Wyłączone
1. Wybierz **opcję Przejrzyj + utwórz,** aby sprawdzić poprawność ustawień sieci wirtualnej.
1. Po sprawdzeniu poprawności ustawień wybierz pozycję **Utwórz**.
