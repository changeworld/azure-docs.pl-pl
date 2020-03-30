---
title: Katalog kopii zapasowych Menedżera migawek StorSimple | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób używania przystawki PROGRAMU MMC Menedżera migawek StorSimple do wyświetlania katalogu kopii zapasowych i zarządzania nim.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 6abdbfd2-22ce-45a5-aa15-38fae4c8f4ec
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 38ef7774263e4b28b7c316fd0870ca8f7b89d6b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931706"
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Zarządzanie katalogiem kopii zapasowych za pomocą Menedżera migawek StorSimple

## <a name="overview"></a>Omówienie
Podstawową funkcją StorSimple Snapshot Manager jest umożliwienie tworzenia spójnych aplikacji kopii zapasowych woluminów StorSimple w postaci migawek. Migawki są następnie wyświetlane w pliku XML o nazwie *katalog kopii zapasowych*. Katalog kopii zapasowych organizuje migawki według grupy woluminów, a następnie przez migawkę lokalną lub migawkę w chmurze.

W tym samouczku opisano, jak można wykonać następujące zadania za pomocą **węzła Wykaz kopii zapasowych:**

* Przywracanie woluminu
* Klonowanie grupy woluminów lub woluminów
* Usuwanie kopii zapasowej
* Odzyskiwanie pliku
* Przywracanie bazy danych Menedżera migawek storasimple

Katalog kopii zapasowych można wyświetlić, rozwiń węzeł **Wykaz kopii zapasowych** w okienku **Zakres,** a następnie rozwiń grupę woluminów.

