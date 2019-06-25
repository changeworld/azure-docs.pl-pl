---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/24/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 35f987f26ce47c19e3d5eb1ca5d2bb32d0c7ad1b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183088"
---
1. Na stronie portalu po lewej kliknij pozycję **+** i wpisz tekst „brama sieci wirtualnej” w polu wyszukiwania. W obszarze **Wyniki** zlokalizuj i kliknij pozycję **Brama sieci wirtualnej**.
2. U dołu strony „Brama sieci wirtualnej” kliknij pozycję **Utwórz**. Spowoduje to otwarcie strony **Tworzenie bramy sieci wirtualnej**.
3. Na stronie **Tworzenie bramy sieci wirtualnej** określ wartości dla swojej bramy sieci wirtualnej.

    ![Pola strony Tworzenie bramy sieci wirtualnej](./media/vpn-gateway-add-gateway-portal-include/create-gateway.png "Nowa brama")

   - **Nazwa**: Nazwa bramy. Nie chodzi o nazwę podsieci bramy. Jest to nazwa obiektu bramy, który zostanie utworzony.
   - **Typ bramy**: wybierz pozycję **VPN**. Bramy sieci VPN używają typu bramy sieci wirtualnej **Sieć VPN**. 
   - **Typ sieci VPN**: Wybierz typ sieci VPN, który jest określony dla danej konfiguracji. Większość konfiguracji wymaga zastosowania typu sieci VPN opartego na trasach.
   - **SKU**: Wybierz jednostkę SKU bramy z listy rozwijanej. Jednostki SKU wymienione na liście rozwijanej zależą od wybranego typu sieci VPN. Więcej informacji o jednostkach SKU bramy zawiera artykuł [Gateway SKUs](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Jednostki SKU bramy).

     Ustawienie **Włącz tryb aktywny-aktywny** wybierz tylko wtedy, gdy tworzysz konfigurację bramy typu aktywne-aktywne. W przeciwnym razie pozostaw to ustawienie niewybrane.
   - **Lokalizacja**: Może być konieczne przewinięcie w celu wyświetlenia lokalizacji. Dostosuj pole **Lokalizacja**, aby wskazywało miejsce, w którym znajduje się sieć wirtualna. Na przykład: Zachodnie stany USA. Jeśli lokalizacja nie wskazuje regionu, w którym znajduje się Twoja sieć wirtualna, sieć ta nie będzie widoczna na liście rozwijanej po wybraniu sieci wirtualnej w następnym kroku.
   - **Sieć wirtualna**: Wybierz sieć wirtualną, do której chcesz dodać bramę. Kliknij pozycję **Sieć wirtualna**, aby otworzyć stronę „Wybieranie sieci wirtualnej”. Wybierz sieć wirtualną. Jeśli sieć wirtualna nie jest widoczna, upewnij się, że wartość w polu Lokalizacja wskazuje region, w którym znajduje się sieć wirtualna.
   - **Zakres adresów podsieci bramy**: To ustawienie będą widzieć tylko, jeśli nie został wcześniej utworzony podsieć bramy dla sieci wirtualnej. Jeśli wcześniej utworzono prawidłową podsieć bramy, to ustawienie nie będzie wyświetlane.
   - **Publiczny adres IP**: To ustawienie określa obiektu publicznego adresu IP, który zostaje skojarzony z bramą sieci VPN. Publiczny adres IP jest dynamicznie przypisywany do tego obiektu podczas tworzenia bramy sieci VPN. Brama sieci VPN aktualnie obsługuje tylko *dynamiczne* przypisywanie publicznych adresów IP. Nie oznacza to jednak, że adres IP zmienia się po przypisaniu go do bramy sieci VPN. Jedyną sytuacją, w której ma miejsce zmiana publicznego adresu IP, jest usunięcie bramy i jej ponowne utworzenie. Nie zmienia się on w przypadku zmiany rozmiaru, zresetowania ani przeprowadzania innych wewnętrznych czynności konserwacyjnych bądź uaktualnień bramy sieci VPN.

     - Pozostaw wybraną opcję **Utwórz nowy**.
     - W polu tekstowym wprowadź **nazwę** publicznego adresu IP.

4. Pozostaw opcję **Skonfiguruj numer ASN protokołu BGP** niewybraną, chyba że Twoja konfiguracja wymaga tego ustawienia. Jeśli to ustawienie jest wymagane, domyślny numer ASN to 65515, ale można go zmienić.
5. Sprawdź poprawność ustawień. Jeśli chcesz, aby brama była wyświetlana na pulpicie nawigacyjnym, możesz wybrać pozycję **Przypnij do pulpitu nawigacyjnego** u dołu strony. 
6. Kliknij przycisk **Utwórz**, aby rozpocząć tworzenie bramy sieci VPN. Zostanie sprawdzona poprawność ustawień, a na pulpicie nawigacyjnym pojawi się kafelek „Wdrażanie bramy sieci wirtualnej”. Tworzenie bramy może potrwać do 45 minut. Być może będzie trzeba odświeżyć stronę portalu, aby zobaczyć, czy tworzenie zostało ukończone.
   brama.

Po utworzeniu bramy sprawdź adres IP, który został do niej przypisany, spoglądając na sieć wirtualną w portalu. Brama jest widoczna jako urządzenie podłączone. Możesz kliknąć podłączone urządzenie (bramę sieci wirtualnej), aby wyświetlić więcej informacji.