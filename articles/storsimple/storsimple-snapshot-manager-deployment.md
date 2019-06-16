---
title: Deploy StorSimple Snapshot Manager | Microsoft Docs
description: Dowiedz się, jak pobrać i zainstalować StorSimple Snapshot Manager, przystawkę programu MMC do zarządzania funkcjami ochrony i kopii zapasowej danych StorSimple.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: f0128f57-519e-49ec-9187-23575809cdbe
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: ee17e4b69d1e6c9de465e4241ee2237361e320b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61077745"
---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>Wdrażanie w przystawce programu MMC przystawki StorSimple Snapshot Manager

## <a name="overview"></a>Omówienie
StorSimple Snapshot Manager jest przystawką Microsoft Management Console (MMC), która ułatwia ochronę danych i zarządzania kopiami zapasowymi w środowisku Microsoft Azure StorSimple. Za pomocą Menedżera migawek StorSimple można zarządzać Microsoft Azure StorSimple w środowisku lokalnym i Magazyn w chmurze tak, jakby był to system magazynu w pełni zintegrowane, tym samym upraszczanie procesów i przywracania kopii zapasowych i obniżając koszty. 

W tym samouczku opisano wymagania dotyczące konfiguracji, a także procedury dotyczące instalowania, usuwanie i uaktualnianie programu StorSimple Snapshot Manager.

> [!NOTE]
> * Nie można użyć przystawki StorSimple Snapshot Manager do zarządzania Microsoft Azure macierze wirtualne StorSimple (znany także jako StorSimple w środowisku lokalnym urządzeń wirtualnych).
> * Jeśli planujesz zainstalować storsimple z aktualizacją Update 2 na urządzeniu StorSimple, pamiętaj pobrać najnowszą wersję programu StorSimple Snapshot Manager i zainstaluj go **przed zainstalowaniem storsimple z aktualizacją Update 2**. Najnowszą wersję programu StorSimple Snapshot Manager jest zgodny z poprzednimi wersjami i działa ze wszystkimi wersjami wydanej programu Microsoft Azure StorSimple. Jeśli używasz poprzedniej wersji programu StorSimple Snapshot Manager musisz zaktualizować go (nie musisz odinstalować poprzednią wersję przed zainstalowaniem nowej wersji).


## <a name="storsimple-snapshot-manager-installation"></a>Instalacja programu StorSimple Snapshot Manager
Przystawki StorSimple Snapshot Manager można zainstalować na komputerach z systemem Windows Server 2008 R2 z dodatkiem SP1, Windows Server 2012 lub system operacyjny Windows Server 2012 R2. Na serwerach z systemem Windows 2008 R2 należy także zainstalować system Windows Server 2008 z dodatkiem SP1 i Windows Management Framework 3.0.

Przed zainstalowaniem lub uaktualnieniem przystawki StorSimple Snapshot Manager dla programu Microsoft Management Console (MMC), upewnij się, że serwer Microsoft Azure StorSimple urządzeń i hostów są prawidłowo skonfigurowane.

## <a name="configure-prerequisites"></a>Konfigurowanie wymagań wstępnych
W poniższych krokach przedstawiono ogólny przegląd zadań konfiguracji, które należy wykonać przed zainstalowaniem programu StorSimple Snapshot Manager. Kompletna konfiguracja Microsoft Azure StorSimple i informacje o instalacji, w tym wymagania systemowe i instrukcje krok po kroku znajdują się [wdrażanie urządzenia StorSimple w środowisku lokalnym](storsimple-8000-deployment-walkthrough-u2.md).

