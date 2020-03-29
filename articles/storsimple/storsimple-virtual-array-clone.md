---
title: Klonowanie kopii zapasowej tablicy wirtualnej StorSimple | Dokumenty firmy Microsoft
description: Dowiedz się, jak sklonować kopię zapasową i odzyskać plik z tablicy wirtualnej StorSimple.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60580943"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>Klonowanie z kopii zapasowej tablicy wirtualnej StorSimple

## <a name="overview"></a>Omówienie

W tym artykule opisano krok po kroku, jak sklonować zestaw kopii zapasowych udziałów lub woluminów w macierzy wirtualnej Microsoft Azure StorSimple. Sklonowana kopia zapasowa służy do odzyskiwania usuniętego lub utraconego pliku. Artykuł zawiera również szczegółowe kroki, aby wykonać odzyskiwanie na poziomie elementu na storsimple virtual array skonfigurowany jako serwer plików.

## <a name="clone-shares-from-a-backup-set"></a>Klonowanie udziałów z zestawu kopii zapasowych

**Przed przystąpieniem do klonowania udziałów upewnij się, że masz wystarczająco dużo miejsca na urządzeniu, aby ukończyć tę operację.** Aby sklonować z kopii zapasowej, w [witrynie Azure portal](https://portal.azure.com/)wykonaj następujące kroki.

#### <a name="to-clone-a-share"></a>Aby sklonować udział

1. Przejdź do bloku **Urządzenia.** Zaznacz i kliknij urządzenie, a następnie kliknij pozycję **Udostępni .** Wybierz udział, który chcesz sklonować, kliknij go prawym przyciskiem myszy, aby wywołać menu kontekstowe. Wybierz pozycję **Klonuj**.
   
   ![Klonowanie kopii zapasowej](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. W bloku **Klonuj** kliknij **pozycję Kopia zapasowa > Wybierz,** a następnie wykonaj następujące czynności: 
   
   a.    Filtruj kopię zapasową na tym urządzeniu na podstawie zakresu czasu. Możesz wybrać z **ostatnich 7 dni**, **Ostatnie 30 dni**i miniony **rok.**
   
   b.    Na wyświetlonych listach filtrowanych kopii zapasowych wybierz kopię zapasową, z z wytłoczynę.
   
   d.    Kliknij przycisk **OK**.
   
   ![Klonowanie kopii zapasowej](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. W bloku **Klonuj** kliknij pozycję **Ustawienia celu,** a następnie wykonaj następujące czynności:
   
   a.    Podaj nazwę udziału. Nazwa udziału musi zawierać od 3 do 127 znaków.
   
   b.    Opcjonalnie podaj opis sklonowanego udziału.
   
   d.    Nie można zmienić typu udziału, do którego się przywracasz. Udział warstwowy jest klonowany jako udział warstwowy i przypięty lokalnie jako przypięty lokalnie.
   
   d.    Pojemność jest ustawiona jako równa rozmiarowi udziału, z którego klonujesz.
   
   e.    Przypisz administratorów dla tego udziału. Będzie można zmodyfikować właściwości udziału za pośrednictwem Eksploratora plików po zakończeniu klonowania.
   
   f.    Kliknij przycisk **OK**.
   
   ![Klonowanie kopii zapasowej](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Kliknij **przycisk Klonuj,** aby rozpocząć zadanie klonowania. Po zakończeniu zadania rozpoczyna się operacja klonowania i użytkownik zostanie powiadomiony. Aby monitorować postęp klonowania, przejdź do **zadania** bloku i kliknij zadanie, aby wyświetlić szczegóły zadania.
5. Po pomyślnym utworzeniu klonu przejdź z powrotem do bloku **Udziały** na urządzeniu.
6. Teraz możesz wyświetlić nowy sklonowany udział na liście udziałów na urządzeniu. Udział warstwowy jest klonowany jako udział warstwowy i lokalnie przypięty jako udział przypięty lokalnie.
   
   ![Klonowanie kopii zapasowej](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Klonowanie woluminów z zestawu kopii zapasowych

Aby sklonować z kopii zapasowej, w witrynie Azure portal, należy wykonać kroki podobne do tych podczas klonowania udziału. Operacja klonowania klonuje kopię zapasową na nowy wolumin na tym samym urządzeniu wirtualnym; nie można sklonować na inne urządzenie.

#### <a name="to-clone-a-volume"></a>Aby sklonować wolumin

1. Przejdź do bloku **Urządzenia.** Zaznacz i kliknij urządzenie, a następnie kliknij pozycję **Woluminy**. Wybierz wolumin, który chcesz sklonować, kliknij prawym przyciskiem myszy wolumin, aby wywołać menu kontekstowe. Wybierz pozycję **Klonuj**.
   
   ![Klonowanie woluminu](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. W bloku **Klonuj** kliknij pozycję **Kopia zapasowa,** a następnie wykonaj następujące czynności: 
   
   a.    Filtruj kopię zapasową na tym urządzeniu na podstawie zakresu czasu. Możesz wybrać z **ostatnich 7 dni**, **Ostatnie 30 dni**i miniony **rok.** 
   
   b.    Na wyświetlonych listach filtrowanych kopii zapasowych wybierz kopię zapasową, z z wytłoczynę.
   
   d.    Kliknij przycisk **OK**.
   
   ![Klonowanie kopii zapasowej](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. W bloku **Klonuj** kliknij pozycję **Ustawienia woluminu docelowego,** a następnie wykonaj następujące czynności::
   
   a. Nazwa urządzenia jest wypełniana automatycznie.
   
   b. Podaj nazwę woluminu **sklonowanego woluminu**. Nazwa woluminu musi zawierać od 3 do 127 znaków.
   
   d. Typ woluminu jest automatycznie ustawiany na oryginalny wolumin. Wolumin warstwowy jest klonowany jako wolumin warstwowy i wolumin przypięty lokalnie jako przypięty lokalnie.
   
   d. W przypadku **połączonych hostów**kliknij przycisk **Wybierz**.
   
   ![Klonowanie kopii zapasowej](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. W bloku **Połączone hosty** wybierz z istniejącego acr lub dodaj nowy ACR. Aby dodać nowy acr, należy podać nazwę ACR i iQN hosta. Kliknij **pozycję Wybierz**.
   
   ![Klonowanie kopii zapasowej](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Kliknij **przycisk Klonuj,** aby uruchomić zadanie klonowania.
   
   ![Klonowanie kopii zapasowej](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. Po utworzeniu zadania klonowania rozpocznie się klonowanie. Po utworzeniu klonu jest on wyświetlany na bloku Woluminy na urządzeniu. Należy zauważyć, że wolumin warstwowy jest klonowany jako warstwowy, a wolumin przypięty lokalnie jest klonowany jako wolumin przypięty lokalnie.
   
   ![Klonowanie kopii zapasowej](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. Gdy wolumin pojawi się w trybie online na liście woluminów, wolumin jest dostępny do użycia. Na hoście inicjatora iSCSI odśwież listę obiektów docelowych w oknie właściwości inicjatora iSCSI. Nowy obiekt docelowy zawierający sklonowaną nazwę woluminu powinien być wyświetlany jako "nieaktywny" w kolumnie stanu.
8. Wybierz cel i kliknij przycisk **Połącz**. Po podłączeniu inicjatora do obiektu docelowego stan powinien ulec zmianie na **Połączony**.
9. W oknie **Zarządzanie dyskami** zainstalowane woluminy są wyświetlane w sposób pokazany na poniższej ilustracji. Kliknij prawym przyciskiem myszy odnaleziony wolumin (kliknij nazwę dysku), a następnie kliknij pozycję **Online**.

> [!IMPORTANT]
> Podczas próby sklonowania woluminu lub udziału z zestawu kopii zapasowych, jeśli zadanie klonowania nie powiedzie się, wolumin docelowy lub udział mogą być nadal tworzone w portalu. Ważne jest, aby usunąć ten wolumin docelowy lub udział w portalu, aby zminimalizować wszelkie przyszłe problemy wynikające z tego elementu.
> 
> 

## <a name="item-level-recovery-ilr"></a>Odzyskiwanie na poziomie elementu (ILR)

W tej wersji wprowadzono odzyskiwanie na poziomie elementu (ILR) w tablicy wirtualnej StorSimple skonfigurowaną jako serwer plików. Funkcja ta umożliwia szczegółowe odzyskiwanie plików i folderów z kopii zapasowej w chmurze wszystkich udziałów na urządzeniu StorSimple. Usunięte pliki można pobrać z ostatnich kopii zapasowych przy użyciu modelu samoobsługowego.

Każdy udział ma folder *.backups zawierający* najnowsze kopie zapasowe. Możesz przejść do żądanej kopii zapasowej, skopiować odpowiednie pliki i foldery z kopii zapasowej i przywrócić je. Ta funkcja eliminuje połączenia z administratorami w celu przywrócenia plików z kopii zapasowych.

1. Podczas wykonywania ILR, można wyświetlić kopie zapasowe za pośrednictwem Eksploratora plików. Kliknij określony udział, dla którego chcesz sprawdzić kopię zapasową. Zostanie wyświetlony folder *.backups* utworzony w ramach udziału, który przechowuje wszystkie kopie zapasowe. Rozwiń folder *.backups,* aby wyświetlić kopie zapasowe. Folder pokazuje widok rozstrzelony całej hierarchii kopii zapasowych. Ten widok jest tworzony na żądanie i zwykle trwa tylko kilka sekund, aby utworzyć.
   
   Ostatnie pięć kopii zapasowych są wyświetlane w ten sposób i może służyć do wykonywania odzyskiwania na poziomie elementu. Pięć ostatnich kopii zapasowych zawiera zarówno domyślne zaplanowane, jak i ręczne kopie zapasowe.
   
   * **Zaplanowane kopie zapasowe** o&gt;nazwie &lt;DailySchedule-YYYYMMDD-HHMMSS-UTC.
   * **Ręczne kopie zapasowe** o nazwie Ad-hoc-RRRRMDD-HHMMSS-UTC.
     
     ![](./media/storsimple-virtual-array-clone/image14.png)

2. Zidentyfikuj kopię zapasową zawierającą najnowszą wersję usuniętego pliku. Chociaż nazwa folderu zawiera sygnaturę czasową UTC w każdym z poprzednich przypadków, czas, w którym folder został utworzony, jest rzeczywistym czasem urządzenia po uruchomieniu kopii zapasowej. Użyj sygnatury czasowej folderu, aby zlokalizować i zidentyfikować kopie zapasowe.

3. Znajdź folder lub plik, który chcesz przywrócić w kopii zapasowej zidentyfikowanej w poprzednim kroku. Uwaga Można wyświetlać tylko pliki lub foldery, do których masz uprawnienia. Jeśli nie możesz uzyskać dostępu do niektórych plików lub folderów, skontaktuj się z administratorem udziału. Administrator może użyć Eksploratora plików do edytowania uprawnień udziału i udzielenia dostępu do określonego pliku lub folderu. Zaleca się, aby administrator udostępniania był grupą użytkowników, a nie pojedynczym użytkownikiem.

4. Skopiuj plik lub folder do odpowiedniego udziału na serwerze plików StorSimple.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [administrowaniu tablicą wirtualną StorSimple przy użyciu lokalnego interfejsu użytkownika sieci Web](storsimple-ova-web-ui-admin.md).

