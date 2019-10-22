---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/24/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9ce9fa29a29559a1eaaff6173737159f11aa83d8
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "71268892"
---
1. W portalu po lewej stronie kliknij pozycję **+ Utwórz zasób** i wpisz „Brama sieci wirtualnej” w polu wyszukiwania. Znajdź pozycję **Brama sieci wirtualnej** w wynikach wyszukiwania i kliknij tę pozycję. Na stronie **Brama sieci wirtualnej** kliknij pozycję **Utwórz**. Spowoduje to otwarcie strony **Tworzenie bramy sieci wirtualnej**.

   ![Utwórz pola strony bramy sieci wirtualnej](./media/vpn-gateway-add-gw-p2s-rm-portal-include/p2sgw.png "Utwórz pola strony bramy sieci wirtualnej")

   ![Utwórz pola strony bramy sieci wirtualnej](./media/vpn-gateway-add-gw-p2s-rm-portal-include/p2sgw2.png "Utwórz pola strony bramy sieci wirtualnej")
2. Na stronie **Tworzenie bramy sieci wirtualnej** wypełnij wartości dla swojej bramy sieci wirtualnej.

   **Szczegóły projektu**

   - **Subskrypcja**: wybierz subskrypcję, której chcesz użyć z listy rozwijanej.
   - **Grupa zasobów**: to ustawienie jest wypełniane po wybraniu sieci wirtualnej na tej stronie.

   **Szczegóły wystąpienia**

   - **Nazwa**: Nadaj nazwę bramie. Nazwa bramy nie jest taka sama jak nazwa podsieci bramy. Jest to nazwa obiektu bramy, który zostanie utworzony.
   - **Region**: Wybierz region, w którym chcesz utworzyć ten zasób. Region bramy musi być taki sam jak w przypadku sieci wirtualnej.
   - **Typ bramy**: Wybierz pozycję **Sieć VPN**. Bramy sieci VPN używają typu bramy sieci wirtualnej **Sieć VPN**. 
   - **Typ sieci VPN**: Wybierz typ sieci VPN określony dla danej konfiguracji. Większość konfiguracji wymaga zastosowania typu sieci VPN opartego na trasach.
   - **Jednostka SKU**: Wybierz jednostkę SKU bramy z listy rozwijanej. Jednostki SKU wymienione na liście rozwijanej zależą od wybranego typu sieci VPN. Więcej informacji o jednostkach SKU bramy zawiera artykuł [Gateway SKUs](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Jednostki SKU bramy).

      **Sieć wirtualna**: Wybierz sieć wirtualną, do której chcesz dodać tę bramę.

      **Zakres adresów podsieci bramy**: to pole jest wyświetlane tylko wtedy, gdy wybrana Sieć wirtualna nie ma podsieci bramy. Wypełnij zakres, jeśli nie masz jeszcze podsieci bramy. Jeśli to możliwe, należy wprowadzić zakres/27 lub większy (/26,/25 itp.)

   **Publiczny adres IP**: to ustawienie określa obiekt publicznego adresu IP, który zostaje skojarzony z bramą sieci VPN. Publiczny adres IP jest dynamicznie przypisywany do tego obiektu podczas tworzenia bramy sieci VPN. Jedyną sytuacją, w której ma miejsce zmiana publicznego adresu IP, jest usunięcie bramy i jej ponowne utworzenie. Nie zmienia się on w przypadku zmiany rozmiaru, zresetowania ani przeprowadzania innych wewnętrznych czynności konserwacyjnych bądź uaktualnień bramy sieci VPN.

     - **Publiczny adres IP**: pozostaw opcję **Utwórz nowe** .
     - **Nazwa publicznego adresu IP**: w polu tekstowym wpisz nazwę dla wystąpienia publicznego adresu IP.
     - **Przypisanie**: Brama sieci VPN obsługuje tylko dynamiczne.

   **Tryb aktywny-aktywny**: wybierz opcję **Włącz tryb aktywny-aktywny** tylko w przypadku tworzenia konfiguracji bramy Active-Active. W przeciwnym razie pozostaw to ustawienie niewybrane.

   Pozostaw opcję **Skonfiguruj numer ASN protokołu BGP** niewybraną, chyba że Twoja konfiguracja wymaga tego ustawienia. Jeśli to ustawienie jest wymagane, domyślny numer ASN to 65515, ale można go zmienić.

3. Kliknij przycisk **Przegląd + Utwórz** , aby uruchomić walidację. Po zakończeniu walidacji kliknij pozycję **Utwórz** , aby wdrożyć bramę sieci VPN. Aby w pełni utworzyć i wdrożyć bramę, może upłynąć do 45 minut. Stan wdrożenia można sprawdzić na stronie Przegląd dla bramy.

Po utworzeniu bramy można znaleźć adres IP, który został do niej przypisany, spoglądając na sieć wirtualną w portalu. Brama jest widoczna jako urządzenie podłączone.