---
title: Katalog kopii zapasowej programu StorSimple Snapshot Manager | Dokumentacja firmy Microsoft
description: Informacje dotyczące używania przystawki MMC programu StorSimple Snapshot Manager można wyświetlać i zarządzać nimi katalogu kopii zapasowej.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 6abdbfd2-22ce-45a5-aa15-38fae4c8f4ec
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: b97753e6f1b67e3c8d247281c5e5208033a56eca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876712"
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Użyj StorSimple Snapshot Manager do zarządzania katalogu kopii zapasowej

## <a name="overview"></a>Omówienie
Podstawową funkcją StorSimple Snapshot Manager jest pozwalają tworzyć w formie migawek spójnych z aplikacją kopii zapasowych woluminów StorSimple. Migawki są następnie wyświetlane w pliku XML o nazwie *katalogu kopii zapasowej*. Katalog kopii zapasowej organizuje migawek woluminu grupy, a następnie według migawka lokalna i migawka w chmurze.

W tym samouczku opisano, jak używasz **katalog kopii zapasowej** węzeł, aby wykonać następujące zadania:

* Przywracanie woluminu
* Klonowanie woluminu lub grupy woluminu
* Usuwanie kopii zapasowej
* Odzyskiwanie pliku
* Przywróć bazę danych programu Storsimple Snapshot Manager

Katalog kopii zapasowej można wyświetlić, rozwijając **katalogu kopii zapasowej** w węźle **zakres** okienka, a następnie rozszerzania grupy woluminu.

