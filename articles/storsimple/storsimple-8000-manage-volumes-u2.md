---
title: Zarządzanie woluminami StorSimple (aktualizace 3) | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak dodawanie, modyfikowanie, monitorować i usuwanie woluminów StorSimple oraz sposób wykonania je w trybie offline, jeśli to konieczne.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/08/2017
ms.author: alkohli
ms.openlocfilehash: f7bfe41b4cdc9989c6b949011bc240275886b6f0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60634902"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>Usługa Menedżer urządzeń StorSimple umożliwia zarządzanie woluminami (Update 3 lub nowszy)

## <a name="overview"></a>Omówienie

W tym samouczku wyjaśniono, jak używać usługi Menedżer urządzeń StorSimple do tworzenia i zarządzania nimi woluminów na urządzeniach serii StorSimple 8000 z aktualizacją Update 3 i nowszych.

Usługa Menedżer urządzeń StorSimple jest rozszerzeniem w witrynie Azure portal, która umożliwia zarządzanie rozwiązania StorSimple z jednej sieci web interfejsu. Witryna Azure portal umożliwia zarządzanie woluminami na wszystkich urządzeniach. Można również utworzyć i zarządzać usługi StorSimple, zarządzania urządzeniami, zasadami tworzenia kopii zapasowych i wykazem kopii zapasowych i wyświetlać alerty.

## <a name="volume-types"></a>Typy woluminów

Woluminy StorSimple może być:

* **Lokalnie przypięte woluminy**: Dane w tych woluminach pozostanie na lokalnym urządzeniu StorSimple przez cały czas.
* **Woluminy warstwowe**: Dane w tych woluminach można zostaną przeniesione do chmury.

Archiwizacja wolumin jest typ woluminu warstwowego. Większy rozmiar fragmentu deduplikacji, używany do archiwizacji woluminy umożliwia urządzeniu przesyłanie większych segmentów danych do chmury.

