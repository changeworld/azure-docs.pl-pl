---
title: Nawiązywanie połączenia, konfigurowanie i aktywować bramy pola danych platformy Azure w witrynie Azure portal | Dokumentacja firmy Microsoft
description: Trzeci samouczek do wdrożenia bramy pola danych powoduje, że możesz się połączyć, konfigurowanie i Aktywuj urządzenia wirtualnego.
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
ms.date: 09/24/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 27c43df6cf53536b835d2d73a5be30e0e8e97078
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037096"
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
> - Brama pole danych jest w wersji zapoznawczej. Przed zamówieniem i wdrożeniem tego rozwiązania zapoznaj się z [warunkami świadczenia usług Azure w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 


## <a name="prerequisites"></a>Wymagania wstępne

Przed skonfigurowaniem i skonfigurować bramę pole danych, upewnij się, że:

* Masz przeprowadzono aprowizację urządzenia wirtualnego, a uzyskane połączonych adres URL, aby go zgodnie z opisem w [Aprowizowanie bramy pola danych w funkcji Hyper-V](data-box-gateway-deploy-provision-hyperv.md) lub [Aprowizowanie bramy pola danych w środowisku VMware](data-box-gateway-deploy-provision-vmware.md).
* Masz klucz aktywacji usługi bramy pola danych, utworzony w celu zarządzania urządzeniami bramy pola danych. Aby uzyskać więcej informacji, przejdź do [przygotowanie do wdrożenia usługi Azure Gateway pole danych](data-box-gateway-deploy-prep.md).

<!--* If this is the second or subsequent virtual device that you are registering with an existing StorSimple Device Manager service, you should have the service data encryption key. This key was generated when the first device was successfully registered with this service. If you have lost this key, see [Get the service data encryption key](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) for your Data Box Gateway.-->

## <a name="connect-to-the-local-web-ui-setup"></a>Łączenie do lokalnego internetowego interfejsu użytkownika konfiguracji 

1. Otwórz okno przeglądarki i nawiązać połączenie z lokalnego Interfejsu w przeglądarce. Wpisz:
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   Użyj adresu URL połączenia zanotowanego w poprzednim samouczku. Zostanie wyświetlony komunikat o błędzie informujący, że występuje problem z certyfikatem zabezpieczeń witryny sieci Web. Kliknij przycisk **Kontynuuj, aby ta strona sieci Web**. (Te kroki mogą być różne opartych na przeglądarce używana.)
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

2. Zaloguj się do internetowego interfejsu użytkownika urządzenia wirtualnego. Domyślne hasło jest *Password1*. 
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

3. Monit o zmianę hasła administratora urządzenia. Wpisz nowe hasło, który zawiera od 8 do 16 znaków. Hasło musi zawierać 3 z następujących elementów: wielkie litery, małe litery, cyfry i znaki specjalne.

    ![](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Jesteś teraz w **pulpit nawigacyjny** urządzenia.

## <a name="set-up-and-activate-the-virtual-device"></a>Konfigurowanie i Aktywuj urządzenia wirtualnego
 
1. Na pulpicie nawigacyjnym możesz przejść do różne ustawienia wymagane do skonfigurowania, a następnie zarejestrować urządzenie wirtualne przy użyciu usługi bramy pola danych. **Ustawienia sieciowe**, **ustawień serwera proxy sieci Web**, i **ustawienia czasu** są opcjonalne. Tylko ustawienia wymagane są **nazwy urządzenia** i **ustawienia chmury**.
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

2. W **nazwy urządzenia** strony, skonfigurować przyjazną nazwę dla danego urządzenia. Przyjazna nazwa może zawierać 1 do 15 znaków i może zawierać litery, cyfry i łączniki.

    ![](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

3. (Opcjonalnie) skonfiguruj swoje **ustawienia sieciowe**. Zostanie wyświetlony interfejs sieciowy co najmniej 1 i inne w zależności od tego, ile skonfigurowanych na podstawowej maszynie wirtualnej. **Ustawienia sieciowe** strony dla urządzenia wirtualnego z interfejsami sieciowymi włączone jest pokazany poniżej.
    
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Podczas konfigurowania ustawień sieciowych należy pamiętać o następujących czynności:

    - Jeżeli DHCP jest włączona w danym środowisku, interfejsy sieciowe są konfigurowane automatycznie. Dlatego adres IP, podsieci, bramy i DNS są automatycznie przypisywane.
    - Jeśli DHCP nie jest włączone, można przypisać statyczne adresy IP, jeśli to konieczne.
    - Możesz skonfigurować interfejsu sieciowego w przypadku protokołu IPv4.
   
4. (Opcjonalnie) skonfiguruj serwer proxy sieci web. Mimo że konfiguracja serwera proxy sieci web jest opcjonalny, należy pamiętać, że jeśli używasz serwera proxy sieci web, można skonfigurować tylko go tutaj.
   
   ![](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   W **serwera proxy sieci Web** strony:
   
   1. Podaj **adres URL serwera proxy sieci Web** w następującym formacie: *http://&lt;adres IP hosta lub nazwy FDQN&gt;: numer portu*. Należy pamiętać, że adresy URL HTTPS nie są obsługiwane.
   2. Określ **uwierzytelniania** jako **podstawowe** lub **Brak**.
   3. Jeśli przy użyciu uwierzytelniania, należy również zapewnienie **Username** i **hasło**.
   4. Kliknij przycisk **Zastosuj**. Spowoduje to zweryfikować i zastosowania ustawień serwera proxy sieci web skonfigurowany.

5. (Opcjonalnie) skonfiguruj ustawienia czasu dla danego urządzenia, takie jak strefę czasową i podstawowych i pomocniczych serwerów NTP. Serwery NTP są wymagane, ponieważ urządzenie musi synchronizować czas, aby zapewnić możliwość uwierzytelnienia przy użyciu dostawców usług w chmurze.
    
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    W **ustawienia czasu** strony:
    
    1. Z listy rozwijanej wybierz **strefa czasowa** na podstawie lokalizacji geograficznej, w której jest wdrażane urządzenie. Domyślna strefa czasowa dla Twojego urządzenia jest czasu PST. Wszystkie zaplanowane operacje urządzenia będą wykonywane w ramach tej strefy czasowej.
    2. Określ **podstawowy serwer NTP** dla urządzenia lub zaakceptuj wartość domyślną z time.windows.com. Upewnij się, że sieć zezwala na ruch NTP z centrum danych do Internetu.
    3. Opcjonalnie można określić **serwera NTP dodatkowej** dla Twojego urządzenia.
    4. Kliknij przycisk **Zastosuj**. Spowoduje to zweryfikować i zastosować ustawienia czasu skonfigurowane.

6. W **ustawienia chmury** strony, aktywacji urządzenia przy użyciu usługi bramy pola danych w witrynie Azure portal.
    
    1. Wprowadź **klucza aktywacji** uzyskany [Pobierz klucz aktywacji](data-box-gateway-deploy-prep.md#get-the-activation-key) bramy pola danych.

    2. Kliknij przycisk **aktywować**. 
       
         ![](./media/data-box-gateway-deploy-connect-setup-activate/image10.png)
    
    3. Może być konieczne Poczekaj chwilę, zanim urządzenia została pomyślnie aktywowana. Po wykonaniu aktywacji strona zostanie zaktualizowana do wskazania, że urządzenia została pomyślnie aktywowana.


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku omówiono tematy bramy pola danych takich jak:

> [!div class="checklist"]
> * Nawiązać połączenie z urządzeniem wirtualnym
> * Konfigurowanie i Aktywuj urządzenia wirtualnego


Przejdź do następnego samouczka, aby dowiedzieć się, jak transfer danych za pomocą bramy pola danych.

> [!div class="nextstepaction"]
> [Transferowanie danych za pomocą bramy pola danych](./data-box-gateway-deploy-add-shares.md).