> [!IMPORTANT]
> Przed rozpoczęciem należy przejrzeć [Lista kontrolna wdrażania konfiguracji](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist) i i [wymagania wstępne dotyczące wdrażania](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites) w [wdrażanie urządzenia StorSimple w środowisku lokalnym](storsimple-8000-deployment-walkthrough-u2.md).
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>Przed zainstalowaniem programu StorSimple Snapshot Manager
1. Rozpakowywanie, instalowanie i połączyć to urządzenie Microsoft Azure StorSimple zgodnie z opisem w [instalowania urządzenia StorSimple 8100](storsimple-8100-hardware-installation.md) lub [instalowania urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md).
2. Upewnij się, że komputer-host działa jeden z następujących systemów operacyjnych:
   
   * Windows Server 2008 R2 (na serwerach z systemem Windows 2008 R2, należy także zainstalować system Windows Server 2008 z dodatkiem SP1 i Windows Management Framework 3.0)
   * Windows Server 2012
   * Windows Server 2012 R2
     
     Dla urządzenia wirtualnego StorSimple host musi być maszyny wirtualne Microsoft Azure.
3. Upewnij się, że są spełnione wszystkie wymagania konfiguracji programu Microsoft Azure StorSimple. Aby uzyskać więcej informacji, przejdź do [wymagania wstępne dotyczące wdrażania](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites).
4. Podłącz urządzenie do hosta, a następnie wykonaj konfigurację początkową. Aby uzyskać więcej informacji, przejdź do [kroki związane z wdrażaniem](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps).
5. Tworzyć woluminów na urządzeniu, a następnie przypisać je do hosta i sprawdź, czy host można zainstalować i używać ich. Przystawki StorSimple Snapshot Manager obsługuje następujące typy woluminów:
   
   * Woluminy podstawowe
   * Woluminy proste
   * Woluminy dynamiczne
   * Dynamiczne woluminy dublowane (RAID 1)
   * Udostępnione woluminy klastra
     
     Aby uzyskać informacje dotyczące tworzenia woluminów na urządzeniu StorSimple lub urządzenia wirtualnego StorSimple, przejdź do [krok 6: Tworzenie woluminu](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume)w [wdrażanie urządzenia StorSimple w środowisku lokalnym](storsimple-8000-deployment-walkthrough-u2.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Instalowanie nowego programu StorSimple Snapshot Manager
Przed zainstalowaniem programu StorSimple Snapshot Manager, upewnij się, że woluminy, który został utworzony na urządzeniu StorSimple lub urządzenia wirtualnego StorSimple są zainstalowane, zainicjować i formatowana zgodnie z opisem w [konfigurowanie wymagań wstępnych](#configure-prerequisites).

> [!IMPORTANT]
> * Dla urządzenia wirtualnego StorSimple host musi być maszyny wirtualne Microsoft Azure. 
> * Host musi działać Windows 2008 R2, Windows Server 2012 lub Windows Server 2012 R2. Jeśli serwer jest uruchomiony system Windows Server 2008 R2, należy również zainstalować system Windows Server 2008 z dodatkiem SP1 i Windows Management Framework 3.0.
> * Należy skonfigurować połączenia iSCSI z hosta do urządzenia StorSimple, zanim będzie można połączyć urządzenia do programu StorSimple Snapshot Manager.

Wykonaj następujące kroki, aby ukończyć nowej instalacji programu StorSimple Snapshot Manager. Jeśli instalujesz uaktualnienia, przejdź do strony [uaktualnienia lub ponownego zainstalowania programu StorSimple Snapshot Manager](#upgrade-or-reinstall-storsimple-snapshot-manager).

* Krok 1: Instalowanie programu StorSimple Snapshot Manager 
* Krok 2: Połącz programu StorSimple Snapshot Manager do urządzenia 
* Krok 3: Weryfikowanie połączenia z urządzeniem 

### <a name="step-1-install-storsimple-snapshot-manager"></a>Krok 1: Instalowanie programu StorSimple Snapshot Manager
Wykonaj następujące kroki, aby zainstalować przystawki StorSimple Snapshot Manager.

#### <a name="to-install-storsimple-snapshot-manager"></a>Do zainstalowania programu StorSimple Snapshot Manager
1. Pobierz oprogramowanie StorSimple Snapshot Manager (Przejdź do [programu StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) w programie Microsoft Download Center) i zapisz go lokalnie na hoście.
2. W Eksploratorze plików kliknij prawym przyciskiem myszy folder skompresowany, a następnie kliknij przycisk **wyodrębnić wszystkie**.
3. W **foldery Wyodrębnij skompresowane (Zipped)** okna w **wybierz lokalizację docelową i wyodrębnić pliki** wpisz lub przejdź do ścieżki, w którym chcesz pliku do wyodrębnienia.
   
    > [!IMPORTANT]
    > Na dysku C:, należy zainstalować przystawki StorSimple Snapshot Manager.
    
4. Wybierz **Pokaż wyodrębnione pliki po ukończeniu** pole wyboru, a następnie kliknij przycisk **wyodrębnić**.
   
    ![Wyodrębnij pliki, okno dialogowe](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. Po zakończeniu wyodrębniania otwiera folder docelowy. Kliknij dwukrotnie ikonę ustawienia aplikacji, który pojawia się w folderze docelowym.
6. Gdy **Instalacja zakończona pomyślnie** pojawi się komunikat, kliknij przycisk **Zamknij**. Ikona programu StorSimple Snapshot Manager powinien być widoczny na pulpicie.
   
    ![ikony pulpitu](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Krok 2: Połącz programu StorSimple Snapshot Manager do urządzenia
Wykonaj następujące kroki, aby Połącz programu StorSimple Snapshot Manager na urządzeniu StorSimple.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Aby nawiązać połączenie urządzenia StorSimple Snapshot Manager
1. Kliknij ikonę programu StorSimple Snapshot Manager na pulpicie. Zostanie wyświetlone okno programu StorSimple Snapshot Manager. Okno zawiera **zakres** okienku **wyniki** okienku i **akcje** okienka. 
   
    ![Interfejs użytkownika programu StorSimple Snapshot Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * **Zakres** okienko (okienko po lewej stronie) zawiera listę węzłów strukturę drzewa. Można rozwinąć niektóre węzły, aby wybrać widok lub określone dane dotyczące tego węzła. Kliknij ikonę strzałki aby rozwinąć lub zwinąć węzeł. Kliknij prawym przyciskiem myszy element w **zakres** okienko, aby wyświetlić listę dostępnych akcji dla tego elementu.
   * **Wyniki** okienko (środkowym) zawiera szczegółowe informacje o węźle, widoku lub danych, które wybrano w stanie **zakres** okienka.
   * **Akcje** okienko zawiera listę operacji, które można wykonywać na węzeł, widoku lub danych, które wybrano w **zakres** okienka.
     
     Aby uzyskać pełny opis interfejsu użytkownika programu StorSimple Snapshot Manager, zobacz [interfejsu użytkownika programu StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).
2. W **zakres** okienku kliknij prawym przyciskiem myszy **urządzeń** węzłem, a następnie kliknij przycisk **skonfigurować urządzenie**. **Skonfigurować urządzenie** pojawi się okno dialogowe.
   
    ![Konfigurowanie urządzenia](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. W **urządzenia** pola listy, wybierz adres IP dla urządzenia Microsoft Azure StorSimple lub urządzenia wirtualnego. W **hasło** tekstu wpisz hasło programu StorSimple Snapshot Manager utworzone dla urządzenia w witrynie Azure portal. Kliknij przycisk **OK**.
4. Wyszukuje programu StorSimple Snapshot Manager dla urządzenia, który został zidentyfikowany. Jeśli urządzenie jest dostępne, przystawki StorSimple Snapshot Manager dodaje połączenie. Możesz [sprawdzanie połączenia z urządzeniem](#to-verify-the-connection) aby upewnić się, że pomyślnie dodano połączenie.
   
    Jeśli urządzenie jest niedostępne z jakiegokolwiek powodu, przystawki StorSimple Snapshot Manager zwraca komunikat o błędzie. Kliknij przycisk **OK** Zamknij komunikat o błędzie, a następnie kliknij przycisk **anulować** zamknąć **skonfigurować urządzenie** okno dialogowe.
5. Podczas łączenia się z urządzeniem, przystawki StorSimple Snapshot Manager importuje każdej grupy woluminu skonfigurowane dla tego urządzenia, pod warunkiem, że grupa wolumin ma skojarzone kopie zapasowe. Grupami woluminów, które nie mają skojarzonych kopii zapasowych nie są importowane. Ponadto zasady tworzenia kopii zapasowych, które zostały utworzone dla grupy woluminu nie są importowane. Aby wyświetlić zaimportowane grupy, kliknij prawym przyciskiem myszy najlepszy **grupami woluminów** w węźle **zakres** okienka, a następnie kliknij przycisk **Przełącz zaimportowane grupy**.

### <a name="step-3-verify-the-connection-to-the-device"></a>Krok 3: Weryfikowanie połączenia z urządzeniem
Wykonaj następujące kroki, aby sprawdzić, czy programu StorSimple Snapshot Manager jest połączony z urządzeniem StorSimple.

#### <a name="to-verify-the-connection"></a>Aby zweryfikować połączenie
1. W **zakres** okienku kliknij **urządzeń** węzła.
   
    ![Stan urządzenia StorSimple Snapshot Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. Sprawdź **wyniki** okienka: 
   
   * Na ikonę urządzenia pojawi się zielony wskaźnik i **dostępne** pojawia się w **stan** kolumny, a następnie urządzenie jest podłączone. 
   * Jeśli wyświetlany jest wskaźnik czerwoną ikonę urządzenia i jest niedostępny, zostanie wyświetlony w **stan** kolumny, a następnie urządzenie nie jest połączony. 
   * Jeśli **odświeżanie** pojawia się w **stan** kolumny, a następnie programu StorSimple Snapshot Manager pobiera woluminu grup i skojarzonych kopii zapasowych dla połączonych urządzeń.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Uaktualnianie lub ponowne zainstalowanie programu StorSimple Snapshot Manager
Należy całkowicie odinstalować programu StorSimple Snapshot Manager zanim uaktualnisz lub ponownie zainstalować oprogramowanie. 

Przed ponownym zainstalowaniem programu StorSimple Snapshot Manager, należy wykonać kopię zapasową istniejącej bazy danych programu StorSimple Snapshot Manager na komputerze-hoście. Spowoduje to zapisanie kopii zapasowej zasady i informacje o konfiguracji, tak, aby można je łatwo przywrócić dane z kopii zapasowej.

Jeśli uaktualniasz lub ponowne zainstalowanie programu StorSimple Snapshot Manager, wykonaj następujące kroki:

* Krok 1: Odinstalowywanie programu StorSimple Snapshot Manager 
* Krok 2: Tworzenie kopii zapasowej bazy danych programu StorSimple Snapshot Manager 
* Krok 3: Ponowne zainstalowanie programu StorSimple Snapshot Manager i przywrócić bazę danych 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Krok 1: Odinstalowywanie programu StorSimple Snapshot Manager
Wykonaj następujące kroki, aby odinstalować programu StorSimple Snapshot Manager.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>Aby odinstalować programu StorSimple Snapshot Manager
1. Na komputerze-hoście, otwórz **Panelu sterowania**, kliknij przycisk **programy**, a następnie kliknij przycisk **programy i funkcje**.
2. W okienku po lewej stronie kliknij **Odinstaluj lub zmień program**.
3. Kliknij prawym przyciskiem myszy **programu StorSimple Snapshot Manager**, a następnie kliknij przycisk **Odinstaluj**.
4. Spowoduje to uruchomienie programu instalacyjnego programu StorSimple Snapshot Manager. Kliknij przycisk **modyfikowanie ustawień**, a następnie kliknij przycisk **Odinstaluj**.
   
   > [!NOTE]
   > W przypadku programu MMC procesów działających w tle, takie jak przystawki StorSimple Snapshot Manager lub przystawki Zarządzanie dyskami, odinstalowywanie nie powiedzie i zostanie wyświetlony komunikat, aby zamknąć wszystkie wystąpienia programu MMC, przed podjęciem próby odinstalowania programu. Wybierz **automatycznie Zamknij aplikacje i spróbuj uruchomić je ponownie po zakończeniu instalacji**, a następnie kliknij przycisk **OK**.
   > 
   > 
5. Po zakończeniu dezinstalacji **Instalacja zakończona pomyślnie** zostanie wyświetlony komunikat. Kliknij przycisk **Zamknij**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Krok 2: Tworzenie kopii zapasowej bazy danych programu StorSimple Snapshot Manager
Wykonaj następujące kroki, aby utworzyć i zapisać kopię bazy danych programu StorSimple Snapshot Manager.

#### <a name="to-back-up-the-database"></a>Aby utworzyć kopię zapasową bazy danych
1. Zatrzymaj usługę zarządzania Microsoft StorSimple:
   
   1. Uruchom Menedżera serwera.
   2. Na pulpicie nawigacyjnym Menedżera serwera na **narzędzia** menu, wybierz opcję **usług**.
   3. Na **usług** wybierz opcję **Microsoft StorSimple Management Service**.
   4. W okienku po prawej stronie w obszarze **Microsoft StorSimple Management Service**, kliknij przycisk **Zatrzymaj usługę**.
      
        ![Zatrzymaj usługę Menedżera urządzeń StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. Przejdź do C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > Folder ProgramData jest folderem ukrytym.
  
3. Znajdź katalog pliku XML, skopiuj plik i przechowywać kopię w bezpiecznym miejscu lub w chmurze.
   
    ![Plik wykaz kopii zapasowych usługi StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. Uruchom ponownie usługę zarządzania Microsoft StorSimple: 
   
   1. Na pulpicie nawigacyjnym Menedżera serwera na **narzędzia** menu, wybierz opcję **usług**.
   2. Na **usług** wybierz opcję **Microsoft StorSimple Management Service**.
   3. W okienku po prawej stronie w obszarze **Microsoft StorSimple Management Service**, kliknij przycisk **Uruchom ponownie usługę**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Krok 3: Ponowne zainstalowanie programu StorSimple Snapshot Manager i przywrócić bazę danych
Aby ponownie zainstalować przystawki StorSimple Snapshot Manager, wykonaj kroki opisane w [instalacji nowego programu StorSimple Snapshot Manager](#install-a-new-storsimple-snapshot-manager). Następnie użyj poniższej procedury, aby przywrócić bazę danych programu StorSimple Snapshot Manager.

#### <a name="to-restore-the-database"></a>Przywracanie bazy danych programu
1. Zatrzymaj usługę zarządzania Microsoft StorSimple:
   
   1. Uruchom Menedżera serwera.
   2. Na pulpicie nawigacyjnym Menedżera serwera na **narzędzia** menu, wybierz opcję **usług**.
   3. Na **usług** wybierz opcję **Microsoft StorSimple Management Service**.
   4. W okienku po prawej stronie w obszarze **Microsoft StorSimple Management Service**, kliknij przycisk **Zatrzymaj usługę**.
2. Przejdź do C:\ProgramData\Microsoft\StorSimple\BACatalog.
   
   > [!NOTE]
   > Folder ProgramData jest folderem ukrytym.
   > 
   > 
3. Usuń katalog pliku XML, a następnie zamień wersji, który został wcześniej zapisany.
4. Uruchom ponownie usługę zarządzania Microsoft StorSimple: 
   
   1. Na pulpicie nawigacyjnym Menedżera serwera na **narzędzia** menu, wybierz opcję **usług**.
   2. Na **usług** wybierz opcję **Microsoft StorSimple Management Service**.
   3. W okienku po prawej stronie w obszarze **Microsoft StorSimple Management Service**, kliknij przycisk **Uruchom ponownie usługę**.

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej na temat programu StorSimple Snapshot Manager, przejdź do [co to jest StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md).
* Aby dowiedzieć się więcej o interfejsie użytkownika programu StorSimple Snapshot Manager, przejdź do [interfejsu użytkownika programu StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).
* Aby dowiedzieć się więcej o korzystaniu z programu StorSimple Snapshot Manager, przejdź do [użyj przystawki StorSimple Snapshot Manager do administrowania rozwiązania StorSimple](storsimple-snapshot-manager-admin.md).

