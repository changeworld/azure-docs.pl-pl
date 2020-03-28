---
title: Samouczek, aby połączyć się, skonfigurować, aktywować urządzenie usługi Azure Data Box Edge w witrynie Azure portal | Dokumenty firmy Microsoft
description: Samouczek wdrażania usługi Data Box Edge nakazuje łączenie, konfigurowanie i aktywowanie urządzenia fizycznego.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 144c59c8bc24e8e10584702ec6cd48f7aa8c15c1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239056"
---
# <a name="tutorial-connect-set-up-and-activate-azure-data-box-edge"></a>Samouczek: Łączenie, konfigurowanie i aktywowanie usługi Azure Data Box Edge 

W tym samouczku opisano, jak można połączyć się, skonfigurować i aktywować urządzenie usługi Azure Data Box Edge przy użyciu lokalnego interfejsu użytkownika sieci web.

Proces konfiguracji i aktywacji może potrwać około 20 minut.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Łączenie się z urządzeniem fizycznym
> * Konfigurowanie i aktywowanie urządzenia fizycznego

## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfigurowaniem urządzenia Data Box Edge należy upewnić się, że:

* Urządzenie fizyczne jest zainstalowane zgodnie z opisem w [oknie Zainstaluj krawędź pola danych](data-box-edge-deploy-install.md).
* Masz klucz aktywacyjny z usługi Data Box Edge, który został utworzony w celu zarządzania urządzeniem Data Box Edge. Aby uzyskać więcej informacji, przejdź do [witryny Przygotowanie do wdrożenia usługi Azure Data Box Edge](data-box-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Łączenie się z lokalną konfiguracją interfejsu użytkownika sieci Web 

1. Skonfiguruj kartę Ethernet komputera do łączenia się z urządzeniem Data Box Edge o statycznym adresie IP 192.168.100.5 i podsieci 255.255.0.

2. Podłącz komputer do portu 1 w urządzeniu. Poniższa ilustracja służy do identyfikowania portu 1 na urządzeniu.

    ![Płyta montażowa okablowanego urządzenia](./media/data-box-edge-deploy-install/backplane-cabled.png)


3. Otwórz okno przeglądarki i uzyskaj dostęp do lokalnego interfejsu użytkownika urządzenia w sieci Web pod adresem `https://192.168.100.10`.  
    Ta akcja może potrwać kilka minut po włączeniu urządzenia. 

    Zostanie wyświetlony błąd lub ostrzeżenie wskazujące, że wystąpił problem z certyfikatem zabezpieczeń witryny sieci Web. 
   
    ![Komunikat o błędzie certyfikatu zabezpieczeń witryny sieci Web](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

4. Wybierz **pozycję Kontynuuj do tej strony sieci Web**.  
    Te kroki mogą się różnić w zależności od używanej przeglądarki.

5. Zaloguj się do internetowego interfejsu użytkownika urządzenia. Domyślnym hasłem jest *Password1*. 
   
    ![Strona logowania urządzenia Data Box Edge](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

6. W wierszu polecenia zmień hasło administratora urządzenia.  
    Nowe hasło musi zawierać od 8 do 16 znaków. Musi zawierać trzy z następujących znaków: wielkie, małe, numeryczne i znaki specjalne.

Jesteś teraz na pulpicie nawigacyjnym urządzenia.

## <a name="set-up-and-activate-the-physical-device"></a>Konfigurowanie i aktywowanie urządzenia fizycznego
 
Pulpit nawigacyjny wyświetla różne ustawienia, które są wymagane do skonfigurowania i zarejestrowania urządzenia fizycznego w usłudze Data Box Edge. **Nazwa urządzenia,** **ustawienia sieciowe,** ustawienia serwera proxy **sieci Web**i **ustawienia czasu** są opcjonalne. Jedynymi wymaganymi ustawieniami są **ustawienia chmury**.
   
![Strona "Pulpit nawigacyjny" interfejsu użytkownika sieci web](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. W lewym okienku wybierz pozycję **Nazwa urządzenia**, a następnie wprowadź przyjazną nazwę urządzenia.  
    Przyjazna nazwa musi zawierać od 1 do 15 znaków i zawierać litery, cyfry i łączniki.

    ![Strona lokalny interfejs użytkownika sieci Web "Nazwa urządzenia"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

2. (Opcjonalnie) W lewym okienku wybierz pozycję **Ustawienia sieciowe,** a następnie skonfiguruj ustawienia.  
    Na urządzeniu fizycznym istnieje sześć interfejsów sieciowych. PORT 1 i PORT 2 są interfejsami sieciowymi 1 Gb/s. PORT 3, PORT 4, PORT 5 i PORT 6 to interfejsy sieciowe 25 Gb/s, które mogą również służyć jako interfejsy sieciowe 10 Gb/s. Port 1 jest automatycznie konfigurowany jako port tylko do zarządzania, a PORT 2 do PORT 6 to wszystkie porty danych. **Strona Ustawienia sieci** jest przedstawiona poniżej.
    
    ![Strona Lokalny interfejs użytkownika sieci web "Ustawienia sieci"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Podczas konfigurowania ustawień sieciowych należy pamiętać:

   - Jeżeli w Twoim środowisku włączony jest protokół DHCP, interfejsy sieciowe są automatycznie konfigurowane. Adres IP, podsieć, brama i dns są przypisywane automatycznie.
   - Jeśli usługa DHCP nie jest włączona, w razie potrzeby można przypisać statyczne serwery IP.
   - Interfejs sieciowy można skonfigurować jako IPv4.

     >[!NOTE] 
     > Zaleca się, aby nie przełączać lokalnego adresu IP interfejsu sieciowego ze statycznego na DCHP, chyba że masz inny adres IP do łączenia się z urządzeniem. W przypadku korzystania z jednego interfejsu sieciowego i przełączenia się na protokół DHCP nie można określić adresu DHCP. Jeśli chcesz zmienić adres DHCP, poczekaj, aż urządzenie zostanie zarejestrowane w usłudze, a następnie zmień. Następnie można wyświetlić ustawienia IP wszystkich kart we **właściwościach urządzenia** w witrynie Azure portal dla twojej usługi.

3. (Opcjonalnie) W lewym okienku wybierz pozycję **Ustawienia serwera proxy sieci Web,** a następnie skonfiguruj serwer proxy sieci Web. Chociaż konfiguracja serwera proxy sieci Web jest opcjonalna, jeśli używasz serwera proxy sieci Web, można go skonfigurować tylko na tej stronie.
   
   ![Strona lokalny interfejs użytkownika sieci Web "Ustawienia serwera proxy sieci Web"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Na stronie **Ustawienia serwera proxy sieci Web** wykonaj następujące czynności:
   
   a. W polu **Adres URL serwera proxy sieci** `http://host-IP address or FQDN:Port number`Web wprowadź adres URL w tym formacie: . Adresy URL HTTPS nie są obsługiwane.

   b. W obszarze **Uwierzytelnianie**wybierz **pozycję Brak** lub **NTLM**.

   d. Jeśli używasz uwierzytelniania, wprowadź nazwę użytkownika i hasło.

   d. Aby sprawdzić poprawność i zastosować skonfigurowane ustawienia serwera proxy sieci Web, wybierz pozycję **Zastosuj ustawienia**.
   
   > [!NOTE]
   > Pliki konfiguracji proxy-auto (PAC) nie są obsługiwane. Plik PAC definiuje, w jaki sposób przeglądarki internetowe i inni agenci użytkowników mogą automatycznie wybrać odpowiedni serwer proxy (metodę dostępu) do pobierania danego adresu URL.
   > Serwery proxy, które próbują przechwycić i odczytać cały ruch (a następnie ponownie podpisać wszystko z własnej certyfikacji) nie są zgodne, ponieważ certyfikat serwera proxy nie jest zaufany.
   > Zazwyczaj przezroczyste serwery proxy działają dobrze z usługą Azure Data Box Edge.

4. (Opcjonalnie) W lewym okienku wybierz **pozycję Ustawienia czasu**, a następnie skonfiguruj strefę czasową oraz podstawowe i pomocnicze serwery NTP dla urządzenia.  
    Serwery NTP są wymagane, ponieważ urządzenie musi synchronizować czas, aby można było uwierzytelnić się z dostawcami usług w chmurze.
       
    Na stronie **Ustawienia czasu** wykonaj następujące czynności:
    
    1. Na liście rozwijanej **Strefa czasowa** wybierz strefę czasową odpowiadającą lokalizacji geograficznej, w której urządzenie jest wdrażane.
        Domyślną strefą czasową urządzenia jest PST. Wszystkie zaplanowane operacje urządzenia będą wykonywane w ramach tej strefy czasowej.

    2. W polu **Podstawowy serwer NTP** wprowadź serwer podstawowy urządzenia lub zaakceptuj domyślną wartość time.windows.com.  
        Upewnij się, że sieć umożliwia przepływ ruchu NTP z centrum danych do Internetu.

    3. Opcjonalnie w polu **Serwer pomocniczy NTP** wprowadź serwer pomocniczy dla urządzenia.

    4. Aby sprawdzić poprawność i zastosować skonfigurowane ustawienia czasu, wybierz pozycję **Zastosuj ustawienia**.

        ![Strona lokalny interfejs użytkownika sieci Web "Ustawienia czasu"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)

5. (Opcjonalnie) W lewym okienku wybierz **pozycję Ustawienia magazynu,** aby skonfigurować odporność magazynu na urządzeniu. Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Domyślnie magazyn na urządzeniu nie jest odporny i występuje utrata danych, jeśli dysk danych ulegnie awarii na urządzeniu. Po włączeniu opcji Resilient magazyn na urządzeniu zostanie ponownie skonfigurowany, a urządzenie może wytrzymać awarię jednego dysku danych bez utraty danych. Skonfigurowanie magazynu jako odpornego zmniejszy użyteczną pojemność urządzenia.

    > [!IMPORTANT] 
    > Odporność można skonfigurować tylko przed aktywacją urządzenia. 

    ![Strona Lokalny interfejs użytkownika sieci Web "Ustawienia magazynu"](./media/data-box-edge-deploy-connect-setup-activate/storage-settings.png)

6. W lewym okienku wybierz **pozycję Ustawienia chmury**, a następnie aktywuj urządzenie za pomocą usługi Data Box Edge w witrynie Azure portal.
    
    1. W polu **Klucz aktywacji** wprowadź klucz aktywacyjny, który otrzymałeś w [pobierz klucz aktywacyjny](data-box-edge-deploy-prep.md#get-the-activation-key) dla data box edge.
    2. Wybierz przycisk **Zastosuj**.
       
        ![Strona lokalny interfejs użytkownika sieci Web "Ustawienia chmury"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)

    3. Najpierw urządzenie jest włączone. Urządzenie jest następnie skanowane w poszukiwaniu wszelkich aktualizacji krytycznych, a jeśli są dostępne, są automatycznie stosowane. Zobaczysz powiadomienie w tej sprawie.

        Okno dialogowe zawiera również klucz odzyskiwania, który należy skopiować i zapisać w bezpiecznym miejscu. Ten klucz służy do odzyskiwania danych w przypadku, gdy urządzenie nie może się uruchomić.

        ![Zaktualizowano stronę lokalnego interfejsu użytkownika sieci Web "Ustawienia chmury"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)

    4. Może być konieczne odczekanie kilku minut po pomyślnym zakończeniu aktualizacji. Strona zostanie zaktualizowana, aby wskazać, że urządzenie zostało pomyślnie aktywowane.

        ![Zaktualizowano stronę lokalnego interfejsu użytkownika sieci Web "Ustawienia chmury"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-8.png)

Konfiguracja urządzenia została zakończona. Teraz możesz dodać udziały na swoim urządzeniu.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Łączenie się z urządzeniem fizycznym
> * Konfigurowanie i aktywowanie urządzenia fizycznego

Aby dowiedzieć się, jak przesyłać dane za pomocą urządzenia Data Box Edge, zobacz:

> [!div class="nextstepaction"]
> [Przesyłanie danych za pomocą usługi Data Box Edge](./data-box-edge-deploy-add-shares.md).
