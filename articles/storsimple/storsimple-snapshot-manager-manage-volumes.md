---
title: StorSimple Snapshot Manager i woluminy | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób używania przystawki PROGRAMU MMC Menedżera migawek StorSimple do wyświetlania woluminów i zarządzania nimi oraz konfigurowania kopii zapasowych.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: 78896323-e57c-431e-bbe2-0cbde1cf43a2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/18/2016
ms.author: twooley
ms.openlocfilehash: f09d4dd46a50f1794e51342a939b8919c5c523ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254653"
---
# <a name="use-storsimple-snapshot-manager-to-view-and-manage-volumes"></a>Wyświetlanie woluminów i zarządzanie nimi za pomocą Menedżera migawek StorSimple
## <a name="overview"></a>Omówienie
Za pomocą węzła StorSimple Snapshot Manager **Volumes** (w okienku **Zakres)** można wybrać woluminy i wyświetlić informacje o nich. Woluminy są przedstawiane jako dyski odpowiadające woluminom zamontowanym przez hosta. Węzeł **Woluminy** pokazuje woluminy lokalne i typy woluminów obsługiwane przez StorSimple, w tym woluminy wykryte za pomocą interfejsu iSCSI i urządzenia. 

Aby uzyskać więcej informacji na temat obsługiwanych woluminów, przejdź do [pomocy technicznej dla wielu typów woluminów](storsimple-what-is-snapshot-manager.md#support-for-multiple-volume-types).

![Lista woluminów w okienku Wyniki](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Volume_node.png)

Węzeł **Woluminy** umożliwia również ponowne wyszewanie lub usuwanie woluminów po odnajdyniu ich przez StorSimple Snapshot Manager. 

W tym samouczku wyjaśniono, jak można zainstalować, zainicjować i sformatować woluminy, a następnie użyć StorSimple Snapshot Manager do:

* Wyświetlanie informacji o woluminach 
* Usuwanie woluminów
* Ponowne przeskanowywki 
* Konfigurowanie woluminu podstawowego i jego utworzenie kopii zapasowej
* Konfigurowanie woluminu z wiercem lustrzanym dynamicznym i jego utworzenie kopii zapasowej

> [!NOTE]
> Wszystkie akcje węzła **woluminu** są również dostępne w okienku **Akcje.**
> 
> 

## <a name="mount-volumes"></a>Mocowanie woluminów
Poniższa procedura służy do instalowania, inicjowania i formatowania woluminów StorSimple. Ta procedura używa przystawki Zarządzanie dyskami, narzędzie systemowe do zarządzania dyskami twardymi i odpowiednimi woluminami lub partycjami. Aby uzyskać więcej informacji na temat zarządzania dyskami, przejdź do [przystawki Zarządzanie dyskami](https://technet.microsoft.com/library/cc770943.aspx) w witrynie microsoft technet w sieci Web.

#### <a name="to-mount-volumes"></a>Aby zainstalować woluminy
1. Na komputerze-hoście uruchom inicjatora iSCSI firmy Microsoft.
2. Podaj jeden z adresów IP interfejsu jako docelowy portal lub adres IP odnajdywania i połącz się z urządzeniem. Po podłączeniu urządzenia woluminy będą dostępne dla systemu Windows. Aby uzyskać więcej informacji na temat korzystania z inicjatora iSCSI firmy Microsoft, przejdź do sekcji "Łączenie się z urządzeniem docelowym iSCSI" w [temacie Instalowanie i konfigurowanie inicjatora iSCSI firmy Microsoft][1].
3. Aby uruchomić zarządzanie dyskami, użyj dowolnej z następujących opcji:
   
   * Wpisz diskmgmt.msc w polu **Uruchom.**
   * Uruchom Menedżera serwera, rozwiń węzeł **Magazyn,** a następnie wybierz pozycję **Zarządzanie dyskami**.
   * Uruchom **narzędzia administracyjne**, rozwiń węzeł **Zarządzanie komputerem,** a następnie wybierz pozycję **Zarządzanie dyskami**. 
     
     > [!NOTE]
     > Aby uruchomić zarządzanie dyskami, należy użyć uprawnień administratora.
     > 
     > 
4. Weź woluminy online:
   
   1. W obszarze Zarządzanie dyskami kliknij prawym przyciskiem myszy dowolny wolumin oznaczony **w trybie offline**.
   2. Kliknij **pozycję Ponownie uaktywnić dysk**. Dysk powinien być oznaczony **jako w trybie online** po ponownym uaktywnieniu dysku.
5. Inicjowanie woluminów:
   
   1. Kliknij prawym przyciskiem myszy wykryte woluminy.
   2. W menu wybierz polecenie **Inicjuj dysk**.
   3. W oknie dialogowym **Inicjowanie dysku** zaznacz dyski, które chcesz zainicjować, a następnie kliknij przycisk **OK**.
6. Formatowanie woluminów prostych:
   
   1. Kliknij prawym przyciskiem myszy wolumin, który chcesz sformatować.
   2. W menu wybierz polecenie **Nowa wolumin prosty**.
   3. Użyj Kreatora nowego woluminu prostego, aby sformatować wolumin:
      
      * Określ rozmiar woluminu.
      * Podaj list napędowy.
      * Wybierz system plików NTFS.
      * Określ rozmiar jednostki alokacji 64 KB.
      * Przeprowadź szybkie formatowanie.
7. Formatowanie woluminów wielodzielnych. Aby uzyskać instrukcje, przejdź do sekcji "Partycje i woluminy" w [sekcji Implementowanie zarządzania dyskami](https://msdn.microsoft.com/library/dd163556.aspx).

## <a name="view-information-about-your-volumes"></a>Wyświetlanie informacji o woluminach
Poniższa procedura służy do wyświetlania informacji o woluminach lokalnych i azure storproste.

#### <a name="to-view-volume-information"></a>Aby wyświetlić informacje o woluminie
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple. 
2. W okienku **Zakres** kliknij węzeł **Woluminy.** W okienku **Wyniki** pojawi się lista woluminów lokalnych i zainstalowanych, w tym wszystkie woluminy usługi Azure StorSimple. Kolumny w okienku **Wyniki** można konfigurować. (Kliknij prawym przyciskiem myszy węzeł **Woluminy,** wybierz polecenie **Widok**, a następnie wybierz polecenie **Dodaj/Usuń kolumny).**
   
    ![Konfigurowanie kolumn](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_View_volumes.png)
   
   | Kolumna Wyniki | Opis |
   |:--- |:--- |
   |  Nazwa |Kolumna **Nazwa** zawiera literę dysku przypisaną do każdego odnalezionego woluminu. |
   |  Urządzenie |Kolumna **Urządzenie** zawiera adres IP urządzenia podłączonego do komputera-hosta. |
   |  Nazwa woluminu urządzenia |Kolumna **Nazwa woluminu urządzenia** zawiera nazwę woluminu urządzenia, do którego należy wybrany wolumin. Jest to nazwa woluminu zdefiniowana w witrynie Azure portal dla tego określonego woluminu. |
   |  Ścieżki dostępu |W kolumnie **Ścieżki dostępu** wyświetlana jest ścieżka dostępu do woluminu. Jest to litera dysku lub punkt instalacji, w którym wolumin jest dostępny na komputerze-hoście. |

## <a name="delete-a-volume"></a>Usuwanie woluminu
Poniższa procedura służy do usuwania woluminu z Menedżera migawek StorSimple.

> [!NOTE]
> Woluminu nie można usunąć, jeśli jest on częścią dowolnej grupy woluminów. (Opcja usuwania nie jest dostępna dla woluminów, które są członkami grupy woluminów). Aby usunąć wolumin, należy usunąć całą grupę woluminów.

#### <a name="to-delete-a-volume"></a>Aby usunąć wolumin
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple.
2. W okienku **Zakres** kliknij węzeł **Woluminy.** 
3. W okienku **Wyniki** kliknij prawym przyciskiem myszy wolumin, który chcesz usunąć.
4. W menu kliknij polecenie **Usuń**. 
   
    ![Usuwanie woluminu](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Delete_volume.png) 
5. Zostanie wyświetlone okno dialogowe **Usuwanie woluminu.** Wpisz pole tekstowe **Potwierdź,** a następnie kliknij przycisk **OK**.
6. Domyślnie StorSimple Snapshot Manager kopii zapasowej woluminu przed usunięciem go. Ten środek ostrożności może chronić przed utratą danych, jeśli usunięcie było niezamierzone. StorSimple Snapshot Manager wyświetla komunikat o **postępie automatycznej migawki** podczas pracy zapasowej woluminu. 
   
    ![Automatyczny komunikat migawki](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Automatic_snap.png) 

## <a name="rescan-volumes"></a>Ponowne przeskanowywki
Poniższa procedura umożliwia ponowne wyskanowanie woluminów połączonych z Menedżerem migawek StorSimple.

#### <a name="to-rescan-the-volumes"></a>Aby ponownie przeskanować woluminy
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple.
2. W okienku **Zakres** kliknij prawym przyciskiem myszy pozycję **Woluminy,** a następnie kliknij polecenie **Ponownieskanuj woluminy**.
   
    ![Ponowne przeskanowywki](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Rescan_volumes.png)
   
    Ta procedura synchronizuje listę woluminów z Menedżerem migawek StorSimple. Wszelkie zmiany, takie jak nowe woluminy lub usunięte woluminy, zostaną odzwierciedlone w wynikach.

## <a name="configure-and-back-up-a-basic-volume"></a>Konfigurowanie i konfigurowanie kopii zapasowej woluminu podstawowego
Poniższa procedura służy do konfigurowania kopii zapasowej woluminu podstawowego, a następnie natychmiastowego rozpoczęcia tworzenia kopii zapasowej lub utworzenia zasad dla zaplanowanych kopii zapasowych.

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem:

* Upewnij się, że urządzenie StorSimple i komputer-host są poprawnie skonfigurowane. Aby uzyskać więcej informacji, przejdź do [witryny Lokalne urządzenie StorSimple](storsimple-deployment-walkthrough-u2.md).
* Zainstaluj i skonfiguruj StorSimple Snapshot Manager. Aby uzyskać więcej informacji, przejdź do [programu Deploy StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

#### <a name="to-configure-backup-of-a-basic-volume"></a>Aby skonfigurować kopię zapasową woluminu podstawowego
1. Utwórz wolumin podstawowy na urządzeniu StorSimple.
2. Zamontuj, zaaicjuj i sformatować wolumin zgodnie z opisem w [obszarze Mount volumes](#mount-volumes). 
3. Kliknij ikonę StorSimple Snapshot Manager na pulpicie. Zostanie wyświetlone okno Menedżer migawek StorSimple. 
4. W okienku **Zakres** kliknij prawym przyciskiem myszy węzeł **Woluminy,** a następnie wybierz polecenie **Ponownie przeskanuj woluminy**. Po zakończeniu skanowania w okienku **Wyniki** powinna pojawić się lista woluminów. 
5. W okienku **Wyniki** kliknij prawym przyciskiem myszy wolumin, a następnie wybierz polecenie **Utwórz grupę woluminów**. 
   
    ![Tworzenie grupy woluminów](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Create_volume_group.png) 
6. W oknie dialogowym **Tworzenie grupy woluminów** wpisz nazwę grupy woluminów, przypisz do niej woluminy, a następnie kliknij przycisk **OK**.
7. W okienku **Zakres** rozwiń węzeł **Grupy woluminów.** Nowa grupa woluminów powinna pojawić się w węźle **Grupy woluminów.** 
8. Kliknij prawym przyciskiem myszy nazwę grupy woluminów.
   
   * Aby uruchomić interaktywne zadanie tworzenia kopii zapasowej (na żądanie), kliknij przycisk **Zrób kopię zapasową**. 
   * Aby zaplanować automatyczną kopię zapasową, kliknij przycisk **Utwórz zasady tworzenia kopii zapasowych**. Na stronie **Ogólne** wybierz grupę woluminów z listy. Na stronie **Harmonogram** wprowadź szczegóły harmonogramu. Po zakończeniu kliknij przycisk **OK**. 
9. Aby potwierdzić, że zadanie tworzenia kopii zapasowej zostało uruchomione, rozwiń węzeł **Zadania** w okienku **Zakres,** a następnie kliknij węzeł **Uruchomione.** Lista aktualnie uruchomionych zadań pojawi się w okienku **Wyniki.** 

## <a name="configure-and-back-up-a-dynamic-mirrored-volume"></a>Konfigurowanie dynamicznego woluminu dublowanego i ich utworzenie kopii zapasowej
Wykonaj następujące czynności, aby skonfigurować kopię zapasową woluminu dublowanego dynamicznie:

* Krok 1: Użyj przystawki Zarządzanie dyskami, aby utworzyć wolumin z wiercem lustrzanym. 
* Krok 2: Użyj StorSimple Snapshot Manager, aby skonfigurować kopię zapasową.

### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem:

* Upewnij się, że urządzenie StorSimple i komputer-host są poprawnie skonfigurowane. Aby uzyskać więcej informacji, przejdź do [witryny Lokalne urządzenie StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Zainstaluj i skonfiguruj StorSimple Snapshot Manager. Aby uzyskać więcej informacji, przejdź do [programu Deploy StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).
* Skonfiguruj dwa woluminy na urządzeniu StorSimple. (W przykładach dostępne woluminy to **Dysk 1** i **Dysk 2.)** 

### <a name="step-1-use-disk-management-to-create-a-dynamic-mirrored-volume"></a>Krok 1: Użyj przystawki Zarządzanie dyskami, aby utworzyć dynamiczny wolumin dublowany
Zarządzanie dyskami to narzędzie systemowe do zarządzania dyskami twardymi i woluminami lub partycjami, które zawierają. Aby uzyskać więcej informacji na temat zarządzania dyskami, przejdź do [przystawki Zarządzanie dyskami](https://technet.microsoft.com/library/cc770943.aspx) w witrynie microsoft technet w sieci Web.

#### <a name="to-create-a-dynamic-mirrored-volume"></a>Aby utworzyć wolumin z dynamicznym odbiciem lustrzanym
1. Aby uruchomić zarządzanie dyskami, użyj dowolnej z następujących opcji: 
   
   * Otwórz pole **Uruchom,** wpisz **diskmgmt.msc**i naciśnij klawisz Enter.
   * Uruchom Menedżera serwera, rozwiń węzeł **Magazyn,** a następnie wybierz pozycję **Zarządzanie dyskami**. 
   * Uruchom **narzędzia administracyjne**, rozwiń węzeł **Zarządzanie komputerem,** a następnie wybierz pozycję **Zarządzanie dyskami**. 
2. Upewnij się, że masz dwa woluminy dostępne na urządzeniu StorSimple. (W tym przykładzie dostępne woluminy to **Dysk 1** i **Dysk 2.)** 
3. W oknie Zarządzanie dyskami w prawej kolumnie dolnego okienka kliknij prawym przyciskiem myszy pozycję **Dysk 1** i wybierz polecenie **Nowy wolumin dublowany**. 
   
    ![Nowy wolumin dublowany](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_New_mirrored_volume.png) 
4. Na stronie **Kreatora nowego woluminu dublowanego** kliknij przycisk **Dalej**.
5. Na stronie **Wybieranie dysków** wybierz pozycję **Dysk 2** w **wybranym** okienku, kliknij pozycję **Dodaj**, a następnie kliknij przycisk **Dalej**. 
6. Na stronie **Przypisywanie litery dysku lub Ścieżka** zaakceptuj wartości domyślne, a następnie kliknij przycisk **Dalej**. 
7. Na stronie **Formatowanie woluminu** w polu **Rozmiar jednostki alokacji** wybierz pozycję **64K**. Zaznacz pole wyboru **Wykonaj szybki format,** a następnie kliknij przycisk **Dalej**. 
8. Na stronie **Kończenie nowego woluminu dublowanego** przejrzyj ustawienia, a następnie kliknij przycisk **Zakończ**. 
9. Pojawia się komunikat wskazujący, że dysk podstawowy zostanie przekonwertowany na dysk dynamiczny. Kliknij **przycisk Tak**.
   
    ![Komunikat o konwersji dysku dynamicznego](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Disk_management_msg.png) 
10. W obszarze Zarządzanie dyskami sprawdź, czy dyski 1 i dysk 2 są wyświetlane jako woluminy dublowane dynamicznie. **(Dynamiczny** powinien pojawić się w kolumnie stanu, a kolor paska pojemności powinien zmienić kolor na czerwony, wskazując wolumin dublowany). 
    
    ![Zarządzanie dyskami dublowane dyski dynamiczne](./media/storsimple-snapshot-manager-manage-volumes/HCS_SSM_Verify_dynamic_disks_2.png) 

### <a name="step-2-use-storsimple-snapshot-manager-to-configure-backup"></a>Krok 2: Konfigurowanie kopii zapasowej za pomocą Menedżera migawek StorSimple
Poniższa procedura służy do konfigurowania woluminu dublowanego dynamicznie, a następnie natychmiast uruchamiaj kopię zapasową lub utwórz zasady dla zaplanowanych kopii zapasowych.

#### <a name="to-configure-backup-of-a-dynamic-mirrored-volume"></a>Aby skonfigurować kopię zapasową woluminu dublowanego dynamicznego
1. Kliknij ikonę StorSimple Snapshot Manager na pulpicie. Zostanie wyświetlone okno Menedżer migawek StorSimple. 
2. W okienku **Zakres** kliknij prawym przyciskiem myszy węzeł **Woluminy** i wybierz polecenie **Ponownieskanuj woluminy**. Po zakończeniu skanowania w okienku **Wyniki** powinna pojawić się lista woluminów. Wolumin z woluminem odbiciem lustrzanym dynamicznym jest wyświetlany jako pojedynczy wolumin. 
3. W okienku **Wyniki** kliknij prawym przyciskiem myszy wolumin z woluminem dublowanym dynamicznie, a następnie kliknij polecenie **Utwórz grupę woluminów**. 
4. W oknie dialogowym **Tworzenie grupy woluminów** wpisz nazwę grupy woluminów, przypisz dynamiczny wolumin dublowany do tej grupy, a następnie kliknij przycisk **OK**. 
5. W okienku **Zakres** rozwiń węzeł **Grupy woluminów.** Nowa grupa woluminów powinna pojawić się w węźle **Grupy woluminów.** 
6. Kliknij prawym przyciskiem myszy nazwę grupy woluminów. 
   
   * Aby uruchomić interaktywne zadanie tworzenia kopii zapasowej (na żądanie), kliknij przycisk **Zrób kopię zapasową**. 
   * Aby zaplanować automatyczną kopię zapasową, kliknij przycisk **Utwórz zasady tworzenia kopii zapasowych**. Na stronie **Ogólne** wybierz grupę woluminów z listy. Na stronie **Harmonogram** wprowadź szczegóły harmonogramu. Po zakończeniu kliknij przycisk **OK**. 
7. Zadanie tworzenia kopii zapasowej można monitorować podczas jego wykonywania. W okienku **Zakres** rozwiń węzeł **Zadania,** a następnie kliknij pozycję **Uruchomione**, szczegóły zadania zostaną wyświetlone w okienku **Wyniki.** Po zakończeniu zadania tworzenia kopii zapasowej szczegóły są przenoszone do listy zadań **ostatnie 24** godziny. 

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [zarządzać rozwiązaniem StorSimple, za pomocą Menedżera migawek StorSimple.](storsimple-snapshot-manager-admin.md)
* Dowiedz się, jak [tworzyć grupy woluminów za pomocą Menedżera migawek StorSimple.](storsimple-snapshot-manager-manage-volume-groups.md)

<!--Reference links-->
[1]: https://msdn.microsoft.com/library/ee338480(v=ws.10).aspx
