---
title: StorSimple Snapshot Manager i woluminów | Dokumentacja firmy Microsoft
description: Informacje dotyczące używania przystawki MMC programu StorSimple Snapshot Manager można wyświetlać i zarządzać nimi woluminów i skonfigurowania kopii zapasowych.
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
ms.openlocfilehash: 2c0b211bced99d272a73a7b018a22f99d8d58aa9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876992"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>StorSimple Snapshot Manager umożliwia wyświetlanie i zarządzanie woluminami
## <a name="overview"></a>Omówienie
Można użyć programu StorSimple Snapshot Manager **woluminów** węzła (na **zakres** okienko) wybierz wolumin i wyświetlania informacji o nich. Woluminy są widoczne jako dyski, które odpowiadają na tych woluminach zainstalowane przez hosta. **Woluminów** węzeł zawiera woluminy lokalne i typów woluminów, które są obsługiwane przez StorSimple, w tym także woluminów o wykryte za pomocą inicjatora iSCSI i urządzenia. 

Aby uzyskać więcej informacji o obsługiwanych woluminów, przejdź do [obsługi wielu typów woluminu](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Lista woluminu w okienku wyników](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

**Woluminów** węzeł umożliwia również w ponownie przeskanuj lub Usuń woluminy, po StorSimple Snapshot Manager odnajduje je. 

W tym samouczku wyjaśniono, jak można zainstalować, zainicjować i sformatować woluminy i następnie użyć StorSimple Snapshot Manager do:

* Wyświetlanie informacji o woluminach 
* Usuwanie woluminów
* Skanuj woluminów 
* Konfigurowanie podstawowego woluminu i wykonać ich kopię zapasową
* Konfigurowanie dynamicznego woluminu dublowanego i wykonać ich kopię zapasową

> [!NOTE]
> Wszystkie **woluminu** są również dostępne w węźle akcji **akcje** okienka.
> 
> 

## <a name="mount-volumes"></a>Zainstalować woluminów
Użyj poniższej procedury można zainstalować, zainicjować i sformatować woluminy StorSimple. Ta procedura wykorzystuje Zarządzanie dyskami, narzędzie systemu zarządzania dyski twarde i odpowiednich woluminów lub partycji. Aby uzyskać więcej informacji na temat narzędzia Zarządzanie dyskami, przejdź do [Zarządzanie dyskami](https://technet.microsoft.com/library/cc770943.aspx) w witrynie Microsoft TechNet.

#### <a name="to-mount-volumes"></a>Aby zainstalować woluminów
1. Uruchom inicjatora iSCSI firmy Microsoft na komputerze hosta.
2. Podaj adresy IP interfejsu portalu obiektu docelowego lub adres IP odnajdywania i nawiąż połączenie z urządzeniem. Po podłączeniu urządzenia woluminów będzie dostępna dla systemu Windows. Aby uzyskać więcej informacji o korzystaniu z inicjatora iSCSI firmy Microsoft, przejdź do sekcji "Połączenie z urządzeniem docelowym iSCSI" w [Instalowanie i konfigurowanie programu Microsoft iSCSI Initiator][1].
3. Rozpocznij zarządzanie dyskami przy użyciu jednej z następujących opcji:
   
   * Wpisz Diskmgmt.msc w **Uruchom** pole.
   * Uruchom Menedżera serwera, rozwiń **magazynu** węzeł, a następnie wybierz **Zarządzanie dyskami**.
   * Uruchom **narzędzia administracyjne**, rozwiń węzeł **Zarządzanie komputerem** węzeł, a następnie wybierz **Zarządzanie dyskami**. 
     
     > [!NOTE]
     > Uruchamianie przystawki Zarządzanie dyskami, należy użyć uprawnień administratora.
     > 
     > 
4. Wykonaj woluminy online:
   
   1. W przystawce Zarządzanie dyskami kliknij prawym przyciskiem myszy każdy wolumin oznaczony **Offline**.
   2. Kliknij przycisk **Uaktywnij ponownie dysk**. Powinna być oznaczona jako dysk **Online** po ponownym uaktywnieniu.
5. Inicjowanie woluminy:
   
   1. Kliknij prawym przyciskiem myszy odnalezionych woluminów.
   2. W menu, wybierz **Inicjowanie dysku**.
   3. W **Inicjowanie dysku** okno dialogowe Wybierz dyski, które chcesz zainicjować, a następnie kliknij przycisk **OK**.
6. Formatowanie woluminów prostych:
   
   1. Kliknij prawym przyciskiem myszy wolumin, który chcesz sformatować.
   2. W menu, wybierz **nowy wolumin prosty**.
   3. Sformatuj wolumin za pomocą Kreatora nowy wolumin prosty:
      
      * Określ rozmiar woluminu.
      * Należy podać literę dysku.
      * Wybierz system plików NTFS.
      * Określ rozmiar jednostki alokacji 64 KB.
      * Przeprowadź szybkie formatowanie.
7. Formatowanie woluminów wielu partycji. Aby uzyskać instrukcje, przejdź do sekcji "Partycje i woluminy" [implementacja Zarządzanie dyskami](https://msdn.microsoft.com/library/dd163556.aspx).

## <a name="view-information-about-your-volumes"></a>Wyświetlanie informacji o woluminach
Poniższa procedura umożliwia wyświetlenie informacji o lokalnej i woluminach Azure StorSimple.

#### <a name="to-view-volume-information"></a>Aby wyświetlić informacji o woluminie
1. Kliknij ikonę pulpitu, aby uruchomić StorSimple Snapshot Manager. 
2. W **zakres** okienku, kliknij przycisk **woluminów** węzła. Lista lokalnych i zainstalowanych woluminów, w tym wszystkie woluminy Azure StorSimple, jest wyświetlana w **wyniki** okienka. Kolumn w **wyniki** okienku są konfigurowane. (Kliknij prawym przyciskiem myszy **woluminów** węzła, wybierz opcję **widoku**, a następnie wybierz **Dodaj/Usuń kolumny**.)
   
    ![Konfigurowanie kolumn](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Kolumny wyników | Opis |
   |:--- |:--- |
   |  Nazwa |**Nazwa** kolumna zawiera literę dysku przypisaną do każdego odnaleziony wolumin. |
   |  Urządzenie |**Urządzenia** kolumna zawiera adres IP urządzenia, podłączane do komputera hosta. |
   |  Nazwa woluminu urządzenia |**Nazwa woluminu urządzenia** kolumna zawiera nazwę woluminu urządzenia, do którego należy wybranego woluminu. Jest to nazwa woluminu zdefiniowane w portalu Azure dla tego określonego woluminu. |
   |  Dostęp do ścieżki |**Ścieżek dostępu** kolumnie jest wyświetlana ścieżka dostępu do woluminu. Jest to dysk litery lub punktu instalacji co wolumin jest dostępny na komputerze hosta. |

## <a name="delete-a-volume"></a>Usuwanie woluminu
Użyj poniższej procedury można usunąć wolumin z StorSimple Snapshot Manager.

> [!NOTE]
> Nie można usunąć woluminu, jeśli jest częścią żadnej grupy woluminu. (Opcja usuwania nie jest dostępna dla woluminów, które są elementami członkowskimi grupy woluminu). Należy usunąć grupę cały wolumin do usunięcia woluminu.

#### <a name="to-delete-a-volume"></a>Aby usunąć wolumin
1. Kliknij ikonę pulpitu, aby uruchomić StorSimple Snapshot Manager.
2. W **zakres** okienku, kliknij przycisk **woluminów** węzła. 
3. W **wyniki** okienku kliknij prawym przyciskiem myszy wolumin, który chcesz usunąć.
4. W menu, kliknij polecenie **usunąć**. 
   
    ![Usuwanie woluminu](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. **Usuń wolumin** zostanie wyświetlone okno dialogowe. Typ **Potwierdź** w polu tekstowym, a następnie kliknij przycisk **OK**.
6. Domyślnie StorSimple Snapshot Manager kopię zapasową woluminu przed jego usunięciem. W ten sposób może chronić należy przed utratą danych niezamierzone został usunięty. Wyświetla StorSimple Snapshot Manager **automatyczne migawki** komunikatu o postępie podczas jego kopię zapasową woluminu. 
   
    ![Komunikat automatyczne migawki](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Skanuj woluminów
Użyj poniższej procedury do ponownego skanowania woluminów, dołączony do programu StorSimple Snapshot Manager.

#### <a name="to-rescan-the-volumes"></a>Aby ponownie przeskanować woluminów
1. Kliknij ikonę pulpitu, aby uruchomić StorSimple Snapshot Manager.
2. W **zakres** okienku kliknij prawym przyciskiem myszy **woluminów**, a następnie kliknij przycisk **ponownego skanowania woluminów**.
   
    ![Skanuj woluminów](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Ta procedura synchronizuje Lista woluminów z StorSimple Snapshot Manager. Wszelkie zmiany, takie jak nowe woluminy lub usunięte, zostaną uwzględnione w wynikach.

## <a name="configure-and-back-up-a-basic-volume"></a>Konfigurowanie i tworzenie kopii zapasowej woluminu podstawowego
Poniższa procedura umożliwia skonfigurowanie kopii zapasowej woluminu podstawowego, a następnie natychmiast rozpocząć tworzenie kopii zapasowej lub utworzyć zasady dla zaplanowanych kopii zapasowych.

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem:

* Upewnij się, że StorSimple komputera urządzeń i hostów są skonfigurowane prawidłowo. Aby uzyskać więcej informacji, przejdź do [wdrażanie lokalnego urządzenia StorSimple](storsimple-deployment-walkthrough-u2.md).
* Instalowanie i konfigurowanie programu StorSimple Snapshot Manager. Aby uzyskać więcej informacji, przejdź do [wdrażanie StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Aby skonfigurować kopię zapasową woluminu podstawowego
1. Tworzenie woluminu podstawowego na urządzeniu StorSimple.
2. Zainstalować, zainicjować i sformatować wolumin, zgodnie z opisem w [instalowania woluminów](#mount-volumes). 
3. Kliknij ikonę StorSimple Snapshot Manager na pulpicie. Zostanie wyświetlone okno programu StorSimple Snapshot Manager. 
4. W **zakres** okienku kliknij prawym przyciskiem myszy **woluminów** węzeł, a następnie wybierz **ponownego skanowania woluminów**. Po zakończeniu skanowania listę woluminów powinna zostać wyświetlona w **wyniki** okienka. 
5. W **wyniki** okienku kliknij prawym przyciskiem myszy wolumin, a następnie wybierz **Utwórz grupę woluminu**. 
   
    ![Utwórz grupę woluminu](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. W **Utwórz grupę woluminu** okno dialogowe, wpisz nazwę grupy woluminu, przypisać woluminy, a następnie kliknij **OK**.
7. W **zakres** okienku rozwiń **grup woluminu** węzła. Nową grupę woluminu powinna zostać wyświetlona w obszarze **grup woluminu** węzła. 
8. Kliknij prawym przyciskiem myszy nazwę grupy woluminu.
   
   * Aby uruchomić zadanie tworzenia kopii zapasowej interakcyjne (na żądanie), kliknij przycisk **wykonać kopii zapasowej**. 
   * Aby zaplanować automatyczne wykonywanie kopii zapasowej, kliknij przycisk **Utwórz zasady tworzenia kopii zapasowej**. Na **ogólne** wybierz grupę woluminu z listy. Na **harmonogram** wprowadź szczegóły harmonogramu. Gdy skończysz, kliknij przycisk **OK**. 
9. Aby upewnić się, że zadania tworzenia kopii zapasowej została uruchomiona, rozwiń **zadania** w węźle **zakres** okienka, a następnie kliknij przycisk **systemem** węzła. Zostanie wyświetlona lista aktualnie uruchomionych zadań w **wyniki** okienka. 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Konfigurowanie i tworzenie kopii zapasowej woluminu dublowanego dynamiczne
Wykonaj poniższe kroki, aby skonfigurować tworzenie kopii zapasowych woluminu dublowanego dynamicznego:

* Krok 1: Użyj przystawki Zarządzanie dyskami można utworzyć wolumin dublowany dynamicznych. 
* Krok 2: Użyj programu StorSimple Snapshot Manager można skonfigurować tworzenia kopii zapasowej.

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem:

* Upewnij się, że StorSimple komputera urządzeń i hostów są skonfigurowane prawidłowo. Aby uzyskać więcej informacji, przejdź do [wdrażanie lokalnego urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Instalowanie i konfigurowanie programu StorSimple Snapshot Manager. Aby uzyskać więcej informacji, przejdź do [wdrażanie StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
* Skonfiguruj dwa woluminy na urządzeniu StorSimple. (W przykładach są dostępne woluminy **dysk 1** i **Disk 2**.) 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Krok 1: Użyj przystawki Zarządzanie dyskami można utworzyć wolumin dublowany dynamiczne
Zarządzanie dyskami jest narzędziem system zarządzania dyski i woluminy lub partycje, które zawierają. Aby uzyskać więcej informacji na temat narzędzia Zarządzanie dyskami, przejdź do [Zarządzanie dyskami](https://technet.microsoft.com/library/cc770943.aspx) w witrynie Microsoft TechNet.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Aby utworzyć wolumin dublowany dynamiczne
1. Rozpocznij zarządzanie dyskami przy użyciu jednej z następujących opcji: 
   
   * Otwórz **Uruchom** wpisz **Diskmgmt.msc**, i naciśnij klawisz Enter.
   * Uruchom Menedżera serwera, rozwiń **magazynu** węzeł, a następnie wybierz **Zarządzanie dyskami**. 
   * Uruchom **narzędzia administracyjne**, rozwiń węzeł **Zarządzanie komputerem** węzeł, a następnie wybierz **Zarządzanie dyskami**. 
2. Upewnij się, że są dostępne dwa woluminy na urządzeniu StorSimple. (W tym przykładzie są dostępne woluminy **dysk 1** i **Disk 2**.) 
3. W oknie Zarządzanie dyskami w prawej kolumnie w dolnym okienku kliknij prawym przyciskiem myszy **dysk 1** i wybierz **nowy wolumin dublowany**. 
   
    ![Nowy wolumin dublowany](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. Na **nowy wolumin dublowany** strona kreatora, kliknij przycisk **dalej**.
5. Na **Wybierz dyski** wybierz **Disk 2** w **wybrane** okienku, kliknij przycisk **Dodaj**, a następnie kliknij przycisk **dalej**. 
6. Na **Przypisz literę dysku lub ścieżka** Zaakceptuj ustawienia domyślne, a następnie kliknij pozycję **dalej**. 
7. Na **Format wolumin** strony w **rozmiar jednostki alokacji** wybierz opcję **64 KB**. Wybierz **Przeprowadź szybkie formatowanie** pole wyboru, a następnie kliknij przycisk **dalej**. 
8. Na **Kończenie pracy Kreatora nowych woluminów dublowanych** , przejrzyj ustawienia, a następnie kliknij przycisk **Zakończ**. 
9. Pojawi się komunikat, aby wskazać, że dysk podstawowy zostanie przekonwertowany na dysk dynamiczny. Kliknij przycisk **Yes** (Tak).
   
    ![Komunikat konwersji dysku dynamicznego](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. W przystawce Zarządzanie dyskami sprawdź, czy dysk 1 i 2 dysku są widoczne jako dynamiczne woluminy dublowane. (**Dynamiczne** powinny być wyświetlane w kolumnie Stan i kolor paska pojemności należy zmienić na czerwony i wskazujący woluminu dublowanego.) 
    
    ![Dyski dynamiczne zarządzanie dublowany dysk](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Krok 2: Użyj StorSimple Snapshot Manager, w celu skonfigurowania kopii zapasowej
Poniższa procedura umożliwia konfigurowanie dynamicznego woluminu dublowanego, a następnie natychmiast rozpocząć tworzenie kopii zapasowej lub utworzyć zasady dla zaplanowanych kopii zapasowych.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Aby skonfigurować kopię zapasową woluminu dublowanego dynamiczne
1. Kliknij ikonę StorSimple Snapshot Manager na pulpicie. Zostanie wyświetlone okno programu StorSimple Snapshot Manager. 
2. W **zakres** okienku kliknij prawym przyciskiem myszy **woluminów** a następnie wybierz węzeł **ponownego skanowania woluminów**. Po zakończeniu skanowania listę woluminów powinna zostać wyświetlona w **wyniki** okienka. Dynamicznego woluminu dublowanego znajduje się w jednym woluminie. 
3. W **wyniki** okienku kliknij prawym przyciskiem myszy wolumin dublowany dynamiczny, a następnie kliknij **Utwórz grupę woluminu**. 
4. W **Utwórz grupę woluminu** okno dialogowe, wpisz nazwę grupy woluminu, przypisz dynamicznego woluminu dublowanego do tej grupy, a następnie kliknij **OK**. 
5. W **zakres** okienku rozwiń **grup woluminu** węzła. Nową grupę woluminu powinna zostać wyświetlona w obszarze **grup woluminu** węzła. 
6. Kliknij prawym przyciskiem myszy nazwę grupy woluminu. 
   
   * Aby uruchomić zadanie tworzenia kopii zapasowej interakcyjne (na żądanie), kliknij przycisk **wykonać kopii zapasowej**. 
   * Aby zaplanować automatyczne wykonywanie kopii zapasowej, kliknij przycisk **Utwórz zasady tworzenia kopii zapasowej**. Na **ogólne** wybierz grupy woluminu z listy. Na **harmonogram** wprowadź szczegóły harmonogramu. Gdy skończysz, kliknij przycisk **OK**. 
7. Zadanie tworzenia kopii zapasowej można monitorować podczas jego wykonywania. W **zakres** okienku rozwiń **zadania** węzeł, a następnie kliknij przycisk **systemem**, szczegóły zadania są wyświetlane w **wyniki** okienka. Po zakończeniu zadania tworzenia kopii zapasowej, dane są przekazywane do **ostatnich 24** godziny zadania listy. 

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [zarządzać rozwiązania StorSimple przy użyciu programu StorSimple Snapshot Manager](storsimple-snapshot-manager-admin.md).
* Dowiedz się, jak [StorSimple Snapshot Manager umożliwia tworzenie i zarządzanie grupami woluminu](storsimple-snapshot-manager-manage-volume-groups.md).

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
