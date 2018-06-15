---
title: Klonowanie kopii zapasowej tablicy wirtualnego StorSimple | Dokumentacja firmy Microsoft
description: Dowiedz się, jak w klonowania kopii zapasowej i odzyskiwanie pliku z macierzy wirtualne StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: af6e979c-55e3-477c-b53e-a76a697f80c9
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: 768c9a1c906999f4690c9c8f7d075743ab1678ff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875991"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>Klonowanie z kopii zapasowej macierzy wirtualnego StorSimple

## <a name="overview"></a>Omówienie

W tym artykule opisano krok po kroku można sklonować zestawu kopii zapasowych udziały lub woluminy na tablica wirtualne Microsoft Azure StorSimple. Sklonowany kopia zapasowa służy do odzyskania plików usuniętych lub zostały utracone. Artykuł zawiera także szczegółowe kroki, aby wykonać odzyskiwanie na poziomie elementu na tablica wirtualnego StorSimple skonfigurowany jako serwer plików.

## <a name="clone-shares-from-a-backup-set"></a>Akcje w klonowania z zestawu kopii zapasowych

**Przed podjęciem próby sklonowania udziałów, upewnij się, że masz wystarczającą ilość miejsca na urządzeniu, aby wykonać tę operację.** Klonowanie z kopii zapasowej w [portalu Azure](https://portal.azure.com/), wykonaj następujące kroki.

#### <a name="to-clone-a-share"></a>Klonowanie udziału

1. Przejdź do **urządzeń** bloku. Wybierz i kliknij urządzenie, a następnie kliknij przycisk **udziałów**. Wybierz udział, który chcesz sklonować, kliknij prawym przyciskiem myszy udział można wywołać z menu kontekstowego. Wybierz **klonowania**.
   
   ![Klonowanie kopii zapasowej](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. W **klonowania** bloku, kliknij przycisk **kopii zapasowej > Wybierz** , a następnie wykonaj następujące czynności: 
   
   a.    Filtr kopii zapasowej na tym urządzeniu, na podstawie zakresu czasu. Możesz wybrać spośród **ostatnie 7 dni**, **w ciągu ostatnich 30 dni**, i **ciągu ostatniego roku**.
   
   b.    Na liście filtrowane tworzenia kopii zapasowych, które są wyświetlane Wybierz kopię zapasową można sklonować z.
   
   c.    Kliknij przycisk **OK**.
   
   ![Klonowanie kopii zapasowej](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. W **klonowania** bloku, kliknij przycisk **Target ustawienia** , a następnie wykonaj następujące czynności:
   
   a.    Podaj nazwę udziału. Nazwa udziału musi zawierać od 3 do 127 znaków.
   
   b.    Opcjonalnie wprowadź opis udziału sklonowany.
   
   c.    Nie można zmienić typu przywracane do udziału. Udział warstwowych został sklonowany jako warstwowych a udziałem przypięty lokalnie, przypięty lokalnie.
   
   d.    Pojemność jest ustawiana jako równa rozmiarowi udziału, które są klonowania z.
   
   e.    Przypisz administratorów dla tego udziału. Będzie można zmodyfikować właściwości udziału za pomocą Eksploratora plików, po ukończeniu klonowania.
   
   f.    Kliknij przycisk **OK**.
   
   ![Klonowanie kopii zapasowej](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Kliknij przycisk **klonowania** można rozpocząć zadania klonowania. Po zakończeniu zadania uruchamiania operacji klonowania i zostanie wyświetlone powiadomienie. Aby monitorować postęp w klonowania, przejdź do **zadania** bloku i kliknij pozycję zadania, aby wyświetlić szczegóły zadania.
5. Po pomyślnym utworzeniu klonu, przejdź wstecz do **udziałów** bloku na urządzeniu.
6. Możesz teraz przeglądać nowego udziału sklonowany listy udziałów na urządzeniu. Udział warstwowych został sklonowany, ponieważ do warstwy a udziałem przypiętych lokalnie jako udział przypiętych lokalnie.
   
   ![Klonowanie kopii zapasowej](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Klonowanie woluminów z zestawu kopii zapasowych

Klonowanie z kopii zapasowej, w portalu Azure, trzeba wykonywać podobne do tych czynności w przypadku klonowania udziału. Operacja klonowania klonów kopii zapasowej do nowego woluminu na tym samym urządzeniu wirtualnym; Nie można sklonować do innego urządzenia.

#### <a name="to-clone-a-volume"></a>Klonowanie woluminów

1. Przejdź do **urządzeń** bloku. Wybierz i kliknij urządzenie, a następnie kliknij przycisk **woluminów**. Wybier wolumin, który ma zostać sklonowany, kliknij prawym przyciskiem myszy wolumin, który można wywołać z menu kontekstowego. Wybierz **klonowania**.
   
   ![Klonowanie woluminu](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. W **klonowania** bloku, kliknij przycisk **kopii zapasowej** , a następnie wykonaj następujące czynności: 
   
   a.    Filtr kopii zapasowej na tym urządzeniu, na podstawie zakresu czasu. Możesz wybrać spośród **ostatnie 7 dni**, **w ciągu ostatnich 30 dni**, i **ciągu ostatniego roku**. 
   
   b.    Na liście filtrowane tworzenia kopii zapasowych, które są wyświetlane Wybierz kopię zapasową można sklonować z.
   
   c.    Kliknij przycisk **OK**.
   
   ![Klonowanie kopii zapasowej](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. W **klonowania** bloku, kliknij przycisk **docelowy wolumin ustawienia** , a następnie wykonaj następujące czynności::
   
   a. Nazwa urządzenia jest wypełniane automatycznie.
   
   b. Podaj nazwę woluminu **sklonować woluminu**. Nazwa woluminu musi zawierać od 3 do 127 znaków.
   
   c. Typ woluminu jest automatycznie ustawiana oryginalnego woluminu. Wolumin warstwowy został sklonowany, ponieważ do warstwy i przypięty lokalnie woluminu przypiętego lokalnie.
   
   d. Dla **połączone hosty**, kliknij przycisk **wybierz**.
   
   ![Klonowanie kopii zapasowej](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. W **połączone hosty** bloku, wybierz z istniejących ACR lub Dodaj nowe ACR. Aby dodać nowy ACR, konieczne będzie podanie nazwy ACR i IQN hosta. Kliknij pozycję **Wybierz**.
   
   ![Klonowanie kopii zapasowej](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Kliknij przycisk **klonowania** można uruchomić zadania klonowania.
   
   ![Klonowanie kopii zapasowej](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. Po utworzeniu zadania sklonowany klonowania zostanie uruchomiony. Po utworzeniu klonu jest wyświetlany w bloku woluminów na urządzeniu. Warto zauważyć, że wolumin warstwowy został sklonowany, ponieważ do warstwy woluminu przypiętego lokalnie został sklonowany jako woluminu przypiętego lokalnie.
   
   ![Klonowanie kopii zapasowej](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. Gdy wolumin pojawi się w trybie online na listę woluminów, wolumin jest dostępny do użycia. Na hoście inicjatora iSCSI Odśwież listę elementów docelowych w oknie właściwości inicjatora iSCSI. Nowy docelowy, który zawiera nazwę sklonowanego woluminu powinny być wyświetlane jako "nieaktywne" w kolumnie Stan.
8. Wybierz element docelowy, a następnie kliknij przycisk **Connect**. Po podłączeniu do obiektu docelowego inicjatora stan powinien zmienić się **połączony**.
9. W **Zarządzanie dyskami** wyświetlone zainstalowane woluminy okno, jak pokazano na poniższej ilustracji. Kliknij prawym przyciskiem myszy odnaleziony wolumin (kliknij nazwę dysku), a następnie kliknij pozycję **Online**.

> [!IMPORTANT]
> Podczas próby klonowania wolumin lub udział z kopii zapasowej ustawiona, jeśli zadanie klonowanie zakończy się niepowodzeniem, wolumin docelowy lub nadal można utworzyć udziału w portalu. Należy usunąć tego woluminu docelowego, lub udostępnianie w portalu, aby zminimalizować problemy przyszłych wynikające z tego elementu.
> 
> 

## <a name="item-level-recovery-ilr"></a>Odzyskiwanie na poziomie elementu (ILR)

Tej wersji wprowadzono odzyskiwanie poziomie elementu (ILR) w macierzy wirtualnego StorSimple, skonfigurowany jako serwer plików. Funkcja umożliwia wykonaj szczegółową odzyskiwanie plików i folderów z kopii zapasowej chmury wszystkich akcji w urządzeniu StorSimple. Ostatnie kopie zapasowe przy użyciu modelu samoobsługi może pobrać usuniętych plików.

Każdy udział ma *.backups* folderu, który zawiera najnowsze kopii zapasowych. Można przejść do żądanej kopii zapasowej, skopiuj odpowiednie pliki i foldery z kopii zapasowej i je przywrócić. Ta funkcja eliminuje wywołania do administratorów przywracania plików z kopii zapasowych.

1. Podczas przeprowadzania odzyskiwania na poziomie elementów, można wyświetlić za pomocą Eksploratora plików kopii zapasowych. Kliknij przycisk określonego udziału, do którego chcesz przejrzeć kopii zapasowej. Zobaczysz *.backups* folder utworzony w ramach udziału, który przechowuje wszystkie kopie zapasowe. Rozwiń węzeł *.backups* folder, aby wyświetlić kopie zapasowe. Folder zawiera widok całej hierarchii kopii zapasowej. Ten widok jest tworzony na żądanie i zazwyczaj trwa tylko kilka sekund do utworzenia.
   
   Pięć ostatnich kopie zapasowe są wyświetlane w ten sposób i może posłużyć do wykonania odzyskiwania na poziomie pozycji. Pięć najnowszych kopii zapasowych obejmują zarówno domyślny zaplanowany i ręcznego tworzenia kopii zapasowych.
   
   * **Zaplanowane tworzenie kopii zapasowych** jako &lt;nazwa urządzenia&gt;DailySchedule-RRRRMMDD-HHMMSS-UTC.
   * **Ręczne tworzenie kopii zapasowych** jako Ad-hoc — RRRRMMDD-HHMMSS-UTC.
     
     ![](./media/storsimple-virtual-array-clone/image14.png)

2. Zidentyfikuj zawierający najnowszą wersję usuniętego pliku kopii zapasowej. Jeśli nazwa folderu zawiera sygnaturę czasową UTC w każdym z powyższych przypadków, czas, w którym został utworzony folder jest czasu rzeczywistego urządzenia podczas uruchamiania kopii zapasowej. Sygnatura czasowa folderu umożliwia lokalizację i identyfikację kopii zapasowych.

3. Zlokalizuj folder lub plik, który chcesz przywrócić z kopii zapasowej, który został zidentyfikowany w poprzednim kroku. Należy pamiętać, że można wyświetlać tylko pliki lub foldery, które mają uprawnienia do. Jeśli nie masz dostępu do niektórych plików lub folderów, skontaktuj się z administratorem udziału. Administrator może być Eksploratora plików Edytuj uprawnienia udziału i uzyskania dostępu do określonego pliku lub folderu. Jest zalecanym najlepszym rozwiązaniem, że administrator udziału to grupa użytkowników zamiast pojedynczego użytkownika.

4. Skopiuj plik lub folder do odpowiedniego udziału na serwerze plików StorSimple.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o sposobie [administrowania tablica wirtualnego StorSimple przy użyciu lokalnego interfejsu użytkownika sieci web](storsimple-ova-web-ui-admin.md).

