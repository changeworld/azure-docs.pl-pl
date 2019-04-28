---
title: Nawiązywanie połączenia, konfigurowanie i aktywować bramy pola danych platformy Azure w witrynie Azure portal | Dokumentacja firmy Microsoft
description: Trzeci samouczek do wdrożenia bramy pola danych powoduje, że możesz się połączyć, konfigurowanie i Aktywuj urządzenia wirtualnego.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: 898cb63f8868ce2abaee8784214322edf9a56997
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60756499"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Samouczek: Łączenie, konfigurowanie, Aktywuj bramy pola danych platformy Azure

## <a name="introduction"></a>Wprowadzenie

W tym samouczku opisano, jak nawiązać połączenie, konfigurowanie i aktywować urządzenie bramy pola danych za pomocą lokalnego Interfejsu w przeglądarce. 

Proces instalacji i aktywacji może potrwać około 10 minut, aby zakończyć. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Nawiązać połączenie z urządzeniem wirtualnym
> * Konfigurowanie i Aktywuj urządzenia wirtualnego

## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfigurować bramę pole danych, upewnij się, że:

* Już przeprowadzono aprowizację urządzenia wirtualnego i uzyskać połączonych adres URL, aby go zgodnie z opisem w [Aprowizowanie bramy pola danych w funkcji Hyper-V](data-box-gateway-deploy-provision-hyperv.md) lub [Aprowizowanie bramy pola danych w środowisku VMware](data-box-gateway-deploy-provision-vmware.md).
* Masz klucz aktywacji usługi bramy pola danych, utworzony w celu zarządzania urządzeniami bramy pola danych. Aby uzyskać więcej informacji, przejdź do [przygotowanie do wdrożenia usługi Azure Gateway pole danych](data-box-gateway-deploy-prep.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Łączenie do lokalnego internetowego interfejsu użytkownika konfiguracji 

1. Otwórz okno przeglądarki i dostęp do lokalnego internetowego interfejsu użytkownika z urządzenia pod adresem:
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   Użyj adresu URL połączenia zanotowanego w poprzednim samouczku. Zostanie wyświetlony błąd lub ostrzeżenie informujące, że występuje problem z certyfikatem zabezpieczeń witryny sieci Web.

2. Wybierz **Kontynuuj, aby ta strona sieci Web**. Te kroki mogą różnić się w zależności od przeglądarki, z którego korzystasz.
   
    ![Komunikat błędu certyfikatu zabezpieczeń witryny sieci Web](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Zaloguj się do internetowego interfejsu użytkownika urządzenia wirtualnego. Domyślne hasło jest *Password1*. 
   
    ![Zaloguj się do lokalnego internetowego interfejsu użytkownika](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. W wierszu polecenia Zmień hasło urządzenia. Nowe hasło musi zawierać od 8 do 16 znaków. Musi zawierać 3 z następujących elementów: wielkie litery, małe litery, cyfry i znaki specjalne.

    ![Zmienianie hasła urządzenia](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Jesteś teraz w **pulpit nawigacyjny** urządzenia.

## <a name="set-up-and-activate-the-virtual-device"></a>Konfigurowanie i Aktywuj urządzenia wirtualnego
 
Pulpit nawigacyjny wyświetla różne ustawienia, które są wymagane, aby skonfigurować i zarejestrować urządzenie wirtualne przy użyciu usługi bramy pola danych. **Nazwy urządzenia**, **ustawienia sieciowe**, **ustawień serwera proxy sieci Web**, i **ustawienia czasu** są opcjonalne. Tylko ustawienia wymagane są **ustawienia chmury**.
   
![Lokalnego internetowego interfejsu użytkownika "Pulpit nawigacyjny" strony](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. W okienku po lewej stronie wybierz **nazwy urządzenia**, a następnie wprowadź przyjazną nazwę dla danego urządzenia. Przyjazna nazwa musi zawierać od 1 do 15 znaków i być litery, cyfry i łączniki.

    ![Lokalnego internetowego interfejsu użytkownika "Nazwa urządzenia" strony](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. (Opcjonalnie) W okienku po lewej stronie wybierz **ustawienia sieciowe** a następnie skonfiguruj ustawienia. Na urządzeniu wirtualnym zostanie wyświetlona co najmniej jeden interfejs sieciowy i inne w zależności od tego, ile skonfigurowanych na podstawowej maszynie wirtualnej. **Ustawienia sieciowe** strony dla urządzenia wirtualnego z interfejsami sieciowymi włączone jest pokazany poniżej.
    
    ![Strona "Ustawienia sieci" systemu lokalnego internetowego interfejsu użytkownika](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Podczas konfigurowania ustawień sieciowych, należy pamiętać:

    - Jeżeli w Twoim środowisku włączony jest protokół DHCP, interfejsy sieciowe są automatycznie konfigurowane. Adres IP, podsieci, bramy i DNS są automatycznie przypisywane.
    - Jeśli nie jest włączony protokół DHCP, możesz przypisać statyczne adresy IP, w razie potrzeby.
    - Możesz skonfigurować interfejsu sieciowego w przypadku protokołu IPv4.

     >[!NOTE] 
     > Firma Microsoft zaleca, przełącza lokalny adres IP interfejsu sieciowego z statycznego protokołu DHCP, o ile nie masz innego adresu IP, aby połączyć się z urządzeniem. Jeśli przy użyciu jednego interfejsu sieciowego i przełącz się do protokołu DHCP, będzie można ustalić adres DHCP. Jeśli chcesz zmienić adres DHCP, poczekaj, aż po urządzenia został zarejestrowany w usłudze, a następnie Zmień. Można wyświetlić adresy IP wszystkich kart w **właściwości urządzenia** w witrynie Azure portal dla usługi.

3. Opcjonalnie skonfiguruj serwer proxy sieci Web. Mimo, że konfiguracja serwera proxy sieci web jest opcjonalny, jeśli używasz serwera proxy sieci web, możesz go skonfigurować tylko na tej stronie.
   
   ![Strona "Ustawienia serwera proxy sieci Web" interfejsu użytkownika lokalnego sieci web](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Na **serwera proxy sieci Web** wykonaj następujące czynności:
   
   1. W **adres URL serwera proxy sieci Web** wprowadź adres URL w następującym formacie: `http://&lt;host-IP address or FQDN&gt;:Port number`. Adresy URL HTTPS nie są obsługiwane.
   2. W obszarze **uwierzytelniania**, wybierz opcję **Brak** lub **NTLM**.
   3. Jeśli używasz uwierzytelniania wprowadź **Username** i **hasło**.
   4. Aby zweryfikować i zastosowania ustawień serwera proxy sieci web skonfigurowany, wybierz pozycję **Zastosuj**.

4. (Opcjonalnie) W okienku po lewej stronie wybierz **ustawienia czasu**, a następnie skonfigurować strefę czasową i podstawowych i pomocniczych serwerów NTP dla urządzenia. 

    Serwery NTP są wymagane, ponieważ urządzenie musi synchronizować czas, aby zapewnić możliwość uwierzytelnienia przy użyciu dostawców usług w chmurze.
    
    ![Strona "Ustawienia czasu" usługi lokalnego internetowego interfejsu użytkownika](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    W **ustawienia czasu** wykonaj następujące czynności:
    
    1. W **strefa czasowa** listę rozwijaną, wybierz strefę czasową, która odnosi się do lokalizacji geograficznej, w której wdrożono urządzenie na liście.
        Domyślna strefa czasowa dla Twojego urządzenia jest czasu PST. Wszystkie zaplanowane operacje urządzenia będą wykonywane w ramach tej strefy czasowej.

    2. Określ **podstawowy serwer NTP** dla urządzenia lub zaakceptuj wartość domyślną `time.windows.com`.   
        Upewnij się, że sieć zezwala na ruch NTP z centrum danych do Internetu.

    3. Opcjonalnie w **serwera NTP dodatkowej** wprowadź serwera pomocniczego dla Twojego urządzenia.

    4. Aby zweryfikować i zastosować ustawienia czasu skonfigurowane, wybierz pozycję **Zastosuj**.

6. W okienku po lewej stronie wybierz **ustawienia chmury**, a następnie uaktywnić swoje urządzenia w usłudze bramy pola danych, w witrynie Azure portal.
    
    1. W **klucza aktywacji** wprowadź **klucza aktywacji** uzyskany [Pobierz klucz aktywacji](data-box-gateway-deploy-prep.md#get-the-activation-key) bramy pola danych.

    2. Wybierz **aktywować**.
       
         ![Strona "Ustawienia chmury" usługi lokalnego internetowego interfejsu użytkownika](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. Urządzenie jest aktywowana i aktualizacji krytycznych, jeśli to możliwe, są automatycznie stosowane. Zostanie wyświetlone powiadomienie w tym celu. Monitoruj postęp aktualizacji za pośrednictwem witryny Azure portal.

        ![Strona "Ustawienia chmury" usługi lokalnego internetowego interfejsu użytkownika](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **Okno dialogowe ma także klucz odzyskiwania, które należy skopiować i zapisać w bezpiecznym miejscu. Ten klucz jest używany, można odzyskać dane, w przypadku, gdy urządzenie nie może uruchomić się.**


    4. Konieczne może potrwać kilka minut w celu pomyślnego ukończenia aktualizacji. Po aktualizacji jest ukończone, zaloguj się do urządzenia. **Ustawienia chmury** aktualizacji, aby wskazać, że urządzenia została pomyślnie aktywowana stron.

        ![Strona "Ustawienia chmury" lokalnego internetowego interfejsu użytkownika z aktualizacji](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

Konfigurowanie urządzenia zostało zakończone. Teraz możesz dodać akcji na urządzeniu.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Nawiązać połączenie z urządzeniem wirtualnym
> * Konfigurowanie i Aktywuj urządzenia wirtualnego

Aby dowiedzieć się, jak transfer danych za pomocą bramy pola danych, zobacz:

> [!div class="nextstepaction"]
> [Transferowanie danych za pomocą bramy pola danych](./data-box-gateway-deploy-add-shares.md).
