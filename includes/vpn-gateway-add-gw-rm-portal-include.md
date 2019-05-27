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
ms.openlocfilehash: 72e61a36b58c0bc666f3e19b71fb1abe842208f5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150254"
---
1. Zaloguj się w witrynie Azure portal i wybierz **Utwórz zasób**. **New** zostanie otwarta strona.

2. W **pole marketplace wyszukiwania**, wprowadź *bramy sieci wirtualnej*i wybierz **bramy sieci wirtualnej** z listy wyszukiwania. 

3. Na **bramy sieci wirtualnej** wybierz opcję **Utwórz** otworzyć **Tworzenie bramy sieci wirtualnej** strony.

   ![Pola na stronie Tworzenie bramy sieci wirtualnej](./media/vpn-gateway-add-gw-rm-portal-include/gw.png "Pola na stronie Tworzenie bramy sieci wirtualnej")

4. Na **Tworzenie bramy sieci wirtualnej** strony, podaj wartości dla swojej bramy sieci wirtualnej:

   - **Nazwa**: Wprowadź nazwę obiektu bramy, który tworzysz. Ta nazwa jest inna niż nazwa podsieci bramy. 

   - **Typ bramy**: Wybierz **VPN** dla bram sieci VPN. 

   - **Typ sieci VPN**: Wybierz typ sieci VPN, który jest określony dla danej konfiguracji. Większość konfiguracji wymaga zastosowania **oparte na trasach** typu sieci VPN.

   - **SKU**: Wybierz jednostkę SKU bramy z listy rozwijanej. Jednostki SKU wymienione na liście rozwijanej zależą od wybranego typu sieci VPN. Więcej informacji o jednostkach SKU bramy zawiera artykuł [Gateway SKUs](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Jednostki SKU bramy).

      Wybierz tylko **Włącz tryb aktywny aktywny** przy tworzeniu konfiguracji aktywne aktywne bramy. W przeciwnym razie pozostaw to ustawienie niewybrane.
  
   - **Lokalizacja**: Może być konieczne przewinięcie w celu wyświetlenia **lokalizacji**. Ustaw **lokalizacji** do lokalizacji, w którym znajduje się Twoja sieć wirtualna. Na przykład **zachodnie stany USA**. Jeśli nie ustawisz lokalizacji do regionu, w którym znajduje się Twoja sieć wirtualna nie będzie on widoczny na liście rozwijanej po wybraniu sieci wirtualnej.

   - **Sieć wirtualna**: Wybierz sieć wirtualną, do której chcesz dodać bramę. Wybierz **sieć wirtualna** otworzyć **wybierz sieć wirtualną** strony i wybierz sieć wirtualną. Jeśli nie widzisz sieci wirtualnej, upewnij się, **lokalizacji** pole jest ustawione na region, w którym znajduje się Twoja sieć wirtualna.

   - **Zakres adresów podsieci bramy**: To ustawienie, będą widoczne tylko, jeśli wcześniej nie utworzono podsieć bramy dla sieci wirtualnej. Jeśli wcześniej utworzono prawidłową podsieć bramy, to ustawienie nie będzie wyświetlane.

   - **Publiczny adres IP**: To ustawienie określa obiektu publicznego adresu IP, skojarzony z bramą sieci VPN. Publiczny adres IP jest dynamicznie przypisywany do tego obiektu podczas tworzenia bramy sieci VPN. Brama sieci VPN aktualnie obsługuje tylko *dynamiczne* publicznych przydzielanie adresów IP. Dynamiczna alokacja nie oznacza to jednak, że adres IP zmienia się po przypisaniu go do bramy sieci VPN. Jedyną sytuacją zmian publicznego adresu IP jest, gdy usunięcie bramy i jej ponowne utworzenie. Nie zmienia się on w przypadku zmiany rozmiaru, zresetowania ani przeprowadzania innych wewnętrznych czynności konserwacyjnych bądź uaktualnień bramy sieci VPN.
    
      - Pozostaw wybraną opcję **Utwórz nowy**.

      - W polu tekstowym wprowadź nazwę dla publicznego adresu IP.

   - **Skonfiguruj numer ASN protokołu BGP**: Pozostaw niezaznaczoną, chyba że specjalnie wymaga konfiguracji. Jeśli to ustawienie jest wymagane, domyślny numer ASN jest *65515*, które można zmienić.
     
5. Sprawdź ustawienia i wybierz **Utwórz** aby rozpocząć tworzenie bramy sieci VPN. Ustawienia zostaną zweryfikowane, a zobaczysz **wdrażanie bramy sieci wirtualnej** Kafelek na pulpicie nawigacyjnym. Tworzenie bramy może potrwać do 45 minut. Być może będzie trzeba odświeżyć stronę portalu, aby zobaczyć, czy tworzenie zostało ukończone.

6. Po utworzeniu bramy Sprawdź adres IP, który jest do niej przypisany, wyświetlając sieć wirtualną w portalu. Brama jest widoczna jako urządzenie podłączone. Możesz wybrać podłączone urządzenie (bramę sieci wirtualnej) aby wyświetlić więcej informacji.