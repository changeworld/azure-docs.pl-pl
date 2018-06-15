---
title: Wdrażanie programu StorSimple Snapshot Manager | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pobrać i zainstalować StorSimple Snapshot Manager, przystawkę programu MMC do zarządzania funkcji ochrony i kopii zapasowej danych StorSimple.
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
ms.openlocfilehash: cde355381b0d726a1ab340bc4230b2dc8f6e2c56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876320"
---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>Wdrażanie w przystawce MMC StorSimple Snapshot Manager

## <a name="overview"></a>Omówienie
StorSimple Snapshot Manager jest przystawką Microsoft Management Console (MMC), która ułatwia ochronę danych i zarządzania kopiami zapasowymi w środowisku Microsoft Azure StorSimple. StorSimple Snapshot Manager można zarządzać lokalnym Microsoft Azure StorSimple i magazynu w chmurze tak, jakby była systemu pełni zintegrowane magazynu, w związku z tym uproszczenie i przywracania kopii zapasowej i zmniejszenie kosztów. 

W tym samouczku opisano wymagania dotyczące konfiguracji, a także procedury instalowanie, usuwanie i uaktualnianie programu StorSimple Snapshot Manager.

> [!NOTE]
> * Nie można użyć programu StorSimple Snapshot Manager do zarządzania Microsoft Azure StorSimple wirtualnego tablic (znanej także jako StorSimple lokalnego urządzenia wirtualnego).
> * Jeśli planujesz zainstalować StorSimple Update 2 na urządzeniu StorSimple, pamiętaj pobrać najnowszą wersję programu StorSimple Snapshot Manager i zainstalować ją **przed zainstalowaniem StorSimple Update 2**. Najnowszą wersję programu StorSimple Snapshot Manager jest zgodne z poprzednimi wersjami i działa w przypadku wszystkich wersji wydanej programu Microsoft Azure StorSimple. Jeśli używasz poprzedniej wersji programu StorSimple Snapshot Manager, należy zaktualizować je (nie należy odinstalować poprzednią wersję, przed zainstalowaniem nowej wersji).


## <a name="storsimple-snapshot-manager-installation"></a>Instalacja programu StorSimple Snapshot Manager
StorSimple Snapshot Manager można zainstalować na komputerach z systemem Windows Server 2008 R2 z dodatkiem SP1, Windows Server 2012 lub system operacyjny Windows Server 2012 R2. Na serwerach z systemem Windows 2008 R2 należy również zainstalować system Windows Server 2008 z dodatkiem SP1 i Windows Management Framework 3.0.

Przed zainstalowaniem lub uaktualnieniem przystawki StorSimple Snapshot Manager dla programu Microsoft Management Console (MMC), upewnij się, że serwer Microsoft Azure StorSimple urządzeń i hostów są skonfigurowane prawidłowo.

