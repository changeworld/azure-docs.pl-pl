---
title: Klonowanie woluminu z serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Opisano w klonowania różne typy i użycia i opisano sposób korzystania z zestawu sklonować poszczególnych woluminów na urządzeniu z serii StorSimple 8000 kopii zapasowych.
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
ms.openlocfilehash: d3965c74d51996b3e4144130fad01fae9e265897
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2017
ms.locfileid: "26333206"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>Użyj usługi Menedżer StorSimple urządzenia w portalu Azure Klonowanie woluminów

## <a name="overview"></a>Omówienie

Ten przewodnik opisuje sposób korzystania z zestawu sklonować poszczególnych woluminów za pomocą kopii zapasowych **katalog kopii zapasowej** bloku. Objaśniono także różnice między *przejściowa* i *stałe* klonów. Wskazówki zawarte w tym samouczku ma zastosowanie do wszystkich urządzeń serii StorSimple 8000 uruchomioną aktualizacją 3 lub nowszym.

Usługę Menedżer StorSimple urządzenia **katalog kopii zapasowej** bloku Wyświetla wszystkie zestawy kopii zapasowych, które są tworzone podczas ręczne lub automatyczne kopie zapasowe są pobierane. Następnie można wybrać wolumin w kopii zapasowej ustawioną w klonowania.

 ![Listy zestawu kopii zapasowych](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>Zagadnienia dotyczące klonowania woluminu

W przypadku klonowania woluminu, należy wziąć pod uwagę następujące informacje.

- Klon działa w taki sam sposób jak regularne woluminu. Wszelkie operacje dostępnego na woluminie jest dostępna dla klonu.

- Monitorowanie i domyślnego tworzenia kopii zapasowej są automatycznie wyłączane na sklonowanym woluminie. Musisz skonfigurować sklonowany wolumin do wszelkich kopii zapasowych.

- Wolumin przypięty lokalnie jest sklonować jako woluminu warstwowego. Jeśli potrzebujesz sklonowany wolumin przypięty lokalnie, klonu można przekonwertować woluminu przypiętego lokalnie po pomyślnym zakończeniu operacji klonowania. Aby uzyskać informacje o konwertowanie woluminu warstwowego na wolumin przypięty lokalnie, przejdź do [zmienić typ woluminu](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).

- Próba konwersji sklonowany woluminu z warstwy do przypięty lokalnie natychmiast po zakończeniu klonowania (gdy nadal jest przejściowy klonowania), konwersja kończy się niepowodzeniem z następujący komunikat o błędzie:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    To jest błąd tylko wtedy, gdy są klonowania na innym urządzeniu. Pomyślnie przekonwertować wolumin przypięty lokalnie konwersji przejściowej klonu do stałego klonowania. Utwórz migawkę chmury przejściowej klonu, aby przekształcić ją w klonowania trwałych.

## <a name="create-a-clone-of-a-volume"></a>Tworzenie własnego klonu woluminu

Można utworzyć klona na tym samym urządzeniu, innego urządzenia lub nawet urządzenia do chmury przy użyciu lokalnego lub migawka w chmurze.

Poniższa procedura opisuje sposób tworzenia sklonowanego z katalogu kopii zapasowej.  Alternatywną metodą do inicjowania klonowania ma przejść do **woluminów**, wybierz wolumin, a następnie kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowego, a następnie wybierz **klonowania**.

Wykonaj poniższe kroki, aby utworzyć klonu woluminu z katalogu kopii zapasowej.

#### <a name="to-clone-a-volume"></a>Klonowanie woluminów

1. Przejdź do usługi Menedżer StorSimple urządzenia, a następnie kliknij przycisk **katalog kopii zapasowej**.

2. Wybierz kopię zapasową ustawione w następujący sposób:
   
   1. Wybierz odpowiednie urządzenie.
   2. Na liście rozwijanej wybierz zasady woluminu lub kopii zapasowej do utworzenia kopii zapasowej, który chcesz wybrać.
   3. Określ zakres czasu.
   4. Kliknij przycisk **Zastosuj** do wykonania tego zapytania.

    Tworzenie kopii zapasowych skojarzony z wybranego woluminu lub zasad tworzenia kopii zapasowej powinien zostać wyświetlony na liście zestawów kopii zapasowych.
   
    ![Listy zestawu kopii zapasowych](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Rozwiń zestaw, aby wyświetlić skojarzone woluminu i wybierz wolumin w zestawie kopii zapasowych kopii zapasowych. Kliknij prawym przyciskiem myszy, a następnie z menu kontekstowego wybierz **klonowania**.

   ![Listy zestawu kopii zapasowych](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. W **klonowania** blok, wykonaj następujące czynności:
   
    1. Określ urządzenia docelowego. Jest to lokalizacja, w której zostanie utworzona klonu. Można wybrać tego samego urządzenia lub Określ innego urządzenia.

      > [!NOTE]
      > Upewnij się, że wydajność wymaganą dla klonu jest starsza niż pojemność dostępna na urządzeniu docelowym.
       
    2. Określ nazwę unikatową woluminu z klonu. Nazwa musi zawierać od 3 do 127 znaków.
      
        > [!NOTE]
        > **Klonowania woluminu jako** pole będzie **warstwowego** nawet wtedy, gdy są klonowania woluminu przypiętego lokalnie. Nie można zmienić to ustawienie; Jednak jeśli potrzebujesz sklonowany woluminu, który ma być przypięty lokalnie również można przekonwertować klonu do woluminu przypiętego lokalnie po utworzeniu klonu. Aby uzyskać informacje o konwertowanie woluminu warstwowego na wolumin przypięty lokalnie, przejdź do [zmienić typ woluminu](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).
          
    3. W obszarze **połączone hosty**, określ rekord kontroli dostępu (ACR) dla klonu. Można dodawać nowe ACR lub wybierz z listy. ACR określają, które hosty mogą uzyskiwać dostęp do tego klonu.
      
        ![Listy zestawu kopii zapasowych](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

    4. Kliknij przycisk **klonowania** do ukończenia tej operacji.

4. Zadanie klonowania jest inicjowane i użytkownik jest powiadamiany o pomyślnym utworzeniu klonu. Kliknij powiadomienie o zadaniu lub przejdź do **zadania** bloku, aby monitorować zadanie klonowania.

    ![Listy zestawu kopii zapasowych](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. Po zakończeniu zadania sklonowany, przejdź do urządzenia, a następnie kliknij przycisk **woluminów**. Na liście woluminów powinna zostać wyświetlona klonowania, który został utworzony w tym samym kontenerze woluminu, który ma woluminu źródłowego.

    ![Listy zestawu kopii zapasowych](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

Sklonowany, która jest tworzona w ten sposób jest przejściowy klonowania. Aby uzyskać więcej informacji na temat typów w klonowania, zobacz [przejściowej a stałe klony](#transient-vs-permanent-clones).


## <a name="transient-vs-permanent-clones"></a>Przejściowy a klony stałych
Przejściowa klony są tworzone tylko wtedy, gdy w klonowania na innym urządzeniu. Można sklonować określonego woluminu z zestawu do innego urządzenia zarządzane przez Menedżera urządzeń StorSimple kopii zapasowych. Przejściowa klonu odwołuje się do danych w ramach oryginalnego woluminu i używa tych danych do odczytu i zapisu lokalnie na urządzeniu docelowym.

Po wykonaniu migawkę chmury w klonowania przejściowej wynikowy klon jest *stałe* klonowania. W trakcie tego procesu zostanie utworzona kopia danych w chmurze i czas kopiowania tych danych jest zależny od rozmiaru danych i opóźnienia Azure (jest to kopia Azure do platformy Azure). Ten proces może potrwać dni, tygodnie. Przejściowa klonu staje się klonowania stałe i nie zawiera żadnych odwołań do oryginalnych danych woluminu, który został sklonowany z.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Scenariusze dotyczące klony przejściowych i stałe
W poniższych sekcjach opisano przykład sytuacji, w których można użyć klony przejściowych i stałe.

### <a name="item-level-recovery-with-a-transient-clone"></a>Odzyskiwanie na poziomie elementu przejściowej klonu
Należy odzyskać plik prezentacji programu Microsoft PowerPoint co roku tygodniowych. IT administrator identyfikuje kopii zapasowej z tego czasu, a następnie filtruje woluminu. Następnie administrator klonuje wolumin, lokalizuje plik, którego szukasz i przekazuje go do użytkownika. W tym scenariuszu jest używana przejściowej klonowania.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testowanie w środowisku produkcyjnym klonu stałych
Należy sprawdzić usterkę testowania w środowisku produkcyjnym. Tworzenie własnego klonu woluminu w środowisku produkcyjnym i wykonaj migawkę chmury dla tego klonu. do utworzenia woluminu sklonowany niezależne. W tym scenariuszu stały klonowania jest używany.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [przywracania woluminu StorSimple z zestawu kopii zapasowych](storsimple-8000-restore-from-backup-set-u2.md).
* Dowiedz się, jak [zarządzać urządzenia StorSimple przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-8000-manager-service-administration.md).

