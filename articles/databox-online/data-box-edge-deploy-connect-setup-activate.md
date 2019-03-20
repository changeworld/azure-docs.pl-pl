---
title: Nawiązywanie połączenia, konfigurowanie i aktywacji urządzenia usługi Azure Data Box Edge w witrynie Azure portal | Dokumentacja firmy Microsoft
description: Trzeci samouczek, aby wdrożyć krawędź pola danych powoduje, że możesz się połączyć, konfigurowanie i Aktywuj urządzenie fizyczne.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 01/09/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 4fd52510abd61c4d319a3fcbc8f722df5edbc476
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120605"
---
# <a name="tutorial-connect-set-up-and-activate-azure-data-box-edge-preview"></a>Samouczek: Łączenie, konfigurowanie i aktywowania usługi Azure Data Box Edge (wersja zapoznawcza) 

W tym samouczku opisano, jak połączyć, konfigurowanie i aktywacji urządzenia usługi Edge pole danych Azure przy użyciu lokalnego Interfejsu w przeglądarce. 

Proces instalacji i aktywacja może potrwać około 20 minut, aby zakończyć. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Nawiązać połączenie z urządzeniem fizycznym
> * Konfigurowanie i Aktywuj urządzenia fizycznego

> [!IMPORTANT]
> Usługa Data Box Edge jest dostępna w wersji zapoznawczej. Przed zamówieniem i wdrożeniem tego rozwiązania zapoznaj się z [warunkami świadczenia usług Azure w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 


## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfiguruj urządzenie brzegowe pole danych, upewnij się, że:

* Po zainstalowaniu urządzenia fizycznego zgodnie z opisem w [zainstalować krawędź pola danych](data-box-edge-deploy-install.md).
* Masz klucz aktywacji usługi krawędź pola danych, utworzony w celu zarządzania urządzeniem krawędź pola danych. Aby uzyskać więcej informacji, przejdź do [przygotowanie do wdrożenia usługi Azure Data Box Edge](data-box-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Łączenie do lokalnego internetowego interfejsu użytkownika konfiguracji 

1. Skonfiguruj kartę Ethernet na komputerze, aby połączyć się z urządzeniem Edge ze statycznym adresem IP 192.168.100.5 i podsieć 255.255.255.0.

1. Podłącz komputer do PORT 1 na urządzeniu. 

1. Otwórz okno przeglądarki i dostęp do lokalnego internetowego interfejsu użytkownika urządzenia w https://192.168.100.10.  
    Ta akcja może potrwać kilka minut po włączeniu urządzenia. 

    Zostanie wyświetlony błąd lub ostrzeżenie informujące, że występuje problem z certyfikatem zabezpieczeń witryny sieci Web. 
   
    ![Komunikat błędu certyfikatu zabezpieczeń witryny sieci Web](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

1. Wybierz **Kontynuuj, aby ta strona sieci Web**.  
    Te kroki mogą różnić się w zależności od przeglądarki, z którego korzystasz.

1. Zaloguj się do internetowego interfejsu użytkownika urządzenia. Domyślne hasło jest *Password1*. 
   
    ![Dane strony logowania dla urządzenia krawędź pola](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

1. W wierszu polecenia Zmień hasło administratora urządzenia.  
    Nowe hasło musi zawierać od 8 do 16 znaków. Musi zawierać trzy z następujących znaków: wielkie litery, małe litery, cyfry i znaki specjalne.

Teraz możesz się na pulpicie nawigacyjnym urządzenia.

## <a name="set-up-and-activate-the-physical-device"></a>Konfigurowanie i Aktywuj urządzenia fizycznego
 
Pulpit nawigacyjny wyświetla różne ustawienia, które są wymagane, aby skonfigurować i zarejestrować urządzenie fizyczne w usłudze krawędź pola danych. **Nazwy urządzenia**, **ustawienia sieciowe**, **ustawień serwera proxy sieci Web**, i **ustawienia czasu** są opcjonalne. Tylko ustawienia wymagane są **ustawienia chmury**.
   
![Pulpit nawigacyjny urządzeń krawędź pola danych](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. W okienku po lewej stronie wybierz **nazwy urządzenia**, a następnie wprowadź przyjazną nazwę dla danego urządzenia.  
    Przyjazna nazwa musi zawierać od 1 do 15 znaków i zawierać litery, cyfry i łączniki.

    ![Na stronie "Nazwa urządzenia"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

1. (Opcjonalnie) W okienku po lewej stronie wybierz **ustawienia sieciowe** a następnie skonfiguruj ustawienia.  
    Na urządzenie fizyczne są sześć interfejsów sieciowych. PORT 1 i 2 portu są interfejsów sieciowych 1 GB/s. PORT 3, 4 portu, PORT 5 i 6 portu są wszystkie interfejsy sieciowe 25-GB/s. PORT 1 jest automatycznie konfigurowany jako tylko do zarządzania port i PORT 2 do 6 portu są wszystkie porty danych. **Ustawienia sieciowe** stronie, jak pokazano poniżej.
    
    ![Strona "Ustawienia sieci"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Podczas konfigurowania ustawień sieciowych, należy pamiętać:

   - Jeżeli w Twoim środowisku włączony jest protokół DHCP, interfejsy sieciowe są automatycznie konfigurowane. Adres IP, podsieci, bramy i DNS są automatycznie przypisywane.
   - Jeśli nie jest włączony protokół DHCP, możesz przypisać statyczne adresy IP, w razie potrzeby.
   - Możesz skonfigurować interfejsu sieciowego w przypadku protokołu IPv4.

     >[!NOTE] 
     > Firma Microsoft zaleca, przełącza lokalny adres IP interfejsu sieciowego z statycznego do protokołu DHCP, o ile nie masz innego adresu IP, aby połączyć się z urządzeniem. Jeśli przy użyciu jednego interfejsu sieciowego i przełącz się do protokołu DHCP, będzie można ustalić adres DHCP. Jeśli chcesz zmienić adres DHCP, poczekaj, aż po urządzenia został zarejestrowany w usłudze, a następnie Zmień. Można wyświetlić adresy IP wszystkich kart w **właściwości urządzenia** w witrynie Azure portal dla usługi.

1. (Opcjonalnie) W okienku po lewej stronie wybierz **ustawień serwera proxy sieci Web**, a następnie skonfiguruj serwer proxy sieci web. Mimo, że konfiguracja serwera proxy sieci web jest opcjonalny, jeśli używasz serwera proxy sieci web, możesz go skonfigurować tylko na tej stronie.
   
   ![Strona "Ustawienia serwera proxy sieci Web"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Na **ustawień serwera proxy sieci Web** wykonaj następujące czynności:
   
   a. W **adres URL serwera proxy sieci Web** wprowadź adres URL w następującym formacie: `http://host-IP address or FQDN:Port number`. Adresy URL HTTPS nie są obsługiwane.

   b. W obszarze **uwierzytelniania**, wybierz opcję **Brak** lub **NTLM**.

   c. Jeśli używane jest uwierzytelnianie, wprowadź nazwę użytkownika i hasło.

   d. Aby zweryfikować i zastosowania ustawień serwera proxy sieci web skonfigurowany, wybierz pozycję **Zastosuj ustawienia**.

1. (Opcjonalnie) W okienku po lewej stronie wybierz **ustawienia czasu**, a następnie skonfigurować strefę czasową i podstawowych i pomocniczych serwerów NTP dla urządzenia.  
    Serwery NTP są wymagane, ponieważ urządzenie musi synchronizować czas, aby zapewnić możliwość uwierzytelnienia przy użyciu dostawców usług w chmurze.
    
    ![Strona "Ustawienia czasu"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)
    
    Na **ustawienia czasu** wykonaj następujące czynności:
    
    a. W **strefa czasowa** listę rozwijaną, wybierz strefę czasową, który odnosi się do lokalizacji geograficznej, w której jest wdrażane urządzenie na liście.  
        Domyślna strefa czasowa dla Twojego urządzenia jest czasu PST. Wszystkie zaplanowane operacje urządzenia będą wykonywane w ramach tej strefy czasowej.

    b. W **podstawowy serwer NTP** wprowadź serwera podstawowego dla urządzenia lub zaakceptuj wartość domyślną z time.windows.com.  
        Upewnij się, że sieć zezwala na ruch NTP z centrum danych do Internetu.

    c. Opcjonalnie w **serwera NTP dodatkowej** wprowadź serwera pomocniczego dla Twojego urządzenia.

    d. Aby zweryfikować i zastosować ustawienia czasu skonfigurowane, wybierz pozycję **Zastosuj**.

6. W okienku po lewej stronie wybierz **ustawienia chmury**, a następnie uaktywnić swoje urządzenia w usłudze krawędź pola danych, w witrynie Azure portal.
    
    a. W **klucza aktywacji** wprowadź klucz aktywacji, która jest wyświetlana w [Pobierz klucz aktywacji](data-box-edge-deploy-prep.md#get-the-activation-key) do krawędzi ramki danych.

    b. Wybierz przycisk **Zastosuj**. 
       
    ![Strona "Ustawienia chmury"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)
    
    Po pomyślnym aktywowaniu urządzenia pojawi się informacja o opcje trybu łączności. Te ustawienia są konfigurowane, jeśli potrzebne do pracy z urządzeniem w trybie częściowo odłączone lub odłączony. 

    ![Potwierdzenie aktywacji "W chmurze ustawienia"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)    

Konfigurowanie urządzenia zostało zakończone. Teraz możesz dodać akcji na urządzeniu.


## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Nawiązać połączenie z urządzeniem fizycznym
> * Konfigurowanie i Aktywuj urządzenia fizycznego


Aby dowiedzieć się, jak na przesyłanie danych z urządzenia usługi Edge pole danych, zobacz:

> [!div class="nextstepaction"]
> [Transferowanie danych za pomocą krawędź pola danych](./data-box-edge-deploy-add-shares.md).
