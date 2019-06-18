---
title: Klonowanie z kopii zapasowej macierzy wirtualnej StorSimple | Dokumentacja firmy Microsoft
description: Dowiedz się, jak sklonować kopii zapasowej i odzyskiwania pliku z rozwiązania StorSimple Virtual Array.
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
ms.openlocfilehash: feffbb634af62d70a840febcf2a04afb7bdeeddd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60580943"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>Klonowanie z kopii zapasowej rozwiązania StorSimple Virtual Array

## <a name="overview"></a>Omówienie

W tym artykule opisano krok po kroku sposób klonowania zestawu kopii zapasowych udziałów lub woluminów na usługi Microsoft Azure StorSimple Virtual Array. Sklonowanej kopii zapasowej jest używany do odzyskania pliku usunięty lub utracony. Artykuł zawiera również uzyskać szczegółowy opis kroków, aby wykonać odzyskiwanie na poziomie elementu na rozwiązania StorSimple Virtual Array, skonfigurowany jako serwer plików.

## <a name="clone-shares-from-a-backup-set"></a>Klonowanie udziałów z zestawu kopii zapasowych

**Przed podjęciem próby sklonowania udziałów, upewnij się, że masz wystarczająco dużo miejsca na urządzeniu, aby ukończyć tę operację.** Klonowanie z kopii zapasowej w [witryny Azure portal](https://portal.azure.com/), wykonaj następujące kroki.

#### <a name="to-clone-a-share"></a>Aby sklonować udział

1. Przejdź do **urządzeń** bloku. Wybierz i kliknij swoje urządzenie, a następnie kliknij przycisk **udziałów**. Wybierz udział, który chcesz sklonować, kliknij prawym przyciskiem myszy udział, aby wywołać menu kontekstowe. Wybierz pozycję **Klonuj**.
   
   ![Klonowanie z kopii zapasowej](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. W **klonowania** bloku kliknij **kopii zapasowej > Wybierz** , a następnie wykonaj następujące czynności: 
   
   a.    Filtruj kopii zapasowej na tym urządzeniu, na podstawie zakresu czasu. Możesz wybrać spośród **ostatnie 7 dni**, **w ciągu ostatnich 30 dni**, i **ostatni rok**.
   
   b.    Na liście filtrowane tworzenia kopii zapasowych, które są wyświetlane Wybierz kopię zapasową, będący źródłem klonowania.
   
   c.    Kliknij przycisk **OK**.
   
   ![Klonowanie z kopii zapasowej](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. W **klonowania** bloku kliknij **docelowe ustawienia** , a następnie wykonaj następujące czynności:
   
   a.    Podaj nazwę udziału. Nazwa udziału musi zawierać od 3 do 127 znaków.
   
   b.    Opcjonalnie podaj opis sklonowany udział.
   
   c.    Nie można zmienić typ udziału, w których odbywa się przywracanie do. Udziału warstwowego został sklonowany warstwowych i udział przypiętego lokalnie przypięty lokalnie.
   
   d.    Pojemność jest ustawiony jako równe rozmiarowi udziału, które są klonowanie.
   
   e.    Przypisz administratorów dla tego udziału. Będzie można zmodyfikować właściwości udziału za pomocą Eksploratora plików, po ukończeniu klonowania.
   
   f.    Kliknij przycisk **OK**.
   
   ![Klonowanie z kopii zapasowej](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Kliknij przycisk **klonowania** można uruchomić zadanie klonowania. Po ukończeniu zadania rozpoczęcia operacji klonowania i zostanie wyświetlone powiadomienie. Aby monitorować postęp klonowania, przejdź do **zadań** bloku i kliknij pozycję zadania, aby wyświetlić szczegóły zadania.
5. Po pomyślnym utworzeniu klonu, przejdź z powrotem do **udziałów** bloku na urządzeniu.
6. Teraz można wyświetlić nowe sklonowany udział w listy udziałów na urządzeniu. Udziału warstwowego został sklonowany, zgodnie z zasadami warstwowej a udziałem przypiętego lokalnie jako udział przypiętego lokalnie.
   
   ![Klonowanie z kopii zapasowej](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Klonowanie woluminów z zestawu kopii zapasowych

Klonowanie z kopii zapasowej, w witrynie Azure portal, należy wykonać kroki podobne do tych, podczas klonowania udział. Operacja klonowania klony kopii zapasowej do nowego woluminu na tym samym urządzeniu wirtualnym; Nie można sklonować do innego urządzenia.

#### <a name="to-clone-a-volume"></a>Klonowanie woluminu

1. Przejdź do **urządzeń** bloku. Wybierz i kliknij swoje urządzenie, a następnie kliknij przycisk **woluminów**. Wybierz wolumin, który chcesz sklonować, kliknij prawym przyciskiem myszy wolumin, aby wywołać menu kontekstowe. Wybierz pozycję **Klonuj**.
   
   ![Klonowanie woluminu](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. W **klonowania** bloku kliknij **kopii zapasowej** , a następnie wykonaj następujące czynności: 
   
   a.    Filtruj kopii zapasowej na tym urządzeniu, na podstawie zakresu czasu. Możesz wybrać spośród **ostatnie 7 dni**, **w ciągu ostatnich 30 dni**, i **ostatni rok**. 
   
   b.    Na liście filtrowane tworzenia kopii zapasowych, które są wyświetlane Wybierz kopię zapasową, będący źródłem klonowania.
   
   c.    Kliknij przycisk **OK**.
   
   ![Klonowanie z kopii zapasowej](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. W **klonowania** bloku kliknij **docelowe ustawienia woluminu** , a następnie wykonaj następujące czynności::
   
   a. Nazwa urządzenia jest wypełniane automatycznie.
   
   b. Podaj nazwę woluminu **sklonowany wolumin**. Nazwa woluminu musi zawierać od 3 do 127 znaków.
   
   c. Typ woluminu jest automatycznie ustawiona do oryginalnego woluminu. Wolumin warstwowy został sklonowany, zgodnie z zasadami warstwowej i przypięty lokalnie woluminu przypiętego lokalnie.
   
   d. Aby uzyskać **połączone hosty**, kliknij przycisk **wybierz**.
   
   ![Klonowanie z kopii zapasowej](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. W **połączone hosty** bloku wybierz istniejący rekord ACR lub Dodaj nowy rekord ACR. Aby dodać nowy rekord ACR, należy podać nazwę rejestru Azure container Registry i nazwy IQN hosta. Kliknij pozycję **Wybierz**.
   
   ![Klonowanie z kopii zapasowej](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Kliknij przycisk **klonowania** można uruchomić zadanie klonowania.
   
   ![Klonowanie z kopii zapasowej](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. Po utworzeniu zadanie klonowania klonowania zostanie uruchomiony. Po utworzeniu klonu jest wyświetlany w bloku woluminów na urządzeniu. Należy pamiętać, że wolumin warstwowy został sklonowany, zgodnie z zasadami warstwowej i został sklonowany wolumin przypięty lokalnie, jako woluminu przypiętego lokalnie.
   
   ![Klonowanie z kopii zapasowej](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. Gdy wolumin online na liście woluminów, wolumin jest dostępny do użytku. Na hoście inicjatora iSCSI Odśwież listę elementów docelowych w oknie właściwości inicjatora iSCSI. Nowy obiekt docelowy, który zawiera nazwę sklonowany wolumin powinny być wyświetlane jako "nieaktywne" w kolumnie Stan.
8. Wybierz element docelowy, a następnie kliknij przycisk **Connect**. Po podłączeniu inicjatora do obiektu docelowego, stan powinien zmienić się **połączono**.
9. W **przystawki Zarządzanie dyskami** oknie zainstalowane woluminy są wyświetlane jak pokazano na poniższej ilustracji. Kliknij prawym przyciskiem myszy odnaleziony wolumin (kliknij nazwę dysku), a następnie kliknij pozycję **Online**.

> [!IMPORTANT]
> Podczas próby sklonowania wolumin lub udział z kopii zapasowej Ustaw, jeśli zadanie klonowania nie powiedzie się, wolumin docelowy lub nadal można utworzyć udziału w portalu. Jest ważne, możesz usunąć ten wolumin docelowy lub udostępnianie w portalu, aby zminimalizować problemy w przyszłości wynikające z tego elementu.
> 
> 

## <a name="item-level-recovery-ilr"></a>Odzyskiwanie na poziomie elementu (ILR)

W tej wersji wprowadzono odzyskiwanie poziomie elementu (ILR) na macierz wirtualną StorSimple, skonfigurowany jako serwer plików. Ta funkcja umożliwia to szczegółowe odzyskiwanie plików i folderów z kopia zapasowa wszystkich akcji na urządzeniu StorSimple w chmurze. Usunięte pliki można pobrać z najnowszych kopii zapasowych przy użyciu modelu samoobsługi.

Każdy udział ma *.backups* folder, który zawiera najnowsze kopie zapasowe. Możesz przejść do żądanego kopii zapasowej, skopiuj odpowiednie pliki i foldery z kopii zapasowej i je przywrócić. Ta funkcja pozwala wyeliminować wywołania do administratorów przywracania plików z kopii zapasowych.

1. Podczas przeprowadzania odzyskiwania na poziomie elementów, można wyświetlić kopie zapasowe za pośrednictwem Eksploratora plików. Kliknij pozycję określonego udziału, który chcesz Przyjrzyj się kopii zapasowej. Zostanie wyświetlony *.backups* folder utworzony w udziale, który przechowuje wszystkie kopie zapasowe. Rozwiń *.backups* folder, aby wyświetlić kopie zapasowe. Folder zawiera widok całej hierarchii kopii zapasowej. Ten widok jest tworzony na żądanie i zazwyczaj trwa tylko kilka sekund, aby utworzyć.
   
   Pięć ostatnich kopie zapasowe są wyświetlane w ten sposób i może służyć do przeprowadzenia odzyskiwania na poziomie elementu. Pięć najnowszych kopii zapasowych obejmują domyślny zaplanowany i ręcznego tworzenia kopii zapasowych.
   
   * **Zaplanowane tworzenie kopii zapasowych** o nazwie &lt;nazwy urządzenia&gt;atrybutem DailySchedule-RRRRMMDD-HHMMSS-UTC.
   * **Ręczne tworzenie kopii zapasowych** o nazwie jako Ad-hoc RRRRMMDD-HHMMSS — czas UTC.
     
     ![](./media/storsimple-virtual-array-clone/image14.png)

2. Zidentyfikuj zawierający najnowszą wersję usunięty plik kopii zapasowej. Chociaż nazwa folderu zawiera sygnaturę czasową UTC, we wszystkich powyższych przypadkach, czas, w którym został utworzony folder jest czasu rzeczywistego urządzenia, gdy tworzenie kopii zapasowej zostało rozpoczęte. Sygnatura czasowa folderu umożliwia znajdowanie i rozpoznawanie kopii zapasowych.

3. Zlokalizuj folder lub plik który chcesz przywrócić z kopii zapasowej, który został zidentyfikowany w poprzednim kroku. Należy pamiętać, że można przeglądać tylko pliki lub foldery, które mają uprawnienia do. Jeśli nie masz dostępu określone pliki lub foldery, skontaktuj się z administratorem udziału. Administrator można użyć Eksploratora plików do edytowania uprawnień udziału i uzyskanie dostępu do określonego pliku lub folderu. Jest zalecanym najlepszym rozwiązaniem, że administrator udziału jest grupą użytkowników, a nie pojedynczego użytkownika.

4. Skopiuj plik lub folder do odpowiedniego udziału na serwerze plików StorSimple.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej na temat [administrowania rozwiązania StorSimple Virtual Array przy użyciu lokalnego Interfejsu w przeglądarce](storsimple-ova-web-ui-admin.md).

