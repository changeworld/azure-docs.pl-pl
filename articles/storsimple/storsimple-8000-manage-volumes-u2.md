---
title: Zarządzanie woluminami StorSimple (aktualizacja Update 3) | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak dodawać, modyfikować, monitorowania i Usuń woluminy StorSimple i sposób wykonania ich w trybie offline, jeśli to konieczne.
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
ms.openlocfilehash: c9c575f42e6c8730b9404c62fb60e710d9d3bc80
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2017
ms.locfileid: "26578877"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>Zarządzanie woluminami (Update 3 lub nowszej) przy użyciu usługi Menedżer StorSimple urządzenia

## <a name="overview"></a>Omówienie

W tym samouczku opisano sposób korzystania z usługi Menedżer StorSimple urządzenia do tworzenia i zarządzania woluminach urządzeń z serii StorSimple 8000 z aktualizacji, 3 lub nowszym.

Usługę Menedżer StorSimple urządzenia jest rozszerzeniem w portalu Azure, która umożliwia zarządzanie rozwiązania StorSimple z interfejsem sieci web jednej. Użyj portalu Azure do zarządzania woluminami na wszystkich urządzeniach. Można również utworzyć i zarządzać usługami StorSimple, zarządzania urządzeniami, zasady tworzenia kopii zapasowej i kopii zapasowej wykazu i wyświetlać alerty.

## <a name="volume-types"></a>Typy woluminu

Woluminy StorSimple można:

* **Przypięty lokalnie woluminach**: dane w tych woluminów pozostanie na lokalnym urządzeniu StorSimple przez cały czas.
* **Woluminami warstwowymi**: dane w tych woluminów mogą zostaną przeniesione do chmury.

Archiwizacja wolumin jest typem woluminu warstwowego. Większy rozmiar fragmentu deduplikacji używana w przypadku woluminów archiwizacji umożliwia urządzeniu transfer większej segmentów danych do chmury.