* Jeśli klikniesz przycisk nazwę grupy woluminu **wyniki** okienko pokazuje liczbę migawek lokalnych i dostępnych dla grupy woluminu migawki w chmurze. 
* Jeśli klikniesz przycisk **migawka lokalna** lub **migawka w chmurze**, **wyniki** w okienku zostaną wyświetlone następujące informacje dotyczące każdej kopii zapasowej migawki (w zależności od Twojego  **Wyświetl** ustawienia):
  
  * **Nazwa** — czas migawki.
  * **Typ** — czy jest to migawka lokalna i migawka w chmurze.
  * **Właściciel** — właściciel zawartości. 
  * **Dostępne** — czy migawka jest obecnie dostępna. **Wartość true,** wskazuje, że migawki jest dostępny i czy można przywrócić; **False** wskazuje, że migawka nie jest już dostępny. 
  * **Zaimportowane** — czy zaimportowano kopii zapasowej. **Wartość true,** wskazuje, czy kopia zapasowa została zaimportowana z usługi Menedżer StorSimple urządzenia w chwili urządzenie zostało skonfigurowane w programie StorSimple Snapshot Manager; **False** wskazuje, że nie został zaimportowany, ale został utworzony przez StorSimple Snapshot Manager. (Można łatwo zidentyfikować grupy woluminu zaimportowany, ponieważ jest dodawany sufiks identyfikujący urządzenie, z której zaimportowano grupy woluminu.)
    
    ![Katalog kopii zapasowej](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* Po rozwinięciu **migawka lokalna** lub **migawka w chmurze**, a następnie kliknij nazwę migawki poszczególnych **wyniki** w okienku zostaną wyświetlone następujące informacje dotyczące migawki czy wybrane:
  
  * **Nazwa** — określone przez literę dysku woluminu. 
  * **Lokalna nazwa** — lokalna nazwa dysku (jeśli jest dostępna). 
  * **Urządzenie** — nazwa urządzenia, na którym znajduje się wolumin. 
  * **Dostępne** — czy migawka jest obecnie dostępna. **Wartość true,** wskazuje, że migawki jest dostępny i czy można przywrócić; **False** wskazuje, że migawka nie jest już dostępny. 

## <a name="restore-a-volume"></a>Przywracanie woluminu
Poniższa procedura umożliwia przywrócenie z kopii zapasowej woluminu.

#### <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie zostało to jeszcze zrobione, Utwórz wolumin i grupy woluminu, a następnie Usuń wolumin. Domyślnie StorSimple Snapshot Manager kopię zapasową woluminu przed pozwalający na usunięcie. W ten sposób można zapobiec utracie danych, jeśli wolumin zostanie przypadkowo usunięty lub jeśli dane mają zostać odzyskane różnych przyczyn. 

Podczas tworzenia kopii zapasowej ostrożności StorSimple Snapshot Manager zostanie wyświetlony komunikat.

![Komunikat automatyczne migawki](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> Nie można usunąć woluminu, który jest częścią grupy woluminu. Opcja usuwania jest niedostępna. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>Aby przywrócić woluminu
1. Kliknij ikonę pulpitu, aby uruchomić StorSimple Snapshot Manager. 
2. W **zakres** okienku rozwiń **katalog kopii zapasowej** węzła, rozwiń grupę woluminu, a następnie kliknij przycisk **migawki lokalne** lub **migawki w chmurze**. Zostanie wyświetlona lista migawek kopii zapasowych w **wyniki** okienka.
3. Znajdź kopię zapasową, której chcesz przywrócić, kliknij prawym przyciskiem myszy, a następnie kliknij przycisk **przywrócić**.
   
    ![Przywracanie kopii zapasowej katalogu](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. Na stronie Potwierdzenie Przejrzyj szczegóły, typ **Potwierdź**, a następnie kliknij przycisk **OK**. StorSimple Snapshot Manager używa kopii zapasowej do przywrócenia woluminu.
   
    ![Potwierdzenie przywracania](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. Akcja przywracania można monitorować podczas jego wykonywania. W **zakres** okienku rozwiń **zadania** węzeł, a następnie kliknij przycisk **systemem**. Szczegóły zadania są wyświetlane w **wyniki** okienka. Po zakończeniu zadania przywracania, szczegóły zadania są przenoszone do **ostatnich 24 godzin** listy.

## <a name="clone-a-volume-or-volume-group"></a>Klonowanie woluminu lub grupy woluminu
Poniższa procedura umożliwia utworzenie duplikat (klonowania) woluminu lub grupy woluminu.

#### <a name="to-clone-a-volume-or-volume-group"></a>Klonowanie woluminu lub grupy woluminu
1. Kliknij ikonę pulpitu, aby uruchomić StorSimple Snapshot Manager.
2. W **zakres** okienku rozwiń **katalog kopii zapasowej** węzła, rozwiń grupę woluminu, a następnie kliknij przycisk **migawki w chmurze**. Zostanie wyświetlona lista kopii zapasowych w **wyniki** okienka.
3. Znajdź woluminu lub grupy woluminu, który chcesz sklonować, kliknij prawym przyciskiem myszy wolumin lub nazwę grupy woluminu, a następnie kliknij przycisk **klonowania**. **Migawka w chmurze klonowania** zostanie wyświetlone okno dialogowe.
   
    ![Klonowanie migawka w chmurze](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Zakończenie **migawka w chmurze klonowania** okno dialogowe w następujący sposób: 
   
   1. W **nazwa** polu tekstowym, wpisz nazwę dla sklonowanego woluminu. Ta nazwa będzie wyświetlana w **woluminów** węzła. 
   2. (Opcjonalnie) zaznacz **dysku**, a następnie wybierz literę dysku z listy rozwijanej.
   3. (Opcjonalnie) zaznacz **folderu NTFS**i wpisz ścieżkę folderu, lub kliknij przycisk Przeglądaj i wybierz lokalizację folderu. 
   4. Kliknij przycisk **Utwórz**.
5. Po zakończeniu procesu klonowania, należy zainicjować sklonowany woluminu. Uruchom Menedżera serwera, a następnie uruchom Zarządzanie dyskami. Aby uzyskać szczegółowe instrukcje, zobacz [instalowania woluminów](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Po zainicjowaniu, wolumin będzie wyświetlane w obszarze **woluminów** w węźle **zakres** okienka. Jeśli nie ma woluminu na liście, Odśwież listę woluminów (kliknij prawym przyciskiem myszy **woluminów** węzeł, a następnie kliknij przycisk **Odśwież**).

## <a name="delete-a-backup"></a>Usuwanie kopii zapasowej
Użyj poniższej procedury można usunąć migawki z katalogu kopii zapasowej. 

> [!NOTE]
> Usunięcie migawki spowoduje usunięcie skojarzonych z migawki kopii zapasowej danych. Jednak proces wyczyszczenie danych z chmury może potrwać pewien czas.<br>


#### <a name="to-delete-a-backup"></a>Aby usunąć kopię zapasową
1. Kliknij ikonę pulpitu, aby uruchomić StorSimple Snapshot Manager.
2. W **zakres** okienku rozwiń **katalog kopii zapasowej** węzła, rozwiń grupę woluminu, a następnie kliknij przycisk **migawki lokalne** lub **migawki w chmurze**. Zostanie wyświetlona lista migawek w **wyniki** okienka.
3. Kliknij prawym przyciskiem myszy migawek, ma zostać usunięty, a następnie kliknij przycisk **usunąć**.
4. Po wyświetleniu komunikatu potwierdzenia kliknij **OK**.

## <a name="recover-a-file"></a>Odzyskiwanie pliku
Jeśli plik zostanie przypadkowo usunięty z woluminu, można odzyskać pliku, pobieranie migawki, która wstępnie daty usunięcia, za pomocą migawki na tworzenie własnego klonu woluminu, a następnie skopiować plik z sklonowany woluminu do oryginalnego woluminu.

#### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że masz kopię zapasową bieżącej grupy woluminu. Następnie należy usunąć plik przechowywane na jednym z woluminów w tej grupie woluminu. Na koniec wykonaj następujące kroki, aby przywrócić usunięty plik z kopii zapasowej. 

#### <a name="to-recover-a-deleted-file"></a>Aby odzyskać usuniętego pliku
1. Kliknij ikonę StorSimple Snapshot Manager na pulpicie. Zostanie wyświetlone okno konsoli programu StorSimple Snapshot Manager. 
2. W **zakres** okienku rozwiń **katalog kopii zapasowej** węzła i przejdź do migawki, która zawiera usuniętego pliku. Zazwyczaj należy zaznaczyć migawkę, który został utworzony bezpośrednio przed usunięcia.
3. Znajdź wolumin, który ma zostać sklonowany, kliknij prawym przyciskiem myszy, a następnie kliknij przycisk **klonowania**. **Migawka w chmurze klonowania** zostanie wyświetlone okno dialogowe.
   
    ![Klonowanie migawka w chmurze](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Zakończenie **migawka w chmurze klonowania** okno dialogowe w następujący sposób: 
   
   1. W **nazwa** polu tekstowym, wpisz nazwę dla sklonowanego woluminu. Ta nazwa będzie wyświetlana w **woluminów** węzła. 
   2. (Opcjonalnie) Wybierz **dysku**, a następnie wybierz literę dysku z listy rozwijanej. 
   3. (Opcjonalnie) Wybierz **folderu NTFS**i wpisz ścieżkę folderu, lub kliknij przycisk **Przeglądaj** i wybierz lokalizację folderu. 
   4. Kliknij przycisk **Utwórz**. 
5. Po zakończeniu procesu klonowania, należy zainicjować sklonowany woluminu. Uruchom Menedżera serwera, a następnie uruchom Zarządzanie dyskami. Aby uzyskać szczegółowe instrukcje, zobacz [instalowania woluminów](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Po zainicjowaniu, wolumin będzie wyświetlane w obszarze **woluminów** w węźle **zakres** okienka. 
   
    Jeśli nie ma woluminu na liście, Odśwież listę woluminów (kliknij prawym przyciskiem myszy **woluminów** węzeł, a następnie kliknij przycisk **Odśwież**).
6. Otwórz folderu NTFS, który zawiera wolumin sklonowany, rozwiń węzeł **woluminów** węzeł, a następnie otwórz sklonowany woluminu. Znajdź plik, który chcesz odzyskać, a następnie skopiować go do woluminu podstawowego.
7. Po przywróceniu plików, można usunąć folderu NTFS, który zawiera sklonowany woluminu.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Przywróć bazę danych programu StorSimple Snapshot Manager
Należy regularnie wykonywać kopie zapasowe bazy danych programu StorSimple Snapshot Manager na komputerze hosta. Jeśli komputer-host nie powiedzie się z jakiegokolwiek powodu katastrofy lub poważnej awarii, można przywrócić go z kopii zapasowej. Tworzenie kopii zapasowej bazy danych jest proces ręczny.

#### <a name="to-back-up-and-restore-the-database"></a>Tworzenie kopii zapasowej i przywracanie bazy danych
1. Zatrzymaj usługę zarządzania Microsoft StorSimple:
   
   1. Uruchom Menedżera serwera.
   2. Na pulpicie nawigacyjnym Menedżera serwera na **narzędzia** menu, wybierz opcję **usług**.
   3. Na **usług** wybierz **usługi zarządzania StorSimple Microsoft**.
   4. W prawym okienku w obszarze **usługi zarządzania StorSimple Microsoft**, kliknij przycisk **Zatrzymaj usługę**.
2. Na komputerze hosta przejdź do C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > Folder ProgramData jest folderem ukrytym.
   > 
   > 
3. Znajdź plik XML katalogu, skopiuj plik i przechowywać kopię w bezpiecznym miejscu lub w chmurze. Jeśli host nie powiedzie się, można użyć tego pliku kopii zapasowej do odzyskiwania zasad tworzenia kopii zapasowych, które zostały utworzone w programie StorSimple Snapshot Manager.
   
    ![Plik kopii zapasowej katalogu usługi Azure StorSimple](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Uruchom ponownie usługę zarządzania Microsoft StorSimple: 
   
   1. Na pulpicie nawigacyjnym Menedżera serwera na **narzędzia** menu, wybierz opcję **usług**.
   2. Na **usług** wybierz **usługi zarządzania StorSimple Microsoft**.
   3. W prawym okienku w obszarze **usługi zarządzania StorSimple Microsoft**, kliknij przycisk **Uruchom ponownie usługę**.
5. Na komputerze hosta przejdź do C:\ProgramData\Microsoft\StorSimple\BACatalog. 
6. Usuń plik XML katalogu i zastąp go wersji w kopii zapasowej, który został utworzony. 
7. Kliknij ikonę pulpitu StorSimple Snapshot Manager można uruchomić programu StorSimple Snapshot Manager. 

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [przy użyciu programu StorSimple Snapshot Manager do administrowania rozwiązania StorSimple](storsimple-snapshot-manager-admin.md).
* Dowiedz się więcej o [StorSimple Snapshot Manager zadań i przepływów pracy](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).

