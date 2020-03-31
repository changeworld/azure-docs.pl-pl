---
title: Klonowanie woluminu z serii StorSimple 8000 | Dokumenty firmy Microsoft
description: W tym artykule opisano różne typy klonów i użycie oraz wyjaśniono, jak można użyć zestawu kopii zapasowych do klonowania pojedynczego woluminu na urządzeniu z serii StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/05/2017
ms.author: alkohli
ms.openlocfilehash: 84734aefb72a3330d99c5707b461de2cd5e30484
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255004"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>Klonowanie woluminu przy użyciu usługi Menedżer urządzeń StorSimple w witrynie Azure Portal

## <a name="overview"></a>Omówienie

W tym samouczku opisano, jak można użyć zestawu kopii zapasowych do klonowania pojedynczego woluminu za pośrednictwem bloku **katalogu kopii zapasowej.** Wyjaśnia również różnicę między *przejściowych* i *trwałych* klonów. Wskazówki zawarte w tym samouczku dotyczą wszystkich urządzeń z serii StorSimple 8000 z aktualizacją 3 lub nowszą.

Usługa StorSimple Device Manager **Blok katalogu kopii zapasowych** wyświetla wszystkie zestawy kopii zapasowych, które są tworzone podczas wykonywania ręcznych lub automatycznych kopii zapasowych. Następnie można wybrać wolumin w zestawie kopii zapasowych do klonowania.

 ![Lista zestawów kopii zapasowych](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>Zagadnienia dotyczące klonowania woluminu

Podczas klonowania woluminu należy wziąć pod uwagę następujące informacje.

- Klon zachowuje się w taki sam sposób jak zwykła objętość. Każda operacja, która jest możliwa na woluminie jest dostępna dla klonu.

- Monitorowanie i domyślna kopia zapasowa są automatycznie wyłączane na sklonowanym woluminie. Należy skonfigurować sklonowany wolumin dla wszystkich kopii zapasowych.

- Wolumin przypięty lokalnie jest klonowany jako wolumin warstwowy. Jeśli potrzebny jest lokalny przypięty wolumin sklonowany, po pomyślnym zakończeniu operacji klonowania można przekonwertować klon na wolumin przypięty lokalnie. Aby uzyskać informacje dotyczące konwertowania woluminu warstwowego na wolumin przypięty lokalnie, przejdź do [tematu Zmień typ woluminu](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).

- Jeśli spróbujesz przekonwertować sklonowany wolumin z warstwowego na lokalnie przypięty natychmiast po klonowaniu (gdy jest jeszcze klonem przejściowym), konwersja nie powiedzie się z następującym komunikatem o błędzie:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    Ten błąd jest odbierany tylko wtedy, gdy klonujesz na innym urządzeniu. Wolumin można pomyślnie przekonwertować na przypięty lokalnie, jeśli klon przejściowy zostanie przekonwertowany na klon stały. Zrób migawkę chmury przejściowego klonu, aby przekonwertować go na stały klon.

## <a name="create-a-clone-of-a-volume"></a>Tworzenie klonu woluminu

Klon można utworzyć na tym samym urządzeniu, innym urządzeniu, a nawet urządzeniu w chmurze przy użyciu migawki lokalnej lub w chmurze.

W poniższej procedurze opisano sposób tworzenia klonu z katalogu kopii zapasowych.  Alternatywną metodą inicjowania klonowania jest przejść do **:Volumes**, wybierz wolumin, a następnie kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe i wybierz polecenie **Klonuj**.

Wykonaj następujące kroki, aby utworzyć klon woluminu z katalogu kopii zapasowej.

#### <a name="to-clone-a-volume"></a>Aby sklonować wolumin

1. Przejdź do usługi StorSimple Device Manager, a następnie kliknij pozycję **Katalog kopii zapasowych**.

2. Wybierz zestaw kopii zapasowych w następujący sposób:
   
   1. Wybierz odpowiednie urządzenie.
   2. Na liście rozwijanej wybierz zasady woluminu lub kopii zapasowej dla kopii zapasowej, którą chcesz wybrać.
   3. Określ zakres czasu.
   4. Kliknij przycisk **Zastosuj,** aby wykonać tę kwerendę.

      Kopie zapasowe skojarzone z wybranymi zasadami woluminu lub kopii zapasowej powinny być wyświetlane na liście zestawów kopii zapasowych.
   
      ![Lista zestawów kopii zapasowych](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Rozwiń zestaw kopii zapasowych, aby wyświetlić skojarzony wolumin i wybierz wolumin w zestawie kopii zapasowych. Kliknij prawym przyciskiem myszy, a następnie z menu kontekstowego wybierz polecenie **Klonuj**.

   ![Lista zestawów kopii zapasowych](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. W bloku **Klonuj** wykonaj następujące czynności:
   
   1. Zidentyfikuj urządzenie docelowe. Jest to lokalizacja, w której zostanie utworzony klon. Możesz wybrać to samo urządzenie lub określić inne urządzenie.

      > [!NOTE]
      > Upewnij się, że pojemność wymagana dla klonu jest niższa niż pojemność dostępna na urządzeniu docelowym.
       
   2. Określ unikatową nazwę woluminu klonu. Nazwa musi zawierać od 3 do 127 znaków.
      
       > [!NOTE]
       > Pole **Sklonuj wolumin jako** będzie **warstwowe,** nawet jeśli klonujesz wolumin przypięty lokalnie. Nie można zmienić tego ustawienia; Jeśli jednak sklonowany wolumin ma być również przypięty lokalnie, można przekonwertować klon na wolumin przypięty lokalnie po pomyślnym utworzeniu klonu. Aby uzyskać informacje dotyczące konwertowania woluminu warstwowego na wolumin przypięty lokalnie, przejdź do [tematu Zmień typ woluminu](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).
          
   3. W obszarze **Połączone hosty**określ rekord kontroli dostępu (ACR) dla klonu. Możesz dodać nowy acr lub wybrać z istniejącej listy. ACR określi, które hosty mogą uzyskać dostęp do tego klonu.
      
       ![Lista zestawów kopii zapasowych](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

   4. Kliknij **przycisk Klonuj,** aby zakończyć operację.

4. Zadanie klonowania jest inicjowane i użytkownik jest powiadamiany, gdy klon został pomyślnie utworzony. Kliknij powiadomienie o zadaniu lub przejdź do **bloku Zadania,** aby monitorować zadanie klonowania.

    ![Lista zestawów kopii zapasowych](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. Po zakończeniu zadania klonowania przejdź do urządzenia, a następnie kliknij pozycję **Woluminy**. Na liście woluminów powinien zostać wyświetlony klon, który został właśnie utworzony w tym samym kontenerze woluminu, który ma wolumin źródłowy.

    ![Lista zestawów kopii zapasowych](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

Klon, który jest tworzony w ten sposób jest przejściowy klon. Aby uzyskać więcej informacji na temat typów klonów, zobacz [Przejściowe i trwałe klony](#transient-vs-permanent-clones).


## <a name="transient-vs-permanent-clones"></a>Przejściowe i trwałe klony
Klony przejściowe są tworzone tylko podczas klonowania na innym urządzeniu. Można sklonować określony wolumin z zestawu kopii zapasowych na inne urządzenie zarządzane przez Menedżera urządzeń StorSimple. Klon przejściowy ma odwołania do danych w oryginalnym woluminie i używa tych danych do odczytu i zapisu lokalnie na urządzeniu docelowym.

Po dokonaniu migawki chmury przejściowego klonu wynikowy klon jest *klonem trwałym.* Podczas tego procesu kopia danych jest tworzona w chmurze, a czas kopiowania tych danych zależy od rozmiaru danych i opóźnień platformy Azure (jest to kopia platformy Azure do platformy Azure). Ten proces może potrwać dni do tygodni. Klon przejściowy staje się stałym klonem i nie ma żadnych odwołań do oryginalnych danych woluminu, z których został sklonowany.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Scenariusze dla klonów przejściowych i trwałych
W poniższych sekcjach opisano przykładowe sytuacje, w których można używać klonów przejściowych i trwałych.

### <a name="item-level-recovery-with-a-transient-clone"></a>Odzyskiwanie na poziomie towaru z klonem przejściowym
Musisz odzyskać roczny plik prezentacji programu Microsoft PowerPoint. Administrator IT identyfikuje określoną kopię zapasową z tego czasu, a następnie filtruje wolumin. Następnie administrator klonuje wolumin, lokalizuje szukany plik i udostępnia go. W tym scenariuszu używany jest klon przejściowy.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testowanie w środowisku produkcyjnym przy stałym klonie
Należy zweryfikować błąd testowania w środowisku produkcyjnym. Utwórz klon woluminu w środowisku produkcyjnym, a następnie zrobić migawkę chmury tego klonu, aby utworzyć niezależny sklonowany wolumin. W tym scenariuszu używany jest stały klon.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [przywrócić wolumin StorSimple z zestawu kopii zapasowych](storsimple-8000-restore-from-backup-set-u2.md).
* Dowiedz się, jak [zarządzać urządzeniem StorSimple za pomocą usługi StorSimple Device Manager.](storsimple-8000-manager-service-administration.md)

