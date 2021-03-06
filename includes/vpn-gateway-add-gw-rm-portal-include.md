---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c3c9dbca6a76bf0b10a83087fc31d9fa41c6bd03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331304"
---
1. Z menu [Portalu platformy Azure](https://portal.azure.com) wybierz polecenie **Utwórz zasób**. 

   ![Tworzenie zasobu w witrynie Azure portal](./media/vpn-gateway-add-gw-rm-portal-include/azure-portal-create-resource.png)
2. W polu **Wyszukaj w portalu Marketplace** wpisz "Brama sieci wirtualnej". Znajdź **bramę sieci wirtualnej** w powrocie wyszukiwania i wybierz wpis. Na stronie **Brama sieci wirtualnej** wybierz pozycję **Utwórz**. Spowoduje to otwarcie strony **Tworzenie bramy sieci wirtualnej**.
3. Na karcie **Podstawy** wprowadź wartości bramy sieci wirtualnej.

   ![Tworzenie pól strony bramy bramy sieci wirtualnej](./media/vpn-gateway-add-gw-rm-portal-include/gateway1.png "Tworzenie pól strony bramy bramy sieci wirtualnej")

   ![Tworzenie pól strony bramy bramy sieci wirtualnej](./media/vpn-gateway-add-gw-rm-portal-include/gateway2.png "Tworzenie pól strony bramy bramy sieci wirtualnej")

   **Szczegóły projektu**

   - **Subskrypcja**: Wybierz subskrypcję, której chcesz użyć z listy rozwijanej.
   - **Grupa zasobów:** To ustawienie jest wypełniane automatycznie po wybraniu sieci wirtualnej na tej stronie.

   **Szczegóły wystąpienia**

   - **Nazwa**: Nadaj nazwę bramie. Nazywanie bramy nie tym samym, co nazywanie podsieci bramy. Jest to nazwa obiektu bramy, który zostanie utworzony.
   - **Region:** Wybierz region, w którym chcesz utworzyć ten zasób. Region bramy musi być taki sam jak sieć wirtualna.
   - **Typ bramy**: Wybierz pozycję **Sieć VPN**. Bramy sieci VPN używają typu bramy sieci wirtualnej **Sieć VPN**.
   - **Typ sieci VPN**: Wybierz typ sieci VPN określony dla danej konfiguracji. Większość konfiguracji wymaga zastosowania typu sieci VPN opartego na trasach.
   - **Jednostka SKU**: Wybierz jednostkę SKU bramy z listy rozwijanej. Jednostki SKU wymienione na liście rozwijanej zależą od wybranego typu sieci VPN. Więcej informacji o jednostkach SKU bramy zawiera artykuł [Gateway SKUs](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Jednostki SKU bramy).
   - **Generowanie**: Aby uzyskać informacje na temat generowania bramy sieci VPN, zobacz [Jednostki SKU bramy](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).
   - **Sieć wirtualna:** z listy rozwijanej wybierz sieć wirtualną, do której chcesz dodać tę bramę.
   - **Zakres adresów podsieci bramy:** to pole jest wyświetlane tylko wtedy, gdy w sieci wirtualnej nie ma podsieci bramy. Jeśli to możliwe, należy wykonać zakres /27 lub większy (/26,/25 itp.). Nie zaleca się tworzenia zakresu mniejszego niż /28. Jeśli masz już podsieć bramy, możesz wyświetlić szczegóły GatewaySubnet, przechodząc do sieci wirtualnej. Kliknij **podsieci,** aby wyświetlić zakres. Jeśli chcesz zmienić zakres, możesz usunąć i ponownie utworzyć GatewaySubnet.

   **Publiczny adres IP**: to ustawienie określa obiekt publicznego adresu IP, który zostaje skojarzony z bramą sieci VPN. Publiczny adres IP jest dynamicznie przypisywany do tego obiektu podczas tworzenia bramy sieci VPN. Jedyną sytuacją, w której ma miejsce zmiana publicznego adresu IP, jest usunięcie bramy i jej ponowne utworzenie. Nie zmienia się on w przypadku zmiany rozmiaru, zresetowania ani przeprowadzania innych wewnętrznych czynności konserwacyjnych bądź uaktualnień bramy sieci VPN.

     - **Publiczny adres IP**: Pozostaw **utwórz nową** wybraną.
     - **Nazwa publicznego adresu IP**: W polu tekstowym wpisz nazwę wystąpienia publicznego adresu IP.
     - **Przypisanie:** brama sieci VPN obsługuje tylko usługę Dynamic.

   **Tryb aktywny-aktywny:** Tylko w przypadku tworzenia konfiguracji bramy aktywnej i aktywnej wybierz opcję **Włącz tryb aktywny-aktywny.** W przeciwnym razie pozostaw to ustawienie niewybrane.

   Pozostaw opcję **Skonfiguruj numer ASN protokołu BGP** niewybraną, chyba że Twoja konfiguracja wymaga tego ustawienia. Jeśli to ustawienie jest wymagane, domyślny numer ASN to 65515, ale można go zmienić.
4. Wybierz **pozycję Przejrzyj + utwórz,** aby uruchomić sprawdzanie poprawności. Po przejściu sprawdzania poprawności wybierz pozycję **Utwórz,** aby wdrożyć bramę sieci VPN. Pełne utworzenie i wdrożenie bramy może potrwać do 45 minut. Stan wdrożenia można zobaczyć na stronie Przegląd bramy.

Po utworzeniu bramy można znaleźć adres IP, który został do niej przypisany, spoglądając na sieć wirtualną w portalu. Brama jest widoczna jako urządzenie podłączone.
