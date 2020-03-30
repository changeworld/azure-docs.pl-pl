---
title: Konfiguracja serwera iSCSI tablicy wirtualnej usługi Microsoft Azure StorSimple | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób przeprowadzania wstępnej konfiguracji, rejestrowania serwera iSCSI storsimple i ukończenia konfiguracji urządzenia.
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
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 4560ca2b07826e2a071f515f147dfab8cbec3624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254497"
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>Wdrażanie tablicy wirtualnej StorSimple — konfigurowanie jako serwera iSCSI za pośrednictwem witryny Azure portal

![przepływ procesu konfiguracji iscsi](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Omówienie

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Ten samouczek wdrażania dotyczy tablicy wirtualnej Microsoft Azure StorSimple. W tym samouczku opisano sposób wykonywania początkowej konfiguracji, rejestrowania serwera iSCSI storsimple, ukończenia konfiguracji urządzenia, a następnie tworzenia, instalowania, inicjowania i formatowania woluminów na macierzy wirtualnej StorSimple skonfigurowanej jako serwer iSCSI. 

Procedury opisane tutaj trwać około 30 minut do 1 godziny, aby zakończyć. Informacje opublikowane w tym artykule dotyczą tylko tablic wirtualnych StorSimple.

## <a name="setup-prerequisites"></a>Wymagania wstępne instalacji

Przed skonfigurowaniem i skonfigurowaniem tablicy wirtualnej StorSimple upewnij się, że:

* Aprowizacja tablicy wirtualnej i połączenie z nią zgodnie z opisem w [deploy StorSimple Virtual Array — aprowizowanie tablicy wirtualnej w macierzy wirtualnej hyper-V](storsimple-ova-deploy2-provision-hyperv.md) lub [Deploy StorSimple Virtual Array — aprowizowanie tablicy wirtualnej w VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
* Masz klucz rejestracji usługi z usługi StorSimple Device Manager, który został utworzony w celu zarządzania storsimple tablicwirtua. Aby uzyskać więcej informacji, zobacz **krok 2: Pobierz klucz rejestracji usługi** w deploy [StorSimple Virtual Array - Przygotuj portal](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
* Jeśli jest to druga lub kolejna tablica wirtualna, która rejestrujesz się w istniejącej usłudze StorSimple Device Manager, powinien mieć klucz szyfrowania danych usługi. Ten klucz został wygenerowany, gdy pierwsze urządzenie zostało pomyślnie zarejestrowane w tej usłudze. Jeśli ten klucz został utracony, zobacz **Pobierz klucz szyfrowania danych usługi** w obszarze [Administrowanie tablicą wirtualną StorSimple za pomocą interfejsu użytkownika sieci Web](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Konfiguracja krok po kroku

Użyj następujących instrukcji krok po kroku, aby skonfigurować i skonfigurować tablicę wirtualną StorSimple:

* [Krok 1: Ukończ konfigurację lokalnego interfejsu użytkownika sieci Web i zarejestruj urządzenie](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* Krok 2: Wykonaj wymaganą konfigurację urządzenia
* [Krok 3: Dodawanie woluminu](#step-3-add-a-volume)
* [Krok 4: Montowanie, inicjowanie i formatowanie woluminu](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Krok 1: Ukończ konfigurację lokalnego interfejsu użytkownika sieci Web i zarejestruj urządzenie

#### <a name="to-complete-the-setup-and-register-the-device"></a>Aby zakończyć konfigurację i zarejestrować urządzenie

1. Otwórz okno przeglądarki. Aby połączyć się z webowym typu interfejsu użytkownika:
   
    `https://<ip-address of network interface>`
   
    Użyj adresu URL połączenia odnotowanego w poprzednim kroku. Zostanie wyświetlony komunikat o błędzie informujący o problemie z certyfikatem zabezpieczeń witryny sieci Web. Kliknij **przycisk Kontynuuj do tej strony sieci Web**.
   
    ![błąd certyfikatu zabezpieczeń](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Zaloguj się do internetowego interfejsu użytkownika urządzenia wirtualnego jako **StorSimpleAdmin**. Wprowadź hasło administratora urządzenia, które zostało zmienione w kroku 3: Uruchom urządzenie wirtualne w [deploy StorSimple Virtual Array - Aprowizuj urządzenie wirtualne w macierzy wirtualnej Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) lub [Deploy StorSimple Virtual Array - Aprowizuj urządzenie wirtualne w VMware](storsimple-virtual-array-deploy2-provision-vmware.md).
   
    ![Strona logowania](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. Zostaniesz przesuń na stronę **główną.** Na tej stronie opisano różne ustawienia wymagane do skonfigurowania i zarejestrowania urządzenia wirtualnego w usłudze StorSimple Device Manager. Należy zauważyć, że **ustawienia sieci**, **ustawienia serwera proxy sieci Web**i ustawienia **czasu** są opcjonalne. Jedynymi wymaganymi **ustawieniami** są ustawienia urządzenia i **ustawienia chmury**.
   
    ![Strona główna](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. Na stronie **Ustawienia sieci** w obszarze **Interfejsy sieci**dane 0 zostaną automatycznie skonfigurowane. Każdy interfejs sieciowy jest domyślnie ustawiany tak, aby automatycznie uzyskać adres IP (DHCP). W związku z tym adres IP, podsieć i brama zostaną przypisane automatycznie (zarówno dla protokołu IPv4, jak i IPv6).
   
    Planujące wdrożenie urządzenia jako serwera iSCSI (w celu aprowizowania magazynu blokowego) zaleca się wyłączenie opcji **Pobierz adres IP automatycznie** i skonfigurowanie statycznych adresów IP.
   
    ![Strona ustawień sieciowych](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Jeśli dodano więcej niż jeden interfejs sieciowy podczas inicjowania obsługi administracyjnej urządzenia, można skonfigurować je w tym miejscu. Należy zauważyć, że można skonfigurować interfejs sieciowy tylko jako IPv4 lub zarówno jako IPv4 i IPv6. Konfiguracje tylko IPv6 nie są obsługiwane.
5. Serwery DNS są wymagane, ponieważ są używane, gdy urządzenie próbuje komunikować się z dostawcami usług magazynu w chmurze lub rozpoznać urządzenie według nazwy, jeśli jest skonfigurowane jako serwer plików. Na stronie **Ustawienia sieciowe** pod **serwerami DNS:**
   
   1. Podstawowy i pomocniczy serwer DNS zostanie skonfigurowany automatycznie. Jeśli zdecydujesz się skonfigurować statyczne adresy IP, można określić serwery DNS. Aby uzyskać wysoką dostępność, zaleca się skonfigurowanie podstawowego i pomocniczego serwera DNS.
   2. Kliknij przycisk **Zastosuj**. Spowoduje to zastosowanie i sprawdź poprawność ustawień sieciowych.
6. Na stronie **Ustawienia urządzenia:**
   
   1. Przypisz unikatową **nazwę** do urządzenia. Ta nazwa może zawierać od 1 do 15 znaków i może zawierać litery, cyfry i łączniki.
   2. Kliknij ikonę](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) **serwera iSCSI** iSCSI ![dla **typu** urządzenia, które tworzysz. Serwer iSCSI umożliwia aprowizowanie magazynu blokowego.
   3. Określ, czy to urządzenie ma być przyłączone do domeny. Jeśli urządzenie jest serwerem iSCSI, dołączenie do domeny jest opcjonalne. Jeśli zdecydujesz się nie łączyć serwera iSCSI z domeną, kliknij przycisk **Zastosuj**, poczekaj na zastosowanie ustawień, a następnie przejdź do następnego kroku.
      
       Jeśli chcesz dołączyć urządzenie do domeny. Wprowadź **nazwę domeny**, a następnie kliknij przycisk **Zastosuj**.
      
      > [!NOTE]
      > Jeśli dołączanie serwera iSCSI do domeny, upewnij się, że tablica wirtualna znajduje się we własnej jednostce organizacyjnej (OU) dla usługi Microsoft Azure Active Directory i nie są do niej stosowane żadne obiekty zasad grupy.
      > 
      > 
   4. Pojawi się okno dialogowe. Wprowadź poświadczenia domeny w określonym formacie. Kliknij ikonę znacznika wyboru, ![ikona znacznika wyboru](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png). Poświadczenia domeny zostaną zweryfikowane. Zostanie wyświetlony komunikat o błędzie, jeśli poświadczenia są niepoprawne.
      
       ![poświadczenia](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. Kliknij przycisk **Zastosuj**. Spowoduje to zastosowanie i sprawdź poprawność ustawień urządzenia.
7. (Opcjonalnie) skonfiguruj serwer proxy sieci Web. Konfiguracja serwera proxy sieci Web jest opcjonalna, jednak warto wiedzieć, że w przypadku korzystania z serwera proxy sieci Web można go skonfigurować tylko w tym miejscu.
   
    ![konfigurowanie serwera proxy sieci Web](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    Na stronie **serwera proxy sieci Web:**
   
   1. Podaj **adres URL serwera proxy sieci Web** w tym formacie: *http:\//host-IP address* or *FQDN:Port number*. Należy pamiętać, że adresy URL HTTPS nie są obsługiwane.
   2. Określ **uwierzytelnianie** jako **podstawowe** lub **brak**.
   3. Jeśli korzystasz z uwierzytelniania, musisz również podać **nazwę użytkownika** i **hasło.**
   4. Kliknij przycisk **Zastosuj**. Spowoduje to sprawdzenie poprawności i zastosowanie skonfigurowanych ustawień serwera proxy sieci Web.
8. (Opcjonalnie) skonfiguruj ustawienia czasu dla urządzenia, takie jak strefa czasowa oraz podstawowe i pomocnicze serwery NTP. Serwery NTP są wymagane, ponieważ urządzenie musi synchronizować czas, aby można było uwierzytelnić się z dostawcami usług w chmurze.
   
    ![Ustawienia czasu](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    Na stronie **Ustawienia czasu:**
   
   1. Z listy rozwijanej wybierz **strefę czasową** na podstawie lokalizacji geograficznej, w której urządzenie jest wdrażane. Domyślną strefą czasową urządzenia jest PST. Wszystkie zaplanowane operacje urządzenia będą wykonywane w ramach tej strefy czasowej.
   2. Określ **podstawowy serwer NTP** dla urządzenia lub zaakceptuj domyślną wartość time.windows.com. Upewnij się, że sieć zezwala na ruch NTP z centrum danych do Internetu.
   3. Opcjonalnie określ **pomocniczy serwer NTP** dla urządzenia.
   4. Kliknij przycisk **Zastosuj**. Spowoduje to sprawdzenie poprawności i zastosowanie skonfigurowanych ustawień czasu.
9. Skonfiguruj ustawienia chmury dla swojego urządzenia. W tym kroku zostanie zakończona konfiguracja urządzenia lokalnego, a następnie zarejestrujesz urządzenie w usłudze StorSimple Device Manager.
   
   1. Wprowadź **klucz rejestracji usługi,** który został wprowadzony w **kroku 2: Pobierz klucz rejestracji usługi** w deploy [StorSimple Virtual Array - Przygotuj portal](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
   2. Jeśli nie jest to pierwsze urządzenie, które rejestrujesz w tej usłudze, musisz podać **klucz szyfrowania danych usługi**. Ten klucz jest wymagany przy przy tym kluczu rejestracji usługi do rejestrowania dodatkowych urządzeń w usłudze StorSimple Device Manager. Aby uzyskać więcej informacji, zobacz [Pobierz klucz szyfrowania danych usługi](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) w lokalnym interfejsie użytkownika sieci Web.
   3. Kliknij **pozycję Zarejestruj**. Spowoduje to ponowne uruchomienie urządzenia. Przed pomyślnym zarejestrowaniem urządzenia może być konieczne odczekanie 2-3 minut. Po ponownym uruchomieniu urządzenia zostaniesz przesunięty na stronę logowania.
      
      ![Zarejestruj urządzenie](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Wróć do witryny Azure Portal.
11. Przejdź do bloku **Urządzenia** usługi. Jeśli masz dużo zasobów, kliknij pozycję **Wszystkie zasoby**, kliknij nazwę usługi (wyszukaj ją w razie potrzeby), a następnie kliknij pozycję **Urządzenia**.
12. Na **urządzenia bloku,** sprawdź, czy urządzenie pomyślnie połączyło się z usługą, wyszukując stan. Urządzenie powinno mieć stan **Gotowe do skonfigurowania**.
    
    ![Zarejestruj urządzenie](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>Krok 2: Konfigurowanie urządzenia jako serwera iSCSI

Wykonaj następujące kroki w witrynie Azure portal, aby ukończyć wymaganą konfigurację urządzenia.

#### <a name="to-configure-the-device-as-iscsi-server"></a>Aby skonfigurować urządzenie jako serwer iSCSI

1. Przejdź do usługi StorSimple Device Manager, a następnie przejdź do **witryny Zarządzanie > urządzeniami**. W bloku **Urządzenia** wybierz właśnie utworzone urządzenie. To urządzenie będzie wyświetlane jako **Gotowe do skonfigurowania**.
   
    ![Konfigurowanie urządzenia jako serwera iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Kliknij urządzenie, a zobaczysz komunikat banera informujący, że urządzenie jest gotowe do konfiguracji.
   
    ![Konfigurowanie urządzenia jako serwera iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Kliknij **pozycję Konfiguruj** na pasku poleceń urządzenia. Spowoduje to otwarcie **bloku Konfiguruj.** W bloku **Konfiguruj** wykonaj następujące czynności:
   
   * Nazwa serwera iSCSI jest wypełniana automatycznie.
   * Upewnij się, że szyfrowanie magazynu w chmurze jest ustawione na **Włączone**. Gwarantuje to, że dane wysyłane z urządzenia do chmury są szyfrowane.
   * Określ 32-znakowy klucz szyfrowania i zapisz go w aplikacji do zarządzania kluczami do wykorzystania w przyszłości.
   * Wybierz konto pamięci masowej, które ma być używane z urządzeniem. W tej subskrypcji można wybrać istniejące konto magazynu lub kliknąć przycisk **Dodaj,** aby wybrać konto z innej subskrypcji.
     
     ![Konfigurowanie urządzenia jako serwera iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Kliknij **przycisk Konfiguruj,** aby zakończyć konfigurowanie serwera iSCSI.
   
    ![Konfigurowanie urządzenia jako serwera iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. Zostaniesz powiadomiony, że tworzenie serwera iSCSI jest w toku. Po pomyślnym utworzeniu serwera iSCSI, **serwer urządzenia** jest aktualizowany, a odpowiedni stan urządzenia jest **w trybie online**.
   
    ![Konfigurowanie urządzenia jako serwera iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>Krok 3: Dodawanie woluminu

1. W bloku **Urządzenia** wybierz urządzenie, które właśnie skonfigurowano jako serwer iSCSI. Kliknij **...** (alternatywnie kliknij prawym przyciskiem myszy w tym wierszu) i z menu kontekstowego wybierz polecenie **Dodaj objętość**. Można również kliknąć **przycisk + Dodaj wolumin** z paska poleceń. Spowoduje to otwarcie bloku **Dodaj wolumin.**
   
    ![Dodawanie woluminu](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. W bloku **Dodaj głośność** wykonaj następujące czynności:
   
   * W polu **Nazwa woluminu** wprowadź unikatową nazwę woluminu. Nazwa musi być ciągiem zawierającym od 3 do 127 znaków.
   * Na liście rozwijanej **Typ** określ, czy wolumin **warstwowy** lub **lokalnie przypięty.** W przypadku obciążeń wymagających gwarancji lokalnych, małych opóźnień i wyższej wydajności wybierz **opcję Lokalnie przypięty** **wolumin**. W przypadku wszystkich innych danych wybierz opcję **Wolumin** **warstwowy** .
   * W polu **Pojemność** określ rozmiar woluminu. Wolumin warstwowy musi mieć wartość od 500 GB do 5 TB, a wolumin przypięty lokalnie musi mieć od 50 GB do 500 GB.
     
     Wolumin przypięty lokalnie jest aprowicznie aprowiowany i zapewnia, że podstawowe dane na woluminie pozostają na urządzeniu i nie wyciekną do chmury.
     
     Wolumin warstwowy z drugiej strony jest cienko aprowizacji. Podczas tworzenia woluminu warstwowego około 10% miejsca jest aprowizowana w warstwie lokalnej i 90% miejsca jest aprowizowana w chmurze. Na przykład jeśli aprowizowana wolumin 1 TB, 100 GB będzie znajdować się w przestrzeni lokalnej i 900 GB będą używane w chmurze, gdy warstwy danych. To z kolei oznacza, że jeśli zabraknie całej przestrzeni lokalnej na urządzeniu, nie można aprowizować udział warstwowy (ponieważ 10% nie będzie dostępne).
     
     ![Dodawanie woluminu](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * Kliknij **pozycję Połączone hosty**, wybierz rekord kontroli dostępu (ACR) odpowiadający inicjatorowi iSCSI, który chcesz połączyć z tym woluminem, a następnie kliknij przycisk **Wybierz**. <br><br> 
3. Aby dodać nowego połączonego hosta, kliknij pozycję **Dodaj nowy**, wprowadź nazwę hosta i jego nazwę kwalifikowaną iSCSI (IQN), a następnie kliknij przycisk **Dodaj**. Jeśli nie masz IQN, przejdź do [dodatku A: Pobierz IQN hosta systemu Windows Server](#appendix-a-get-the-iqn-of-a-windows-server-host).
   
      ![Dodawanie woluminu](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. Po zakończeniu konfigurowania woluminu kliknij przycisk **OK**. Wolumin zostanie utworzony z określonymi ustawieniami i zostanie wyświetlone powiadomienie. Domyślnie monitorowanie i tworzenie kopii zapasowych będzie włączone dla woluminu.
   
     ![Dodawanie woluminu](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. Aby potwierdzić, że wolumin został pomyślnie utworzony, przejdź do **bloku Woluminy.** Wolumin powinien być widoczny na liście.
   
   ![Dodawanie woluminu](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Krok 4: Montowanie, inicjowanie i formatowanie woluminu

Wykonaj następujące czynności, aby zainstalować, zainicjować i sformatować woluminy StorSimple na hoście systemu Windows Server.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Aby zainstalować, zainicjować i sformatować wolumin

1. Otwórz aplikację **inicjatora iSCSI** na odpowiednim serwerze.
2. W oknie **Właściwości inicjatora iSCSI** na karcie **Odnajdowanie** kliknij pozycję **Odnajdź portal**.
   
    ![odkryj portal](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. W oknie dialogowym **Odnajdowanie portalu obiektu docelowego** podaj adres IP interfejsu sieci iSCSI, a następnie kliknij przycisk **OK**.
   
    ![Adres IP](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. W oknie **Właściwości inicjatora iSCSI** na karcie **Obiekty docelowe** zlokalizuj wartości **Wykryte obiekty docelowe**. (Każdy wolumin będzie odnalezionym obiektem docelowym). Stan urządzenia powinien być wyświetlany jako **nieaktywny**.
   
    ![odkryte cele](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Wybierz urządzenie docelowe, a następnie kliknij przycisk **Połącz**. Po połączeniu urządzenia stan powinien zmienić się na **Połączone**. (Aby uzyskać więcej informacji na temat korzystania z inicjatora iSCSI firmy Microsoft, zobacz [Instalowanie i konfigurowanie inicjatora iSCSI firmy Microsoft][1].
   
    ![wybierz urządzenie docelowe](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. Na hoście systemu Windows naciśnij klawisz z logo systemu Windows + X, a następnie kliknij pozycję **Uruchom**.
7. W oknie dialogowym **Uruchom** wpisz **Diskmgmt.msc**. Kliknij przycisk **OK**. Zostanie wyświetlone okno **Zarządzanie dyskami**. W prawym okienku zostaną wyświetlone woluminy na hoście.
8. W oknie **Zarządzanie dyskami** zostaną wyświetlone zainstalowane woluminy (tak jak na poniższym rysunku). Kliknij prawym przyciskiem myszy odnaleziony wolumin (kliknij nazwę dysku), a następnie kliknij pozycję **Online**.
   
    ![zarządzanie dyskami](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Kliknij prawym przyciskiem myszy i wybierz pozycję **Inicjuj dysk**.
   
    ![inicjowanie dysku 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. W oknie dialogowym zaznacz dyski do zainicjowania, a następnie kliknij przycisk **OK**.
    
    ![inicjowanie dysku 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. Zostanie uruchomiony Kreator nowego woluminu prostego. Wybierz rozmiar dysku, a następnie kliknij przycisk **Dalej**.
    
    ![kreator nowego woluminu 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. Przypisz literę dysku do woluminu, a następnie kliknij przycisk **Dalej**.
    
    ![kreator nowego woluminu 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Wprowadź parametry, aby sformatować wolumin. **W systemie Windows Server obsługiwany jest tylko system NTFS.** Ustaw rozmiar jednostki alokacji na 64K. Podaj etykietę dla swojej objętości. Zalecana jest najlepsza zmiana dla tej nazwy, aby być identyczne z nazwą woluminu podanego w storsimple virtual array. Kliknij przycisk **alej**.
    
    ![kreator nowego woluminu 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Sprawdź wartości woluminu, a następnie kliknij przycisk **Zakończ**.
    
    ![kreator nowego woluminu 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    Woluminy będą wyświetlane w **trybie online** na stronie **Zarządzanie dyskami.**
    
    ![woluminów online](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zarządzać [tablicą wirtualną StorSimple](storsimple-ova-web-ui-admin.md)za pomocą lokalnego interfejsu użytkownika sieci Web.

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Dodatek A: Pobierz IQN hosta systemu Windows Server

Wykonaj poniższe kroki, aby pobrać kwalifikowaną nazwę iSCSI (IQN) hosta z systemem Windows, na którym uruchomiono system Windows Server 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Aby pobrać nazwę IQN hosta z systemem Windows

1. Uruchom inicjator iSCSI firmy Microsoft na hoście z systemem Windows.
2. W oknie **Właściwości inicjatora iSCSI** na karcie **Konfiguracja** zaznacz i skopiuj ciąg z pola **Nazwa inicjatora**.
   
    ![Właściwości inicjatora iSCSI](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Zapisz ten ciąg.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



