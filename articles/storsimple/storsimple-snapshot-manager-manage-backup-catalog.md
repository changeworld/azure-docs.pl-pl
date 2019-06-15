---
title: Wykaz kopii zapasowych programu StorSimple Snapshot Manager | Dokumentacja firmy Microsoft
description: Opisuje sposób używania przystawki MMC przystawki StorSimple Snapshot Manager do wyświetlania i Zarządzanie wykazem kopii zapasowych.
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
ms.openlocfilehash: dc24ebd59fd977ef35766c304aec5824e2c7bb4c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127192"
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>Użyj StorSimple Snapshot Manager Zarządzanie wykazem kopii zapasowych

## <a name="overview"></a>Omówienie
Podstawowa funkcja programu StorSimple Snapshot Manager jest pozwala na tworzenie kopii zapasowych spójnych z aplikacją woluminów StorSimple w postaci migawki. Migawki są następnie wyświetlane w pliku XML o nazwie *wykaz kopii zapasowych*. Wykaz kopii zapasowych organizuje migawek według grupy woluminów, a następnie przez lokalne migawki lub migawka w chmurze.

W tym samouczku opisano, jak można użyć **katalog kopii zapasowej** węzeł, aby wykonać następujące zadania:

* Przywracanie woluminu
* Klonowanie woluminu lub wolumin grupy
* Usuwanie kopii zapasowej
* Odzyskiwanie pliku
* Przywracanie bazy danych programu Storsimple Snapshot Manager

Wykaz kopii zapasowych można wyświetlić, rozwijając **wykaz kopii zapasowych** w węźle **zakres** okienku, a następnie rozwijając grupy woluminu.