Jeśli to konieczne, można zmienić woluminu typu z lokalnym warstwowej lub z warstwy do lokalnego. Aby uzyskać więcej informacji, przejdź do [zmienić typ woluminu](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Woluminów przypiętych lokalnie

Woluminów przypiętych lokalnie to pełni woluminy, które wykonują nie warstwy danych w chmurze, zapewniając lokalnego gwarantuje dla danych podstawowych, niezależnie od łączności chmury. Dane dotyczące woluminów przypiętych lokalnie nie jest deduplikowany i skompresowane; jednak migawek woluminów przypiętych lokalnie są deduplikowane. 

Woluminów przypiętych lokalnie są w pełni zaaprowizowanym; w związku z tym musi mieć wystarczającą ilość miejsca na urządzeniu, podczas ich tworzenia. Można alokować woluminy przypięte lokalnie do 8 TB na urządzeniu StorSimple 8100 i 20 TB na urządzeniu 8600 maksymalnego rozmiaru. StorSimple rezerwuje pozostałe lokalne miejsce na urządzeniu do migawki, metadane i przetwarzania danych. Można zwiększyć rozmiar woluminu przypiętego lokalnie do maksymalne dostępne miejsce, ale nie można zmniejszyć rozmiar woluminu raz utworzony.

Podczas tworzenia woluminu przypiętego lokalnie, miejsce dostępne na potrzeby tworzenia woluminów warstwowych zostanie zmniejszona. To również sytuacja odwrotna: Jeśli masz istniejące woluminy warstwowe do tworzenia lokalnie przypięty woluminy miejsca będzie niższa niż maksymalnych podanych powyżej. Aby uzyskać więcej informacji na woluminach lokalnych, zapoznaj się [— często zadawane pytania na woluminów przypiętych lokalnie](storsimple-8000-local-volume-faq.md).

### <a name="tiered-volumes"></a>Woluminy warstwowe

Woluminy warstwowe są alokowane elastycznie woluminów, w których często używanych danych pozostaje na urządzeniu lokalnym, a rzadziej używanych danych jest automatycznie warstwy do chmury. Alokowanie elastyczne jest technologii wirtualizacji, dostępny magazyn prawdopodobnie przekracza zasobów fizycznych. Zamiast z wyprzedzeniem rezerwowania wystarczającej ilości miejsca, StorSimple używa alokowania elastycznego można przydzielić tylko za mało miejsca, aby spełniały bieżące wymagania. Elastyczne rodzaj magazynu w chmurze ułatwia takie podejście, ponieważ StorSimple można zwiększyć lub zmniejszyć do zmieniających się wymagań dotyczących magazynu w chmurze.

Jeśli używasz woluminu warstwowego na potrzeby danych archiwalnych, wybierz **Użyj tego woluminu w przypadku rzadziej używanych danych archiwalnych** pole wyboru, aby zmienić rozmiar fragmentu deduplikacji dla woluminu na 512 KB. Jeśli nie zaznaczysz tej opcji, odpowiedni wolumin warstwowy użyje rozmiaru fragmentu wynoszącego 64 KB. Większy rozmiar fragmentu deduplikacji umożliwia urządzeniu usprawnienie transferu dużej ilości danych archiwalnych w chmurze.


### <a name="provisioned-capacity"></a>Udostępnione pojemności

Zapoznaj się z poniższej tabeli maksymalną pojemność udostępnione dla każdego typu urządzenia i woluminu. (Należy pamiętać, że woluminy przypięte lokalnie nie są dostępne na urządzeniu wirtualnym).

|  | Rozmiar maksymalny wolumin warstwowy | Rozmiar woluminu przypiętego lokalnie maksymalna |
| --- | --- | --- |
| **Fizyczne urządzenia** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Urządzenia wirtualne** | | |
| 8010 |30 TB |Nie dotyczy |
| 8020 |64 TB |Nie dotyczy |

## <a name="the-volumes-blade"></a>Blok woluminów

**Woluminów** bloku umożliwia zarządzanie woluminami magazynu, które są udostępniane na urządzeniu Microsoft Azure StorSimple na potrzeby Twojego inicjatorów (serwerów). Wyświetli listę woluminów na urządzenia StorSimple połączona z usługą.

 ![Strony woluminów](./media/storsimple-8000-manage-volumes-u2/volumeslist.png)

Wolumin składa się z szeregu atrybuty:

* **Nazwa woluminu** — opisową nazwę, która musi być unikatowa i pomaga w identyfikacji woluminu. Ta nazwa jest również używana do monitorowania raportów podczas filtrowania w określonym woluminie. Po utworzeniu woluminu, nie można zmienić nazwy.
* **Stan** — może być w trybie online lub offline. Jeśli wolumin jest w trybie offline, nie jest widoczny dla inicjatorów (serwerów), które mają dostęp do korzystać z woluminu.
* **Pojemność** — określa łączną ilość danych, które mogą być przechowywane przez inicjatora (serwer). Przypięty lokalnie woluminy są w pełni zaaprowizowanym i znajdują się na urządzeniu StorSimple. Woluminy warstwowe są alokowane elastycznie i jest deduplikowany danych. Z woluminy alokowane elastycznie urządzenia nie wstępnie przydzielić pojemności magazynu fizycznego wewnętrznych lub w chmurze zgodnie ze skonfigurowanym pojemność. Pojemność woluminu jest przydzielone i używane na żądanie.
* **Typ** — wskazuje, czy wolumin jest **warstwowego** (ustawienie domyślne) lub **przypięty lokalnie**.

Wykonaj instrukcje w tym samouczku, aby wykonywać następujące zadania:

* Dodawanie woluminu 
* Modyfikowanie woluminu 
* Zmień typ woluminu
* Usuwanie woluminu 
* Przełącz do trybu offline woluminu 
* Monitor woluminu 

## <a name="add-a-volume"></a>Dodawanie woluminu

Możesz [utworzony wolumin](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) podczas wdrażania urządzenia StorSimple 8000 serii. Dodawanie woluminu jest podobnej procedury.

#### <a name="to-add-a-volume"></a>Aby dodać wolumin

1. Z tabelarycznej listy urządzeń w bloku **Urządzenia** wybierz swoje urządzenie. Kliknij pozycję **+ Dodaj wolumin**.

    ![Dodawanie nowego woluminu](./media/storsimple-8000-manage-volumes-u2/step5createvol1.png)

2. W bloku **Dodawanie woluminu**:
   
    1. Pole **Wybierz urządzenie** jest wypełniane automatycznie przy użyciu danych bieżącego urządzenia.

    2. Z listy rozwijanej wybierz kontener woluminów, do którego chcesz dodać wolumin.

    3.  Wpisz wartość pola **Nazwa** dla woluminu. Po utworzeniu woluminu, nie można zmienić nazwy woluminu.

    4. Z listy rozwijanej wybierz wartość **Typ** dla woluminu. W przypadku obciążeń, które wymagają lokalnych gwarancji, małych opóźnień i większej wydajności, wybierz wolumin typu **Przypięty lokalnie**. W przypadku wszystkich innych danych wybierz wolumin typu **Warstwowy**. Jeśli używasz tego woluminu na potrzeby danych archiwalnych, wybierz opcję **Użyj tego woluminu w przypadku rzadziej używanych danych archiwalnych**.
      
       Wolumin warstwowy jest alokowany elastycznie i można go szybko utworzyć. W przypadku woluminu warstwowego przeznaczonego na dane archiwalne wybranie opcji **Użyj tego woluminu w przypadku rzadziej używanych danych archiwalnych** spowoduje zmianę rozmiaru fragmentu deduplikacji dla woluminu na 512 KB. Jeśli to pole nie zostanie zaznaczone, odpowiedni wolumin warstwowy użyje rozmiaru fragmentu wynoszącego 64 KB. Większy rozmiar fragmentu deduplikacji umożliwia urządzeniu usprawnienie transferu dużej ilości danych archiwalnych w chmurze.
       
       Wolumin przypięty lokalnie jest alokowany nieelastycznie i gwarantuje, że główne dane na woluminie pozostaną na urządzeniu lokalnym i nie zostaną przeniesione do chmury.  W przypadku tworzenia woluminu przypiętego lokalnie urządzenie sprawdza dostępne miejsce w warstwach lokalnych, aby zaalokować żądaną ilość miejsca na woluminie. Operacja tworzenia woluminów przypiętych lokalnie może obejmować przenoszenie istniejących danych z urządzenia do chmury i czas tworzenia woluminu może być długi. Łączny czas zależy od rozmiaru alokowanego woluminu, dostępnej przepustowości sieci i danych na urządzeniu.

    5. Określ **alokowaną pojemność** woluminu. Zanotuj wielkość pojemności dostępnej w oparciu o wybrany typ woluminu. Wybrany rozmiar woluminu nie może przekraczać wielkości dostępnego miejsca.
      
       Na urządzeniu 8100 można alokować woluminy przypięte lokalnie do 8,5 TB lub woluminy warstwowe do 200 TB. Na większym urządzeniu 8600 można alokować woluminy przypięte lokalnie do 22,5 TB lub woluminy warstwowe do 500 TB. Ponieważ lokalne miejsce na urządzeniu jest wymagane do obsługi zestawu roboczego woluminów warstwowych, tworzenie woluminów przypiętych lokalnie ma wpływ na miejsce dostępne do aprowizacji na woluminach warstwowych. Dlatego jeśli tworzysz wolumin przypięty lokalnie, miejsce dostępne na potrzeby tworzenia woluminów warstwowych zmniejsza się. Podobnie jeśli utworzysz wolumin warstwowy, miejsce dostępne na potrzeby tworzenia woluminów przypiętych lokalnie zmniejszy się.
      
       W przypadku alokowania woluminu przypiętego lokalnie o rozmiarze 8,5 TB (maksymalny dozwolony rozmiar) na urządzeniu 8100 całe lokalne miejsce dostępne na urządzeniu zostanie wyczerpane. Od tego momentu nie można tworzyć woluminów warstwowych, ponieważ w urządzeniu nie ma już miejsca lokalnego do hostowania roboczego zestawu woluminu warstwowego. Istniejące woluminy warstwowe również wpływają na dostępne miejsce. Jeśli na przykład masz urządzenie 8100 z woluminami warstwowymi o wielkości około 106 TB, tylko 4 TB są dostępne dla woluminów przypiętych lokalnie.

    6. W polu **Połączone hosty** kliknij strzałkę. W **połączone hosty** bloku, wybierz istniejący ACR lub Dodaj nowe ACR. Jeśli nowy ACR, następnie podaj **nazwa** dla rekordu ACR, podaj **iSCSI Qualified Name** (IQN) hosta z systemem Windows. Jeśli nie masz nazwy IQN, przejdź do tematu [Pobieranie nazwy IQN hosta z systemem Windows Server](#get-the-iqn-of-a-windows-server-host). Kliknij przycisk **Utwórz**. Wolumin zostanie utworzony przy użyciu wybranych ustawień.

        ![Kliknięcie pozycji Utwórz](./media/storsimple-8000-manage-volumes-u2/step5createvol3.png)

Nowy wolumin jest teraz gotowa do użycia.

> [!NOTE]
> Jeśli tworzenia woluminu przypiętego lokalnie, a następnie utworzyć inny wolumin przypięty lokalnie natychmiast po tworzenia woluminu, które zadania są wykonywane sekwencyjnie. Pierwszy zadanie tworzenia woluminu musi zakończyć się przed rozpoczęciem następnego zadania tworzenia woluminu.

## <a name="modify-a-volume"></a>Modyfikowanie woluminu

Zmodyfikuj woluminu, gdy trzeba go rozwinąć lub zmienić hosty, które uzyskują dostęp do woluminu.

> [!IMPORTANT]
> * Jeśli zmodyfikujesz rozmiar woluminu na urządzeniu, rozmiar woluminu musi zostać zmienione na hoście, a także.
> * Są opisane tutaj kroki po stronie hosta dla systemu Windows Server 2012 (2012 R2). Procedury dla systemu Linux lub z innymi systemami operacyjnymi hosta może się różnić. Zapoznaj się z instrukcjami podanymi przez system operacyjny hosta, modyfikując na woluminie z innym systemem operacyjnym hosta.

#### <a name="to-modify-a-volume"></a>Aby zmodyfikować woluminu

1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie kliknij pozycję **Urządzenia**. Z Tabelaryczny spis urządzeń wybierz urządzenia, które ma wolumin, który chcesz zmodyfikować. Kliknij przycisk **Ustawienia > woluminów**.

    ![Przejdź do bloku woluminów](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

2. Wybierz wolumin z tabelarycznej listę woluminów, a kliknij prawym przyciskiem myszy, aby wywołać z menu kontekstowego. Wybierz **przełączyć do trybu offline** woluminu, należy zmodyfikować przełączenia do trybu offline.

    ![Wybierz, a następnie przełącz wolumin do trybu offline](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. W **przełączyć do trybu offline** bloku, przejrzyj wpływ przełączania w tryb offline wolumin i zaznacz odpowiednie pole wyboru. Upewnij się, że odpowiedni wolumin na hoście jest najpierw w trybie offline. Informacje dotyczące sposobu podjęcia woluminu w trybie offline na serwerze hosta połączony StorSimple zapoznaj się z instrukcjami określonego systemu operacyjnego. Kliknij przycisk **przełączyć do trybu offline**.

    ![Przejrzyj wpływ przełączania w tryb offline woluminu](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

4. Gdy wolumin jest w trybie offline (jak to przedstawiono stan woluminu), wybierz wolumin, a następnie kliknij prawym przyciskiem myszy, aby wywołać z menu kontekstowego. Wybierz **zmodyfikować woluminu**.

    ![Wybierz Modyfikuj woluminu](./media/storsimple-8000-manage-volumes-u2/modifyvol9.png)


5. W **zmodyfikować woluminu** bloku, należy wprowadzić następujące zmiany:
   
   1. Wolumin **nazwa** nie może być edytowany.
   2. Konwertuj **typu** z przypięte lokalnie do warstwy lub z warstwy do przypięty lokalnie (zobacz [zmienić typ woluminu](#change-the-volume-type) Aby uzyskać więcej informacji).
   3. Zwiększ **elastycznie pojemności**. **Alokowana pojemność** zwiększyć. Nie można zmniejszyć wolumin, po jego utworzeniu.
   4. W obszarze **połączone hosty**, można zmodyfikować ACR. Aby zmodyfikować ACR, wolumin musi być w trybie offline.

       ![Przejrzyj wpływ przełączania w tryb offline woluminu](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

5. Kliknij przycisk **zapisać** Aby zapisać zmiany. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**. Azure portal wyświetli komunikat aktualizowania woluminu. Wyświetli komunikat informujący gdy wolumin został pomyślnie zaktualizowany.

    ![Przejrzyj wpływ przełączania w tryb offline woluminu](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

7. Jeśli rozszerzasz woluminu, należy wykonać następujące czynności na komputerze hosta z systemem Windows:
   
   1. Przejdź do **Zarządzanie komputerem** ->**dysku zarządzania**.
   2. Kliknij prawym przyciskiem myszy **Zarządzanie dyskami** i wybierz **Skanuj dyski**.
   3. Na liście dysków, wybierz wolumin, który zostanie zaktualizowany, kliknij prawym przyciskiem myszy, a następnie wybierz **Rozszerz wolumin**. Zostanie uruchomiony Kreator rozszerzania woluminów. Kliknij przycisk **Dalej**.
   4. Ukończ pracę kreatora, akceptując wartości domyślne. Po zakończeniu pracy Kreatora woluminu powinien być wyświetlony zwiększenie wielkości.
      
      > [!NOTE]
      > Jeśli Rozszerz wolumin przypięty lokalnie, a następnie rozwiń węzeł przypięty lokalnie inny wolumin natychmiast po woluminu rozszerzenia zadania są wykonywane sekwencyjnie. Pierwszy zadania rozszerzenia woluminu musi zakończyć się przed rozpoczęciem następnego zadania rozszerzania woluminów.
      

## <a name="change-the-volume-type"></a>Zmień typ woluminu

Można zmienić typ woluminu z warstwy do przypięty lokalnie lub z przypięty lokalnie do warstwowego. Jednak ta konwersja nie powinien być częste wystąpienie.

### <a name="tiered-to-local-volume-conversion-considerations"></a>Aby zagadnienia dotyczące konwersji woluminu lokalnego do warstwy

Niektóre przyczyny Konwertowanie woluminu z warstwy do przypięty lokalnie są:

* Lokalnych gwarancji dotyczących wydajności i dostępności danych
* Eliminacja chmury opóźnienia i problemy z łącznością chmury.

Zazwyczaj są to mały istniejące woluminy, które mają być często uzyskuje dostęp do. Wolumin przypięty lokalnie jest w pełni zaaprowizowanym podczas jego tworzenia. 

Jeśli wolumin warstwowy są konwertowane na wolumin przypięty lokalnie, StorSimple sprawdza, czy masz wystarczającą ilość miejsca na urządzeniu przed rozpoczęciem konwersji. Jeśli masz za mało miejsca, zostanie wyświetlony błąd, a operacja zostanie anulowana. 

> [!NOTE]
> Przed rozpoczęciem konwersji do warstwy do przypięty lokalnie, upewnij się, należy rozważyć wymagania dotyczące miejsca na innych obciążeń. 

Konwersja z warstwowych do woluminu przypiętego lokalnie może niekorzystnie wpłynąć na wydajność urządzenia. Ponadto następujące czynniki może wydłużyć czas wymagany do ukończenia konwersji:

* Jest za mała przepustowość.
* Nie istnieje żadne obecnie wykonać kopii zapasowej.

Aby zminimalizować wpływ, które mogą być następujące czynniki:

* Zapoznaj się z przepustowości zasad i upewnij się, że dedykowany 40 MB/s przepustowości jest dostępny.
* Planowanie konwersji poza godzinami szczytu.
* Przed rozpoczęciem konwersji, należy utworzyć migawkę chmury.

Są konwersji wielu woluminów (obsługi różnych obciążeń), następnie należy powinien priorytet konwersji woluminu, aby wyższy priorytet woluminy są konwertowane najpierw. Na przykład należy przekonwertować woluminy, które obsługi maszyn wirtualnych (VM) lub z obciążeniami SQL przed dokonaniem konwersji woluminy z obciążeniami udziału plików.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Zagadnienia dotyczące konwersji woluminu warstwowego lokalne

Możesz zmienić woluminu przypiętego lokalnie na wolumin warstwowy, jeśli potrzebujesz dodatkowego miejsca do obsługi administracyjnej inne woluminy. Podczas konwertowania woluminu przypiętego lokalnie do warstwy dostępnej pojemności na zwiększa urządzenia według rozmiaru wydanych pojemności. Jeśli problemy z łącznością zapobiec konwersji woluminu z lokalnego typu do typu warstwowych, lokalnym woluminie będzie mieć właściwości wolumin warstwowy, aż do ukończenia konwersji. Jest to spowodowane część danych może mieć rozrzucone do chmury. Te dane rozlanej w dalszym ciągu zajmują lokalne miejsce na urządzeniu, który nie jest możliwe, dopóki operacja jest uruchomiona ponownie oraz zostać zakończona.

> [!NOTE]
> Konwertowanie woluminu może zająć trochę czasu i nie można anulować konwersji po jego uruchomieniu. Gdy wolumin pozostaje w trybie online podczas konwersji i korzystać z kopii zapasowych, ale nie można rozwinąć lub przywracania woluminu, podczas konwersji.


#### <a name="to-change-the-volume-type"></a>Aby zmienić typ woluminu

1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie kliknij pozycję **Urządzenia**. Z Tabelaryczny spis urządzeń wybierz urządzenia, które ma wolumin, który chcesz zmodyfikować. Kliknij przycisk **Ustawienia > woluminów**.

    ![Przejdź do bloku woluminów](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Wybierz wolumin z tabelarycznej listę woluminów, a kliknij prawym przyciskiem myszy, aby wywołać z menu kontekstowego. Wybierz **zmodyfikować**.

    ![Modyfikowanie wybierz z menu kontekstowego](./media/storsimple-8000-manage-volumes-u2/changevoltype2.png)

4. Na **zmodyfikować woluminu** bloku, zmienić typ woluminu, wybierając nowy typ z **typu** listy rozwijanej.
   
   * W przypadku zmiany typu **przypięty lokalnie**, StorSimple będzie sprawdzać, czy ma wystarczającej wydajności.
   * W przypadku zmiany typu **warstwowego** i ten wolumin, które będą używane na potrzeby danych archiwalnych, wybierz opcję **Użyj tego woluminu w przypadku rzadziej używanych danych archiwalnych** pole wyboru.
   * Jeśli konfigurujesz woluminu przypiętego lokalnie, jak warstwowej lub _odwrotnie_, zostanie wyświetlony następujący komunikat.
   
    ![Komunikat typu woluminu zmiany](./media/storsimple-8000-manage-volumes-u2/changevoltype3.png)

7. Kliknij przycisk **Zapisz**, aby zapisać zmiany. Po wyświetleniu monitu o potwierdzenie, kliknij przycisk **tak** aby rozpocząć proces konwersji. 

    ![Zapisz i Potwierdź](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

8. Azure portal wyświetli powiadomienie w celu utworzenia zadania, który zaktualizuje woluminu. Kliknij powiadomienie, aby monitorować stan zadania woluminu w konwersji.

    ![Zadania dla konwersji woluminu](./media/storsimple-8000-manage-volumes-u2/changevoltype5.png)

## <a name="take-a-volume-offline"></a>Przełącz do trybu offline woluminu

Konieczne może być Przełącz wolumin do trybu offline podczas planowania do modyfikacji lub usunięcia woluminu. Gdy wolumin jest w trybie offline, nie jest dostępny do odczytu i zapisu. Należy wykonać woluminu w trybie offline na hoście i urządzenia.

#### <a name="to-take-a-volume-offline"></a>Wolumin przełączenia do trybu offline

1. Upewnij się, że w danym woluminie nie jest używany przed przełączeniem do trybu offline.
2. Zająć pierwszy woluminu w trybie offline na hoście. Eliminuje to potencjalne ryzyko uszkodzeniem danych na woluminie. Aby poznać konkretne kroki zapoznaj się z instrukcjami dla systemu operacyjnego hosta.
3. Gdy host jest w trybie offline, należy wykonać woluminu na urządzeniu w trybie offline, wykonując następujące czynności:
   
    1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie kliknij pozycję **Urządzenia**. Z Tabelaryczny spis urządzeń wybierz urządzenia, które ma wolumin, który chcesz zmodyfikować. Kliknij przycisk **Ustawienia > woluminów**.

        ![Przejdź do bloku woluminów](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

    2. Wybierz wolumin z tabelarycznej listę woluminów, a kliknij prawym przyciskiem myszy, aby wywołać z menu kontekstowego. Wybierz **przełączyć do trybu offline** woluminu, należy zmodyfikować przełączenia do trybu offline.

        ![Wybierz, a następnie przełącz wolumin do trybu offline](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. W **przełączyć do trybu offline** bloku, przejrzyj wpływ przełączania w tryb offline wolumin i zaznacz odpowiednie pole wyboru. Kliknij przycisk **przełączyć do trybu offline**. 

    ![Przejrzyj wpływ przełączania w tryb offline woluminu](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)
      
      Użytkownik jest powiadamiany, gdy wolumin jest w trybie offline. Stan woluminu aktualizuje również do trybu Offline.
      
4. Gdy wolumin jest w trybie offline, w przypadku wybrania wolumin i kliknij prawym przyciskiem myszy, **przejdź do trybu Online** opcja staje się dostępna w menu kontekstowym.

> [!NOTE]
> **Przełącz do trybu Offline** polecenie wysyła żądanie do urządzenia woluminu przełączenia do trybu offline. Jeśli hosty w dalszym ciągu używają woluminu, powoduje to zerwane połączenia, ale przełączania w tryb offline wolumin nie powiedzie się.

## <a name="delete-a-volume"></a>Usuwanie woluminu

> [!IMPORTANT]
> Wolumin można usunąć tylko wtedy, gdy jest w trybie offline.

Wykonaj poniższe kroki, aby usunąć wolumin.

#### <a name="to-delete-a-volume"></a>Aby usunąć wolumin

1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie kliknij pozycję **Urządzenia**. Z Tabelaryczny spis urządzeń wybierz urządzenia, które ma wolumin, który chcesz zmodyfikować. Kliknij przycisk **Ustawienia > woluminów**.

    ![Przejdź do bloku woluminów](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Sprawdź stan woluminu, który chcesz usunąć. Jeśli wolumin, który chcesz usunąć, nie jest w trybie offline, przejść do trybu offline najpierw. Postępuj zgodnie z instrukcjami [Przełącz do trybu offline wolumin](#take-a-volume-offline).
4. Gdy wolumin jest w trybie offline, wybierz wolumin, kliknij prawym przyciskiem myszy, aby wywołać z menu kontekstowego, a następnie wybierz **usunąć**.

    ![Wybierz opcję usunięcia z menu kontekstowego](./media/storsimple-8000-manage-volumes-u2/deletevol1.png)

5. W **usunąć** bloku, przejrzyj i zaznacz pole wyboru przed jego wpływ na usunięcie woluminu. Po usunięciu woluminu wszystkie dane, które znajduje się na woluminie zostaną utracone. 

    ![Zapisz i potwierdzenie zmian](./media/storsimple-8000-manage-volumes-u2/deletevol2.png)

6. Po usunięciu woluminu tabelarycznej listę woluminów aktualizuje wskazująca usunięcia.

    ![Lista zaktualizowanych woluminów](./media/storsimple-8000-manage-volumes-u2/deletevol3.png)
   
   > [!NOTE]
   > Jeśli usuniesz woluminu przypiętego lokalnie, miejsce dostępne dla nowych woluminów mogły nie zostać zaktualizowane od razu. Usługę Menedżer StorSimple urządzenia okresowo aktualizuje lokalne miejsce dostępne. Sugerujemy, zaczekaj kilka minut, zanim zostanie podjęta próba utworzenia nowego woluminu.
   >
   > Ponadto jeśli Usuń wolumin przypięty lokalnie, a następnie usuń innego lokalnie przypięty woluminu natychmiast po usuwanie woluminów, które zadania są wykonywane sekwencyjnie. Pierwszy zadania usuwania woluminu musi zakończyć się przed rozpoczęciem następnego zadania usuwania woluminu.

## <a name="monitor-a-volume"></a>Monitor woluminu

Monitorowanie woluminów umożliwia zbieranie I/E-statystyki związane z woluminu. Monitorowanie jest domyślnie włączona dla pierwszych 32 woluminów utworzonych przez Ciebie. Monitorowanie z dodatkowych woluminów jest domyślnie wyłączone. 

> [!NOTE]
> Monitorowanie woluminów sklonowany jest domyślnie wyłączone.


Wykonaj poniższe kroki, aby włączyć lub wyłączyć monitorowanie w woluminie.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Aby włączyć lub wyłączyć monitorowanie woluminu

1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie kliknij pozycję **Urządzenia**. Z Tabelaryczny spis urządzeń wybierz urządzenia, które ma wolumin, który chcesz zmodyfikować. Kliknij przycisk **Ustawienia > woluminów**.
2. Wybierz wolumin z tabelarycznej listę woluminów, a kliknij prawym przyciskiem myszy, aby wywołać z menu kontekstowego. Wybierz **zmodyfikować**.
3. W **zmodyfikować woluminu** bloku dla **monitorowanie** wybierz **włączyć** lub **wyłączyć** Aby włączyć lub wyłączyć monitorowanie.

    ![Wyłącz monitorowanie](./media/storsimple-8000-manage-volumes-u2/monitorvol1.png) 

4. Kliknij przycisk **zapisać** i po wyświetleniu monitu o potwierdzenie, kliknij przycisk **tak**. Azure portal wyświetla powiadomienie o aktualizacji woluminu, a następnie komunikat z potwierdzeniem po wolumin został pomyślnie zaktualizowany.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [sklonować woluminu StorSimple](storsimple-8000-clone-volume-u2.md).
* Dowiedz się, jak [zarządzać urządzenia StorSimple przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-8000-manager-service-administration.md).

