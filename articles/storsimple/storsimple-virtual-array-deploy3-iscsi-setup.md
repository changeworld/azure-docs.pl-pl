---
title: Konfiguracja serwera iSCSI tablicy wirtualne Microsoft Azure StorSimple | Dokumentacja firmy Microsoft
description: Opisuje sposób wykonywania początkowej konfiguracji, Zarejestruj serwer iSCSI StorSimple i przeprowadzić konfigurację urządzenia.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 4db116d1-978b-48e8-b572-a719a8425dbc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: 076df176d7cd40c009aea27004fe0f4415999c80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23877307"
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>Wdrożenie tablicy wirtualne StorSimple — zestawu się jako serwer iSCSI za pośrednictwem portalu Azure

![przepływ procesu instalacji iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Omówienie

W tym samouczku wdrażania stosuje się do tablicy wirtualne Microsoft Azure StorSimple. Ten przewodnik opisuje sposób wykonywania początkowej konfiguracji, Zarejestruj serwer iSCSI StorSimple, skonfigurować urządzenie, następnie utwórz, zainstalować, zainicjować i sformatować woluminy StorSimple tablica wirtualny skonfigurowany jako serwer iSCSI. 

Procedury opisane w tym miejscu zająć około 30 minut do 1 godziny, aby zakończyć. Informacje zawarte w tym artykule dotyczą tylko tablice wirtualne StorSimple.

## <a name="setup-prerequisites"></a>Wymagania wstępne instalacji

Przed skonfigurowaniem i konfigurowanie macierzy wirtualnego StorSimple, upewnij się, że:

* Udostępnione wirtualne tablicy i dołączone do niego, zgodnie z opisem w [wdrażanie StorSimple wirtualnego tablicy - udostępniania wirtualnego tablicy w funkcji Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) lub [wdrażanie StorSimple wirtualnego tablicy - udostępniania wirtualnego tablicy w środowisku programu VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Masz klucz rejestracji usługi z usługi Menedżera urządzeń StorSimple, utworzony w celu zarządzania z macierzami wirtualne StorSimple. Aby uzyskać więcej informacji, zobacz **krok 2: pobieranie klucza rejestracji usługi** w [wdrażanie StorSimple wirtualnego tablicy — przygotowanie portalu](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
* Jeśli jest to drugi lub kolejny wirtualnego tablicy rejestrujesz istniejącą usługę Menedżer urządzeń StorSimple, powinien mieć klucz szyfrowania danych usługi. Ten klucz został wygenerowany podczas pierwszego urządzenia został pomyślnie zarejestrowany z tą usługą. W przypadku utraty tego klucza, zobacz **Pobierz klucz szyfrowania danych usługi** w [zarządzać tablica wirtualnego StorSimple przy użyciu interfejsu użytkownika sieci Web](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Krok po kroku instalacji

Poniższe instrukcje umożliwiają instalowanie i konfigurowanie macierzy wirtualnego StorSimple:

* [Krok 1: Zakończenie ustawienia interfejsu użytkownika sieci web w lokalnej i rejestrowanie urządzenia](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* [Krok 2: Wykonanie konfiguracji urządzeń](#step-2-complete-the-required-device-setup)
* [Krok 3: Dodawanie woluminu](#step-3-add-a-volume)
* [Krok 4: Instalowanie, inicjowanie i formatowanie woluminu](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Krok 1: Zakończenie ustawienia interfejsu użytkownika sieci web w lokalnej i rejestrowanie urządzenia

#### <a name="to-complete-the-setup-and-register-the-device"></a>Aby zakończyć konfigurowanie i rejestrowanie urządzenia

1. Otwórz okno przeglądarki. Aby podłączyć się do typu interfejsu użytkownika sieci web:
   
    `https://<ip-address of network interface>`
   
    Użyj adresu URL połączenia zauważono w poprzednim kroku. Zostanie wyświetlony błąd informujący, że występuje problem z certyfikatem zabezpieczeń witryny sieci Web. Kliknij przycisk **Kontynuuj, aby ta strona sieci web**.
   
    ![Błąd certyfikatu zabezpieczeń](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Zaloguj się do sieci web UI urządzenia wirtualnego jako **StorSimpleAdmin**. Wprowadź hasło administratora urządzenia, który został zmodyfikowany w kroku 3: uruchomić urządzenie wirtualne w [wdrażanie StorSimple wirtualnego tablicy - Provision urządzenia wirtualnego funkcji Hyper-v](storsimple-virtual-array-deploy2-provision-hyperv.md) lub [wdrażanie StorSimple wirtualnego tablicy - Provision urządzenie wirtualne w środowisku programu VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
    ![Strona logowania](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. Nastąpi przekierowanie do **Home** strony. Na tej stronie opisano różne ustawienia wymagane do skonfigurowania i rejestrowanie urządzenia wirtualnego za pomocą usługi Menedżera urządzeń StorSimple. Należy pamiętać, że **ustawień sieciowych**, **ustawienia serwera proxy w sieci Web**, i **ustawienia** są opcjonalne. Tylko ustawienia wymagane są **ustawienia urządzenia** i **ustawienia funkcji Cloud**.
   
    ![Strona główna](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. Na **ustawień sieciowych** w obszarze **interfejsy sieciowe**, dane 0 zostaną automatycznie skonfigurowane dla Ciebie. Każdy interfejs sieciowy jest domyślnie automatycznie uzyskać adres IP (DHCP). W związku z tym adres IP, podsieci i brama zostanie automatycznie przypisany (dla protokołów IPv4 i IPv6).
   
    Zamierzasz wdrożyć urządzenie jako serwer iSCSI (w celu udostępniania magazynu blokowego), zaleca się wyłączenie **uzyskać adres IP automatycznie** opcji i skonfigurować statyczne adresy IP.
   
    ![Strona ustawień sieci](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Jeśli dodano więcej niż jeden interfejs sieciowy podczas inicjowania obsługi urządzenia, można je skonfigurować w tym miejscu. Należy pamiętać, że interfejsu sieciowego można skonfigurować jako IPv4 tylko lub jako protokołów IPv4 i IPv6. Konfiguracje tylko protokół IPv6 nie są obsługiwane.
5. Serwery DNS są wymagane, ponieważ są one używane, gdy urządzenie próbuje nawiązać połączenia z Twojej dostawców usługi magazynu w chmurze lub rozpoznać urządzenia według nazwy, jeśli jest on skonfigurowany jako serwer plików. Na **ustawień sieciowych** w obszarze **serwerów DNS**:
   
   1. Podstawowego i pomocniczego serwera DNS zostanie skonfigurowany automatycznie. Jeśli użytkownik chce skonfigurować statyczne adresy IP, można określić serwery DNS. Wysokiej dostępności firma Microsoft zaleca, aby skonfigurować podstawowy i pomocniczy serwer DNS.
   2. Kliknij przycisk **Zastosuj**. To spowoduje zastosowanie i sprawdź poprawność ustawień sieciowych.
6. Na **ustawienia urządzenia** strony:
   
   1. Przypisz unikatowy **nazwa** na urządzeniu. Ta nazwa może być 1 – 15 znaków i może zawierać litery, cyfry i łączniki.
   2. Kliknij przycisk **serwerem** ikona ![ikona serwera iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) dla **typu** urządzenia, które tworzysz. Serwer iSCSI umożliwia udostępniania magazynu blokowego.
   3. Określ, czy to urządzenie, aby być przyłączony do domeny. Jeśli urządzenie jest serwerem, następnie dołączenia do domeny jest opcjonalna. Jeśli zdecydujesz się nie przyłączyć serwer iSCSI do domeny, kliknij przycisk **Zastosuj**, poczekaj, aż ustawienia można zastosować, a następnie przejdź do następnego kroku.
      
       Jeśli chcesz dołączyć urządzenie do domeny. Wprowadź **nazwy domeny**, a następnie kliknij przycisk **Zastosuj**.
      
      > [!NOTE]
      > Jeśli serwer iSCSI dołączania do domeny, upewnij się, tablica wirtualnej jest w jego własnej jednostce organizacyjnej (OU) dla usługi Microsoft Azure Active Directory, a nie obiekty zasad grupy (GPO) są stosowane do niego.
      > 
      > 
   4. Zostanie wyświetlone okno dialogowe. Wprowadź swoje poświadczenia domeny w określonym formacie. Kliknij ikonę znacznika wyboru ![ikona znacznika wyboru](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). Będzie można zweryfikować poświadczeń domeny. Jeśli poświadczenia są nieprawidłowe, zostanie wyświetlony komunikat o błędzie.
      
       ![poświadczenia](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. Kliknij przycisk **Zastosuj**. To spowoduje zastosowanie i sprawdź poprawność ustawień urządzenia.
7. (Opcjonalnie) skonfiguruj serwer proxy sieci web. Mimo że konfiguracja serwera proxy sieci web jest opcjonalne, należy pamiętać, że jeśli używasz serwera proxy sieci web, można skonfigurować tylko go tutaj.
   
    ![Skonfiguruj serwer proxy sieci web](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    Na **serwer proxy sieci Web** strony:
   
   1. Podaj **adres URL serwera proxy sieci Web** w następującym formacie: *adres http://host-IP* lub *FDQN:Port numer*. Należy pamiętać, że adresy URL HTTPS nie są obsługiwane.
   2. Określ **uwierzytelniania** jako **podstawowe** lub **Brak**.
   3. Jeśli używasz uwierzytelniania, należy również zapewnienie **Username** i **hasło**.
   4. Kliknij przycisk **Zastosuj**. Spowoduje to zweryfikować i zastosowania ustawień serwera proxy sieci web skonfigurowana.
8. (Opcjonalnie) skonfiguruj ustawienia czasu dla urządzenia, na przykład serwery NTP podstawowych i pomocniczych i strefy czasowej. Serwery NTP są wymagane, ponieważ urządzenie musi synchronizować czas, aby zapewnić możliwość uwierzytelnienia z dostawcy usług w chmurze.
   
    ![Ustawienia czasu](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    Na **ustawienia** strony:
   
   1. Z listy rozwijanej wybierz **strefy czasowej** oparte na lokalizacji geograficznej, w którym jest wdrażany urządzenia. Domyślną strefę czasową dla Twojego urządzenia jest PST. Wszystkie zaplanowane operacje urządzenia będą wykonywane w ramach tej strefy czasowej.
   2. Określ **podstawowy serwer NTP** dla urządzenia lub zaakceptuj wartość domyślną time.windows.com. Upewnij się, że sieć zezwala na ruch NTP z centrum danych do Internetu.
   3. Opcjonalnie można określić **serwera NTP dodatkowej** dla danego urządzenia.
   4. Kliknij przycisk **Zastosuj**. Spowoduje to zweryfikować i Zastosuj ustawienia w skonfigurowanym czasie.
9. Skonfiguruj ustawienia chmury dla danego urządzenia. W tym kroku zostanie Zakończ konfigurację urządzenia lokalnego, a następnie zarejestruj urządzenie usługi Menedżer StorSimple urządzenia.
   
   1. Wprowadź **klucz rejestracji usługi** pochodzący **krok 2: pobieranie klucza rejestracji usługi** w [wdrażanie StorSimple wirtualnego tablicy — przygotowanie portalu](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
   2. Jeśli nie jest to pierwszy urządzenia, które są rejestrowanie w tej usłudze, musisz podać **klucza szyfrowania danych usługi**. Ten klucz jest wymagany klucz rejestracji usługi, aby zarejestrować dodatkowych urządzeń w usłudze Menedżer StorSimple urządzenia. Aby uzyskać więcej informacji, zapoznaj się [Pobierz klucz szyfrowania danych usługi](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) lokalnej interfejs użytkownika sieci web.
   3. Kliknij przycisk **zarejestrować**. Ponownie uruchomić urządzenie. Konieczne może być poczekaj 2 – 3 minutach urządzenie zostanie pomyślnie zarejestrowana. Po ponownym uruchomieniu urządzenia, nastąpi przekierowanie na stronę logowania.
      
      ![Rejestrowanie urządzenia](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Wróć do portalu Azure.
11. Przejdź do **urządzeń** bloku usługi. Jeśli masz wiele zasobów, kliknij przycisk **wszystkie zasoby**, kliknij nazwę usługi (wyszukaj go w razie potrzeby), a następnie kliknij przycisk **urządzeń**.
12. Na **urządzeń** bloku, sprawdź, czy urządzenie ma pomyślnie nawiązano połączenie z usługą sprawdzając stan. Urządzenie powinno mieć stan **Gotowe do skonfigurowania**.
    
    ![Rejestrowanie urządzenia](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>Krok 2: Konfigurowanie urządzenia jako serwer iSCSI

Wykonaj następujące czynności w portalu Azure w celu zakończenia instalacji wymagane urządzenia.

#### <a name="to-configure-the-device-as-iscsi-server"></a>Aby skonfigurować urządzenia jako serwer iSCSI

1. Przejdź do usługi Menedżer StorSimple urządzenia, a następnie przejdź do **zarządzania > urządzenia**. W **urządzeń** bloku, wybierz urządzenie właśnie utworzony. To urządzenie będzie wyświetlany jako **przejść do konfigurowania**.
   
    ![Konfigurowanie urządzenia jako serwer iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Kliknij urządzenie i będzie wyświetlany komunikat transparentu wskazujący, że urządzenie jest gotowe do instalacji.
   
    ![Konfigurowanie urządzenia jako serwer iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Kliknij przycisk **Konfiguruj** na pasku poleceń urządzenia. Spowoduje to otwarcie **Konfiguruj** bloku. W **Konfiguruj** blok, wykonaj następujące czynności:
   
   * Nazwa serwera iSCSI jest wypełniane automatycznie.
   * Upewnij się, że szyfrowanie magazynu w chmurze ma ustawioną wartość **włączone**. Dzięki temu szyfrowanie danych wysyłanych z urządzenia do chmury.
   * Określ klucz szyfrowania 32 znaków i zarejestruj go w aplikacji zarządzania kluczami do użytku w przyszłości.
   * Wybierz konto magazynu do użycia z urządzeniem. W tej subskrypcji, można wybrać istniejące konto magazynu, lub możesz kliknąć **Dodaj** do wyboru konta w ramach innej subskrypcji.
     
     ![Konfigurowanie urządzenia jako serwer iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Kliknij przycisk **Konfiguruj** aby dokończyć konfigurowanie serwera iSCSI.
   
    ![Konfigurowanie urządzenia jako serwer iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. Użytkownik zostanie powiadomiony, że trwa tworzenie serwera iSCSI. Po pomyślnym utworzeniu serwera iSCSI **urządzeń** bloku jest aktualizowana, a odpowiedni stan urządzenia jest **Online**.
   
    ![Konfigurowanie urządzenia jako serwer iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>Krok 3: Dodawanie woluminu

1. W **urządzeń** bloku, wybierz urządzenie właśnie skonfigurowany jako serwer iSCSI. Kliknij przycisk **...**  (można również kliknąć prawym przyciskiem myszy w tym wierszu) i wybierz z menu kontekstowego **Dodaj wolumin**. Możesz również kliknąć **+ Dodaj wolumin** z paska poleceń. Spowoduje to otwarcie **Dodaj wolumin** bloku.
   
    ![Dodawanie woluminu](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. W **Dodaj wolumin** blok, wykonaj następujące czynności:
   
   * W **nazwa woluminu** wprowadź unikatową nazwę dla woluminu. Nazwa musi być ciągiem o długości 3 do 127 znaków.
   * W **typu** listy rozwijanej liście, określ, czy można utworzyć **warstwowego** lub **przypięty lokalnie** woluminu. W przypadku obciążeń, które wymagają lokalnych gwarancji, małych opóźnień i większej wydajności, wybierz **przypięty lokalnie** **woluminu**. Dla wszystkich innych danych wybierz **warstwowego** **woluminu**.
   * W **pojemności** Określ rozmiar woluminu. Wolumin warstwowy musi należeć do zakresu od 500 GB i 5 TB i woluminu przypiętego lokalnie musi należeć do zakresu od 50 GB i 500 GB.
     
     Wolumin przypięty lokalnie jest alokowany nieelastycznie i gwarantuje, że główne dane na woluminie pozostaje na urządzeniu i nie zostaną przeniesione do chmury.
     
     Wolumin warstwowy z drugiej strony jest alokowany elastycznie. Podczas tworzenia woluminu warstwowego około 10% miejsca jest inicjowana na warstwie lokalnej i 90% miejsca jest udostępniony w chmurze. Na przykład jeśli zainicjowano obsługę administracyjną wolumin 1 TB, 100 GB, czy znajdują się w lokalnej przestrzeni i 900 GB byłoby używanych w chmurze po warstwy danych. To z kolei oznacza to, że jeśli całe lokalne miejsce na urządzeniu, nie może obsłużyć warstwowych udziału (ponieważ nie będą dostępne 10%).
     
     ![Dodawanie woluminu](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * Kliknij przycisk **połączone hosty**, zaznacz rekord kontroli dostępu (ACR) odpowiadający inicjatora iSCSI, który chcesz połączyć do tego woluminu, a następnie kliknij przycisk **wybierz**. <br><br> 
3. Aby dodać nowy host połączony, kliknij przycisk **Dodaj nowe**, wprowadź nazwę hosta i jego iSCSI kwalifikowana nazwa (IQN), a następnie kliknij przycisk **Dodaj**. Jeśli nie masz nazwy IQN, przejdź do [dodatek a. Pobieranie nazwy IQN hosta z systemem Windows Server](#appendix-a-get-the-iqn-of-a-windows-server-host).
   
      ![Dodawanie woluminu](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. Po zakończeniu konfigurowania woluminu kliknij **OK**. Wolumin zostanie utworzony z określonymi ustawieniami i zostanie wyświetlone powiadomienie. Domyślnie monitorowania i kopii zapasowej mają zostać włączone dla woluminu.
   
     ![Dodawanie woluminu](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. Aby upewnić się, że wolumin został utworzony pomyślnie, przejdź do **woluminów** bloku. Powinny pojawić się woluminu na liście.
   
   ![Dodawanie woluminu](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Krok 4: Instalowanie, inicjowanie i formatowanie woluminu

Wykonaj poniższe kroki, aby zainstalować, zainicjować i sformatować woluminy StorSimple na hoście systemu Windows Server.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Aby zainstalować, zainicjować i sformatować wolumin

1. Otwórz **inicjatora iSCSI** aplikacji na odpowiednim serwerze.
2. W oknie **Właściwości inicjatora iSCSI** na karcie **Odnajdowanie** kliknij pozycję **Odnajdź portal**.
   
    ![Odnajdź portal](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. W oknie dialogowym **Odnajdowanie portalu obiektu docelowego** podaj adres IP interfejsu sieci iSCSI, a następnie kliknij przycisk **OK**.
   
    ![Adres IP](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. W oknie **Właściwości inicjatora iSCSI** na karcie **Obiekty docelowe** zlokalizuj wartości **Wykryte obiekty docelowe**. (W poszczególnych woluminów będzie odnalezionych docelowy). Stan urządzenia powinien zostać wyświetlony jako **Nieaktywne**.
   
    ![Wykryte obiekty docelowe](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Wybierz urządzenie docelowe, a następnie kliknij przycisk **Connect**. Po połączeniu urządzenia stan powinien zmienić się na **Połączone**. (Aby uzyskać więcej informacji o korzystaniu z inicjatora iSCSI firmy Microsoft, zobacz [Instalowanie i konfigurowanie programu Microsoft iSCSI Initiator][1].
   
    ![Wybierz urządzenie docelowe](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. Na hoście systemu Windows naciśnij klawisz z logo systemu Windows + X, a następnie kliknij pozycję **Uruchom**.
7. W oknie dialogowym **Uruchom** wpisz **Diskmgmt.msc**. Kliknij przycisk **OK**. Zostanie wyświetlone okno **Zarządzanie dyskami**. W prawym okienku zostaną wyświetlone woluminy na hoście.
8. W oknie **Zarządzanie dyskami** zostaną wyświetlone zainstalowane woluminy (tak jak na poniższym rysunku). Kliknij prawym przyciskiem myszy odnaleziony wolumin (kliknij nazwę dysku), a następnie kliknij pozycję **Online**.
   
    ![Zarządzanie dyskami](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Kliknij prawym przyciskiem myszy i wybierz **Inicjowanie dysku**.
   
    ![Zainicjuj dysk 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. W oknie dialogowym Wybierz dyski do zainicjowania, a następnie kliknij przycisk **OK**.
    
    ![Inicjowanie dysku 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. Zostanie uruchomiony Kreator nowego woluminu prostego. Wybierz rozmiar dysku, a następnie kliknij przycisk **dalej**.
    
    ![Kreator nowego woluminu 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. Przypisz literę dysku do woluminu, a następnie kliknij przycisk **dalej**.
    
    ![Kreator nowego woluminu 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Wprowadź parametry sformatowania woluminu. **W systemie Windows Server jest obsługiwana wyłącznie systemu plików NTFS.** Ustaw rozmiar jednostki alokacji 64 KB. Podaj etykietę woluminu. Jest zalecanym najlepszym rozwiązaniem dla tej nazwy, być taka sama jak nazwa woluminu, podane na tablica wirtualne StorSimple. Kliknij przycisk **Dalej**.
    
    ![Kreator nowego woluminu 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Sprawdź wartości dla woluminu, a następnie kliknij przycisk **Zakończ**.
    
    ![Kreator nowego woluminu 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    Woluminy będą wyświetlane jako **Online** na **Zarządzanie dyskami** strony.
    
    ![woluminy w trybie online](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak używać lokalnej sieci web interfejsu użytkownika Służącego do [administrowania tablica wirtualnego StorSimple](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Dodatek a. Pobieranie nazwy IQN hosta z systemem Windows Server

Wykonaj poniższe kroki, aby pobrać kwalifikowaną nazwę iSCSI (IQN) hosta z systemem Windows, na którym uruchomiono system Windows Server 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Aby pobrać nazwę IQN hosta z systemem Windows

1. Uruchom inicjator iSCSI firmy Microsoft na hoście z systemem Windows.
2. W oknie **Właściwości inicjatora iSCSI** na karcie **Konfiguracja** zaznacz i skopiuj ciąg z pola **Nazwa inicjatora**.
   
    ![Właściwości inicjatora iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Zapisz ten ciąg.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



