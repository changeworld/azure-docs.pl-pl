---
title: Wdrażanie Menedżera migawek StorSimple | Dokumenty firmy Microsoft
description: Dowiedz się, jak pobrać i zainstalować Menedżera migawek StorSimple, przystawkę programu MMC do zarządzania funkcjami ochrony danych i tworzenia kopii zapasowych StorSimple.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: f0128f57-519e-49ec-9187-23575809cdbe
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 6d3e3d6cdf7a831bf09d9c4709c1a60d27683438
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933379"
---
# <a name="deploy-the-storsimple-snapshot-manager-mmc-snap-in"></a>Wdrażanie przystawki programu MMC Menedżera migawek StorSimple

## <a name="overview"></a>Omówienie
Menedżer migawek StorSimple to przystawka programu Microsoft Management Console (MMC), która upraszcza ochronę danych i zarządzanie kopiami zapasowymi w środowisku Microsoft Azure StorSimple. Dzięki StorSimple Snapshot Manager możesz zarządzać lokalną i chmurową pamięcią masową Usługi Microsoft Azure StorSimple, tak jakby był to w pełni zintegrowany system pamięci masowej, upraszczając w ten sposób procesy tworzenia kopii zapasowych i przywracania oraz zmniejszając koszty. 

W tym samouczku opisano wymagania dotyczące konfiguracji, a także procedury instalowania, usuwania i uaktualniania Menedżera migawek StorSimple.

> [!NOTE]
> * Nie można używać Menedżera migawek StorSimple do zarządzania macierzami wirtualnymi usługi Microsoft Azure StorSimple (nazywanymi również lokalnymi urządzeniami wirtualnymi StorSimple).
> * Jeśli planujesz zainstalować storsimple update 2 na urządzeniu StorSimple, należy pobrać najnowszą wersję StorSimple Snapshot Manager i zainstalować go **przed zainstalowaniem StorSimple Update 2**. Najnowsza wersja StorSimple Snapshot Manager jest wstecznie kompatybilny i współpracuje ze wszystkimi wydanymi wersjami Microsoft Azure StorSimple. Jeśli używasz poprzedniej wersji StorSimple Snapshot Manager, należy go zaktualizować (nie trzeba odinstalowywać poprzedniej wersji przed zainstalowaniem nowej wersji).


## <a name="storsimple-snapshot-manager-installation"></a>Instalacja Programu StorSimple Snapshot Manager
Menedżer migawek StorSimple można zainstalować na komputerach z systemem operacyjnym Windows Server 2008 R2 z sp1, Windows Server 2012 lub Windows Server 2012 R2. Na serwerach z systemem Windows 2008 R2 należy również zainstalować system Windows Server 2008 z sp1 i windows management framework 3.0.

Przed zainstalowaniem lub uaktualnieniem przystawki Menedżer migawek StorSimple dla programu Microsoft Management Console (MMC) upewnij się, że urządzenie i serwer hosta Usługi Microsoft Azure StorSimple są poprawnie skonfigurowane.

