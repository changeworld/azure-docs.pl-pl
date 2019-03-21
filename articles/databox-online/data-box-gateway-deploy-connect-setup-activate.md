---
title: Nawiązywanie połączenia, konfigurowanie i aktywować bramy pola danych platformy Azure w witrynie Azure portal | Dokumentacja firmy Microsoft
description: Trzeci samouczek do wdrożenia bramy pola danych powoduje, że możesz się połączyć, konfigurowanie i Aktywuj urządzenia wirtualnego.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 26b560434e6305689781b8c39c7cf814af9bf8aa
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58112300"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway-preview"></a>Samouczek: Łączenie, konfigurowanie, Aktywuj bramy pola danych platformy Azure (wersja zapoznawcza) 

## <a name="introduction"></a>Wprowadzenie

W tym samouczku opisano sposób podłączania do ustawiania i aktywować urządzenie bramy pola danych przy użyciu lokalnego Interfejsu w przeglądarce. 

Proces instalacji i aktywacji może potrwać około 10 minut, aby zakończyć. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Nawiązać połączenie z urządzeniem wirtualnym
> * Konfigurowanie i Aktywuj urządzenia wirtualnego

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


> [!IMPORTANT]
> - Usługa Data Box Gateway jest dostępna w wersji zapoznawczej. Przed zamówieniem i wdrożeniem tego rozwiązania zapoznaj się z [warunkami świadczenia usług Azure w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 


## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfigurować bramę pole danych, upewnij się, że:

* Masz przeprowadzono aprowizację urządzenia wirtualnego, a uzyskane połączonych adres URL, aby go zgodnie z opisem w [Aprowizowanie bramy pola danych w funkcji Hyper-V](data-box-gateway-deploy-provision-hyperv.md) lub [Aprowizowanie bramy pola danych w środowisku VMware](data-box-gateway-deploy-provision-vmware.md).
* Masz klucz aktywacji usługi bramy pola danych, utworzony w celu zarządzania urządzeniami bramy pola danych. Aby uzyskać więcej informacji, przejdź do [przygotowanie do wdrożenia usługi Azure Gateway pole danych](data-box-gateway-deploy-prep.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Łączenie do lokalnego internetowego interfejsu użytkownika konfiguracji 

1. Otwórz okno przeglądarki i nawiązać połączenie z lokalnego Interfejsu w przeglądarce. Wpisz:
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   Użyj adresu URL połączenia zanotowanego w poprzednim samouczku. Zostanie wyświetlony komunikat o błędzie informujący, że występuje problem z certyfikatem zabezpieczeń witryny sieci Web. Kliknij przycisk **Kontynuuj, aby ta strona sieci Web**. (Te kroki mogą być różne opartych na przeglądarce używana.)
   
    ![Wystąpił błąd podczas połączenia](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

2. Zaloguj się do internetowego interfejsu użytkownika urządzenia wirtualnego. Domyślne hasło jest *Password1*. 
   
    ![Zaloguj się do lokalnego internetowego interfejsu użytkownika](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

3. Monit o zmianę hasła administratora urządzenia. Wpisz nowe hasło, który zawiera od 8 do 16 znaków. Hasło musi zawierać 3 z następujących elementów: wielkie litery, małe litery, cyfry i znaki specjalne.

    ![Zmienianie hasła administratora urządzenia](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Jesteś teraz w **pulpit nawigacyjny** urządzenia.

## <a name="set-up-and-activate-the-virtual-device"></a>Konfigurowanie i Aktywuj urządzenia wirtualnego
 
1. Na pulpicie nawigacyjnym możesz przejść do różne ustawienia wymagane do skonfigurowania, a następnie zarejestrować urządzenie wirtualne przy użyciu usługi bramy pola danych. **Ustawienia sieciowe**, **ustawień serwera proxy sieci Web**, i **ustawienia czasu** są opcjonalne. Tylko ustawienia wymagane są **nazwy urządzenia** i **ustawienia chmury**.
   
    ![Lokalnego internetowego interfejsu użytkownika "Pulpit nawigacyjny" strony](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

2. W **nazwy urządzenia** strony, skonfigurować przyjazną nazwę dla danego urządzenia. Przyjazna nazwa może zawierać 1 do 15 znaków i może zawierać litery, cyfry i łączniki.

    ![Lokalnego internetowego interfejsu użytkownika "Nazwa urządzenia" strony](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

3. (Opcjonalnie) skonfiguruj swoje **ustawienia sieciowe**. Zostanie wyświetlony interfejs sieciowy co najmniej 1 i inne w zależności od tego, ile skonfigurowanych na podstawowej maszynie wirtualnej. **Ustawienia sieciowe** strony dla urządzenia wirtualnego z interfejsami sieciowymi włączone jest pokazany poniżej.
    
    ![Strona "Ustawienia sieci" systemu lokalnego internetowego interfejsu użytkownika](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Podczas konfigurowania ustawień sieciowych należy pamiętać o następujących czynności:

   - Jeżeli w Twoim środowisku włączony jest protokół DHCP, interfejsy sieciowe są automatycznie konfigurowane. Dlatego adres IP, podsieci, bramy i DNS są automatycznie przypisywane.
   - Jeśli DHCP nie jest włączone, można przypisać statyczne adresy IP, jeśli to konieczne.
   - Możesz skonfigurować interfejsu sieciowego w przypadku protokołu IPv4.

     >[!NOTE] 
     > Firma Microsoft zaleca, przełącza lokalny adres IP interfejsu sieciowego z statycznego protokołu DHCP, o ile nie masz innego adresu IP, aby połączyć się z urządzeniem. Jeśli przy użyciu jednego interfejsu sieciowego i przełącz się do protokołu DHCP, będzie można ustalić adres DHCP. Jeśli chcesz zmienić adres DHCP, poczekaj, aż po urządzenia został zarejestrowany w usłudze, a następnie Zmień. Można wyświetlić adresy IP wszystkich kart w **właściwości urządzenia** w witrynie Azure portal dla usługi.

4. (Opcjonalnie) skonfiguruj serwer proxy sieci web. Mimo że konfiguracja serwera proxy sieci web jest opcjonalny, należy pamiętać, że jeśli używasz serwera proxy sieci web, można skonfigurować tylko go tutaj.
   
   ![Strona "Ustawienia serwera proxy sieci Web" interfejsu użytkownika lokalnego sieci web](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   W **serwera proxy sieci Web** strony:
   
   1. Podaj **adres URL serwera proxy sieci Web** w następującym formacie: *http://&lt;adres IP hosta lub nazwa FQDN&gt;: numer portu*. Należy pamiętać, że adresy URL HTTPS nie są obsługiwane.
   2. Określ **uwierzytelniania** jako **podstawowe** lub **Brak**.
   3. Jeśli przy użyciu uwierzytelniania, należy również zapewnienie **Username** i **hasło**.
   4. Kliknij przycisk **Zastosuj**. Spowoduje to zweryfikować i zastosowania ustawień serwera proxy sieci web skonfigurowany.

5. (Opcjonalnie) skonfiguruj ustawienia czasu dla danego urządzenia, takie jak strefę czasową i podstawowych i pomocniczych serwerów NTP. Serwery NTP są wymagane, ponieważ urządzenie musi synchronizować czas, aby zapewnić możliwość uwierzytelnienia przy użyciu dostawców usług w chmurze.
    
    ![Strona "Ustawienia czasu" usługi lokalnego internetowego interfejsu użytkownika](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    W **ustawienia czasu** strony:
    
    1. Z listy rozwijanej wybierz **strefa czasowa** na podstawie lokalizacji geograficznej, w której jest wdrażane urządzenie. Domyślna strefa czasowa dla Twojego urządzenia jest czasu PST. Wszystkie zaplanowane operacje urządzenia będą wykonywane w ramach tej strefy czasowej.
    2. Określ **podstawowy serwer NTP** dla urządzenia lub zaakceptuj wartość domyślną z time.windows.com. Upewnij się, że sieć zezwala na ruch NTP z centrum danych do Internetu.
    3. Opcjonalnie można określić **serwera NTP dodatkowej** dla Twojego urządzenia.
    4. Kliknij przycisk **Zastosuj**. Spowoduje to zweryfikować i zastosować ustawienia czasu skonfigurowane.

6. W **ustawienia chmury** strony, aktywacji urządzenia przy użyciu usługi bramy pola danych w witrynie Azure portal.
    
    1. Wprowadź **klucza aktywacji** uzyskany [Pobierz klucz aktywacji](data-box-gateway-deploy-prep.md#get-the-activation-key) bramy pola danych.

    2. Kliknij pozycję **Aktywuj**. 
       
         ![Strona "Ustawienia chmury" usługi lokalnego internetowego interfejsu użytkownika](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. Najpierw zostanie aktywowane urządzenia. Urządzenie jest następnie skanowane pod kątem wszystkie aktualizacje krytyczne i jeśli to możliwe, są automatycznie stosowane aktualizacje. Zostanie wyświetlone powiadomienie w tym celu. 

        Okno dialogowe ma także klucz odzyskiwania, należy skopiować i zapisać go w bezpiecznym miejscu. Ten klucz jest używany, można odzyskać dane, w przypadku, gdy urządzenie nie może uruchomić się.

        ![Strona "Ustawienia chmury" usługi lokalnego internetowego interfejsu użytkownika](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)    

    4. Może być konieczne Poczekaj kilka minut, po pomyślnym zakończeniu aktualizacji. Strona zostanie zaktualizowana, aby wskazać, że urządzenia została pomyślnie aktywowana.

        ![Strona "Ustawienia chmury" lokalnego internetowego interfejsu użytkownika z aktualizacji](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Data Box Gateway, takie jak:

> [!div class="checklist"]
> * Nawiązać połączenie z urządzeniem wirtualnym
> * Konfigurowanie i Aktywuj urządzenia wirtualnego


Przejdź do następnego samouczka, aby dowiedzieć się, jak transfer danych za pomocą bramy pola danych.

> [!div class="nextstepaction"]
> [Transferowanie danych za pomocą bramy pola danych](./data-box-gateway-deploy-add-shares.md).
