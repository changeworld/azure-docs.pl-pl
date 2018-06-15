---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c1680f5b5aead08a927bac4b3234f57a78527248
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30921561"
---
1. Na stronie portalu po lewej kliknij pozycję **+** i wpisz tekst „brama sieci wirtualnej” w polu wyszukiwania. W obszarze **Wyniki** zlokalizuj i kliknij pozycję **Brama sieci wirtualnej**.
2. U dołu strony „Brama sieci wirtualnej” kliknij pozycję **Utwórz**. Spowoduje to otwarcie strony **Tworzenie bramy sieci wirtualnej**.
3. Na stronie **Tworzenie bramy sieci wirtualnej** określ wartości dla swojej bramy sieci wirtualnej.

    ![Pola strony Tworzenie bramy sieci wirtualnej](./media/vpn-gateway-add-gateway-portal-include/create-gateway.png "Nowa brama")

  - **Nazwa**: Nadaj nazwę bramie. Nie chodzi o nazwę podsieci bramy. Jest to nazwa obiektu bramy, który zostanie utworzony.
  - **Typ bramy**: Wybierz pozycję **Sieć VPN**. Bramy sieci VPN używają typu bramy sieci wirtualnej **Sieć VPN**. 
  - **Typ sieci VPN**: Wybierz typ sieci VPN określony dla danej konfiguracji. Większość konfiguracji wymaga zastosowania typu sieci VPN opartego na trasach.
  - **Jednostka SKU**: Wybierz jednostkę SKU bramy z listy rozwijanej. Jednostki SKU wymienione na liście rozwijanej zależą od wybranego typu sieci VPN. Więcej informacji o jednostkach SKU bramy zawiera artykuł [Gateway SKUs](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Jednostki SKU bramy).
  - **Lokalizacja**: Aby zobaczyć pozycję Lokalizacja, może być konieczne przewinięcie ekranu. Dostosuj pole **Lokalizacja**, aby wskazywało miejsce, w którym znajduje się sieć wirtualna. Jeśli lokalizacja nie wskazuje regionu, w którym znajduje się Twoja sieć wirtualna, sieć ta nie będzie widoczna na liście rozwijanej po wybraniu sieci wirtualnej w następnym kroku.
  - **Sieć wirtualna**: Wybierz sieć wirtualną, do której chcesz dodać tę bramę. Kliknij pozycję **Sieć wirtualna**, aby otworzyć stronę „Wybieranie sieci wirtualnej”. Wybierz sieć wirtualną. Jeśli sieć wirtualna nie jest widoczna, upewnij się, że wartość w polu Lokalizacja wskazuje region, w którym znajduje się sieć wirtualna.
  - **Zakres adresów podsieci bramy**: to ustawienie będzie wyświetlane tylko w przypadku, jeśli wcześniej nie utworzono podsieci bramy dla sieci wirtualnej. Jeśli wcześniej utworzono prawidłową podsieć bramy, to ustawienie nie będzie wyświetlane.
  - **Konfiguracja pierwszego adresu IP**: strona „Wybierz publiczny adres IP” służy do tworzenia obiektu publicznego adresu IP, który zostaje skojarzony z bramą sieci VPN. Publiczny adres IP jest dynamicznie przypisywany do tego obiektu podczas tworzenia bramy sieci VPN. Brama sieci VPN aktualnie obsługuje tylko *dynamiczne* przypisywanie publicznych adresów IP. Nie oznacza to jednak, że adres IP zmienia się po przypisaniu go do bramy sieci VPN. Jedyną sytuacją, w której ma miejsce zmiana publicznego adresu IP, jest usunięcie bramy i jej ponowne utworzenie. Nie zmienia się on w przypadku zmiany rozmiaru, zresetowania ani przeprowadzania innych wewnętrznych czynności konserwacyjnych bądź uaktualnień bramy sieci VPN.

    - Najpierw kliknij pozycję **Utwórz konfigurację protokołu IP bramy**, aby otworzyć stronę „Wybierz publiczny adres IP”. Następnie kliknij pozycję **+Utwórz nowe**, aby otworzyć stronę „Tworzenie publicznego adresu IP”.
    - Wprowadź **nazwę** dla publicznego adresu IP. Pozostaw jednostkę SKU jako **Podstawowa**, chyba że istnieje konkretny powód, aby ją zmienić na inną, a następnie kliknij przycisk **OK** u dołu tej strony, aby zapisać zmiany.

      ![Tworzenie publicznego adresu IP](./media/vpn-gateway-add-gateway-portal-include/public-ip-address-name.png "Tworzenie adresu IP")

4. Sprawdź poprawność ustawień. Jeśli chcesz, aby brama była wyświetlana na pulpicie nawigacyjnym, możesz wybrać pozycję **Przypnij do pulpitu nawigacyjnego** u dołu strony. 
5. Kliknij przycisk **Utwórz**, aby rozpocząć tworzenie bramy sieci VPN. Zostanie sprawdzona poprawność ustawień, a na pulpicie nawigacyjnym pojawi się kafelek „Wdrażanie bramy sieci wirtualnej”. Tworzenie bramy może potrwać do 45 minut. Być może będzie trzeba odświeżyć stronę portalu, aby zobaczyć, czy tworzenie zostało ukończone.

Po utworzeniu bramy sprawdź adres IP, który został do niej przypisany, spoglądając na sieć wirtualną w portalu. Brama jest widoczna jako urządzenie podłączone. Możesz kliknąć podłączone urządzenie (bramę sieci wirtualnej), aby wyświetlić więcej informacji.