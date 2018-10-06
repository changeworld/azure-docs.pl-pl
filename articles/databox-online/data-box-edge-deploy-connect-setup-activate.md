---
title: Nawiązywanie połączenia, konfigurowanie i aktywowanie usługi Azure Data Box Edge w witrynie Azure portal | Dokumentacja firmy Microsoft
description: Trzeci samouczek, aby wdrożyć krawędź pola danych powoduje, że możesz się połączyć, konfigurowanie i Aktywuj urządzenie fizyczne.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/08/2018
ms.author: alkohli
ms.custom: ''
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 83bc1d81eaa930fc16c895f4e3b8b9bf1b1ad28c
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2018
ms.locfileid: "48832247"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-edge-preview"></a>Samouczek: Łączenie, konfigurowanie, aktywacji usługi Azure Data Box Edge (wersja zapoznawcza) 

W tym samouczku opisano sposób podłączania do ustawiania i aktywować urządzenia krawędź pola danych przy użyciu lokalnego Interfejsu w przeglądarce. 

Proces instalacji i aktywacja może potrwać około 20 minut, aby zakończyć. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Nawiązać połączenie z urządzenia fizycznego
> * Konfigurowanie i Aktywuj urządzenia fizycznego

> [!IMPORTANT]
> Krawędź pola danych jest w wersji zapoznawczej. Przed zamówieniem i wdrożeniem tego rozwiązania zapoznaj się z [warunkami świadczenia usług Azure w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 


## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfiguruj swoje krawędź pola danych, upewnij się, że:

* Zainstalowano urządzenie fizyczne zgodnie z opisem w [zainstalować krawędź pola danych](data-box-edge-deploy-install.md).
* Masz klucz aktywacji usługi krawędź pola danych, utworzony w celu zarządzania urządzeniem krawędź pola danych. Aby uzyskać więcej informacji, przejdź do [przygotowanie do wdrożenia usługi Azure Data Box Edge](data-box-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Łączenie do lokalnego internetowego interfejsu użytkownika konfiguracji 

1. Skonfiguruj kartę Ethernet na komputerze, którego używasz do nawiązania połączenia z urządzeniem Edge ze statycznym adresem IP 192.168.100.5 i podsieć 255.255.255.0.
2. Podłącz komputer do PORT 1 na urządzeniu. 
3. Otwórz okno przeglądarki i dostęp do lokalnego internetowego interfejsu użytkownika urządzenia w https://192.168.100.10. Ta akcja może potrwać kilka minut po włączeniu urządzenia. 
4. Zostanie wyświetlony błąd lub ostrzeżenie informujące, że występuje problem z certyfikatem zabezpieczeń witryny sieci Web. Kliknij przycisk **Kontynuuj, aby ta strona sieci Web**. (Te kroki mogą być różne opartych na przeglądarce używana.)
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

2. Zaloguj się do internetowego interfejsu użytkownika urządzenia. Domyślne hasło jest *Password1*. 
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

3. Zostanie wyświetlony monit o zmianę hasła administratora urządzenia. Wpisz nowe hasło, który zawiera od 8 do 16 znaków. Hasło musi zawierać 3 z następujących znaków: wielkie litery, małe litery, cyfry i znaki specjalne.

Jesteś teraz w **pulpit nawigacyjny** urządzenia.

## <a name="set-up-and-activate-the-physical-device"></a>Konfigurowanie i Aktywuj urządzenia fizycznego
 
1. Na pulpicie nawigacyjnym możesz przejść do różne ustawienia wymagane do skonfigurowania, a następnie zarejestrować urządzenie fizyczne w usłudze krawędź pola danych. **Nazwy urządzenia**, **ustawienia sieciowe**, **ustawień serwera proxy sieci Web**, i **ustawienia czasu** są opcjonalne. Tylko ustawienia wymagane są **ustawienia chmury**.
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

2. W **nazwy urządzenia** strony, skonfigurować przyjazną nazwę dla danego urządzenia. Przyjazna nazwa może zawierać 1 do 15 znaków i może zawierać litery, cyfry i łączniki.

    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

3. (Opcjonalnie) skonfiguruj swoje **ustawienia sieciowe**. Na urządzenie fizyczne zobaczysz sześć interfejsów sieciowych. PORT 1 i 2 portu są interfejsów sieciowych 1 GB/s. PORT 3, 4 portu, PORT 5 i 6 portu są wszystkie interfejsy sieciowe 25-GB/s. PORT 1 jest automatycznie konfigurowany jako tylko do zarządzania port PORT 2 do 6 portu są wszystkie porty danych. **Ustawienia sieciowe** stronie, jak pokazano poniżej.
    
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Podczas konfigurowania ustawień sieciowych, należy pamiętać:

    - Jeżeli w Twoim środowisku włączony jest protokół DHCP, interfejsy sieciowe są automatycznie konfigurowane. Adres IP, podsieci, bramy i DNS są automatycznie przypisywane.
    - Jeśli nie jest włączony protokół DHCP, możesz przypisać statyczne adresy IP, w razie potrzeby.
    - Możesz skonfigurować interfejsu sieciowego w przypadku protokołu IPv4.
   
4. (Opcjonalnie) skonfiguruj serwer proxy sieci web. Mimo że konfiguracja serwera proxy sieci web jest opcjonalny, jeśli używasz serwera proxy sieci web, można skonfigurować tylko go tutaj.
   
   ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   W **serwera proxy sieci Web** strony:
   
   1. Podaj **adres URL serwera proxy sieci Web** w następującym formacie: `http://host-IP address or FDQN:Port number`. Adresy URL HTTPS nie są obsługiwane.
   2. Określ **uwierzytelniania** jako **podstawowe** lub **Brak**.
   3. Jeśli przy użyciu uwierzytelniania, należy także zapewnienie **Username** i **hasło**.
   4. Kliknij przycisk **Zastosuj** do sprawdzania poprawności i zastosowania ustawień serwera proxy skonfigurowanego w sieci web.

5. (Opcjonalnie) skonfiguruj ustawienia czasu dla danego urządzenia, takie jak strefę czasową i podstawowych i pomocniczych serwerów NTP. Serwery NTP są wymagane, ponieważ urządzenie musi synchronizować czas, aby zapewnić możliwość uwierzytelnienia przy użyciu dostawców usług w chmurze.
    
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)
    
    W **ustawienia czasu** strony:
    
    1. Z listy rozwijanej wybierz **strefa czasowa** na podstawie lokalizacji geograficznej, w której jest wdrażane urządzenie. Domyślna strefa czasowa dla Twojego urządzenia jest czasu PST. Wszystkie zaplanowane operacje urządzenia będą wykonywane w ramach tej strefy czasowej.
    2. Określ **podstawowy serwer NTP** dla urządzenia lub zaakceptuj wartość domyślną z time.windows.com. Upewnij się, że sieć zezwala na ruch NTP z centrum danych do Internetu.
    3. Opcjonalnie można określić **serwera NTP dodatkowej** dla Twojego urządzenia.
    4. Kliknij przycisk **Zastosuj** do sprawdzania poprawności i zastosować ustawienia skonfigurowany czas.

6. W **ustawienia chmury** strony, Aktywuj swoje urządzenia w usłudze krawędź pola danych, w witrynie Azure portal.
    
    1. Wprowadź **klucza aktywacji** uzyskany [Pobierz klucz aktywacji](data-box-edge-deploy-prep.md#get-the-activation-key) do krawędzi ramki danych.

    2. Kliknij przycisk **Zastosuj**. 
       
         ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)
    
    3. Po pomyślnym aktywowaniu urządzenia pojawi się informacja o opcje trybu łączności. Te ustawienia są konfigurowane, jeśli potrzebne do pracy z urządzeniem w trybie częściowo odłączone lub odłączony. 

        ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)    

Konfigurowanie urządzenia zostało zakończone. Teraz możesz dodać akcji na urządzeniu.


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku omówiono tematy krawędź pola danych takich jak:

> [!div class="checklist"]
> * Nawiązać połączenie z urządzenia fizycznego
> * Konfigurowanie i Aktywuj urządzenia fizycznego


Przejdź do następnego samouczka, aby dowiedzieć się, jak na przesyłanie danych za pomocą usługi Edge pola danych.

> [!div class="nextstepaction"]
> [Transferowanie danych za pomocą krawędź pola danych](./data-box-edge-deploy-add-shares.md).