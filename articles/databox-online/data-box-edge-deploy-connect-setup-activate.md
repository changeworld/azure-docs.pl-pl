---
title: Nawiązywanie połączenia, konfigurowanie i aktywacji urządzenia usługi Azure Data Box Edge w witrynie Azure portal | Dokumentacja firmy Microsoft
description: Trzeci samouczek, aby wdrożyć krawędź pola danych powoduje, że możesz się połączyć, konfigurowanie i Aktywuj urządzenie fizyczne.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: b97334ae60715f021cce387f9d73b5ea69eea7fc
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998111"
---
# <a name="tutorial-connect-set-up-and-activate-azure-data-box-edge"></a>Samouczek: Łączenie, konfigurowanie i aktywowania usługi Azure Data Box Edge 

W tym samouczku opisano, jak połączyć, konfigurowanie i aktywacji urządzenia usługi Edge pole danych Azure przy użyciu lokalnego Interfejsu w przeglądarce.

Proces instalacji i aktywacja może potrwać około 20 minut, aby zakończyć.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Nawiązać połączenie z urządzeniem fizycznym
> * Konfigurowanie i Aktywuj urządzenia fizycznego

## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfiguruj urządzenie brzegowe pole danych, upewnij się, że:

* Po zainstalowaniu urządzenia fizycznego zgodnie z opisem w [zainstalować krawędź pola danych](data-box-edge-deploy-install.md).
* Masz klucz aktywacji usługi krawędź pola danych, utworzony w celu zarządzania urządzeniem krawędź pola danych. Aby uzyskać więcej informacji, przejdź do [przygotowanie do wdrożenia usługi Azure Data Box Edge](data-box-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Łączenie do lokalnego internetowego interfejsu użytkownika konfiguracji 

1. Skonfiguruj kartę Ethernet na komputerze, aby połączyć się z urządzeniem krawędź pola danych ze statycznym adresem IP 192.168.100.5 i podsieć 255.255.255.0.

2. Podłącz komputer do PORT 1 na urządzeniu. Na poniższej ilustracji umożliwia zidentyfikowanie PORT 1 na urządzeniu.

    ![Płyta montażowa okablowanego urządzenia](./media/data-box-edge-deploy-install/backplane-cabled.png)


3. Otwórz okno przeglądarki i dostęp do lokalnego internetowego interfejsu użytkownika urządzenia w https://192.168.100.10.  
    Ta akcja może potrwać kilka minut po włączeniu urządzenia. 

    Zostanie wyświetlony błąd lub ostrzeżenie informujące, że występuje problem z certyfikatem zabezpieczeń witryny sieci Web. 
   
    ![Komunikat błędu certyfikatu zabezpieczeń witryny sieci Web](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

4. Wybierz **Kontynuuj, aby ta strona sieci Web**.  
    Te kroki mogą różnić się w zależności od przeglądarki, z którego korzystasz.

5. Zaloguj się do internetowego interfejsu użytkownika urządzenia. Domyślne hasło jest *Password1*. 
   
    ![Dane strony logowania dla urządzenia krawędź pola](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

6. W wierszu polecenia Zmień hasło administratora urządzenia.  
    Nowe hasło musi zawierać od 8 do 16 znaków. Musi zawierać trzy z następujących znaków: wielkie litery, małe litery, cyfry i znaki specjalne.

Teraz możesz się na pulpicie nawigacyjnym urządzenia.

## <a name="set-up-and-activate-the-physical-device"></a>Konfigurowanie i Aktywuj urządzenia fizycznego
 
Pulpit nawigacyjny wyświetla różne ustawienia, które są wymagane, aby skonfigurować i zarejestrować urządzenie fizyczne w usłudze krawędź pola danych. **Nazwy urządzenia**, **ustawienia sieciowe**, **ustawień serwera proxy sieci Web**, i **ustawienia czasu** są opcjonalne. Tylko ustawienia wymagane są **ustawienia chmury**.
   
![Lokalnego internetowego interfejsu użytkownika "Pulpit nawigacyjny" strony](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. W okienku po lewej stronie wybierz **nazwy urządzenia**, a następnie wprowadź przyjazną nazwę dla danego urządzenia.  
    Przyjazna nazwa musi zawierać od 1 do 15 znaków i zawierać litery, cyfry i łączniki.

    ![Lokalnego internetowego interfejsu użytkownika "Nazwa urządzenia" strony](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

2. (Opcjonalnie) W okienku po lewej stronie wybierz **ustawienia sieciowe** a następnie skonfiguruj ustawienia.  
    Na urządzenie fizyczne istnieje sześć interfejsów sieciowych. PORT 1 i 2 portu są interfejsów sieciowych 1 GB/s. PORT 3, 4 portu, PORT 5 i 6 portu są wszystkie interfejsy sieciowe 25 GB, które może również służyć jako interfejsów sieciowych 10 GB/s. PORT 1 jest automatycznie konfigurowany jako tylko do zarządzania port i PORT 2 do 6 portu są wszystkie porty danych. **Ustawienia sieciowe** strona jest pokazany poniżej.
    
    ![Strona "Ustawienia sieci" systemu lokalnego internetowego interfejsu użytkownika](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Podczas konfigurowania ustawień sieciowych, należy pamiętać:

   - Jeżeli w Twoim środowisku włączony jest protokół DHCP, interfejsy sieciowe są automatycznie konfigurowane. Adres IP, podsieci, bramy i DNS są automatycznie przypisywane.
   - Jeśli nie jest włączony protokół DHCP, możesz przypisać statyczne adresy IP, w razie potrzeby.
   - Możesz skonfigurować interfejsu sieciowego w przypadku protokołu IPv4.

     >[!NOTE] 
     > Firma Microsoft zaleca, przełącza lokalny adres IP interfejsu sieciowego z statycznego do protokołu DHCP, o ile nie masz innego adresu IP, aby połączyć się z urządzeniem. Jeśli przy użyciu jednego interfejsu sieciowego i przełącz się do protokołu DHCP, będzie można ustalić adres DHCP. Jeśli chcesz zmienić adres DHCP, poczekaj, aż po urządzenia został zarejestrowany w usłudze, a następnie Zmień. Można wyświetlić adresy IP wszystkich kart w **właściwości urządzenia** w witrynie Azure portal dla usługi.

3. (Opcjonalnie) W okienku po lewej stronie wybierz **ustawień serwera proxy sieci Web**, a następnie skonfiguruj serwer proxy sieci web. Mimo, że konfiguracja serwera proxy sieci web jest opcjonalny, jeśli używasz serwera proxy sieci web, możesz go skonfigurować tylko na tej stronie.
   
   ![Strona "Ustawienia serwera proxy sieci Web" interfejsu użytkownika lokalnego sieci web](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Na **ustawień serwera proxy sieci Web** wykonaj następujące czynności:
   
   a. W **adres URL serwera proxy sieci Web** wprowadź adres URL w następującym formacie: `http://host-IP address or FQDN:Port number`. Adresy URL HTTPS nie są obsługiwane.

   b. W obszarze **uwierzytelniania**, wybierz opcję **Brak** lub **NTLM**.

   c. Jeśli używane jest uwierzytelnianie, wprowadź nazwę użytkownika i hasło.

   d. Aby zweryfikować i zastosowania ustawień serwera proxy sieci web skonfigurowany, wybierz pozycję **Zastosuj ustawienia**.

4. (Opcjonalnie) W okienku po lewej stronie wybierz **ustawienia czasu**, a następnie skonfigurować strefę czasową i podstawowych i pomocniczych serwerów NTP dla urządzenia.  
    Serwery NTP są wymagane, ponieważ urządzenie musi synchronizować czas, aby zapewnić możliwość uwierzytelnienia przy użyciu dostawców usług w chmurze.
       
    Na **ustawienia czasu** wykonaj następujące czynności:
    
    1. W **strefa czasowa** listę rozwijaną, wybierz strefę czasową, który odnosi się do lokalizacji geograficznej, w której jest wdrażane urządzenie na liście.
        Domyślna strefa czasowa dla Twojego urządzenia jest czasu PST. Wszystkie zaplanowane operacje urządzenia będą wykonywane w ramach tej strefy czasowej.

    2. W **podstawowy serwer NTP** wprowadź serwera podstawowego dla urządzenia lub zaakceptuj wartość domyślną z time.windows.com.  
        Upewnij się, że sieć zezwala na ruch NTP z centrum danych do Internetu.

    3. Opcjonalnie w **serwera NTP dodatkowej** wprowadź serwera pomocniczego dla Twojego urządzenia.

    4. Aby zweryfikować i zastosować ustawienia czasu skonfigurowane, wybierz pozycję **Zastosuj ustawienia**.

        ![Strona "Ustawienia czasu" usługi lokalnego internetowego interfejsu użytkownika](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)

5. (Opcjonalnie) W okienku po lewej stronie wybierz **ustawienia magazynu** konfiguracji odporności magazynu na urządzeniu. Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Domyślnie magazyn na urządzeniu nie jest odporne na błędy i następuje utrata danych, jeśli nastąpi awaria dysku danych na urządzeniu. Po włączeniu opcji odporne na błędy, konfiguracja magazynu na urządzeniu zostanie zmieniona, a urządzenie może wytrzymać awarii dysk z danymi bez utraty danych. Konfigurowanie magazynu jako odporne na błędy zmniejszy pojemności do wykorzystania w urządzeniu.

    > [!IMPORTANT] 
    > Odporność można skonfigurować tylko przed aktywowaniem urządzenia. 

    ![Strona "Ustawienia magazynu" usługi lokalnego internetowego interfejsu użytkownika](./media/data-box-edge-deploy-connect-setup-activate/storage-settings.png)

6. W okienku po lewej stronie wybierz **ustawienia chmury**, a następnie uaktywnić swoje urządzenia w usłudze krawędź pola danych, w witrynie Azure portal.
    
    1. W **klucza aktywacji** wprowadź klucz aktywacji, która jest wyświetlana w [Pobierz klucz aktywacji](data-box-edge-deploy-prep.md#get-the-activation-key) do krawędzi ramki danych.
    2. Wybierz przycisk **Zastosuj**.
       
        ![Strona "Ustawienia chmury" usługi lokalnego internetowego interfejsu użytkownika](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)

    3. Najpierw aktywowaniu urządzenia. Urządzenie jest następnie skanowane pod kątem wszystkie aktualizacje krytyczne i jeśli to możliwe, są automatycznie stosowane aktualizacje. Zostanie wyświetlone powiadomienie w tym celu.

        Okno dialogowe ma także klucz odzyskiwania, należy skopiować i zapisać go w bezpiecznym miejscu. Ten klucz jest używany, można odzyskać dane, w przypadku, gdy urządzenie nie może uruchomić się.

        ![Strona "Ustawienia chmury" lokalnego internetowego interfejsu użytkownika z aktualizacji](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)

    4. Konieczne może potrwać kilka minut, po pomyślnym zakończeniu aktualizacji. Strona zostanie zaktualizowana, aby wskazać, że urządzenia została pomyślnie aktywowana.

        ![Strona "Ustawienia chmury" lokalnego internetowego interfejsu użytkownika z aktualizacji](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-8.png)

Konfigurowanie urządzenia zostało zakończone. Teraz możesz dodać akcji na urządzeniu.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Nawiązać połączenie z urządzeniem fizycznym
> * Konfigurowanie i Aktywuj urządzenia fizycznego

Aby dowiedzieć się, jak na przesyłanie danych z urządzenia usługi Edge pole danych, zobacz:

> [!div class="nextstepaction"]
> [Transferowanie danych za pomocą krawędź pola danych](./data-box-edge-deploy-add-shares.md).