Jeśli to konieczne, możesz zmienić woluminu z lokalnego na warstwowy lub z warstwowego na komputer lokalny. Aby uzyskać więcej informacji, przejdź do [zmienić typ woluminu](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Lokalnie przypięte woluminy

Woluminy przypięte lokalnie są inicjowane w pełni woluminów, które wykonują nie warstwy danych w chmurze, zapewniając lokalnego gwarancje dotyczące danych głównych, niezależnie od łączność z chmurą. Dane na woluminy przypięte lokalnie nie jest deduplikowany i skompresowane; jednak są deduplikowane, migawki woluminów przypiętych lokalnie. 

Lokalnie przypięte woluminy są w pełni zaaprowizowanym; w związku z tym musi mieć wystarczającą ilość miejsca na urządzeniu, podczas ich tworzenia. Można alokować woluminy przypięte lokalnie o rozmiarze do 8 TB na urządzeniu StorSimple 8100 i 20 TB na urządzeniu 8600. Usługa StorSimple rezerwuje pozostałe lokalne miejsce na urządzeniu do migawki, metadane i przetwarzania danych. Można zwiększyć rozmiar woluminu przypiętego lokalnie na maksymalną ilość miejsca dostępna, ale nie można zmniejszyć rozmiaru woluminu, po utworzeniu.

Podczas tworzenia woluminu przypiętego lokalnie, miejsce dostępne na potrzeby tworzenia woluminów warstwowych zostanie zmniejszona. Wartość true, występuje również sytuacja odwrotna: Jeśli masz istniejące woluminy warstwowe, miejsce dostępne dla tworzenia lokalnie przypięte woluminy będzie mniejszy niż maksymalny limit podanej powyżej. Aby uzyskać więcej informacji na woluminach lokalnych, zobacz [często zadawane pytania dotyczące woluminów przypiętych lokalnie](storsimple-8000-local-volume-faq.md).

### <a name="tiered-volumes"></a>Woluminy warstwowe

Woluminy warstwowe są woluminy alokowane elastycznie, w których często używanych danych pozostaje lokalne na urządzeniu, a rzadziej używanych danych jest automatycznie warstwowe w chmurze. Alokowanie elastyczne jest technologia wirtualizacji, dostępny magazyn prawdopodobnie przekroczyć zasobów fizycznych. Zamiast rezerwacji z wyprzedzeniem wystarczającej ilości miejsca, usługa StorSimple używa alokowania elastycznego można przydzielić tylko za mało miejsca, aby spełniały bieżące wymagania. Elastyczny charakter magazynu w chmurze ułatwia to podejście, ponieważ StorSimple można zwiększyć lub zmniejszyć magazyn w chmurze, aby spełnić potrzeby zgłaszanych.

Jeśli używasz woluminu warstwowego potrzeby danych archiwalnych, zaznacz **Użyj tego woluminu dla rzadziej używanych danych archiwalnych** pole wyboru, aby zmienić rozmiaru fragmentu deduplikacji dla woluminu na 512 KB. Jeśli ta opcja nie jest zaznaczone, odpowiedni wolumin warstwowy użyje rozmiaru fragmentu wynoszącego 64 KB. Większy rozmiar fragmentu deduplikacji umożliwia urządzeniu usprawnienie transferu dużej ilości danych archiwalnych w chmurze.


### <a name="provisioned-capacity"></a>Zaprowizowaną pojemnością

Można znaleźć w poniższej tabeli, aby uzyskać maksymalną pojemność aprowizowaną dla każdego typu urządzenia i woluminu. (Zwróć uwagę, że woluminy przypięte lokalnie nie są dostępne na urządzeniu wirtualnym).

|  | Rozmiar maksymalny woluminu warstwowego | Maksymalna lokalnie przypięte rozmiar woluminu |
| --- | --- | --- |
| **Urządzenia fizyczne** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Urządzenia wirtualne** | | |
| 8010 |30 TB |ND |
| 8020 |64 TB |ND |

## <a name="the-volumes-blade"></a>W bloku woluminów

**Woluminów** bloku pozwala na zarządzanie woluminami magazynu, które są udostępniane na urządzeniu Microsoft Azure StorSimple na potrzeby Twojego inicjatorów (serwerów). Wyświetla listę woluminów na urządzeniach StorSimple połączoną z usługą.

 ![Strony woluminów](./media/storsimple-8000-manage-volumes-u2/volumeslist.png)

Wolumin składa się z szeregu atrybuty:

* **Nazwa woluminu** — nazwę opisową, muszą być unikatowe, która ułatwia zidentyfikowanie woluminu. Ta nazwa jest również używana do monitorowania raportów podczas filtrowania na określonym woluminie. Po jego utworzeniu, nie można zmienić nazwy.
* **Stan** — może być w trybie online lub offline. Jeśli wolumin jest w trybie offline, nie jest widoczny dla inicjatorów (serwerów), które mogą uzyskiwać dostęp do korzystać z woluminu.
* **Pojemność** — określa całkowitą ilość danych, które mogą być przechowywane przez inicjatora (serwer). Lokalnie przypięte woluminy są w pełni zaaprowizowanym i znajdują się na urządzeniu StorSimple. Woluminy warstwowe są alokowany elastycznie i dane są deduplikowane. Za pomocą woluminy alokowane elastycznie urządzenia nie wstępnie Przydziel pojemność magazynu fizycznego wewnętrznie lub w chmurze zgodnie ze skonfigurowanym pojemność. Pojemność woluminu jest przydzielone i używane na żądanie.
* **Typ** — wskazuje, czy wolumin jest **warstwowy** (ustawienie domyślne) lub **przypięty lokalnie**.

Skorzystaj z instrukcji w tym samouczku, aby wykonać następujące zadania:

* Dodawanie woluminu 
* Modyfikowanie woluminu 
* Zmień typ woluminu
* Usuwanie woluminu 
* Przełącz wolumin w tryb offline 
* Monitor woluminu 

## <a name="add-a-volume"></a>Dodawanie woluminu

Możesz [utworzony wolumin](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) podczas wdrażania urządzenia StorSimple 8000 series. Dodawanie woluminu jest podobnej procedury.

#### <a name="to-add-a-volume"></a>Aby dodać wolumin

1. Z tabelarycznej listy urządzeń w bloku **Urządzenia** wybierz swoje urządzenie. Kliknij pozycję **+ Dodaj wolumin**.

    ![Dodawanie nowego woluminu](./media/storsimple-8000-manage-volumes-u2/step5createvol1.png)

2. W bloku **Dodawanie woluminu**:
   
    1. Pole **Wybierz urządzenie** jest wypełniane automatycznie przy użyciu danych bieżącego urządzenia.

    2. Z listy rozwijanej wybierz kontener woluminów, do którego chcesz dodać wolumin.

    3.  Wpisz wartość pola **Nazwa** dla woluminu. Po jego utworzeniu, nie można zmienić nazwy woluminu.

    4. Z listy rozwijanej wybierz wartość **Typ** dla woluminu. W przypadku obciążeń, które wymagają lokalnych gwarancji, małych opóźnień i większej wydajności, wybierz wolumin typu **Przypięty lokalnie**. W przypadku wszystkich innych danych wybierz wolumin typu **Warstwowy**. Jeśli używasz tego woluminu na potrzeby danych archiwalnych, wybierz opcję **Użyj tego woluminu w przypadku rzadziej używanych danych archiwalnych**.
      
       Wolumin warstwowy jest alokowany elastycznie i można go szybko utworzyć. W przypadku woluminu warstwowego przeznaczonego na dane archiwalne wybranie opcji **Użyj tego woluminu w przypadku rzadziej używanych danych archiwalnych** spowoduje zmianę rozmiaru fragmentu deduplikacji dla woluminu na 512 KB. Jeśli to pole nie zostanie zaznaczone, odpowiedni wolumin warstwowy użyje rozmiaru fragmentu wynoszącego 64 KB. Większy rozmiar fragmentu deduplikacji umożliwia urządzeniu usprawnienie transferu dużej ilości danych archiwalnych w chmurze.
       
       Wolumin przypięty lokalnie jest alokowany nieelastycznie i gwarantuje, że główne dane na woluminie pozostaną na urządzeniu lokalnym i nie zostaną przeniesione do chmury.  W przypadku tworzenia woluminu przypiętego lokalnie urządzenie sprawdza dostępne miejsce w warstwach lokalnych, aby zaalokować żądaną ilość miejsca na woluminie. Operacja tworzenia woluminów przypiętych lokalnie może obejmować przenoszenie istniejących danych z urządzenia do chmury i czas tworzenia woluminu może być długi. Łączny czas zależy od rozmiaru alokowanego woluminu, dostępnej przepustowości sieci i danych na urządzeniu.

    5. Określ **alokowaną pojemność** woluminu. Zanotuj wielkość pojemności dostępnej w oparciu o wybrany typ woluminu. Wybrany rozmiar woluminu nie może przekraczać wielkości dostępnego miejsca.
      
       Na urządzeniu 8100 można alokować woluminy przypięte lokalnie do 8,5 TB lub woluminy warstwowe do 200 TB. Na większym urządzeniu 8600 można alokować woluminy przypięte lokalnie do 22,5 TB lub woluminy warstwowe do 500 TB. Ponieważ lokalne miejsce na urządzeniu jest wymagane do obsługi zestawu roboczego woluminów warstwowych, tworzenie woluminów przypiętych lokalnie ma wpływ na miejsce dostępne do aprowizacji na woluminach warstwowych. Dlatego jeśli tworzysz wolumin przypięty lokalnie, miejsce dostępne na potrzeby tworzenia woluminów warstwowych zmniejsza się. Podobnie jeśli utworzysz wolumin warstwowy, miejsce dostępne na potrzeby tworzenia woluminów przypiętych lokalnie zmniejszy się.
      
       W przypadku alokowania woluminu przypiętego lokalnie o rozmiarze 8,5 TB (maksymalny dozwolony rozmiar) na urządzeniu 8100 całe lokalne miejsce dostępne na urządzeniu zostanie wyczerpane. Od tego momentu nie można tworzyć woluminów warstwowych, ponieważ w urządzeniu nie ma już miejsca lokalnego do hostowania roboczego zestawu woluminu warstwowego. Istniejące woluminy warstwowe również wpływają na dostępne miejsce. Jeśli na przykład masz urządzenie 8100 z woluminami warstwowymi o wielkości około 106 TB, tylko 4 TB są dostępne dla woluminów przypiętych lokalnie.

    6. W polu **Połączone hosty** kliknij strzałkę. W **połączone hosty** bloku wybierz istniejący rekord ACR lub Dodaj nowy rekord ACR. Jeśli wybierzesz nowy rekord ACR, następnie podaj **nazwa** dla rekordu ACR, podaj **iSCSI Qualified nazwę** (IQN) hosta usługi Windows. Jeśli nie masz nazwy IQN, przejdź do pobieranie nazwy IQN hosta z systemem Windows Server. Kliknij pozycję **Utwórz**. Wolumin zostanie utworzony przy użyciu wybranych ustawień.

        ![Kliknięcie pozycji Utwórz](./media/storsimple-8000-manage-volumes-u2/step5createvol3.png)

Nowy wolumin jest teraz gotowa do użycia.

> [!NOTE]
> Jeśli Utwórz wolumin przypięty lokalnie, a następnie utwórz inny wolumin przypięty lokalnie natychmiast później, tworzenie woluminów, które zadania są uruchamiane sekwencyjnie. Pierwsze zadanie tworzenia woluminu musi zakończyć się przed rozpoczęciem następnego zadania tworzenia woluminu.

## <a name="modify-a-volume"></a>Modyfikowanie woluminu

Zmodyfikuj woluminu, gdy trzeba je rozwinąć lub zmienić hostów, które dostęp do woluminu.

> [!IMPORTANT]
> * W przypadku zmodyfikowania rozmiaru woluminu na urządzeniu rozmiar woluminu musi zostać zmieniony na hoście, jak również.
> * Czynności po stronie hosta, opisane w tym miejscu dotyczą systemu Windows Server 2012 (2012 R2). Procedury dotyczące systemu Linux lub inne systemy operacyjne hosta może się różnić. Zapoznaj się z instrukcjami podanymi przez system operacyjny hosta w przypadku modyfikowania woluminu na hoście z systemem innym systemie operacyjnym.

#### <a name="to-modify-a-volume"></a>Aby zmodyfikować woluminu

1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie kliknij pozycję **Urządzenia**. Z tabelarycznej listy urządzeń wybierz urządzenie, które ma wolumin, który chcesz zmodyfikować. Kliknij przycisk **Ustawienia > woluminów**.

    ![Przejdź do bloku woluminów](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

2. Z tabelarycznej liście woluminów Wybierz wolumin, a następnie kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe. Wybierz **przełączyć do trybu offline** do woluminu, należy zmodyfikować przełączenia w tryb offline.

    ![Wybierz, a następnie przełącz wolumin w tryb offline](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. W **przełączyć do trybu offline** bloku, przejrzyj konsekwencje przełączenia woluminu w tryb offline, a następnie wybierz odpowiednie pole wyboru. Upewnij się, że odpowiedni wolumin na hoście jest najpierw w trybie offline. Aby uzyskać informacje na temat sposobu przełączyć wolumin w tryb offline na serwerze hosta podłączony do magazynu StorSimple zapoznaj się z instrukcjami określonego systemu operacyjnego. Kliknij przycisk **przełączyć do trybu offline**.

    ![Przejrzyj konsekwencje przełączenia woluminu w tryb offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

4. Gdy wolumin jest w trybie offline (jak to przedstawiono w stan woluminu), wybierz wolumin, a następnie kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe. Wybierz **modyfikowania woluminu**.

    ![Wybierz pozycję Modyfikuj wolumin](./media/storsimple-8000-manage-volumes-u2/modifyvol9.png)


5. W **modyfikowania woluminu** bloku można wprowadzić następujące zmiany:
   
   1. Wolumin **nazwa** nie może być edytowany.
   2. Konwertuj **typu** z lokalnie przypięte do warstwy lub z warstwowego na lokalnie przypięty (zobacz [zmienić typ woluminu](#change-the-volume-type) Aby uzyskać więcej informacji).
   3. Zwiększ **Aprowizowana pojemność**. **Alokowaną pojemność** tylko można zwiększyć. Nie można zmniejszyć wolumin, po jego utworzeniu.
   4. W obszarze **połączone hosty**, można zmodyfikować usługi ACR. Aby zmodyfikować rekord ACR, wolumin musi być w trybie offline.

       ![Przejrzyj konsekwencje przełączenia woluminu w tryb offline](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

5. Kliknij przycisk **Zapisz** Aby zapisać zmiany. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**. Witryna Azure portal wyświetli komunikat aktualizowanie woluminu. Wyświetli komunikat o powodzeniu po wolumin został pomyślnie zaktualizowany.

    ![Przejrzyj konsekwencje przełączenia woluminu w tryb offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

7. Jeśli rozszerzasz woluminu, wykonaj następujące czynności na komputerze hosta Windows:
   
   1. Przejdź do **Zarządzanie komputerem** ->**Zarządzanie dyskami**.
   2. Kliknij prawym przyciskiem myszy **przystawki Zarządzanie dyskami** i wybierz **Skanuj dyski**.
   3. Na liście dysków, wybierz wolumin, który należy zaktualizować, kliknij prawym przyciskiem myszy, a następnie wybierz **Rozszerz wolumin**. Zostanie uruchomiony Kreator Rozszerz wolumin. Kliknij przycisk **Dalej**.
   4. Ukończ pracę kreatora, akceptując wartości domyślne. Po zakończeniu pracy Kreatora woluminu powinien być wyświetlony zwiększony rozmiar.
      
      > [!NOTE]
      > Jeśli rozwiń woluminu przypiętego lokalnie, a następnie rozwiń innego lokalnie przypięte woluminu, natychmiast po tym dniu zadania rozszerzania woluminów uruchamiają się po kolei. Pierwsze zadanie rozszerzenia woluminu musi zakończyć się przed rozpoczęciem następnego zadania rozszerzenia woluminu.
      

## <a name="change-the-volume-type"></a>Zmień typ woluminu

Można zmienić typu woluminu z warstwowego na lokalnie przypięty, lub z lokalnie przypięte do warstwowego. Jednak ta konwersja nie powinien być często wystąpienia.

### <a name="tiered-to-local-volume-conversion-considerations"></a>Warstwowe zagadnienia dotyczące konwersji wolumin lokalny

Niektóre przyczyny konwersji woluminu z warstwowego na lokalnie przypięty są następujące:

* Lokalne gwarancje dotyczące dostępności i danych wydajności
* Eliminacja chmury opóźnienia i problemy z łącznością chmury.

Zazwyczaj są to małe woluminy, które chcesz uzyskać dostęp do często. Wolumin przypięty lokalnie to w pełni zaaprowizowanym podczas jego tworzenia. 

Jeśli wolumin warstwowy jest konwertowane na wolumin przypięty lokalnie, StorSimple sprawdza, czy mają wystarczającą ilość miejsca na urządzeniu z systemem, przed rozpoczęciem konwersji. Jeśli masz za mało miejsca, otrzymasz komunikat o błędzie, a operacja zostanie anulowana. 

> [!NOTE]
> Przed przystąpieniem do wykonywania konwersji z warstwowego na lokalnie przypięty, upewnij się, należy rozważyć wymagania dotyczące miejsca na innych obciążeń. 

Konwersja z warstwowego woluminu przypiętego lokalnie może niekorzystnie wpłynąć na wydajność urządzenia. Ponadto następujące czynniki wydłużyć czas wymagany do wykonania konwersji:

* Jest za mała przepustowość.
* Nie ma żadnej bieżącej kopii zapasowej.

Aby zminimalizować wpływ, które mogą mieć tych czynników:

* Przejrzyj zasady ograniczania przepustowości i upewnij się, że dedykowanych 40 MB/s przepustowości jest dostępny.
* Zaplanuj konwersji poza godzinami pracy.
* Wykonaj migawkę w chmurze, przed rozpoczęciem konwersji.

Jeśli konwertujesz wiele woluminów (obsługi różnych obciążeń), następnie należy nadać priorytet konwersji woluminu, aby wyższy priorytet woluminy są konwertowane najpierw. Na przykład należy przekonwertować woluminy, które hostują maszyny wirtualne (VM) lub z obciążeniami SQL przed dokonaniem konwersji woluminów z obciążeniami udziału plików.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Wersja lokalna z zagadnienia dotyczące konwersji woluminu warstwowego

Można zmienić woluminu przypiętego lokalnie na wolumin warstwowy, jeśli potrzebujesz więcej miejsca na aprowizowanie inne woluminy. Podczas konwersji woluminu przypiętego lokalnie do warstwy dostępnej pojemności na wzrost urządzenia według rozmiaru pojemności wydana. Jeśli problemy z łącznością zapobiec konwersji woluminu z lokalnego typu do typu warstwowego, do czasu ukończenia konwersji wolumin lokalny pokaże właściwości woluminu warstwowego. Jest to spowodowane może mieć rozlane dane do chmury. Te dane rozlanej w dalszym ciągu zajmować lokalne miejsce na urządzeniu, które nie jest możliwe, dopóki operacja jest uruchomiona ponownie oraz zostać zakończona.

> [!NOTE]
> Konwertowanie woluminu może zająć trochę czasu i nie można anulować konwersji po jego uruchomieniu. Podczas konwersji wolumin pozostaje w trybie online i korzystać z kopii zapasowych, ale nie można rozwinąć ani przywrócić woluminu, podczas gdy Konwersja odbywa się.


#### <a name="to-change-the-volume-type"></a>Aby zmienić typ woluminu

1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie kliknij pozycję **Urządzenia**. Z tabelarycznej listy urządzeń wybierz urządzenie, które ma wolumin, który chcesz zmodyfikować. Kliknij przycisk **Ustawienia > woluminów**.

    ![Przejdź do bloku woluminów](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Z tabelarycznej liście woluminów Wybierz wolumin, a następnie kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe. Wybierz **zmodyfikować**.

    ![Wybierz pozycję Modyfikuj z menu kontekstowego](./media/storsimple-8000-manage-volumes-u2/changevoltype2.png)

4. Na **modyfikowania woluminu** bloku, Zmień typ woluminu, wybierając nowy typ z **typu** listy rozwijanej.
   
   * W przypadku zmiany typu, który ma **przypięty lokalnie**, StorSimple będzie sprawdzać, czy istnieje wystarczająca pojemność.
   * W przypadku zmiany typu, który ma **warstwowy** i ten wolumin będą używane na potrzeby danych archiwalnych, wybierz opcję **Użyj tego woluminu dla rzadziej używanych danych archiwalnych** pole wyboru.
   * Jeśli konfigurujesz woluminu przypiętego lokalnie, zgodnie z zasadami warstwowej lub _odwrotnie_, zostanie wyświetlony następujący komunikat.
   
     ![Komunikat typu woluminu zmiany](./media/storsimple-8000-manage-volumes-u2/changevoltype3.png)

7. Kliknij przycisk **Zapisz**, aby zapisać zmiany. Po wyświetleniu monitu o potwierdzenie, kliknij przycisk **tak** można uruchomić procesu konwersji. 

    ![Zapisz i upewnij się](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

8. Witryna Azure portal wyświetli powiadomienie dla tworzenia zadania, aktualizujących woluminu. Kliknij powiadomienie, aby monitorować stan zadanie konwersji woluminu.

    ![Zadanie konwersji woluminu](./media/storsimple-8000-manage-volumes-u2/changevoltype5.png)

## <a name="take-a-volume-offline"></a>Przełącz wolumin w tryb offline

Może być konieczne przełączyć wolumin w tryb offline podczas planowania można zmodyfikować lub usunąć wolumin. Gdy wolumin jest w trybie offline, nie jest dostępny do odczytu i zapisu. Należy przełączyć wolumin w tryb offline na hoście i na urządzeniu.

#### <a name="to-take-a-volume-offline"></a>Aby przełączyć wolumin w trybie offline

1. Upewnij się, że w danym woluminie nie jest używany przed przełączeniem jej w tryb offline.
2. Wykonaj pierwszy wolumin w tryb offline na hoście. Pozwala to wyeliminować wszelkie potencjalne ryzyko uszkodzenia danych na woluminie. Aby poznać konkretne kroki zapoznaj się z instrukcjami w systemie operacyjnym hosta.
3. Gdy host jest w trybie offline, przełącz go w urządzeniu w trybie offline, wykonując następujące czynności:
   
    1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie kliknij pozycję **Urządzenia**. Z tabelarycznej listy urządzeń wybierz urządzenie, które ma wolumin, który chcesz zmodyfikować. Kliknij przycisk **Ustawienia > woluminów**.

        ![Przejdź do bloku woluminów](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

    2. Z tabelarycznej liście woluminów Wybierz wolumin, a następnie kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe. Wybierz **przełączyć do trybu offline** do woluminu, należy zmodyfikować przełączenia w tryb offline.

        ![Wybierz, a następnie przełącz wolumin w tryb offline](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. W **przełączyć do trybu offline** bloku, przejrzyj konsekwencje przełączenia woluminu w tryb offline, a następnie wybierz odpowiednie pole wyboru. Kliknij przycisk **przełączyć do trybu offline**. 

    ![Przejrzyj konsekwencje przełączenia woluminu w tryb offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)
      
      Otrzymasz powiadomienie, gdy wolumin jest w trybie offline. Stan woluminu także aktualizuje do Offline.
      
4. Gdy wolumin jest w trybie offline należy wybrać wolumin i kliknij prawym przyciskiem myszy, **przejdź do trybu Online** opcja staje się dostępna w menu kontekstowym.

> [!NOTE]
> **Przełącz do trybu Offline** polecenie wysyła żądanie do urządzenia Przełącz wolumin w tryb offline. Jeśli hosty nadal używają woluminu, powoduje to przerwane połączenia, ale przełączania w tryb offline wolumin nie zakończy się niepowodzeniem.

## <a name="delete-a-volume"></a>Usuwanie woluminu

> [!IMPORTANT]
> Wolumin można usunąć tylko wtedy, gdy jest w trybie offline.

Wykonaj poniższe kroki, aby usunąć wolumin.

#### <a name="to-delete-a-volume"></a>Aby usunąć wolumin

1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie kliknij pozycję **Urządzenia**. Z tabelarycznej listy urządzeń wybierz urządzenie, które ma wolumin, który chcesz zmodyfikować. Kliknij przycisk **Ustawienia > woluminów**.

    ![Przejdź do bloku woluminów](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Sprawdź stan woluminów, które chcesz usunąć. Jeśli wolumin, który chcesz usunąć, nie jest w trybie offline, zrób to w trybie offline najpierw. Postępuj zgodnie z instrukcjami w [przełączyć wolumin w tryb offline](#take-a-volume-offline).
4. Gdy wolumin jest w trybie offline, wybierz wolumin, kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe, a następnie wybierz **Usuń**.

    ![Wybierz opcję Usuń z menu kontekstowego](./media/storsimple-8000-manage-volumes-u2/deletevol1.png)

5. W **Usuń** bloku, przejrzyj i zaznacz pole wyboru przeciwko wpływ Usunięcie woluminu. Jeśli usuniesz wolumin, wszystkie dane, które znajdują się na woluminie zostaną utracone. 

    ![Zapisz i Potwierdź zmiany](./media/storsimple-8000-manage-volumes-u2/deletevol2.png)

6. Po usunięciu woluminu Tabelaryczną listę woluminów aktualizacji do wskazania usuwania.

    ![Lista zaktualizowanych woluminów](./media/storsimple-8000-manage-volumes-u2/deletevol3.png)
   
   > [!NOTE]
   > Jeśli usuniesz wolumin przypięty lokalnie, miejsce dostępne do nowych woluminów nie może natychmiast zaktualizować. Usługa Menedżer urządzeń StorSimple, które okresowo aktualizuje lokalne miejsce dostępne. Sugerujemy, poczekaj kilka minut, zanim zostanie podjęta próba utworzenia nowego woluminu.
   >
   > Ponadto jeśli Usuń wolumin przypięty lokalnie, a następnie usuń innego lokalnie przypięte woluminu natychmiast później, usunięcie woluminu, które zadania są uruchamiane sekwencyjnie. Pierwsze zadanie usuwania woluminu musi zakończyć się przed rozpoczęciem następnej zadania usuwania woluminu.

## <a name="monitor-a-volume"></a>Monitor woluminu

Woluminu monitorowanie umożliwia zbieranie I dotyczących wejścia/wyjścia statystyki dla woluminu. Monitorowanie jest domyślnie włączone dla pierwszych 32 woluminów, które tworzysz. Monitorowanie z dodatkowych woluminów jest domyślnie wyłączone. 

> [!NOTE]
> Monitorowanie woluminów sklonowanym jest domyślnie wyłączone.


Wykonaj poniższe kroki, aby włączyć lub wyłączyć monitorowania dla woluminu.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Aby włączyć lub wyłączyć monitorowania woluminu

1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie kliknij pozycję **Urządzenia**. Z tabelarycznej listy urządzeń wybierz urządzenie, które ma wolumin, który chcesz zmodyfikować. Kliknij przycisk **Ustawienia > woluminów**.
2. Z tabelarycznej liście woluminów Wybierz wolumin, a następnie kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe. Wybierz **zmodyfikować**.
3. W **modyfikowania woluminu** bloku dla **monitorowanie** wybierz **Włącz** lub **wyłączyć** do włączania lub wyłączania monitorowania.

    ![Wyłączanie monitorowania](./media/storsimple-8000-manage-volumes-u2/monitorvol1.png) 

4. Kliknij przycisk **Zapisz** i po wyświetleniu monitu o potwierdzenie, kliknij przycisk **tak**. Witryna Azure portal wyświetla powiadomienie o aktualizacji woluminu, a następnie komunikat o powodzeniu po pomyślnym zaktualizowaniu woluminu.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak [klonowania woluminu StorSimple](storsimple-8000-clone-volume-u2.md).
* Dowiedz się, jak [korzystać z usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