* Po kliknięciu nazwy grupy woluminów w okienku **Wyniki** zostanie wyświetleń liczbę migawek lokalnych i migawek w chmurze dostępnych dla grupy woluminów. 
* Jeśli klikniesz **pozycję Migawka lokalna** lub **Migawka w chmurze,** w okienku **Wyniki** zostaną wyświetlenia następujące informacje o każdej migawce kopii zapasowej (w zależności od ustawień **widoku):**
  
  * **Nazwa** — czas, w której zrobiono migawkę.
  * **Typ** — czy jest to migawka lokalna, czy migawka w chmurze.
  * **Właściciel** – właściciel treści. 
  * **Dostępne** — czy migawka jest obecnie dostępna. **True** wskazuje, że migawka jest dostępna i może zostać przywrócona; **False** wskazuje, że migawka nie jest już dostępna. 
  * **Importowane** — czy kopia zapasowa została zaimportowana. **True** oznacza, że kopia zapasowa została zaimportowana z usługi StorSimple Device Manager w czasie, gdy urządzenie zostało skonfigurowane w Menedżerze migawek StorSimple; **False** wskazuje, że nie został zaimportowany, ale został utworzony przez StorSimple Snapshot Manager. (Można łatwo zidentyfikować zaimportowane grupy woluminów, ponieważ dodano sufiks identyfikujący urządzenie, z którego grupa woluminów została zaimportowana).
    
    ![Katalog kopii zapasowych](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* Jeśli rozwiniesz **migawkę lokalną** lub **migawkę w chmurze,** a następnie klikniesz indywidualną nazwę migawki, w okienku **Wyniki** zostaną wyświetlane następujące informacje o wybranej migawce:
  
  * **Nazwa** – wolumin identyfikowany przez literę dysku. 
  * **Nazwa lokalna** — lokalna nazwa dysku (jeśli jest dostępna). 
  * **Urządzenie** – nazwa urządzenia, na którym znajduje się wolumin. 
  * **Dostępne** — czy migawka jest obecnie dostępna. **True** wskazuje, że migawka jest dostępna i może zostać przywrócona; **False** wskazuje, że migawka nie jest już dostępna. 

## <a name="restore-a-volume"></a>Przywracanie woluminu
Poniższa procedura umożliwia przywrócenie woluminu z kopii zapasowej.

#### <a name="prerequisites"></a>Wymagania wstępne
Jeśli jeszcze tego nie zrobiono, utwórz grupę woluminów i woluminów, a następnie usuń wolumin. Domyślnie StorSimple Snapshot Manager kopii zapasowej woluminu przed zezwoleniem na jego usunięcie. Ten środek ostrożności może zapobiec utracie danych, jeśli wolumin zostanie usunięty nieumyślnie lub jeśli dane muszą zostać odzyskane z jakiegokolwiek powodu. 

StorSimple Snapshot Manager wyświetla następujący komunikat podczas tworzenia kopii zapasowej ostrożności.

![Automatyczny komunikat migawki](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> Nie można usunąć woluminu, który jest częścią grupy woluminów. Opcja usuwania jest niedostępna. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>Aby przywrócić wolumin
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple. 
2. W okienku **Zakres** rozwiń węzeł **Wykaz kopii zapasowych,** rozwiń grupę woluminów, a następnie kliknij pozycję **Migawki lokalne** lub **Migawki w chmurze**. W okienku **Wyniki** pojawi się lista migawek kopii zapasowej.
3. Znajdź kopię zapasową, którą chcesz przywrócić, kliknij prawym przyciskiem myszy, a następnie kliknij polecenie **Przywróć**.
   
    ![Przywracanie katalogu kopii zapasowych](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. Na stronie potwierdzenia przejrzyj szczegóły, wpisz **potwierdź**, a następnie kliknij przycisk **OK**. StorSimple Snapshot Manager używa kopii zapasowej, aby przywrócić wolumin.
   
    ![Przywróć komunikat potwierdzający](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. Akcję przywracania można monitorować podczas jej działania. W okienku **Zakres** rozwiń węzeł **Zadania,** a następnie kliknij pozycję **Uruchomiona**. Szczegóły zadania są wyświetlane w okienku **Wyniki.** Po zakończeniu zadania przywracania szczegóły zadania są przenoszone do listy **Ostatnie 24 godziny.**

## <a name="clone-a-volume-or-volume-group"></a>Klonowanie grupy woluminów lub woluminów
Poniższa procedura służy do tworzenia duplikatu (klonowania) grupy woluminów lub woluminów.

#### <a name="to-clone-a-volume-or-volume-group"></a>Aby sklonować wolumin lub grupę woluminów
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple.
2. W okienku **Zakres** rozwiń węzeł **Wykaz kopii zapasowych,** rozwiń grupę woluminów, a następnie kliknij pozycję **Migawki w chmurze**. W okienku **Wyniki** pojawi się lista kopii zapasowych.
3. Znajdź grupę woluminów lub woluminów, które chcesz sklonować, kliknij prawym przyciskiem myszy nazwę woluminu lub grupy woluminów, a następnie kliknij polecenie **Klonuj**. Zostanie wyświetlone okno dialogowe **Klonowanie migawki chmury.**
   
    ![Klonowanie migawki w chmurze](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Wypełnij okno dialogowe **Klonowanie migawki chmury** w następujący sposób: 
   
   1. W polu tekstowym **Nazwa** wpisz nazwę sklonowanego woluminu. Ta nazwa pojawi się w węźle **Woluminy.** 
   2. (Opcjonalnie) wybierz **pozycję Dysk**, a następnie wybierz literę dysku z listy rozwijanej.
   3. (Opcjonalnie) wybierz **folder (NTFS)** i wpisz ścieżkę folderu lub kliknij przycisk Przeglądaj i wybierz lokalizację dla folderu. 
   4. Kliknij przycisk **Utwórz**.
5. Po zakończeniu procesu klonowania należy zainicjować sklonowany wolumin. Uruchom Menedżera serwera, a następnie uruchom narzędzie Zarządzanie dyskami. Aby uzyskać szczegółowe instrukcje, zobacz [Instalowanie woluminów](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Po zainicjowaniu wolumin zostanie wyświetlony w węźle **Woluminy** w okienku **Zakres.** Jeśli wolumin nie jest widoczny na liście, odśwież listę woluminów (kliknij prawym przyciskiem myszy węzeł **Woluminy,** a następnie kliknij polecenie **Odśwież**).

## <a name="delete-a-backup"></a>Usuwanie kopii zapasowej
Poniższa procedura służy do usuwania migawki z katalogu kopii zapasowych. 

> [!NOTE]
> Usunięcie migawki powoduje usunięcie kopii zapasowej danych skojarzonych z migawką. Jednak proces czyszczenia danych z chmury może zająć trochę czasu.<br>


#### <a name="to-delete-a-backup"></a>Aby usunąć kopię zapasową
1. Kliknij ikonę pulpitu, aby uruchomić Menedżera migawek StorSimple.
2. W okienku **Zakres** rozwiń węzeł **Wykaz kopii zapasowych,** rozwiń grupę woluminów, a następnie kliknij pozycję **Migawki lokalne** lub **Migawki w chmurze**. W okienku **Wyniki** pojawi się lista migawek.
3. Kliknij prawym przyciskiem myszy migawkę, którą chcesz usunąć, a następnie kliknij polecenie **Usuń**.
4. Po wyświetleniu komunikatu potwierdzającego kliknij przycisk **OK**.

## <a name="recover-a-file"></a>Odzyskiwanie pliku
Jeśli plik zostanie przypadkowo usunięty z woluminu, można go odzyskać, pobierając migawkę poprzedzającą usunięcie, używając migawki do utworzenia klonu woluminu, a następnie kopiując plik ze sklonowanego woluminu do oryginalnego woluminu.

#### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że masz bieżącą kopię zapasową grupy woluminów. Następnie usuń plik przechowywany na jednym z woluminów w tej grupie woluminów. Na koniec wykonaj następujące czynności, aby przywrócić usunięty plik z kopii zapasowej. 

#### <a name="to-recover-a-deleted-file"></a>Aby odzyskać usunięty plik
1. Kliknij ikonę StorSimple Snapshot Manager na pulpicie. Zostanie wyświetlone okno Konsoli Menedżera migawek StorSimple. 
2. W okienku **Zakres** rozwiń węzeł **Wykaz kopii zapasowych** i przejdź do migawki zawierającej usunięty plik. Zazwyczaj należy wybrać migawkę, która została utworzona tuż przed usunięciem.
3. Znajdź wolumin, który chcesz sklonować, kliknij prawym przyciskiem myszy i kliknij polecenie **Klonuj**. Zostanie wyświetlone okno dialogowe **Klonowanie migawki chmury.**
   
    ![Klonowanie migawki w chmurze](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Wypełnij okno dialogowe **Klonowanie migawki chmury** w następujący sposób: 
   
   1. W polu tekstowym **Nazwa** wpisz nazwę sklonowanego woluminu. Ta nazwa pojawi się w węźle **Woluminy.** 
   2. (Opcjonalnie) Wybierz **pozycję Dysk**, a następnie wybierz literę dysku z listy rozwijanej. 
   3. (Opcjonalnie) Wybierz **folder (NTFS)** i wpisz ścieżkę folderu lub kliknij przycisk **Przeglądaj** i wybierz lokalizację dla folderu. 
   4. Kliknij przycisk **Utwórz**. 
5. Po zakończeniu procesu klonowania należy zainicjować sklonowany wolumin. Uruchom Menedżera serwera, a następnie uruchom narzędzie Zarządzanie dyskami. Aby uzyskać szczegółowe instrukcje, zobacz [Instalowanie woluminów](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Po zainicjowaniu wolumin zostanie wyświetlony w węźle **Woluminy** w okienku **Zakres.** 
   
    Jeśli wolumin nie jest widoczny na liście, odśwież listę woluminów (kliknij prawym przyciskiem myszy węzeł **Woluminy,** a następnie kliknij polecenie **Odśwież**).
6. Otwórz folder NTFS zawierający sklonowany wolumin, rozwiń węzeł **Woluminy,** a następnie otwórz sklonowany wolumin. Znajdź plik, który chcesz odzyskać, i skopiuj go do woluminu podstawowego.
7. Po przywróceniu pliku można usunąć folder NTFS zawierający sklonowany wolumin.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Przywracanie bazy danych Menedżera migawek StorSimple
Należy regularnie wywrzeć zapas bazy danych StorSimple Snapshot Manager na komputerze-hoście. Jeśli wystąpi awaria lub komputer-host ulegnie awarii z jakiegokolwiek powodu, można go przywrócić z kopii zapasowej. Tworzenie kopii zapasowej bazy danych jest procesem ręcznym.

#### <a name="to-back-up-and-restore-the-database"></a>Aby przywrócić kopię zapasową i przywrócić bazę danych
1. Zatrzymaj usługę zarządzania Microsoft StorSimple:
   
   1. Uruchom Menedżera serwera.
   2. Na pulpicie nawigacyjnym Menedżera serwera w menu **Narzędzia** wybierz polecenie **Usługi**.
   3. W oknie **Usługi** wybierz pozycję **Microsoft StorSimple Management Service**.
   4. W prawym okienku w obszarze **Microsoft StorSimple Management Service**kliknij pozycję **Zatrzymaj usługę**.
2. Na komputerze-hoście przejdź do folderu C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > FolderProgramData jest folderem ukrytym.
   > 
   > 
3. Znajdź katalogowy plik XML, skopiuj plik i przechowuj ją w bezpiecznym miejscu lub w chmurze. Jeśli host ulegnie awarii, można użyć tego pliku kopii zapasowej, aby odzyskać zasady kopii zapasowej utworzone w Menedżerze migawek StorSimple.
   
    ![Plik katalogu kopii zapasowych usługi Azure StorSimple](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Uruchom ponownie usługę zarządzania microsoft storsimple: 
   
   1. Na pulpicie nawigacyjnym Menedżera serwera w menu **Narzędzia** wybierz polecenie **Usługi**.
   2. W oknie **Usługi** wybierz pozycję **Microsoft StorSimple Management Service**.
   3. W prawym okienku w obszarze **Microsoft StorSimple Management Service**kliknij pozycję Uruchom ponownie **usługę**.
5. Na komputerze-hoście przejdź do folderu C:\ProgramData\Microsoft\StorSimple\BACatalog. 
6. Usuń plik XML katalogu i zastąp go utworzoną wersją kopii zapasowej. 
7. Kliknij ikonę Menedżera migawek storasty na pulpicie, aby uruchomić Menedżera migawek StorSimple. 

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [administrowaniu rozwiązaniem StorSimple za pomocą Menedżera migawek StorSimple.](storsimple-snapshot-manager-admin.md)
* Dowiedz się więcej o [zadaniach i przepływach pracy Menedżera migawek StorSimple](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).