## <a name="configure-prerequisites"></a>Konfigurowanie wymagań wstępnych
Poniższe kroki zawierają omówienie wysokiego poziomu zadań konfiguracyjnych, które należy wykonać przed zainstalowaniem Menedżera migawek StorSimple. Aby uzyskać pełne informacje o konfiguracji i konfiguracji usługi Microsoft Azure StorSimple, w tym wymagania systemowe i instrukcje krok po kroku, zobacz [Wdrażanie lokalnego urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

> [!IMPORTANT]
> Przed rozpoczęciem zapoznaj się z [listą kontrolną konfiguracji wdrożenia](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist) i [wymaganiami wstępnymi wdrażania](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites) w programie Deploy your [premises StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
> <br>
> 
> 

### <a name="before-you-install-storsimple-snapshot-manager"></a>Przed zainstalowaniem Menedżera migawek StorSimple
1. Rozpakuj, zainstaluj i połącz urządzenie Microsoft Azure StorSimple zgodnie z opisem w [zainstaluj urządzenie StorSimple 8100](storsimple-8100-hardware-installation.md) lub [Zainstaluj urządzenie StorSimple 8600](storsimple-8600-hardware-installation.md).
2. Upewnij się, że na komputerze-hoście jest uruchomiony jeden z następujących systemów operacyjnych:
   
   * Windows Server 2008 R2 (na serwerach z systemem Windows 2008 R2 należy również zainstalować systemy Windows Server 2008 z SP1 i Windows Management Framework 3.0)
   * Windows Server 2012
   * Windows Server 2012 R2
     
     W przypadku urządzenia wirtualnego StorSimple host musi być maszyną wirtualną platformy Microsoft Azure.
3. Upewnij się, że spełniasz wszystkie wymagania konfiguracyjne usługi Microsoft Azure StorSimple. Aby uzyskać szczegółowe informacje, przejdź do [wymagań wstępnych wdrożenia](storsimple-8000-deployment-walkthrough-u2.md#deployment-prerequisites).
4. Podłącz urządzenie do hosta i wykonaj wstępną konfigurację. Aby uzyskać szczegółowe informacje, przejdź do [kroków wdrażania](storsimple-8000-deployment-walkthrough-u2.md#deployment-steps).
5. Utwórz woluminy na urządzeniu, przypisz je do hosta i sprawdź, czy host może je zainstalować i używać. StorSimple Snapshot Manager obsługuje następujące typy woluminów:
   
   * Woluminy podstawowe
   * Proste woluminy
   * Woluminy dynamiczne
   * Woluminy dynamiczne dublowane (RAID 1)
   * Woluminy udostępnione w klastrze
     
     Aby uzyskać informacje dotyczące tworzenia woluminów na urządzeniu storsimple lub urządzeniu wirtualnym StorSimple, przejdź do [kroku 6: Tworzenie woluminu](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume)w programie [Deploy your on-premises StorSimple device](storsimple-8000-deployment-walkthrough-u2.md).

## <a name="install-a-new-storsimple-snapshot-manager"></a>Instalowanie nowego Menedżera migawek StorSimple
Przed zainstalowaniem Menedżera migawek StorSimple upewnij się, że woluminy utworzone na urządzeniu storsimple lub urządzeniu wirtualnym StorSimple są zainstalowane, zainicjowane i sformatowane zgodnie z opisem w [temacie Konfiguruj wymagania wstępne](#configure-prerequisites).

> [!IMPORTANT]
> * W przypadku urządzenia wirtualnego StorSimple host musi być maszyną wirtualną platformy Microsoft Azure. 
> * Na hoście musi być uruchomiony system Windows 2008 R2, Windows Server 2012 lub Windows Server 2012 R2. Jeśli na serwerze jest uruchomiony system Windows Server 2008 R2, należy również zainstalować systemy Windows Server 2008 z sp1 i windows management framework 3.0.
> * Przed podłączeniem urządzenia do Menedżera migawek StorSimple należy skonfigurować połączenie iSCSI z hosta na urządzenie StorSimple.

Wykonaj następujące kroki, aby zakończyć nową instalację StorSimple Snapshot Manager. Jeśli instalujesz uaktualnienie, przejdź do [uaktualnienia lub ponownej instalacji StorSimple Snapshot Manager](#upgrade-or-reinstall-storsimple-snapshot-manager).

* Krok 1: Zainstaluj Menedżera migawek StorSimple 
* Krok 2: Łączenie Menedżera migawek StorSimple z urządzeniem 
* Krok 3: Sprawdź połączenie z urządzeniem 

### <a name="step-1-install-storsimple-snapshot-manager"></a>Krok 1: Zainstaluj Menedżera migawek StorSimple
Aby zainstalować Menedżera migawek StorSimple, należy wykonać następujące czynności.

#### <a name="to-install-storsimple-snapshot-manager"></a>Aby zainstalować Menedżera migawek StorSimple
1. Pobierz oprogramowanie StorSimple Snapshot Manager (przejdź do [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220) w Centrum pobierania Microsoft) i zapisz go lokalnie na hoście.
2. W Eksploratorze plików kliknij prawym przyciskiem myszy skompresowany folder, a następnie kliknij polecenie **Wyodrębnij wszystkie**.
3. W oknie **Wyodrębnij skompresowane (spakowane) foldery** w polu **Wybierz miejsce docelowe i wyodrębnij pliki** wpisz lub przejdź do ścieżki, w której chcesz wyodrębnić plik.
   
    > [!IMPORTANT]
    > Należy zainstalować StorSimple Snapshot Manager na dysku C:.
    
4. Zaznacz pole wyboru **Pokaż wyodrębnione pliki po zakończeniu,** a następnie kliknij pozycję **Wyodrębnij**.
   
    ![Okno dialogowe Wyodrębnianie plików](./media/storsimple-snapshot-manager-deployment/HCS_SSM_extract_files.png) 
5. Po zakończeniu wyodrębniania zostanie otwarty folder docelowy. Kliknij dwukrotnie ikonę konfiguracji aplikacji wyświetlaną w folderze docelowym.
6. Po **wyświetleniu** komunikatu Zakończenie instalacji kliknij przycisk **Zamknij**. Na pulpicie powinna zostać wyświetlona ikona Menedżera migawek StorSimple.
   
    ![ikona pulpitu](./media/storsimple-snapshot-manager-deployment/HCS_SSM_desktop_icon.png) 

### <a name="step-2-connect-storsimple-snapshot-manager-to-a-device"></a>Krok 2: Łączenie Menedżera migawek StorSimple z urządzeniem
Aby połączyć Menedżera migawek StorSimple z urządzeniem StorSimple, należy wykonać następujące czynności.

#### <a name="to-connect-storsimple-snapshot-manager-to-a-device"></a>Aby połączyć StorSimple Snapshot Manager z urządzeniem
1. Kliknij ikonę StorSimple Snapshot Manager na pulpicie. Zostanie wyświetlone okno Menedżer migawek StorSimple. Okno zawiera **okienko Zakres,** **okienko Wyniki** i okienko **Akcje.** 
   
    ![Interfejs użytkownika Menedżera migawek StorSimple](./media/storsimple-snapshot-manager-deployment/HCS_SSM_gui_panes.png)
   
   * **Okienko Zakres** (lewe okienko) zawiera listę węzłów zorganizowanych w strukturze drzewa. Można rozwinąć niektóre węzły, aby wybrać widok lub określone dane związane z tym węzłem. Kliknij ikonę strzałki, aby rozwinąć lub zwinąć węzeł. Kliknij prawym przyciskiem myszy element w okienku **Zakres,** aby wyświetlić listę dostępnych akcji dla tego elementu.
   * Okienko **Wyniki** (okienko środkowe) zawiera szczegółowe informacje o stanie węzła, widoku lub danych wybranych w okienku **Zakres.**
   * Okienko **Akcje** zawiera listę operacji, które można wykonać w węźle, widoku lub danych wybranych w okienku **Zakres.**
     
     Aby uzyskać pełny opis interfejsu użytkownika Menedżera migawek StorSimple, zobacz [Interfejs użytkownika StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).
2. W okienku **Zakres** kliknij prawym przyciskiem myszy węzeł **Urządzenia,** a następnie kliknij polecenie **Konfiguruj urządzenie**. Zostanie wyświetlone okno dialogowe **Konfigurowanie urządzenia.**
   
    ![Konfigurowanie urządzenia](./media/storsimple-snapshot-manager-deployment/HCS_SSM_config_device.png) 
3. W polu listy **Urządzenie** wybierz adres IP urządzenia lub urządzenia wirtualnego usługi Microsoft Azure StorSimple. W polu **tekstowym Hasło** wpisz hasło Menedżera migawek StorSimple utworzone dla urządzenia w witrynie Azure portal. Kliknij przycisk **OK**.
4. StorSimple Snapshot Manager wyszukuje urządzenie, które zostało zidentyfikowane. Jeśli urządzenie jest dostępne, StorSimple Snapshot Manager dodaje połączenie. Można [sprawdzić połączenie z urządzeniem,](#to-verify-the-connection) aby potwierdzić, że połączenie zostało pomyślnie dodane.
   
    Jeśli urządzenie jest niedostępne z jakiegokolwiek powodu, StorSimple Snapshot Manager zwraca komunikat o błędzie. Kliknij **przycisk OK,** aby zamknąć komunikat o błędzie, a następnie kliknij przycisk **Anuluj,** aby zamknąć okno dialogowe **Konfigurowanie urządzenia.**
5. Gdy łączy się z urządzeniem, StorSimple Snapshot Manager importuje każdą grupę woluminów skonfigurowaną dla tego urządzenia, pod warunkiem, że grupa woluminów ma skojarzone kopie zapasowe. Grupy woluminów, które nie mają skojarzonych kopii zapasowych, nie są importowane. Ponadto zasady tworzenia kopii zapasowych, które zostały utworzone dla grupy woluminów nie są importowane. Aby wyświetlić zaimportowane grupy, kliknij prawym przyciskiem myszy węzeł **Najczęściej większość grup woluminów** w okienku **Zakres,** a następnie kliknij polecenie **Przełącz importowane grupy**.

### <a name="step-3-verify-the-connection-to-the-device"></a>Krok 3: Sprawdź połączenie z urządzeniem
Skorzystaj z poniższych kroków, aby sprawdzić, czy Menedżer migawek StorSimple jest połączony z urządzeniem StorSimple.

#### <a name="to-verify-the-connection"></a>Aby zweryfikować połączenie
1. W okienku **Zakres** kliknij węzeł **Urządzenia.**
   
    ![StorSimple Migawka Manager stan urządzenia](./media/storsimple-snapshot-manager-deployment/HCS_SSM_Device_status.png) 
2. Sprawdź okienko **Wyniki:** 
   
   * Jeśli na ikonie urządzenia pojawi się zielony **wskaźnik,** a w kolumnie **Stan** pojawi się zielony wskaźnik, urządzenie jest podłączone. 
   * Jeśli na ikonie urządzenia pojawi się czerwony wskaźnik, a w kolumnie Stan pojawi się ikona **Niedostępna,** urządzenie nie jest podłączone. 
   * Jeśli **odświeżanie** pojawi się w kolumnie **Stan,** Menedżer migawek StorSimple pobiera grupy woluminów i skojarzone kopie zapasowe dla podłączonego urządzenia.

## <a name="upgrade-or-reinstall-storsimple-snapshot-manager"></a>Uaktualnianie lub ponowne instalowanie Menedżera migawek StorSimple
Przed uaktualnieniem lub ponownym zainstalowaniem oprogramowania należy całkowicie odinstalować StorSimple Snapshot Manager. 

Przed ponownym zainstalowaniem Menedżera migawek StorSimple należy ponownie wywrzeć na komputerze-hoście utworzenie kopii zapasowej istniejącej bazy danych StorSimple Snapshot Manager. Spowoduje to zapisanie zasad tworzenia kopii zapasowych i informacji o konfiguracji, dzięki czemu można łatwo przywrócić te dane z kopii zapasowej.

Wykonaj następujące kroki, jeśli uaktualniasz lub ponownie instalujesz Menedżera migawek StorSimple:

* Krok 1: Odinstaluj Menedżera migawek StorSimple 
* Krok 2: Tworzenie kopii zapasowej bazy danych Menedżera migawek StorSimple 
* Krok 3: Ponowne instalowanie Menedżera migawek StorSimple i przywracanie bazy danych 

### <a name="step-1-uninstall-storsimple-snapshot-manager"></a>Krok 1: Odinstaluj Menedżera migawek StorSimple
Aby odinstalować Menedżera migawek StorSimple, należy wykonać następujące czynności.

#### <a name="to-uninstall-storsimple-snapshot-manager"></a>Aby odinstalować Menedżera migawek StorSimple
1. Na komputerze-hoście otwórz **Panel sterowania**, kliknij pozycję **Programy**, a następnie kliknij pozycję Programy **i funkcje**.
2. W lewym okienku kliknij pozycję **Odinstaluj lub zmień program**.
3. Kliknij prawym przyciskiem myszy pozycję **StorSimple Snapshot Manager**, a następnie kliknij polecenie **Odinstaluj**.
4. Spowoduje to uruchomienie programu instalacyjnego Menedżera migawek StorSimple. Kliknij **pozycję Modyfikuj Instalatora**, a następnie kliknij pozycję **Odinstaluj**.
   
   > [!NOTE]
   > Jeśli w tle są uruchomione jakiekolwiek procesy programu MMC, takie jak StorSimple Snapshot Manager lub Disk Management, odinstalowanie zakończy się niepowodzeniem i zostanie wyświetlony komunikat o zamknięciu wszystkich wystąpień programu MMC przed próbą odinstalowania programu. Wybierz **pozycję Automatycznie zamknij aplikacje i spróbuj ponownie uruchomić je po zakończeniu instalacji,** a następnie kliknij przycisk **OK**.
   > 
   > 
5. Po zakończeniu procesu odinstalowywania zostanie wyświetlony komunikat **Pomyślnie instalacji.** Kliknij przycisk **Zamknij**.

### <a name="step-2-back-up-the-storsimple-snapshot-manager-database"></a>Krok 2: Tworzenie kopii zapasowej bazy danych Menedżera migawek StorSimple
Aby utworzyć i zapisać kopię bazy danych Menedżera migawek StorSimple, należy wykonać następujące kroki.

#### <a name="to-back-up-the-database"></a>Aby zrobić kopii zapasowej bazy danych
1. Zatrzymaj usługę zarządzania Microsoft StorSimple:
   
   1. Uruchom Menedżera serwera.
   2. Na pulpicie nawigacyjnym Menedżera serwera w menu **Narzędzia** wybierz polecenie **Usługi**.
   3. Na stronie **Usługi** wybierz pozycję **Microsoft StorSimple Management Service**.
   4. W prawym okienku w obszarze **Microsoft StorSimple Management Service**kliknij pozycję **Zatrzymaj usługę**.
      
        ![Zatrzymywać usługę StorSimple Device Manager](./media/storsimple-snapshot-manager-deployment/HCS_SSM_stop_service.png)
2. Przejdź do folderu C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > FolderProgramData jest folderem ukrytym.
  
3. Znajdź katalogowy plik XML, skopiuj plik i przechowuj ją w bezpiecznym miejscu lub w chmurze.
   
    ![StorSimple plik katalogu kopii zapasowych](./media/storsimple-snapshot-manager-deployment/HCS_SSM_bacatalog.png)
4. Uruchom ponownie usługę zarządzania microsoft storsimple: 
   
   1. Na pulpicie nawigacyjnym Menedżera serwera w menu **Narzędzia** wybierz polecenie **Usługi**.
   2. Na stronie **Usługi** wybierz pozycję **Microsoft StorSimple Management Service**.
   3. W prawym okienku w obszarze **Microsoft StorSimple Management Service**kliknij pozycję Uruchom ponownie **usługę**. 

### <a name="step-3-reinstall-storsimple-snapshot-manager-and-restore-the-database"></a>Krok 3: Ponowne instalowanie Menedżera migawek StorSimple i przywracanie bazy danych
Aby ponownie zainstalować Menedżera migawek StorSimple, wykonaj kroki opisane w [install nowym menedżerze migawek StorSimple](#install-a-new-storsimple-snapshot-manager). Następnie należy użyć poniższej procedury, aby przywrócić storsimple bazy danych Menedżera migawek.

#### <a name="to-restore-the-database"></a>Aby przywrócić bazę danych
1. Zatrzymaj usługę zarządzania Microsoft StorSimple:
   
   1. Uruchom Menedżera serwera.
   2. Na pulpicie nawigacyjnym Menedżera serwera w menu **Narzędzia** wybierz polecenie **Usługi**.
   3. Na stronie **Usługi** wybierz pozycję **Microsoft StorSimple Management Service**.
   4. W prawym okienku w obszarze **Microsoft StorSimple Management Service**kliknij pozycję **Zatrzymaj usługę**.
2. Przejdź do folderu C:\ProgramData\Microsoft\StorSimple\BACatalog.
   
   > [!NOTE]
   > FolderProgramData jest folderem ukrytym.
   > 
   > 
3. Usuń plik XML katalogu i zastąp go wersją zapisaną wcześniej.
4. Uruchom ponownie usługę zarządzania microsoft storsimple: 
   
   1. Na pulpicie nawigacyjnym Menedżera serwera w menu **Narzędzia** wybierz polecenie **Usługi**.
   2. Na stronie **Usługi** wybierz pozycję **Microsoft StorSimple Management Service**.
   3. W prawym okienku w obszarze **Microsoft StorSimple Management Service**kliknij pozycję Uruchom ponownie **usługę**.

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej o StorSimple Snapshot Manager, przejdź do [Co to jest StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md).
* Aby dowiedzieć się więcej o interfejsie użytkownika StorSimple Snapshot Manager, przejdź do [interfejsu użytkownika StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).
* Aby dowiedzieć się więcej na temat korzystania z Menedżera migawek StorSimple, przejdź do [witryny Użyj Menedżera migawek StorSimple, aby administrować rozwiązaniem StorSimple](storsimple-snapshot-manager-admin.md).