* Jeśli klikniesz nazwę grupy woluminu **wyniki** okienko pokazuje liczbę migawki lokalne i migawki w chmurze dostępna dla grupy woluminu. 
* Jeśli klikniesz **migawka lokalna** lub **migawki w chmurze**, **wyniki** okienku są wyświetlane następujące informacje na temat każdego migawki kopii zapasowej (w zależności od Twojej  **Wyświetl** ustawienia):
  
  * **Nazwa** — czas migawka została utworzona.
  * **Typ** — Określa, czy jest to migawka lokalna lub migawkę w chmurze.
  * **Właściciel** — właściciel zawartości. 
  * **Dostępne** — Określa, czy migawka jest obecnie dostępna. **Wartość true,** wskazuje, że migawka jest dostępna i mogą zostać przywrócone; **False** wskazuje, że migawki nie jest już dostępna. 
  * **Zaimportowane** — Określa, czy kopia zapasowa została zaimportowana. **Wartość true,** wskazuje, że kopia zapasowa została zaimportowana z usługi Menedżer urządzeń StorSimple w tym czasie urządzenie zostało skonfigurowane w programie StorSimple Snapshot Manager; **False** wskazuje, że nie zostały zaimportowane, ale został utworzony przez Menedżera migawek StorSimple. (Można łatwo zidentyfikować grupę woluminu zaimportowane, ponieważ jest dodawany sufiks, że urządzenia, z którego został zaimportowany do grupy woluminów, które identyfikują.)
    
    ![wykaz kopii zapasowych](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* Po rozwinięciu **migawka lokalna** lub **migawki w chmurze**, a następnie kliknij nazwę migawki poszczególnych **wyniki** okienku są wyświetlane następujące informacje na temat migawki czy wybrane:
  
  * **Nazwa** — wolumin, określone przez literę dysku. 
  * **Lokalna nazwa** — lokalna nazwa dysku (jeśli jest dostępny). 
  * **Urządzenie** — nazwa urządzenia, na którym znajduje się woluminu. 
  * **Dostępne** — Określa, czy migawka jest obecnie dostępna. **Wartość true,** wskazuje, że migawka jest dostępna i mogą zostać przywrócone; **False** wskazuje, że migawki nie jest już dostępna. 

## <a name="restore-a-volume"></a>Przywracanie woluminu
Poniższa procedura umożliwia przywrócenie woluminu z kopii zapasowej.

#### <a name="prerequisites"></a>Wymagania wstępne
Jeśli jeszcze tego nie zrobiono, Utwórz wolumin i grupy woluminów, a następnie Usuń wolumin. Domyślnie przystawki StorSimple Snapshot Manager tworzy kopię zapasową woluminu przed zezwalające do usunięcia. W ten sposób można zapobiec utracie danych, jeśli wolumin zostanie przypadkowo usunięty lub jeśli dane muszą zostać odzyskane jakiegokolwiek powodu. 

Przystawki StorSimple Snapshot Manager wyświetla następujący komunikat podczas tworzenia kopii zapasowej ostrożności.

![Komunikat o automatycznych migawek](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> Nie można usunąć woluminu, który jest częścią grupy woluminu. Opcja usuwania jest niedostępna. <br>
> 
> 

#### <a name="to-restore-a-volume"></a>Aby przywrócić woluminu
1. Kliknij ikony pulpitu, które można uruchomić programu StorSimple Snapshot Manager. 
2. W **zakres** okienku rozwiń **katalog kopii zapasowej** węzła, rozwiń grupę woluminu, a następnie kliknij przycisk **migawki lokalne** lub **migawki w chmurze**. Zostanie wyświetlona lista kopii zapasowych migawek w **wyniki** okienka.
3. Odnajdzie kopii zapasowej, który chcesz przywrócić, kliknij prawym przyciskiem myszy, a następnie kliknij **przywrócić**.
   
    ![Przywracanie kopii zapasowej katalogu](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. Na stronie Potwierdzenie Przejrzyj szczegóły, typ **Potwierdź**, a następnie kliknij przycisk **OK**. Przystawki StorSimple Snapshot Manager używa kopii zapasowej do przywrócenia woluminu.
   
    ![Przywróć komunikat z potwierdzeniem](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. Można monitorować akcji przywracania, jak działa. W **zakres** okienku rozwiń **zadania** węzłem, a następnie kliknij przycisk **systemem**. Szczegóły zadania są wyświetlane w **wyniki** okienka. Po zakończeniu zadania przywracania szczegóły zadania są przenoszone do **ostatnie 24 godziny** listy.

## <a name="clone-a-volume-or-volume-group"></a>Klonowanie woluminu lub wolumin grupy
Poniższa procedura umożliwia utworzenie duplikatu (Klonuj) woluminu lub wolumin grupy.

#### <a name="to-clone-a-volume-or-volume-group"></a>Klonowanie woluminu lub wolumin grupy
1. Kliknij ikony pulpitu, które można uruchomić programu StorSimple Snapshot Manager.
2. W **zakres** okienku rozwiń **katalog kopii zapasowej** węzła, rozwiń grupę woluminu, a następnie kliknij przycisk **migawki w chmurze**. Zostanie wyświetlona lista kopii zapasowych w **wyniki** okienka.
3. Znajdź wolumin lub grupy woluminów, które chcesz sklonować, kliknij prawym przyciskiem myszy nazwę woluminu lub wolumin grupy, a następnie kliknij **klonowania**. **Migawki w chmurze klonowania** pojawi się okno dialogowe.
   
    ![Klonowanie migawkę w chmurze](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Wykonaj **migawki w chmurze klonowania** okno dialogowe w następujący sposób: 
   
   1. W **nazwa** polu tekstowym wpisz nazwę sklonowany wolumin. Ta nazwa będzie wyświetlana w **woluminów** węzła. 
   2. (Opcjonalnie) wybierz **dysku**, a następnie wybierz literę dysku z listy rozwijanej.
   3. (Opcjonalnie) wybierz **folderu NTFS**, wpisz ścieżkę folderu lub kliknij przycisk Przeglądaj i wybierz lokalizację folderu. 
   4. Kliknij pozycję **Utwórz**.
5. Po zakończeniu procesu klonowania, musi zostać zainicjowany sklonowany wolumin. Uruchom Menedżera serwera, a następnie uruchom przystawki Zarządzanie dyskami. Aby uzyskać szczegółowe instrukcje, zobacz [zainstalować woluminów](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Po zainicjowaniu, wolumin zostaną wyświetlone w obszarze **woluminów** w węźle **zakres** okienka. Jeśli wolumin, na liście nie jest widoczny, Odśwież listę woluminów (kliknij prawym przyciskiem myszy **woluminów** węzłem, a następnie kliknij przycisk **Odśwież**).

## <a name="delete-a-backup"></a>Usuwanie kopii zapasowej
Użyj poniższej procedury można usunąć migawki z wykazem kopii zapasowych. 

> [!NOTE]
> Usunięcie migawki spowoduje usunięcie danych kopii zapasowej skojarzony z migawki. Jednak proces czyszczenia danych w chmurze może trochę potrwać.<br>


#### <a name="to-delete-a-backup"></a>Aby usunąć kopię zapasową
1. Kliknij ikony pulpitu, które można uruchomić programu StorSimple Snapshot Manager.
2. W **zakres** okienku rozwiń **katalog kopii zapasowej** węzła, rozwiń grupę woluminu, a następnie kliknij przycisk **migawki lokalne** lub **migawki w chmurze**. Zostanie wyświetlona lista migawek w **wyniki** okienka.
3. Kliknij prawym przyciskiem myszy migawkę chcesz usunąć, a następnie kliknij przycisk **Usuń**.
4. Po wyświetleniu komunikatu potwierdzenia kliknij **OK**.

## <a name="recover-a-file"></a>Odzyskiwanie pliku
Jeśli plik zostanie przypadkowo usunięty z woluminu, można odzyskać pliku, pobieranie migawki, który wstępnie daty usunięcia, sklonować woluminu za pomocą migawki i następnie kopiowania pliku z sklonowany wolumin do oryginalnego woluminu.

#### <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że bieżąca kopia zapasowa grupy woluminu. Następnie można usunąć pliku przechowywanego na jeden z woluminów w tej grupie woluminu. Na koniec wykonaj następujące kroki, aby przywrócić usunięty plik z kopii zapasowej. 

#### <a name="to-recover-a-deleted-file"></a>Do odzyskania usuniętego pliku
1. Kliknij ikonę programu StorSimple Snapshot Manager na pulpicie. Zostanie wyświetlone okno konsoli programu StorSimple Snapshot Manager. 
2. W **zakres** okienku rozwiń **katalog kopii zapasowej** węzła i przejdź do migawki, która zawiera usunięty plik. Zazwyczaj należy wybrać migawki, który został utworzony właśnie przed usunięciem.
3. Znajdź wolumin, który chcesz sklonować, kliknij prawym przyciskiem myszy, a następnie kliknij przycisk **klonowania**. **Migawki w chmurze klonowania** pojawi się okno dialogowe.
   
    ![Klonowanie migawkę w chmurze](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. Wykonaj **migawki w chmurze klonowania** okno dialogowe w następujący sposób: 
   
   1. W **nazwa** polu tekstowym wpisz nazwę sklonowany wolumin. Ta nazwa będzie wyświetlana w **woluminów** węzła. 
   2. (Opcjonalnie) Wybierz **dysku**, a następnie wybierz literę dysku z listy rozwijanej. 
   3. (Opcjonalnie) Wybierz **folderu NTFS**i wpisz ścieżkę folderu, lub kliknij przycisk **Przeglądaj** i wybierz lokalizację folderu. 
   4. Kliknij pozycję **Utwórz**. 
5. Po zakończeniu procesu klonowania, musi zostać zainicjowany sklonowany wolumin. Uruchom Menedżera serwera, a następnie uruchom przystawki Zarządzanie dyskami. Aby uzyskać szczegółowe instrukcje, zobacz [zainstalować woluminów](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Po zainicjowaniu, wolumin zostaną wyświetlone w obszarze **woluminów** w węźle **zakres** okienka. 
   
    Jeśli wolumin, na liście nie jest widoczny, Odśwież listę woluminów (kliknij prawym przyciskiem myszy **woluminów** węzłem, a następnie kliknij przycisk **Odśwież**).
6. Otwórz folder systemu plików NTFS, który zawiera sklonowany wolumin, rozwiń węzeł **woluminów** węzeł, a następnie otwórz sklonowany wolumin. Znajdź plik, który chcesz odzyskać, a następnie skopiuj go do woluminu podstawowego.
7. Po przywróceniu pliku można usunąć folderu systemu plików NTFS, który zawiera sklonowany wolumin.

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Przywracanie bazy danych programu StorSimple Snapshot Manager
Regularnie należy wykonać kopię zapasową bazy danych programu StorSimple Snapshot Manager na komputerze-hoście. W przypadku poważnej awarii lub komputer-host nie powiedzie się z jakiegokolwiek powodu, można przywrócić ją z kopii zapasowej. Tworzenie kopii zapasowej bazy danych jest procesem wykonywanym ręcznie.

#### <a name="to-back-up-and-restore-the-database"></a>Aby utworzyć kopię zapasową i przywrócić bazę danych
1. Zatrzymaj usługę zarządzania Microsoft StorSimple:
   
   1. Uruchom Menedżera serwera.
   2. Na pulpicie nawigacyjnym Menedżera serwera na **narzędzia** menu, wybierz opcję **usług**.
   3. Na **usług** wybierz **Microsoft StorSimple Management Service**.
   4. W okienku po prawej stronie w obszarze **Microsoft StorSimple Management Service**, kliknij przycisk **Zatrzymaj usługę**.
2. Na komputerze-hoście przejdź do C:\ProgramData\Microsoft\StorSimple\BACatalog. 
   
   > [!NOTE]
   > Folder ProgramData jest folderem ukrytym.
   > 
   > 
3. Znajdź katalog pliku XML, skopiuj plik i przechowywać kopię w bezpiecznym miejscu lub w chmurze. Jeśli host nie powiedzie się, można użyć tego pliku kopii zapasowej do odzyskiwania zasady tworzenia kopii zapasowych, które zostały utworzone w programie StorSimple Snapshot Manager.
   
    ![Plik wykaz kopii zapasowych w usłudze Azure StorSimple](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Uruchom ponownie usługę zarządzania Microsoft StorSimple: 
   
   1. Na pulpicie nawigacyjnym Menedżera serwera na **narzędzia** menu, wybierz opcję **usług**.
   2. Na **usług** wybierz **Microsoft StorSimple Management Service**.
   3. W okienku po prawej stronie w obszarze **Microsoft StorSimple Management Service**, kliknij przycisk **Uruchom ponownie usługę**.
5. Na komputerze-hoście przejdź do C:\ProgramData\Microsoft\StorSimple\BACatalog. 
6. Usuń katalog pliku XML, a następnie zamień tę wersję kopii zapasowej, który został utworzony. 
7. Kliknij ikonę programu StorSimple Snapshot Manager pulpitu, można uruchomić programu StorSimple Snapshot Manager. 

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [za pomocą Menedżera migawek StorSimple do administrowania rozwiązania StorSimple](storsimple-snapshot-manager-admin.md).
* Dowiedz się więcej o [programu StorSimple Snapshot Manager zadaniom i przepływom pracy](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).

