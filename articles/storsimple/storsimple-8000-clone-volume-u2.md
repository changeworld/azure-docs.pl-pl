---
title: Klonowanie woluminu z serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Zawiera opis klonowania różnych typów i użycia i wyjaśnia, jak można użyć zestawu sklonować pojedynczy wolumin w urządzeniu StorSimple 8000 series kopii zapasowych.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60637906"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>Użyj usługi Menedżer urządzeń StorSimple w witrynie Azure portal, aby sklonować woluminu

## <a name="overview"></a>Omówienie

W tym samouczku opisano sposób korzystania z zestawu, aby sklonować pojedynczy wolumin za pomocą kopii zapasowych **katalog kopii zapasowej** bloku. Objaśniono także różnice między *przejściowy* i *stałe* klony. Wskazówki zawarte w tym samouczku ma zastosowanie do wszystkich urządzeń serii StorSimple 8000 z aktualizacją Update 3 lub nowszej.

Usługa Menedżer urządzeń StorSimple **katalog kopii zapasowej** bloku wyświetlane są wszystkie zestawy kopii zapasowych, które są tworzone podczas ręczne lub automatyczne kopie zapasowe są wykonywane. Wybierz z woluminu, w kopii zapasowej Ustaw sklonować.

 ![Lista zestawu kopii zapasowych](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>Zagadnienia dotyczące klonowania woluminu

Podczas klonowania woluminu, należy wziąć pod uwagę następujące informacje.

- Klon działa tak samo jako wolumin regularne. Każdej operacji, która jest możliwe na woluminie jest dostępna dla klonu.

- Monitorowanie i domyślne kopii zapasowej są automatycznie wyłączane na sklonowany wolumin. Należy skonfigurować sklonowany wolumin dla wszelkich kopii zapasowych.

- Wolumin przypięty lokalnie został sklonowany jako woluminu warstwowego. Jeśli potrzebujesz sklonowany wolumin przypięty lokalnie, klonu można przekonwertować wolumin przypięty lokalnie po pomyślnym zakończeniu operacji klonowania. Aby uzyskać informacje o konwersji woluminu warstwowego na wolumin przypięty lokalnie, przejdź do [zmienić typ woluminu](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).

- Jeśli zostanie podjęta próba konwersji sklonowany woluminu z warstwowego na lokalnie przypięty natychmiast po zakończeniu klonowania, (gdy nadal jest przejściowym klonem), konwersja kończy się niepowodzeniem z następujący komunikat o błędzie:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    To jest błąd tylko wtedy, gdy są klonowanie się na innym urządzeniu. Pomyślnie przekonwertować wolumin przypięty lokalnie, po przekonwertowaniu przejściowym klonem do stałego klonowania. Utwórz migawkę chmury przejściowym klonem, aby przekonwertować go na stałe klonowania.

## <a name="create-a-clone-of-a-volume"></a>Tworzenie własnego klonu woluminu

Można utworzyć klonu na tym samym urządzeniu, inne urządzenie lub nawet urządzenia w chmurze przy użyciu lokalnego lub migawkę w chmurze.

Poniższa procedura opisuje sposób utworzenia klona z wykazem kopii zapasowych.  Jest alternatywną metodę inicjowania klonowania, aby przejść do **woluminów**, wybierz wolumin, a następnie kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe i wybrać **klonowania**.

Wykonaj poniższe kroki, aby sklonować woluminu z wykazem kopii zapasowych.

#### <a name="to-clone-a-volume"></a>Klonowanie woluminu

1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie kliknij przycisk **katalog kopii zapasowej**.

2. Wybierz kopię zapasową, ustaw w następujący sposób:
   
   1. Wybierz odpowiednie urządzenie.
   2. Na liście rozwijanej wybierz zasady woluminu lub kopii zapasowej do utworzenia kopii zapasowej, który chcesz wybrać.
   3. Określ zakres czasu.
   4. Kliknij przycisk **Zastosuj** do wykonywania tej kwerendy.

      Kopie zapasowe skojarzone z wybranego woluminu lub zasad tworzenia kopii zapasowej powinna zostać wyświetlona na liście zestawów kopii zapasowych.
   
      ![Lista zestawu kopii zapasowych](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Rozwiń zestaw, aby wyświetlić skojarzone woluminu i wybrać wolumin w zestawie kopii zapasowych kopii zapasowych. Kliknij prawym przyciskiem myszy, a następnie z menu kontekstowego wybierz pozycję **klonowania**.

   ![Lista zestawu kopii zapasowych](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. W **klonowania** blok, wykonaj następujące czynności:
   
   1. Zidentyfikuj urządzenia docelowego. Jest to lokalizacja, w której zostanie utworzony klon. Można wybrać tego samego urządzenia lub określić inne urządzenie.

      > [!NOTE]
      > Upewnij się, że pojemność wymagana dla sklonowanego jest starsza niż pojemność dostępna na urządzeniu docelowym.
       
   2. Określ nazwę woluminu unikatowy klonie. Nazwa musi zawierać od 3 do 127 znaków.
      
       > [!NOTE]
       > **Klonowania woluminu jako** pole będzie **warstwowy** nawet wtedy, gdy są klonowania woluminu przypiętego lokalnie. Nie można zmienić to ustawienie; jednak sklonowany wolumin, aby lokalnie można również przypiąć, należy można przekonwertować klonu do woluminu przypiętego lokalnie po pomyślnym utworzeniu klonu. Aby uzyskać informacje o konwersji woluminu warstwowego na wolumin przypięty lokalnie, przejdź do [zmienić typ woluminu](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).
          
   3. W obszarze **połączone hosty**, określić rekord kontroli dostępu (ACR) dla klonu. Można dodać nowy rekord ACR lub wybierz z listy istniejących. Usługi ACR określi, hosty, które mogą uzyskiwać dostęp do tego klonu.
      
       ![Lista zestawu kopii zapasowych](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

   4. Kliknij przycisk **klonowania** można ukończyć operacji.

4. Zadanie klonowania jest inicjowane, a otrzymasz powiadomienie po pomyślnym utworzeniu klonu. Kliknij powiadomienie o zadaniu lub przejdź do **zadań** bloku, aby monitorować zadanie klonowania.

    ![Lista zestawu kopii zapasowych](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. Po zakończeniu zadania klonowania, przejdź do urządzenia, a następnie kliknij przycisk **woluminów**. Na liście woluminów powinien zostać wyświetlony klonowania, która właśnie została utworzona w tym samym kontenerze woluminu, który zawiera woluminu źródłowego.

    ![Lista zestawu kopii zapasowych](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

Klonowania, który jest tworzony w ten sposób jest przejściowym klonem. Aby uzyskać więcej informacji na temat typów klonowania zobacz [przejściowych, a stałe klony](#transient-vs-permanent-clones).


## <a name="transient-vs-permanent-clones"></a>Przejściowy, a stałe klony
Przejściowy klony są tworzone tylko wtedy, gdy klonowania na innym urządzeniu. Można sklonować określonego woluminu z kopii zapasowej Ustaw na różne urządzenia zarządzane przez Menedżera urządzeń StorSimple. Przejściowym klonem odniesień do danych w oryginalnego woluminu i używa tych danych do odczytu i zapisu lokalnie na urządzeniu docelowym.

Po sprawdzeniu przejściowym klonem migawkę w chmurze, wynikowy klon jest *stałe* klonowania. W trakcie tego procesu kopii danych jest tworzony w chmurze, a czas kopiowania tych danych zależy od rozmiaru danych oraz platformy Azure czasy oczekiwania (jest to kopia Azure – Azure). Ten proces może potrwać dni, tygodnie. Przejściowym klonem staje się klonowania trwałe i nie ma żadnych odwołań do oryginalnych danych woluminu, który został sklonowany z.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Scenariusze dotyczące błędy przejściowe i trwałe klony
W poniższych sekcjach opisano przykład sytuacji, w których można użyć klony błędy przejściowe i trwałe.

### <a name="item-level-recovery-with-a-transient-clone"></a>Odzyskiwanie na poziomie elementu z przejściowym klonem
Musisz odzyskać plik prezentacji jednego miliardową programu Microsoft PowerPoint. IT administrator identyfikuje określonej kopii zapasowej po tym czasie, a następnie filtruje woluminu. Następnie administrator klonuje wolumin, lokalizuje plik, którego szukasz i przekazuje go do Ciebie. W tym scenariuszu przejściowym klonem jest używany.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testowanie w środowisku produkcyjnym za pomocą stałych klonowania
Należy sprawdzić usterkę testowania w środowisku produkcyjnym. Tworzenie własnego klonu woluminu w środowisku produkcyjnym, a następnie wykonaj migawkę w chmurze tego klonu. Aby utworzyć niezależnych sklonowany wolumin. W tym scenariuszu jest używany stały klonowania.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [Przywracanie woluminu StorSimple z zestawu kopii zapasowych](storsimple-8000-restore-from-backup-set-u2.md).
* Dowiedz się, jak [korzystać z usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