## <a name="configure-prerequisites"></a>Konfigurowanie wymagań wstępnych
W poniższych krokach przedstawiono ogólne omówienie zadań konfiguracji, które należy wykonać przed zainstalowaniem programu StorSimple Snapshot Manager. Aby całe środowisko Microsoft Azure StorSimple i informacje o instalacji, w tym wymagania systemowe i instrukcje krok po kroku, zobacz [wdrażanie lokalnego urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

> [!IMPORTANT]
> Przed rozpoczęciem należy przejrzeć [Lista kontrolna dotycząca konfiguracji wdrożenia](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist) i i [wymagania wstępne dotyczące wdrażania](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites) w [wdrażanie lokalnego urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>Przed zainstalowaniem programu StorSimple Snapshot Manager
1. Rozpakowywanie, zainstalować i Podłącz urządzenia Microsoft Azure StorSimple, zgodnie z opisem w [zainstalować do urządzenia StorSimple 8100](storsimple-8100-hardware-installation.md) lub [zainstalować do urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md).
2. Upewnij się, że komputer-host działa jeden z następujących systemów operacyjnych:
   
   * Windows Server 2008 R2 (na serwery z systemem Windows 2008 R2, należy również zainstalować system Windows Server 2008 z dodatkiem SP1 i Windows Management Framework 3.0)
   * Windows Server 2012
   * Windows Server 2012 R2
     
     Dla urządzenia wirtualnego StorSimple host musi być maszyny wirtualnej Microsoft Azure.
3. Upewnij się, czy zostały spełnione wszystkie wymagania konfiguracji Microsoft Azure StorSimple. Aby uzyskać więcej informacji, przejdź do [wymagania wstępne dotyczące wdrażania](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites).
4. Podłącz urządzenie do hosta, a następnie wykonaj konfigurację początkową. Aby uzyskać więcej informacji, przejdź do [kroki wdrażania](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps).
5. Utworzyć woluminy na urządzeniu, a następnie przypisać je do hosta i sprawdź, czy host może zainstalować i używać ich. StorSimple Snapshot Manager obsługuje następujące typy woluminów:
   
   * Woluminy podstawowe
   * Woluminów prostych
   * Woluminy dynamiczne
   * Dynamiczne woluminy dublowane (RAID 1)
   * Udostępnione woluminy klastra
     
     Aby uzyskać informacje dotyczące tworzenia woluminów na urządzeniu StorSimple lub urządzenia wirtualnego StorSimple, przejdź do [krok 6: Tworzenie woluminu](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume)w [wdrażanie lokalnego urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Zainstaluj nowy StorSimple Snapshot Manager
Przed zainstalowaniem programu StorSimple Snapshot Manager, upewnij się, że woluminy utworzone dla urządzenia StorSimple lub urządzenia wirtualnego StorSimple są zainstalowane, zainicjować i sformatowany zgodnie z opisem w [konfigurowanie wymagań wstępnych](#configure-prerequisites).

> [!IMPORTANT]
> * Dla urządzenia wirtualnego StorSimple host musi być maszyny wirtualnej Microsoft Azure. 
> * Na hoście musi być system Windows 2008 R2, Windows Server 2012 lub Windows Server 2012 R2. Jeśli na serwerze jest uruchomiona w systemie Windows Server 2008 R2, należy również zainstalować system Windows Server 2008 z dodatkiem SP1 i Windows Management Framework 3.0.
> * Przed po podłączeniu urządzenia do programu StorSimple Snapshot Manager, należy skonfigurować połączenia iSCSI z hosta na urządzeniu StorSimple.

Wykonaj następujące kroki, aby ukończyć nową instalację programu StorSimple Snapshot Manager. Jeśli instalujesz uaktualnienia, przejdź do [uaktualnienia lub ponownego zainstalowania programu StorSimple Snapshot Manager](#upgrade-or-reinstall-storsimple-snapshot-manager).

* Krok 1: Instalowanie programu StorSimple Snapshot Manager 
* Krok 2: Łączenie StorSimple Snapshot Manager na urządzenie 
* Krok 3: Sprawdź połączenie z urządzeniem 

### <a name="step-1-install-storsimple-snapshot-manager"></a>Krok 1: Instalowanie programu StorSimple Snapshot Manager
Wykonaj następujące kroki, aby zainstalować StorSimple Snapshot Manager.

#### <a name="to-install-storsimple-snapshot-manager"></a>Aby zainstalować StorSimple Snapshot Manager
1. Pobierz oprogramowanie StorSimple Snapshot Manager (Przejdź do [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) w programie Microsoft Download Center) i zapisz go lokalnie na hoście.
2. W Eksploratorze plików kliknij prawym przyciskiem myszy folder skompresowany, a następnie kliknij przycisk **Wyodrębnij wszystkie**.
3. W **foldery Wyodrębnij skompresowane (Zipped)** okna w **wybierz lokalizację docelową i wyodrębnianie plików** wpisz lub przejdź do ścieżki, w którym ma zostać plików do wyodrębnienia.
   
    > [!IMPORTANT]
    > StorSimple Snapshot Manager należy zainstalować na dysku C:.
    
4. Wybierz **Pokaż wyodrębnione pliki po zakończeniu** pole wyboru, a następnie kliknij przycisk **wyodrębnić**.
   
    ![Wyodrębnij plikach — okno dialogowe](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. Po zakończeniu operacji wyodrębniania, zostanie otwarty folder docelowy. Kliknij dwukrotnie ikonę instalacji aplikacji, która pojawia się w folderze docelowym.
6. Gdy **Instalator pomyślnie** pojawi się komunikat, kliknij przycisk **Zamknij**. Ikona programu StorSimple Snapshot Manager powinna zostać wyświetlona na pulpicie.
   
    ![ikony pulpitu](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Krok 2: Łączenie StorSimple Snapshot Manager na urządzenie
Następujące kroki umożliwiają nawiązać StorSimple Snapshot Manager urządzenia StorSimple.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Do nawiązania połączenia urządzenia StorSimple Snapshot Manager
1. Kliknij ikonę StorSimple Snapshot Manager na pulpicie. Zostanie wyświetlone okno programu StorSimple Snapshot Manager. Okno zawiera **zakres** okienku **wyniki** okienku i **akcje** okienka. 
   
    ![Interfejs użytkownika programu StorSimple Snapshot Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * **Zakres** okienka (lewe okienko) zawiera listę węzłów strukturę drzewa. Można rozwinąć niektóre węzły, aby wybrać widok lub określone dane dotyczące tego węzła. Kliknij ikonę strzałki, aby rozwinąć lub zwinąć węzeł. Kliknij prawym przyciskiem myszy element **zakres** okienko, aby zobaczyć listę dostępnych akcji dla tego elementu.
   * **Wyniki** okienko (w środkowym okienku) zawiera szczegółowe informacje o węźle, widoku lub danych, które wybrano w stanie **zakres** okienka.
   * **Akcje** okienko zawiera listę działań, które mogą wykonywać na węźle, widoku lub danych, które wybrano w **zakres** okienka.
     
     Aby uzyskać pełny opis interfejsu użytkownika programu StorSimple Snapshot Manager, zobacz [interfejsu użytkownika programu StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).
2. W **zakres** okienku kliknij prawym przyciskiem myszy **urządzeń** węzeł, a następnie kliknij przycisk **Skonfiguruj urządzenie**. **Skonfiguruj urządzenie** zostanie wyświetlone okno dialogowe.
   
    ![Konfigurowanie urządzenia](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. W **urządzenia** pola listy, wybierz adres IP urządzenia Microsoft Azure StorSimple lub urządzenia wirtualnego. W **hasło** tekstu wpisz hasło programu StorSimple Snapshot Manager utworzone dla urządzenia w portalu Azure. Kliknij przycisk **OK**.
4. Wyszukuje StorSimple Snapshot Manager dla urządzeń, który został zidentyfikowany. Jeśli urządzenie jest dostępne, StorSimple Snapshot Manager dodaje połączenie. Możesz [sprawdzić połączenie z urządzeniem](#to-verify-the-connection) aby upewnić się, że pomyślnie dodano połączenie.
   
    Jeśli urządzenie jest niedostępne z jakiegokolwiek powodu, StorSimple Snapshot Manager zwraca komunikat o błędzie. Kliknij przycisk **OK** zamknąć komunikat o błędzie, a następnie kliknij przycisk **anulować** zamknąć **Skonfiguruj urządzenie** okno dialogowe.
5. Gdy łączy się urządzenie, StorSimple Snapshot Manager importuje każdej grupy woluminu skonfigurowane dla tego urządzenia, pod warunkiem, że grupa woluminu skojarzył kopii zapasowych. Wolumin grup, które nie mają skojarzonych kopii zapasowych nie zostaną zaimportowane. Ponadto zasady tworzenia kopii zapasowej, które zostały utworzone dla grupy woluminu nie zostały zaimportowane. Aby wyświetlić zaimportowane grupy, kliknij prawym przyciskiem myszy najwyższy **grup woluminu** w węźle **zakres** okienka, a następnie kliknij przycisk **Przełącz zaimportowane grupy**.

### <a name="step-3-verify-the-connection-to-the-device"></a>Krok 3: Sprawdź połączenie z urządzeniem
Wykonaj następujące kroki, aby sprawdzić, czy StorSimple Snapshot Manager jest podłączony do urządzenia StorSimple.

#### <a name="to-verify-the-connection"></a>Aby sprawdzić połączenie
1. W **zakres** okienku, kliknij przycisk **urządzeń** węzła.
   
    ![Stan urządzenia StorSimple Snapshot Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. Sprawdź **wyniki** okienka: 
   
   * Widoczna zielony wskaźnik na ikonie urządzenia i **dostępne** pojawia się w **stan** kolumny, a następnie urządzenie jest połączone. 
   * Jeśli czerwony wskaźnik jest wyświetlany na ikonie urządzenia, a jest niedostępny w **stan** kolumny, a następnie urządzenie nie jest połączona. 
   * Jeśli **odświeżanie** pojawia się w **stan** kolumny, a następnie StorSimple Snapshot Manager pobiera grup woluminu i skojarzonych kopii zapasowych dla podłączonego urządzenia.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Uaktualnij lub ponowne zainstalowanie programu StorSimple Snapshot Manager
Należy całkowicie odinstalować StorSimple Snapshot Manager przed uaktualnienia lub ponownej instalacji oprogramowania. 

Przed ponownym zainstalowaniem programu StorSimple Snapshot Manager, należy wykonać kopię zapasową istniejącej bazy danych programu StorSimple Snapshot Manager na komputerze hosta. To zapisuje informacje o kopii zapasowej zasad i konfiguracji, dzięki czemu można łatwo przywrócić dane z kopii zapasowej.

Jeśli uaktualniasz lub ponowne zainstalowanie programu StorSimple Snapshot Manager, wykonaj następujące kroki:

* Krok 1: Odinstaluj StorSimple Snapshot Manager 
* Krok 2: Wykonaj kopię zapasową bazy danych programu StorSimple Snapshot Manager 
* Krok 3: Zainstaluj ponownie StorSimple Snapshot Manager i przywracanie bazy danych 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Krok 1: Odinstaluj StorSimple Snapshot Manager
Wykonaj następujące kroki, aby odinstalować StorSimple Snapshot Manager.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>Aby odinstalować StorSimple Snapshot Manager
1. Otwórz na komputerze hosta **Panelu sterowania**, kliknij przycisk **programy**, a następnie kliknij przycisk **programy i funkcje**.
2. W okienku po lewej stronie kliknij **Odinstaluj lub zmień program**.
3. Kliknij prawym przyciskiem myszy **StorSimple Snapshot Manager**, a następnie kliknij przycisk **Odinstaluj**.
4. Spowoduje to uruchomienie programu instalacyjnego programu StorSimple Snapshot Manager. Kliknij przycisk **zmodyfikować ustawienia**, a następnie kliknij przycisk **Odinstaluj**.
   
   > [!NOTE]
   > W przypadku programu MMC procesów działających w tle, takie jak StorSimple Snapshot Manager lub przystawki Zarządzanie dyskami, dezinstalacja zakończy się niepowodzeniem i zostanie wyświetlony komunikat, aby zamknąć wszystkich wystąpień programu MMC, przed podjęciem próby odinstalowania programu. Wybierz **automatycznie Zamknij aplikacje i spróbuj uruchomić je ponownie po zakończeniu instalacji**, a następnie kliknij przycisk **OK**.
   > 
   > 
5. Po zakończeniu dezinstalacji **Instalator pomyślnie** zostanie wyświetlony komunikat. Kliknij przycisk **Zamknij**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Krok 2: Wykonaj kopię zapasową bazy danych programu StorSimple Snapshot Manager
Wykonaj następujące kroki, aby utworzyć i zapisać kopię bazy danych programu StorSimple Snapshot Manager.

#### <a name="to-back-up-the-database"></a>Aby utworzyć kopię zapasową bazy danych
1. Zatrzymaj usługę zarządzania Microsoft StorSimple:
   
   1. Uruchom Menedżera serwera.
   2. Na pulpicie nawigacyjnym Menedżera serwera na **narzędzia** menu, wybierz opcję **usług**.
   3. Na **usług** wybierz pozycję **usługi zarządzania StorSimple Microsoft**.
   4. W prawym okienku w obszarze **usługi zarządzania StorSimple Microsoft**, kliknij przycisk **Zatrzymaj usługę**.
      
        ![Zatrzymaj usługę Menedżera urządzenia StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. Przejdź do C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > Folder ProgramData jest folderem ukrytym.
  
3. Znajdź plik XML katalogu, skopiuj plik i przechowywać kopię w bezpiecznym miejscu lub w chmurze.
   
    ![Plik kopii zapasowej katalogu StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. Uruchom ponownie usługę zarządzania Microsoft StorSimple: 
   
   1. Na pulpicie nawigacyjnym Menedżera serwera na **narzędzia** menu, wybierz opcję **usług**.
   2. Na **usług** wybierz pozycję **Microsoft StorSimple zarządzania Servic**e.
   3. W prawym okienku w obszarze **usługi zarządzania StorSimple Microsoft**, kliknij przycisk **Uruchom ponownie usługę**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Krok 3: Zainstaluj ponownie StorSimple Snapshot Manager i przywracanie bazy danych
Aby ponownie zainstalować StorSimple Snapshot Manager, postępuj zgodnie z instrukcjami [zainstalować nowy StorSimple Snapshot Manager](#install-a-new-storsimple-snapshot-manager). Przywracanie bazy danych programu StorSimple Snapshot Manager następnie, użyj następującej procedury.

#### <a name="to-restore-the-database"></a>Aby przywrócić bazę danych
1. Zatrzymaj usługę zarządzania Microsoft StorSimple:
   
   1. Uruchom Menedżera serwera.
   2. Na pulpicie nawigacyjnym Menedżera serwera na **narzędzia** menu, wybierz opcję **usług**.
   3. Na **usług** wybierz pozycję **usługi zarządzania StorSimple Microsoft**.
   4. W prawym okienku w obszarze **usługi zarządzania StorSimple Microsoft**, kliknij przycisk **Zatrzymaj usługę**.
2. Przejdź do C:\ProgramData\Microsoft\StorSimple\BACatalog.
   
   > [!NOTE]
   > Folder ProgramData jest folderem ukrytym.
   > 
   > 
3. Usuń plik XML katalogu i zastąp go wersji, który został wcześniej zapisany.
4. Uruchom ponownie usługę zarządzania Microsoft StorSimple: 
   
   1. Na pulpicie nawigacyjnym Menedżera serwera na **narzędzia** menu, wybierz opcję **usług**.
   2. Na **usług** wybierz pozycję **usługi zarządzania StorSimple Microsoft**.
   3. W prawym okienku w obszarze **usługi zarządzania StorSimple Microsoft**, kliknij przycisk **Uruchom ponownie usługę**.

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej o StorSimple Snapshot Manager, przejdź do [co to jest StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md).
* Aby dowiedzieć się więcej o interfejsie użytkownika programu StorSimple Snapshot Manager, przejdź do [interfejsu użytkownika programu StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).
* Aby dowiedzieć się więcej o korzystaniu z programu StorSimple Snapshot Manager, przejdź do [Użyj StorSimple Snapshot Manager do administrowania rozwiązania StorSimple](storsimple-snapshot-manager-admin.md).

