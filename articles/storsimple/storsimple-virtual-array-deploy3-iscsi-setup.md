---
title: Konfiguracja serwera iSCSI firmy Microsoft Azure StorSimple Virtual Array | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób wykonywania początkowej konfiguracji, zarejestrować serwer iSCSI StorSimple i przeprowadzić konfigurację urządzenia.
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
ms.openlocfilehash: 5d3525952ec09474d60618c4f99138cef1fce57a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61417311"
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>Podczas wdrażania rozwiązania StorSimple Virtual Array — zestaw się jako serwera iSCSI za pośrednictwem witryny Azure portal

![przepływ procesu instalacji iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Omówienie

W tym samouczku wdrażania stosuje się do programu Microsoft Azure StorSimple Virtual Array. W tym samouczku opisano sposób wykonywania początkowej konfiguracji, zarejestrować serwer iSCSI StorSimple, przeprowadzić konfigurację urządzenia następnie utworzyć, instalacji, zainicjować i sformatować woluminy na rozwiązania StorSimple Virtual Array, skonfigurowany jako serwer iSCSI. 

Procedury opisane w tym miejscu zajmie około 30 minut do 1 godziny, aby zakończyć. Informacje zawarte w tym artykule dotyczy tylko macierze wirtualne StorSimple.

## <a name="setup-prerequisites"></a>Wymagania wstępne instalacji

Przed skonfigurowaniem i konfigurowanie rozwiązania StorSimple Virtual Array, upewnij się, że:

* Ma obsługiwana macierz wirtualną i do niego podłączone, zgodnie z opisem w [wdrażania rozwiązania StorSimple Virtual Array — Aprowizowanie macierzy wirtualnej w funkcji Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) lub [wdrażania rozwiązania StorSimple Virtual Array — Aprowizowanie macierzy wirtualnej w środowisku programu VMware ](storsimple-virtual-array-deploy2-provision-vmware.md).
* Masz klucz rejestracji usługi z usługą Menedżera urządzeń StorSimple, utworzony w celu zarządzania macierzy wirtualnej StorSimple. Aby uzyskać więcej informacji, zobacz **krok 2: Pobierz klucz rejestracji usługi** w [wdrażania rozwiązania StorSimple Virtual Array — przygotowywanie portalu](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
* Jeśli jest to drugi lub kolejny macierzy wirtualnej, w przypadku rejestracji za pomocą istniejącej usługi Menedżer urządzeń StorSimple, powinny mieć klucz szyfrowania danych usługi. Ten klucz został wygenerowany podczas pierwszego urządzenia został pomyślnie zarejestrowany z tą usługą. W przypadku utraty tego klucza, zobacz **Pobierz klucz szyfrowania danych usługi** w [administrowania rozwiązania StorSimple Virtual Array przy użyciu interfejsu użytkownika sieci Web](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Instrukcje krok po kroku instalacji

Poniższe instrukcje umożliwiają instalowanie i konfigurowanie rozwiązania StorSimple Virtual Array:

* [Krok 1. Ukończ konfigurację lokalnego internetowego interfejsu użytkownika i rejestrowanie urządzenia](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* Krok 2: Kończenie konfiguracji urządzeń
* [Krok 3. Dodawanie woluminu](#step-3-add-a-volume)
* [Krok 4. Instalowanie, inicjowanie i formatowanie woluminu](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Krok 1: Ukończ konfigurację lokalnego internetowego interfejsu użytkownika i rejestrowanie urządzenia

#### <a name="to-complete-the-setup-and-register-the-device"></a>Aby ukończyć instalację i rejestrowanie urządzenia

1. Otwórz okno przeglądarki. Aby podłączyć się do typu interfejsu użytkownika sieci web:
   
    `https://<ip-address of network interface>`
   
    Użyj adresu URL połączenia zanotowanego w poprzednim kroku. Zostanie wyświetlony błąd informujący o tym, że występuje problem z certyfikatem zabezpieczeń witryny sieci Web. Kliknij przycisk **Kontynuuj przeglądanie tej strony sieci web**.
   
    ![Błąd certyfikatu bezpieczeństwa](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Zaloguj się do internetowego interfejsu użytkownika urządzenia wirtualnego jako **StorSimpleAdmin**. Wprowadź hasło administratora urządzenia, który został zmodyfikowany w kroku 3: Uruchom urządzenie wirtualne w [wdrażania rozwiązania StorSimple Virtual Array — Aprowizowanie urządzenia wirtualnego funkcji Hyper-v](storsimple-virtual-array-deploy2-provision-hyperv.md) lub [wdrażania rozwiązania StorSimple Virtual Array — Aprowizowanie urządzenia wirtualnego w oprogramowaniu VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
    ![Strona logowania](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. Nastąpi przekierowanie do **Home** strony. Ta strona w tym artykule opisano różne ustawienia, które są wymagane, aby skonfigurować i zarejestrować urządzenie wirtualne w usłudze Menedżer urządzeń StorSimple. Należy pamiętać, że **ustawienia sieciowe**, **ustawień serwera proxy sieci Web**, i **ustawienia czasu** są opcjonalne. Tylko ustawienia wymagane są **ustawienia urządzenia** i **ustawienia chmury**.
   
    ![Strona główna](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. Na **ustawienia sieciowe** strony w obszarze **interfejsy sieciowe**, interfejs DATA 0 zostaną automatycznie skonfigurowane dla Ciebie. Każdy interfejs sieciowy jest ustawiana domyślnie, aby uzyskać adres IP automatycznie (DHCP). W związku z tym adres IP, podsieci i brama zostanie automatycznie przypisany (dla protokołów IPv4 i IPv6).
   
    Planujesz wdrożyć urządzenie jako serwera iSCSI (w celu aprowizacji magazynu blokowego), zaleca się wyłączenie **Uzyskaj adres IP automatycznie** opcji i skonfigurować statyczne adresy IP.
   
    ![Strona Ustawienia sieci](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Jeśli więcej niż jeden interfejs sieciowy jest dodawany podczas aprowizowania urządzenia, można je skonfigurować w tym miejscu. Należy pamiętać, że interfejsu sieciowego można skonfigurować jako IPv4 tylko lub jako IPv4 i IPv6. Tylko konfiguracje protokołu IPv6 nie są obsługiwane.
5. Serwery DNS są wymagane, ponieważ są one używane, gdy urządzenie próbuje nawiązać połączenia z dostawców usługi magazynu w chmurze lub rozwiązać urządzenia według nazwy, jeśli jest skonfigurowany jako serwer plików. Na **ustawienia sieciowe** strony w obszarze **serwerów DNS**:
   
   1. Podstawowego i pomocniczego serwera DNS zostaną skonfigurowane automatycznie. Jeśli zdecydujesz się skonfigurować statyczne adresy IP, można określić serwery DNS. Wysoką dostępność zaleca się skonfigurowanie podstawowy i pomocniczy serwer DNS.
   2. Kliknij przycisk **Zastosuj**. Spowoduje to zastosowanie i sprawdź poprawność ustawień sieciowych.
6. Na **ustawienia urządzenia** strony:
   
   1. Przypisz unikatowy **nazwa** do Twojego urządzenia. Ta nazwa może zawierać 1 – 15 znaków i może zawierać litery, cyfry i łączniki.
   2. Kliknij przycisk **serwera iSCSI** ikonę ![ikona serwera iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) dla **typu** urządzenia, które tworzysz. Serwer iSCSI pozwoli aprowizowanie blokowej pamięci masowej.
   3. Określ, czy to urządzenie być przyłączone do domeny. Jeśli urządzenie jest serwer iSCSI, następnie dołączenia do domeny jest opcjonalne. Jeśli zdecydujesz się nie Przyłączanie serwera iSCSI do domeny, kliknij przycisk **Zastosuj**, poczekaj, aż ustawienia można zastosować, a następnie przejdź do następnego kroku.
      
       Jeśli chcesz dołączyć urządzenie do domeny. Wprowadź **nazwy domeny**, a następnie kliknij przycisk **Zastosuj**.
      
      > [!NOTE]
      > Jeśli serwer iSCSI przyłączania do domeny, upewnij się, że macierz wirtualna jest w jego własnej jednostce organizacyjnej (OU) usługi Microsoft Azure Active Directory i są do niego zastosowane nie obiekty zasad grupy (GPO).
      > 
      > 
   4. Pojawi się okno dialogowe. Wprowadź swoje poświadczenia domeny w określonym formacie. Kliknij ikonę znacznika wyboru ![ikona znacznika wyboru](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). Będzie można zweryfikować poświadczeń domeny. Zostanie wyświetlony komunikat o błędzie, jeśli poświadczenia są nieprawidłowe.
      
       ![Poświadczenia](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. Kliknij przycisk **Zastosuj**. Spowoduje to zastosowanie i sprawdź poprawność ustawień urządzenia.
7. (Opcjonalnie) skonfiguruj serwer proxy sieci web. Mimo że konfiguracja serwera proxy sieci web jest opcjonalny, należy pamiętać, że jeśli używasz serwera proxy sieci web, można skonfigurować tylko go tutaj.
   
    ![Konfigurowanie serwera proxy sieci web](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    Na **serwera proxy sieci Web** strony:
   
   1. Podaj **adres URL serwera proxy sieci Web** w następującym formacie: *http:\/adres /host-IP* lub *FQDN: port numer*. Należy pamiętać, że adresy URL HTTPS nie są obsługiwane.
   2. Określ **uwierzytelniania** jako **podstawowe** lub **Brak**.
   3. Jeśli używasz uwierzytelniania, należy również zapewnienie **Username** i **hasło**.
   4. Kliknij przycisk **Zastosuj**. Spowoduje to zweryfikować i zastosowania ustawień serwera proxy sieci web skonfigurowany.
8. (Opcjonalnie) skonfiguruj ustawienia czasu dla danego urządzenia, takie jak strefę czasową i podstawowych i pomocniczych serwerów NTP. Serwery NTP są wymagane, ponieważ urządzenie musi synchronizować czas, aby zapewnić możliwość uwierzytelnienia przy użyciu dostawców usług w chmurze.
   
    ![Ustawienia czasu](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    Na **ustawienia czasu** strony:
   
   1. Wybierz z listy rozwijanej **strefa czasowa** na podstawie lokalizacji geograficznej, w której jest wdrażane urządzenie. Domyślna strefa czasowa dla Twojego urządzenia jest czasu PST. Wszystkie zaplanowane operacje urządzenia będą wykonywane w ramach tej strefy czasowej.
   2. Określ **podstawowy serwer NTP** dla urządzenia lub zaakceptuj wartość domyślną z time.windows.com. Upewnij się, że sieć zezwala na ruch NTP z centrum danych do Internetu.
   3. Opcjonalnie można określić **serwera NTP dodatkowej** dla Twojego urządzenia.
   4. Kliknij przycisk **Zastosuj**. Spowoduje to zweryfikować i zastosować ustawienia czasu skonfigurowane.
9. Skonfiguruj ustawienia chmury dla Twojego urządzenia. W tym kroku możesz ukończyć konfiguracji lokalnego urządzenia i zarejestrować urządzenie w usłudze Menedżer urządzeń StorSimple.
   
   1. Wprowadź **klucz rejestracji usługi** uzyskany **krok 2: Pobierz klucz rejestracji usługi** w [wdrażania rozwiązania StorSimple Virtual Array — przygotowywanie portalu](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
   2. Jeśli nie jest to pierwsze urządzenie, które są rejestrowane w tej usłudze, musisz podać **klucza szyfrowania danych usługi**. Ten klucz jest wymagany przy użyciu klucza rejestracji usługi w celu rejestracji dodatkowych urządzeń w usłudze Menedżer urządzeń StorSimple. Aby uzyskać więcej informacji, zobacz [Pobierz klucz szyfrowania danych usługi](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) lokalnej interfejs użytkownika sieci web.
   3. Kliknij przycisk **zarejestrować**. To spowoduje ponowne uruchomienie urządzenia. Może być konieczne poczekaj 2-3 minut, zanim urządzenie zostanie pomyślnie zarejestrowana. Po ponownym uruchomieniu urządzenia, nastąpi przekierowanie do strony logowania.
      
      ![Rejestrowanie urządzenia](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Wróć do witryny Azure Portal.
11. Przejdź do **urządzeń** bloku usługi. Jeśli masz wiele zasobów, kliknij przycisk **wszystkie zasoby**, kliknij nazwę usługi (wyszukaj go w razie potrzeby), a następnie kliknij przycisk **urządzeń**.
12. Na **urządzeń** bloku, sprawdź, czy urządzenie pomyślnie nawiązało z usługą, sprawdzając jego stan. Urządzenie powinno mieć stan **Gotowe do skonfigurowania**.
    
    ![Rejestrowanie urządzenia](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>Krok 2: Konfigurowanie urządzenia jako serwera iSCSI

Wykonaj następujące czynności w witrynie Azure portal, aby ukończyć konfigurację urządzenia wymagane.

#### <a name="to-configure-the-device-as-iscsi-server"></a>Aby skonfigurować urządzenia jako serwera iSCSI

1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie przejdź do **zarządzania > urządzenia**. W **urządzeń** bloku wybierz urządzenia został właśnie utworzony. To urządzenie będzie wyświetlany jako **gotowe do skonfigurowania**.
   
    ![Konfigurowanie urządzenia jako serwera iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Kliknij urządzenie, a następnie zostanie wyświetlony komunikat z transparent informujący, że urządzenie jest gotowe do instalacji.
   
    ![Konfigurowanie urządzenia jako serwera iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Kliknij przycisk **Konfiguruj** na pasku poleceń urządzenia. Spowoduje to otwarcie **Konfiguruj** bloku. W **Konfiguruj** blok, wykonaj następujące czynności:
   
   * Nazwa serwera iSCSI jest wypełniane automatycznie.
   * Upewnij się, że szyfrowanie magazynu w chmurze jest ustawiona na **włączone**. Dzięki temu szyfrowanie danych wysyłanych z urządzenia do chmury.
   * Określ klucz szyfrowania 32-znakowy, a następnie zapisz go w aplikacji do zarządzania kluczami do użytku w przyszłości.
   * Wybierz konto magazynu ma być używany z Twoim urządzeniem. W tej subskrypcji można wybrać istniejące konto magazynu, lub kliknąć **Dodaj** wyboru konta z innej subskrypcji.
     
     ![Konfigurowanie urządzenia jako serwera iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Kliknij przycisk **Konfiguruj** można ukończyć konfigurowania serwera iSCSI.
   
    ![Konfigurowanie urządzenia jako serwera iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. Powiadomimy Cię, że trwa tworzenie serwera iSCSI. Po pomyślnym utworzeniu serwera iSCSI **urządzeń** bloku jest aktualizowana, a odpowiedni stan urządzenia jest **Online**.
   
    ![Konfigurowanie urządzenia jako serwera iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>Krok 3: Dodawanie woluminu

1. W **urządzeń** bloku, wybierz urządzenie, właśnie skonfigurowany jako serwer iSCSI. Kliknij przycisk **...**  (ewentualnie kliknij prawym przyciskiem myszy w tym wierszu) z menu kontekstowego wybierz **Dodaj wolumin**. Możesz również kliknąć **+ Dodaj wolumin** na pasku poleceń. Spowoduje to otwarcie **Dodaj wolumin** bloku.
   
    ![Dodawanie woluminu](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. W **Dodaj wolumin** blok, wykonaj następujące czynności:
   
   * W **nazwa woluminu** wprowadź unikatową nazwę dla woluminu. Nazwa musi być ciąg, który zawiera 3 do 127 znaków.
   * W **typu** listę rozwijaną listę, określ, czy należy utworzyć **warstwowy** lub **przypięty lokalnie** woluminu. Dla obciążeń, które wymagają lokalnych gwarancji, małych opóźnień i większej wydajności, wybierz **przypięty lokalnie** **woluminu**. Dla wszystkich innych danych wybierz **warstwowy** **woluminu**.
   * W **pojemności** Określ rozmiar woluminu. Wolumin warstwowy musi wynosić od 500 GB do 5 TB, a następnie wolumin przypięty lokalnie muszą należeć do zakresu od 50 do 500 GB.
     
     Wolumin przypięty lokalnie jest alokowany nieelastycznie i gwarantuje, że główne dane na woluminie pozostaje na urządzeniu i nie zostaną przeniesione do chmury.
     
     Wolumin warstwowy z drugiej strony jest alokowany elastycznie. Podczas tworzenia woluminu warstwowego około 10% wolnego miejsca jest inicjowana na warstwie lokalnej i 90% miejsca są aprowizowane w chmurze. Na przykład, jeśli aprowizowano 1 TB woluminu 100 GB będzie znajdować się w lokalnej przestrzeni i 900 GB będzie używana w chmurze podczas warstwy danych. To z kolei oznacza to, że jeśli zabraknie całe lokalne miejsce na urządzeniu nie można aprowizować udziału warstwowego (ponieważ 10%, nie będą dostępne).
     
     ![Dodawanie woluminu](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * Kliknij przycisk **połączone hosty**, zaznacz rekord kontroli dostępu (ACR) odpowiadający inicjatora iSCSI, którą chcesz nawiązać połączenie z tego woluminu, a następnie kliknij przycisk **wybierz**. <br><br> 
3. Aby dodać nowy host połączony, kliknij **Dodaj nowe**, wprowadź nazwę hosta i jego iSCSI kwalifikowana nazwa (IQN), a następnie kliknij **Dodaj**. Jeśli nie masz nazwy IQN, przejdź do strony [dodatek A: Pobieranie nazwy IQN hosta z systemem Windows Server](#appendix-a-get-the-iqn-of-a-windows-server-host).
   
      ![Dodawanie woluminu](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. Po zakończeniu konfigurowania woluminu kliknij **OK**. Wolumin zostanie utworzony z określonymi ustawieniami, a następnie zostanie wyświetlone powiadomienie. Domyślnie monitorowanie i tworzenie kopii zapasowych zostaną włączone dla danego woluminu.
   
     ![Dodawanie woluminu](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. Aby upewnić się, że wolumin został utworzony pomyślnie, przejdź do **woluminów** bloku. Wolumin, na liście powinien zostać wyświetlony.
   
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
   
    ![odnalezione obiekty docelowe](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Wybierz urządzenie docelowe, a następnie kliknij przycisk **Connect**. Po połączeniu urządzenia stan powinien zmienić się na **Połączone**. (Aby uzyskać więcej informacji o korzystaniu z inicjatora iSCSI firmy Microsoft, zobacz [Instalowanie i konfigurowanie programu Microsoft iSCSI Initiator][1].
   
    ![Wybierz urządzenie docelowe](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. Na hoście systemu Windows naciśnij klawisz z logo systemu Windows + X, a następnie kliknij pozycję **Uruchom**.
7. W oknie dialogowym **Uruchom** wpisz **Diskmgmt.msc**. Kliknij przycisk **OK**. Zostanie wyświetlone okno **Zarządzanie dyskami**. W prawym okienku zostaną wyświetlone woluminy na hoście.
8. W oknie **Zarządzanie dyskami** zostaną wyświetlone zainstalowane woluminy (tak jak na poniższym rysunku). Kliknij prawym przyciskiem myszy odnaleziony wolumin (kliknij nazwę dysku), a następnie kliknij pozycję **Online**.
   
    ![Zarządzanie dyskami](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Kliknij prawym przyciskiem myszy i wybierz **Zainicjuj dysk**.
   
    ![Zainicjuj dysk 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. W oknie dialogowym Wybierz dyski, zainicjuj, a następnie kliknij przycisk **OK**.
    
    ![Zainicjuj dysk 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. Zostanie uruchomiony Kreator nowego woluminu prostego. Wybierz rozmiar dysku, a następnie kliknij przycisk **dalej**.
    
    ![Kreator nowego woluminu 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. Przypisać literę dysku do woluminu, a następnie kliknij przycisk **dalej**.
    
    ![Kreator nowego woluminu 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Wprowadź parametry, aby sformatować wolumin. **W systemie Windows Server jest obsługiwana tylko systemu plików NTFS.** Ustaw rozmiar jednostki alokacji 64 KB. Podaj etykietę dla woluminu. Jest zalecanym najlepszym rozwiązaniem dla tej nazwy, być taka sama jak nazwa woluminu, które podałeś na rozwiązania StorSimple Virtual Array. Kliknij przycisk **Dalej**.
    
    ![Kreator nowego woluminu 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Sprawdź wartości dla woluminu, a następnie kliknij przycisk **Zakończ**.
    
    ![Kreator nowego woluminu 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    Woluminy będą wyświetlane jako **Online** na **przystawki Zarządzanie dyskami** strony.
    
    ![woluminy w tryb online](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak używać lokalnego internetowego interfejsu użytkownika do [administrowania rozwiązania StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Dodatek A: Pobieranie nazwy IQN hosta z systemem Windows Server

Wykonaj poniższe kroki, aby pobrać kwalifikowaną nazwę iSCSI (IQN) hosta z systemem Windows, na którym uruchomiono system Windows Server 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Aby pobrać nazwę IQN hosta z systemem Windows

1. Uruchom inicjator iSCSI firmy Microsoft na hoście z systemem Windows.
2. W oknie **Właściwości inicjatora iSCSI** na karcie **Konfiguracja** zaznacz i skopiuj ciąg z pola **Nazwa inicjatora**.
   
    ![Właściwości inicjatora iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Zapisz ten ciąg.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



