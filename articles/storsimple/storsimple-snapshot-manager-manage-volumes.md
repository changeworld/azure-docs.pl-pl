---
title: Przystawki StorSimple Snapshot Manager i woluminów | Dokumentacja firmy Microsoft
description: Opisuje sposób używania przystawki MMC przystawki StorSimple Snapshot Manager, wyświetlanie i zarządzanie woluminami i konfigurowanie kopii zapasowych.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: v-sharos
ms.openlocfilehash: 260dfdd4b8fe7c277358fa5773029ea9a532740a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61078302"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Do wyświetlania i zarządzanie woluminami za pomocą Menedżera migawek StorSimple
## <a name="overview"></a>Omówienie
Można użyć programu StorSimple Snapshot Manager **woluminów** węzła (na **zakres** okienko) wybierz wolumin i wyświetlania informacji o nich. Woluminy są uporządkowane jako dyski, które odnoszą się do woluminów zainstalowanych w wyniku hosta. **Woluminów** węzeł zawiera woluminy lokalne i typów woluminów, które są obsługiwane przez rozwiązanie StorSimple, w tym woluminy odnalezione za pomocą interfejsu iSCSI i urządzeń. 

Aby uzyskać więcej informacji o obsługiwanych woluminów, przejdź do [obsługi wielu typów woluminów](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Lista woluminu w okienku wyników](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

**Woluminów** węzeł pozwala także w ponownie przeskanuj lub Usuń woluminy, po przystawki StorSimple Snapshot Manager odnajduje je. 

W tym samouczku wyjaśniono, jak można zainstalować, zainicjować i sformatować woluminy i następnie za pomocą Menedżera migawek StorSimple do:

* Wyświetlanie informacji o woluminach 
* Usuń woluminy
* Skanuj ponownie woluminów 
* Skonfiguruj wolumin podstawowy i wykonać ich kopię zapasową
* Konfigurowanie dynamicznego wolumin dublowany i wykonać ich kopię zapasową

> [!NOTE]
> Wszystkie **woluminu** są również dostępne w węźle akcji **akcje** okienka.
> 
> 

## <a name="mount-volumes"></a>Woluminy instalacji
Poniższa procedura umożliwia instalowanie, inicjowanie i formatowanie woluminów StorSimple. Ta procedura wykorzystuje Zarządzanie dyskami systemu narzędzie służące do zarządzania dyskami twardymi i odpowiednie woluminów lub partycji. Aby uzyskać więcej informacji na temat przystawki Zarządzanie dyskami, przejdź do [przystawki Zarządzanie dyskami](https://technet.microsoft.com/library/cc770943.aspx) w witrynie Microsoft TechNet.

#### <a name="to-mount-volumes"></a>Aby zainstalować woluminów
1. Uruchom inicjatora iSCSI firmy Microsoft na komputerze hosta.
2. Podaj jeden z adresów IP interfejsu jako portal obiektu docelowego lub adres IP odnajdywania i połączyć się z urządzeniem. Po podłączeniu urządzenia woluminy będą dostępne do systemu Windows. Aby uzyskać więcej informacji o korzystaniu z inicjatora iSCSI firmy Microsoft, przejdź do sekcji "Łączenie z urządzeniem docelowym iSCSI" w [Instalowanie i konfigurowanie programu Microsoft iSCSI Initiator][1].
3. Rozpocznij zarządzanie dyskami przy użyciu jednej z następujących opcji:
   
   * Wpisz Diskmgmt.msc w **Uruchom** pole.
   * Uruchom Menedżera serwera, rozwiń **magazynu** węzeł, a następnie wybierz **przystawki Zarządzanie dyskami**.
   * Rozpocznij **narzędzia administracyjne**, rozwiń węzeł **Zarządzanie komputerem** węzeł, a następnie wybierz **przystawki Zarządzanie dyskami**. 
     
     > [!NOTE]
     > Aby uruchomić przystawkę Zarządzanie dyskami, należy użyć uprawnień administratora.
     > 
     > 
4. Wykonaj woluminy w tryb online:
   
   1. W przystawce Zarządzanie dyskami, kliknij prawym przyciskiem myszy każdy wolumin oznaczone **Offline**.
   2. Kliknij przycisk **Uaktywnij ponownie dysk**. Dysk powinien być oznaczony **Online** po ponownym uaktywnieniu.
5. Inicjuj woluminy:
   
   1. Kliknij prawym przyciskiem myszy odnalezionych woluminów.
   2. W menu, wybierz **Zainicjuj dysk**.
   3. W **Zainicjuj dysk** okno dialogowe, wybierz dyski, które chcesz zainicjować, a następnie kliknij przycisk **OK**.
6. Formatowanie woluminów prostych:
   
   1. Kliknij prawym przyciskiem myszy wolumin, który chcesz sformatować.
   2. W menu, wybierz **nowy wolumin prosty**.
   3. Sformatuj wolumin za pomocą Kreatora nowy wolumin prosty:
      
      * Określ rozmiar woluminu.
      * Należy podać literę dysku.
      * Wybierz system plików NTFS.
      * Określ rozmiar jednostki alokacji 64 KB.
      * Przeprowadź szybkie formatowanie.
7. Formatowanie woluminów wielu partycji. Aby uzyskać instrukcje, przejdź do sekcji "Woluminów i partycje" [Implementowanie przystawki Zarządzanie dyskami](https://msdn.microsoft.com/library/dd163556.aspx).

## <a name="view-information-about-your-volumes"></a>Wyświetlanie informacji o woluminach
Aby wyświetlić informacje o lokalnych i woluminów StorSimple systemu Azure, należy użyć następującej procedury.

#### <a name="to-view-volume-information"></a>Aby wyświetlić informacje o woluminie
1. Kliknij ikony pulpitu, które można uruchomić programu StorSimple Snapshot Manager. 
2. W **zakres** okienku kliknij **woluminów** węzła. Zostanie wyświetlona lista woluminów lokalnych i zainstalowanych, w tym wszystkich woluminów StorSimple systemu Azure, w **wyniki** okienka. Kolumny w **wyniki** okienku są konfigurowane. (Kliknij prawym przyciskiem myszy **woluminów** węzeł **widoku**, a następnie wybierz pozycję **Dodaj/Usuń kolumny**.)
   
    ![Konfigurowania kolumn](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Kolumny wyników | Opis |
   |:--- |:--- |
   |  Name (Nazwa) |**Nazwa** kolumna zawiera literę dysku przypisaną do każdej odnaleziony wolumin. |
   |  Urządzenie |**Urządzenia** kolumna zawiera adres IP urządzenia, podłączane do komputera hosta. |
   |  Nazwa woluminu urządzenia |**Nazwa woluminu w urządzeniu** kolumna zawiera nazwę woluminu urządzenia, do której należy wybranego woluminu. Jest to nazwa woluminu, zdefiniowane w witrynie Azure portal dla tego określonego woluminu. |
   |  Dostęp do ścieżki |**Ścieżek dostępu** kolumnie jest wyświetlana ścieżka dostępu do woluminu. Jest to dysk litery lub punktu instalacji co wolumin jest dostępny na komputerze-hoście. |

## <a name="delete-a-volume"></a>Usuwanie woluminu
Użyj poniższej procedury, aby usunąć wolumin z programu StorSimple Snapshot Manager.

> [!NOTE]
> Nie można usunąć woluminu, ponieważ jest częścią żadnej grupy woluminu. (Opcja Usuń nie jest dostępne dla woluminów, które są elementami członkowskimi grupy woluminu). Należy usunąć grupę całego woluminu, aby usunąć wolumin.

#### <a name="to-delete-a-volume"></a>Aby usunąć wolumin
1. Kliknij ikony pulpitu, które można uruchomić programu StorSimple Snapshot Manager.
2. W **zakres** okienku kliknij **woluminów** węzła. 
3. W **wyniki** okienku kliknij prawym przyciskiem myszy wolumin, który chcesz usunąć.
4. W menu, kliknij polecenie **Usuń**. 
   
    ![Usuwanie woluminu](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. **Usuń wolumin** pojawi się okno dialogowe. Typ **Potwierdź** w polu tekstowym, a następnie kliknij przycisk **OK**.
6. Domyślnie przystawki StorSimple Snapshot Manager tworzy kopię zapasową woluminu przed jego usunięciem. W ten sposób można chronić klientów przed przed utratą danych jeśli niezamierzone został usunięty. Wyświetla programu StorSimple Snapshot Manager **automatycznych migawek** komunikat o postępie, podczas gdy tworzy kopię zapasową woluminu. 
   
    ![Komunikat o automatycznych migawek](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Skanuj ponownie woluminów
Przeskanuj woluminy połączone do programu StorSimple Snapshot Manager, należy użyć następującej procedury.

#### <a name="to-rescan-the-volumes"></a>Aby ponownie przeskanować woluminów
1. Kliknij ikony pulpitu, które można uruchomić programu StorSimple Snapshot Manager.
2. W **zakres** okienku kliknij prawym przyciskiem myszy **woluminów**, a następnie kliknij przycisk **Skanuj ponownie woluminy**.
   
    ![Skanuj ponownie woluminów](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Ta procedura synchronizuje listę woluminów za pomocą przystawki StorSimple Snapshot Manager. Wszelkie zmiany, takie jak nowe woluminy lub usunięte, zostaną odzwierciedlone w wynikach.

## <a name="configure-and-back-up-a-basic-volume"></a>Konfigurowanie i tworzenie kopii zapasowej woluminu podstawowego
Poniższa procedura umożliwia skonfigurowanie kopii zapasowej woluminu podstawowego, a następnie natychmiast rozpocząć tworzenie kopii zapasowej lub utworzyć zasady dla zaplanowanych kopii zapasowych.

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem:

* Upewnij się, że komputer StorSimple urządzeń i hostów są prawidłowo skonfigurowane. Aby uzyskać więcej informacji, przejdź do [wdrażanie urządzenia StorSimple w środowisku lokalnym](storsimple-deployment-walkthrough-u2.md).
* Instalowanie i konfigurowanie programu StorSimple Snapshot Manager. Aby uzyskać więcej informacji, przejdź do [wdrażanie programu StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Aby skonfigurować tworzenie kopii zapasowej woluminu podstawowego
1. Tworzenie woluminu podstawowego na urządzeniu StorSimple.
2. Instalowanie, inicjowanie i formatowanie woluminu, zgodnie z opisem w [zainstalować woluminów](#mount-volumes). 
3. Kliknij ikonę programu StorSimple Snapshot Manager na pulpicie. Zostanie wyświetlone okno programu StorSimple Snapshot Manager. 
4. W **zakres** okienku kliknij prawym przyciskiem myszy **woluminów** węzeł, a następnie wybierz **Skanuj ponownie woluminy**. Po zakończeniu skanowania listę woluminów powinna zostać wyświetlona w **wyniki** okienka. 
5. W **wyniki** okienku kliknij prawym przyciskiem myszy wolumin, a następnie wybierz **Utwórz grupę woluminu**. 
   
    ![Tworzenie grupy woluminów](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. W **Utwórz grupę woluminu** okno dialogowe wpisz nazwę grupy woluminów, przypisać woluminy do niego, a następnie kliknij przycisk **OK**.
7. W **zakres** okienku rozwiń **grupami woluminów** węzła. Nowa grupa woluminu powinna zostać wyświetlona w obszarze **grupami woluminów** węzła. 
8. Kliknij prawym przyciskiem myszy nazwę grupy woluminu.
   
   * Aby uruchomić interakcyjne zadania tworzenia kopii zapasowej (na żądanie), kliknij przycisk **wykonać kopii zapasowej**. 
   * Aby zaplanować automatyczne wykonywanie kopii zapasowej, kliknij przycisk **Tworzenie zasad kopii zapasowych**. Na **ogólne** wybierz grupę woluminu z listy. Na **harmonogram** wpisz szczegóły harmonogramu. Gdy skończysz, kliknij przycisk **OK**. 
9. Aby upewnić się, że zadanie tworzenia kopii zapasowej została uruchomiona, rozwiń **zadania** w węźle **zakres** okienku, a następnie kliknij przycisk **systemem** węzła. Zostanie wyświetlona lista aktualnie uruchomionych zadań w **wyniki** okienka. 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Konfigurowanie i tworzenie kopii zapasowej woluminu dublowanego dynamiczne
Wykonaj poniższe kroki, aby skonfigurować tworzenie kopii zapasowej woluminu dublowanego dynamicznej:

* Krok 1: Użyj przystawki Zarządzanie dyskami, aby utworzyć dynamiczny wolumin dublowany. 
* Krok 2: Użyj Menedżera migawek StorSimple do skonfigurowania kopii zapasowej.

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem:

* Upewnij się, że komputer StorSimple urządzeń i hostów są prawidłowo skonfigurowane. Aby uzyskać więcej informacji, przejdź do [wdrażanie urządzenia StorSimple w środowisku lokalnym](storsimple-8000-deployment-walkthrough-u2.md).
* Instalowanie i konfigurowanie programu StorSimple Snapshot Manager. Aby uzyskać więcej informacji, przejdź do [wdrażanie programu StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
* Skonfiguruj dwa woluminy na urządzeniu StorSimple. (W przykładach są dostępne woluminy **dysk 1** i **Disk 2**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Krok 1: Użyj przystawki Zarządzanie dyskami, aby utworzyć dynamiczny wolumin dublowany
Zarządzanie dyskami jest narzędziem do systemu zarządzania dyskami twardymi i woluminy lub partycje, które zawierają. Aby uzyskać więcej informacji na temat przystawki Zarządzanie dyskami, przejdź do [przystawki Zarządzanie dyskami](https://technet.microsoft.com/library/cc770943.aspx) w witrynie Microsoft TechNet.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Aby utworzyć dynamiczny wolumin dublowany
1. Rozpocznij zarządzanie dyskami przy użyciu jednej z następujących opcji: 
   
   * Otwórz **Uruchom** wpisz **Diskmgmt.msc**, i naciśnij klawisz Enter.
   * Uruchom Menedżera serwera, rozwiń **magazynu** węzeł, a następnie wybierz **przystawki Zarządzanie dyskami**. 
   * Rozpocznij **narzędzia administracyjne**, rozwiń węzeł **Zarządzanie komputerem** węzeł, a następnie wybierz **przystawki Zarządzanie dyskami**. 
2. Upewnij się, że dostępne dwa woluminy na urządzeniu StorSimple. (W tym przykładzie są dostępne woluminy **dysk 1** i **Disk 2**.) 
3. W oknie Zarządzanie dyskami w prawej kolumnie w dolnym okienku kliknij prawym przyciskiem myszy **dysk 1** i wybierz **nowy wolumin dublowany**. 
   
    ![Nowy wolumin dublowany](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. Na **nowy wolumin dublowany** strona kreatora, kliknij przycisk **dalej**.
5. Na **Wybierz dyski** wybierz **Disk 2** w **wybrane** okienku kliknij **Dodaj**, a następnie kliknij przycisk **dalej**. 
6. Na **Przypisz literę dysku lub ścieżki** strony, zaakceptuj wartości domyślne, a następnie kliknij przycisk **dalej**. 
7. Na **formatowanie woluminu** stronie **rozmiar jednostki alokacji** wybierz opcję **64 KB**. Wybierz **wykonać szybkie formatowanie** pole wyboru, a następnie kliknij przycisk **dalej**. 
8. Na **Kończenie pracy Kreatora nowych woluminów dublowanych** strony, przejrzyj ustawienia, a następnie kliknij przycisk **Zakończ**. 
9. Pojawi się komunikat, aby wskazać, że dysk podstawowy zostaną przekonwertowane na dysk dynamiczny. Kliknij przycisk **Yes** (Tak).
   
    ![Komunikat o konwersji dysku dynamicznego](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. W przystawce Zarządzanie dyskami sprawdź, czy dysk 1 i 2 dysków są wyświetlane jako dynamiczne woluminy dublowane. (**Dynamiczne** powinien pojawić się w kolumnie Stan, a kolor paska pojemności należy zmienić na czerwony, wskazując wolumin dublowany.) 
    
    ![Dyski dynamiczne zarządzanie zdublowany dysk](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Krok 2: Użyj StorSimple Snapshot Manager w celu skonfigurowania kopii zapasowej
Poniższa procedura umożliwia konfigurowanie dynamicznego woluminu dublowanego, a następnie od razu rozpocząć tworzenie kopii zapasowej lub utworzyć zasady dla zaplanowanych kopii zapasowych.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Aby skonfigurować tworzenie kopii zapasowej woluminu dublowanego dynamiczne
1. Kliknij ikonę programu StorSimple Snapshot Manager na pulpicie. Zostanie wyświetlone okno programu StorSimple Snapshot Manager. 
2. W **zakres** okienku kliknij prawym przyciskiem myszy **woluminów** a następnie wybierz węzeł **Skanuj ponownie woluminy**. Po zakończeniu skanowania listę woluminów powinna zostać wyświetlona w **wyniki** okienka. Dynamiczny wolumin dublowany jest wymieniony jako jeden wolumin. 
3. W **wyniki** , kliknij prawym przyciskiem myszy wolumin dublowany dynamiczne, a potem kliknij **Utwórz grupę woluminu**. 
4. W **Utwórz grupę woluminu** okno dialogowe, wpisz nazwę grupy woluminów, przypisać dynamiczny wolumin dublowany do tej grupy i kliknij przycisk **OK**. 
5. W **zakres** okienku rozwiń **grupami woluminów** węzła. Nowa grupa woluminu powinna zostać wyświetlona w obszarze **grupami woluminów** węzła. 
6. Kliknij prawym przyciskiem myszy nazwę grupy woluminu. 
   
   * Aby uruchomić interakcyjne zadania tworzenia kopii zapasowej (na żądanie), kliknij przycisk **wykonać kopii zapasowej**. 
   * Aby zaplanować automatyczne wykonywanie kopii zapasowej, kliknij przycisk **Tworzenie zasad kopii zapasowych**. Na **ogólne** stronie, wybierz grupę woluminu z listy. Na **harmonogram** wpisz szczegóły harmonogramu. Gdy skończysz, kliknij przycisk **OK**. 
7. Możesz monitorować zadania tworzenia kopii zapasowej, podczas jego wykonywania. W **zakres** okienku rozwiń **zadania** węzłem, a następnie kliknij przycisk **systemem**, szczegóły zadania są wyświetlane w **wyniki** okienka. Po zakończeniu zadania tworzenia kopii zapasowej, szczegółowe informacje są przekazywane do **ostatnie 24** godzin zadań listy. 

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [za pomocą Menedżera migawek StorSimple do administrowania rozwiązania StorSimple](storsimple-snapshot-manager-admin.md).
* Dowiedz się, jak [tworzenie i zarządzanie grupami woluminów za pomocą Menedżera migawek StorSimple](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
