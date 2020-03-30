---
title: Zarządzanie woluminami StorSimple (aktualizacja 3)
description: W tym artykule wyjaśniono, jak dodawać, modyfikować, monitorować i usuwać woluminy StorSimple oraz w razie potrzeby przełącz je w tryb offline.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: alkohli
ms.openlocfilehash: f32f8925bca33d90afa48071d0c0944ba63861cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254770"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>Zarządzanie woluminami za pomocą usługi StorSimple Device Manager (aktualizacja 3 lub nowsza)

## <a name="overview"></a>Omówienie

W tym samouczku wyjaśniono, jak używać usługi StorSimple Device Manager do tworzenia woluminów i zarządzania nimi na urządzeniach z serii StorSimple 8000 z aktualizacją 3 lub nowszą.

Usługa StorSimple Device Manager jest rozszerzeniem w portalu Azure, które umożliwia zarządzanie rozwiązaniem StorSimple za pomocą jednego interfejsu internetowego. Użyj witryny Azure portal do zarządzania woluminami na wszystkich urządzeniach. Można również tworzyć usługi StorSimple i zarządzać nimi, zarządzać urządzeniami, zasadami tworzenia kopii zapasowych i katalogiem kopii zapasowych oraz wyświetlać alerty.

## <a name="volume-types"></a>Typy woluminów

StorSimple woluminy mogą być:

* **Woluminy przypięte lokalnie:** Dane w tych woluminach pozostają przez cały czas na lokalnym urządzeniu StorSimple.
* **Woluminy warstwowe:** dane w tych woluminach mogą wyciekać do chmury.

Wolumin archiwalny jest typem woluminu warstwowego. Większy rozmiar fragmentu deduplikacji używany dla woluminów archiwalnych umożliwia urządzeniu przenoszenie większych segmentów danych do chmury.

W razie potrzeby można zmienić typ woluminu z lokalnego na warstwowy lub warstwowy na lokalny. Aby uzyskać więcej informacji, przejdź do [tematu Zmienianie typu woluminu](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Lokalnie przypięte woluminy

Woluminy przypięte lokalnie są w pełni aprowizowanymi woluminami, które nie warstwują danych w chmurze, zapewniając w ten sposób lokalne gwarancje dla danych podstawowych, niezależnie od łączności w chmurze. Dane na woluminach przypiętych lokalnie nie są deduplikowane i kompresowane; jednak migawki woluminów przypiętych lokalnie są deduplikowane. 

Woluminy przypięte lokalnie są w pełni aprowizowane; w związku z tym podczas tworzenia urządzenia musi być wystarczająca ilość miejsca. Na urządzeniu StorSimple 8100 można aprowizować woluminy przypięte lokalnie do maksymalnego rozmiaru 8 TB i 20 TB na urządzeniu 8600. StorSimple zastrzega sobie pozostałą przestrzeń lokalną na urządzeniu dla migawek, metadanych i przetwarzania danych. Można zwiększyć rozmiar woluminu przypiętego lokalnie do maksymalnego dostępnego miejsca, ale nie można zmniejszyć rozmiaru woluminu po utworzeniu.

Podczas tworzenia woluminu przypiętego lokalnie zmniejsza się dostępne miejsce do tworzenia woluminów warstwowych. Odwrotność jest również prawdą: jeśli masz istniejące woluminy warstwowe, miejsce dostępne do tworzenia woluminów przypiętych lokalnie będzie niższa niż maksymalne limity podane powyżej. Aby uzyskać więcej informacji na temat woluminów lokalnych, zapoznaj się z [często zadawanymi pytaniami dotyczącymi woluminów przypiętych lokalnie](storsimple-8000-local-volume-faq.md).

### <a name="tiered-volumes"></a>Woluminy warstwowe

Woluminy warstwowe są woluminami niesporządkowanymi, w których często dostępne dane pozostają lokalne na urządzeniu, a rzadziej używane dane są automatycznie warstwowe w chmurze. Elastyczne inicjowanie obsługi administracyjnej to technologia wirtualizacji, w której dostępny magazyn wydaje się przekraczać zasoby fizyczne. Zamiast rezerwować wystarczającą ilość miejsca z wyprzedzeniem, StorSimple używa elastycznej inicjowania obsługi administracyjnej, aby przydzielić wystarczającą ilość miejsca, aby spełnić bieżące wymagania. Elastyczny charakter magazynu w chmurze ułatwia to podejście, ponieważ StorSimple może zwiększyć lub zmniejszyć magazyn w chmurze, aby sprostać zmieniającym się wymaganiom.

Jeśli wolumin warstwowy jest używany do archiwizacji danych, zaznacz pole wyboru **Użyj tego woluminu dla rzadziej używanych danych archiwalnych,** aby zmienić rozmiar fragmentu deduplikacji woluminu na 512 KB. Jeśli ta opcja nie zostanie zaznaczona, odpowiedni wolumin warstwowy użyje fragmentu o rozmiarze 64 KB. Większy rozmiar fragmentu deduplikacji umożliwia urządzeniu usprawnienie transferu dużej ilości danych archiwalnych w chmurze.


### <a name="provisioned-capacity"></a>Pojemność aprowizowana

Aby uzyskać maksymalną pojemność aprowizacji dla każdego urządzenia i typu woluminu, zapoznaj się z poniższą tabelą. (Należy pamiętać, że woluminy przypięte lokalnie nie są dostępne na urządzeniu wirtualnym).

|  | Maksymalny rozmiar woluminu warstwowego | Maksymalny rozmiar woluminu przypiętego lokalnie |
| --- | --- | --- |
| **Urządzenia fizyczne** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Urządzenia wirtualne** | | |
| 8010 |30 TB |Nie dotyczy |
| 8020 |64 TB |Nie dotyczy |

## <a name="the-volumes-blade"></a>Tarcza objętościowa

**Blok Woluminy** umożliwia zarządzanie woluminami magazynu, które są aprowidzone na urządzeniu Microsoft Azure StorSimple dla inicjatorów (serwerów). Wyświetla listę woluminów na urządzeniach StorSimple podłączonych do usługi.

 ![Strona Woluminy](./media/storsimple-8000-manage-volumes-u2/volumeslist.png)

Wolumin składa się z serii atrybutów:

* **Nazwa woluminu** — opisowa nazwa, która musi być unikatowa i pomaga zidentyfikować wolumin. Ta nazwa jest również używana w raportach monitorowania podczas filtrowania na określonym woluminie. Po utworzeniu woluminu nie można zmienić jego nazwy.
* **Status** — może być w trybie online lub offline. Jeśli wolumin jest w trybie offline, nie jest widoczny dla inicjatorów (serwerów), którzy mają dostęp do korzystania z woluminu.
* **Pojemność** — określa całkowitą ilość danych, które mogą być przechowywane przez inicjatora (serwera). Woluminy przypięte lokalnie są w pełni aprowizować i znajdują się na urządzeniu StorSimple. Woluminy warstwowe są słabo aprowizacji i dane są deduplikowane. Dzięki woluminom z niesłańcą obsługi administracyjnej urządzenie nie przydziela wstępnie fizycznej pojemności pamięci masowej wewnętrznie ani w chmurze zgodnie ze skonfigurowaną pojemnością woluminu. Pojemność woluminu jest przydzielana i zużywana na żądanie.
* **Typ** — wskazuje, czy wolumin jest **warstwowy** (domyślnie) czy **przypięty lokalnie.**

Użyj instrukcji w tym samouczku, aby wykonać następujące zadania:

* Dodawanie woluminu 
* Modyfikowanie woluminu 
* Zmienianie typu woluminu
* Usuwanie woluminu 
* Przejań wolumin do trybu offline 
* Monitorowanie woluminu 

## <a name="add-a-volume"></a>Dodawanie woluminu

Wolumin [został utworzony](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) podczas wdrażania urządzenia z serii StorSimple 8000. Dodawanie woluminu jest podobną procedurą.

#### <a name="to-add-a-volume"></a>Aby dodać wolumin

1. Z tabelarycznej listy urządzeń w bloku **Urządzenia** wybierz swoje urządzenie. Kliknij pozycję **+ Dodaj wolumin**.

    ![Dodawanie nowego woluminu](./media/storsimple-8000-manage-volumes-u2/step5createvol1.png)

2. W bloku **Dodawanie woluminu**:
   
    1. Pole **Wybierz urządzenie** jest wypełniane automatycznie przy użyciu danych bieżącego urządzenia.

    2. Z listy rozwijanej wybierz kontener woluminów, do którego chcesz dodać wolumin.

    3.  Wpisz wartość pola **Nazwa** dla woluminu. Po utworzeniu woluminu nie można zmienić nazwy woluminu.

    4. Z listy rozwijanej wybierz wartość **Typ** dla woluminu. W przypadku obciążeń, które wymagają lokalnych gwarancji, małych opóźnień i większej wydajności, wybierz wolumin typu **Przypięty lokalnie**. W przypadku wszystkich innych danych wybierz wolumin typu **Warstwowy**. Jeśli używasz tego woluminu na potrzeby danych archiwalnych, wybierz opcję **Użyj tego woluminu w przypadku rzadziej używanych danych archiwalnych**.
      
       Wolumin warstwowy jest alokowany elastycznie i można go szybko utworzyć. W przypadku woluminu warstwowego przeznaczonego na dane archiwalne wybranie opcji **Użyj tego woluminu w przypadku rzadziej używanych danych archiwalnych** spowoduje zmianę rozmiaru fragmentu deduplikacji dla woluminu na 512 KB. Jeśli to pole nie zostanie zaznaczone, odpowiedni wolumin warstwowy użyje rozmiaru fragmentu wynoszącego 64 KB. Większy rozmiar fragmentu deduplikacji umożliwia urządzeniu usprawnienie transferu dużej ilości danych archiwalnych w chmurze.
       
       Wolumin przypięty lokalnie jest alokowany nieelastycznie i gwarantuje, że główne dane na woluminie pozostaną na urządzeniu lokalnym i nie zostaną przeniesione do chmury.  W przypadku tworzenia woluminu przypiętego lokalnie urządzenie sprawdza dostępne miejsce w warstwach lokalnych, aby zaalokować żądaną ilość miejsca na woluminie. Operacja tworzenia woluminów przypiętych lokalnie może obejmować przenoszenie istniejących danych z urządzenia do chmury i czas tworzenia woluminu może być długi. Łączny czas zależy od rozmiaru alokowanego woluminu, dostępnej przepustowości sieci i danych na urządzeniu.

    5. Określ **alokowaną pojemność** woluminu. Zanotuj wielkość pojemności dostępnej w oparciu o wybrany typ woluminu. Wybrany rozmiar woluminu nie może przekraczać wielkości dostępnego miejsca.
      
       Na urządzeniu 8100 można alokować woluminy przypięte lokalnie do 8,5 TB lub woluminy warstwowe do 200 TB. Na większym urządzeniu 8600 można alokować woluminy przypięte lokalnie do 22,5 TB lub woluminy warstwowe do 500 TB. Ponieważ lokalne miejsce na urządzeniu jest wymagane do obsługi zestawu roboczego woluminów warstwowych, tworzenie woluminów przypiętych lokalnie ma wpływ na miejsce dostępne do aprowizacji na woluminach warstwowych. Dlatego jeśli tworzysz wolumin przypięty lokalnie, miejsce dostępne na potrzeby tworzenia woluminów warstwowych zmniejsza się. Podobnie jeśli utworzysz wolumin warstwowy, miejsce dostępne na potrzeby tworzenia woluminów przypiętych lokalnie zmniejszy się.
      
       W przypadku alokowania woluminu przypiętego lokalnie o rozmiarze 8,5 TB (maksymalny dozwolony rozmiar) na urządzeniu 8100 całe lokalne miejsce dostępne na urządzeniu zostanie wyczerpane. Od tego momentu nie można tworzyć woluminów warstwowych, ponieważ w urządzeniu nie ma już miejsca lokalnego do hostowania roboczego zestawu woluminu warstwowego. Istniejące woluminy warstwowe również wpływają na dostępne miejsce. Jeśli na przykład masz urządzenie 8100 z woluminami warstwowymi o wielkości około 106 TB, tylko 4 TB są dostępne dla woluminów przypiętych lokalnie.

    6. W polu **Połączone hosty** kliknij strzałkę. W bloku **Połączone hosty** wybierz istniejący program ACR lub dodaj nowy program ACR. Jeśli wybierzesz nowy układ ACR, a następnie podaj **nazwę** acr, podaj **nazwę kwalifikowaną iSCSI** (IQN) hosta systemu Windows. Jeśli nie masz nazwy IQN, przejdź do tematu Pobieranie nazwy IQN hosta z systemem Windows Server. Kliknij przycisk **Utwórz**. Wolumin zostanie utworzony przy użyciu wybranych ustawień.

        ![Kliknięcie pozycji Utwórz](./media/storsimple-8000-manage-volumes-u2/step5createvol3.png)

Nowy wolumin jest teraz gotowy do użycia.

> [!NOTE]
> Jeśli utworzysz wolumin przypięty lokalnie, a następnie utworzysz kolejny wolumin przypięty lokalnie natychmiast po tym, zadania tworzenia woluminu będą uruchamiane sekwencyjnie. Pierwsze zadanie tworzenia woluminu musi zakończyć się przed rozpoczęciem następnego zadania tworzenia woluminu.

## <a name="modify-a-volume"></a>Modyfikowanie woluminu

Zmodyfikuj wolumin, gdy trzeba go rozwinąć lub zmienić hosty uzyskujące dostęp do woluminu.

> [!IMPORTANT]
> * Jeśli zmodyfikujesz rozmiar woluminu na urządzeniu, rozmiar woluminu musi zostać zmieniony również na hoście.
> * Opisane w tym miejscu kroki po stronie hosta są dla systemu Windows Server 2012 (2012R2). Procedury dla linuksa lub innych systemów operacyjnych hosta będą różne. Podczas modyfikowania woluminu na hoście z innym systemem operacyjnym należy zapoznać się z instrukcjami systemu operacyjnego hosta.

#### <a name="to-modify-a-volume"></a>Aby zmodyfikować wolumin

1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie kliknij pozycję **Urządzenia**. Z tabelarycznej listy urządzeń wybierz urządzenie, które ma wolumin, który ma zostać zmodyfikowany. Kliknij **pozycję Ustawienia > woluminów**.

    ![Przejdź do bloku Woluminy](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

2. Z tabelarycznej listy woluminów wybierz wolumin i kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe. Wybierz **pozycję Przejmij do trybu offline,** aby przetraktować wolumin w trybie offline.

    ![Wybieranie woluminu i przejmanie woluminu w tryb offline](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. W **bloku Przejmij tryb offline** przejrzyj wpływ przejmowania woluminu w tryb offline i zaznacz odpowiednie pole wyboru. Upewnij się, że odpowiedni wolumin na hoście jest najpierw w trybie offline. Aby uzyskać informacje na temat sposobu przełączenia woluminu w tryb offline na serwerze hosta podłączonym do storsimple, zapoznaj się z instrukcjami dotyczącymi systemu operacyjnego. Kliknij **pozycję Przejmij do trybu offline**.

    ![Przegląd wpływu przejmując wolumin w tryb offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

4. Po zakończeniu pracy woluminu (jak pokazuje stan woluminu) wybierz wolumin i kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe. Wybierz **pozycję Modyfikuj głośność**.

    ![Wybierz opcję modyfikowania woluminu](./media/storsimple-8000-manage-volumes-u2/modifyvol9.png)


5. W bloku **Modyfikowanie woluminu** można wprowadzić następujące zmiany:
   
   1. Nie można edytować **nazwy** woluminu.
   2. Konwertuj **typ** z lokalnie przypięty do warstwowych lub warstwowych na przypięte lokalnie (zobacz [Zmienianie typu woluminu,](#change-the-volume-type) aby uzyskać więcej informacji).
   3. Zwiększ **pojemność aprowizowana**. **Aprowizowana pojemność** może zostać zwiększona tylko. Nie można zmniejszyć woluminu po jego utworzeniu.
   4. W obszarze **Połączone hosty**można zmodyfikować acr. Aby zmodyfikować acr, wolumin musi być w trybie offline.

       ![Przegląd wpływu przejmując wolumin w tryb offline](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

5. Kliknij **przycisk Zapisz,** aby zapisać zmiany. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**. Portal Platformy Azure wyświetli komunikat o woluminie aktualizacji. Wyświetli komunikat o powodzenie, gdy wolumin został pomyślnie zaktualizowany.

    ![Przegląd wpływu przejmując wolumin w tryb offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

7. Jeśli wolumin jest rozszerzany, wykonaj następujące czynności na komputerze-hoście systemu Windows:
   
   1. Przejdź do usługi Zarządzanie **dyskami do zarządzania** ->**komputerami**.
   2. Kliknij prawym przyciskiem myszy pozycję **Zarządzanie dyskami** i wybierz polecenie **Ponownie przeskanuj dyski**.
   3. Na liście dysków wybierz wolumin, który został zaktualizowany, kliknij prawym przyciskiem myszy, a następnie wybierz polecenie **Rozszerz wolumin**. Zostanie uruchomiony Kreator rozszerzania woluminu. Kliknij przycisk **alej**.
   4. Ukończ kreatora, akceptując wartości domyślne. Po zakończeniu pracy kreatora wolumin powinien wyświetlać zwiększony rozmiar.
      
      > [!NOTE]
      > Jeśli rozwiniesz wolumin przypięty lokalnie, a następnie natychmiast rozwiniesz inny wolumin przypięty lokalnie, zadania rozszerzenia woluminu będą uruchamiane sekwencyjnie. Pierwsze zadanie rozszerzenia woluminu musi zostać zażene przed rozpoczęciem następnego zadania rozszerzenia woluminu.
      

## <a name="change-the-volume-type"></a>Zmienianie typu woluminu

Typ woluminu można zmienić z warstwowego na przypięty lokalnie lub z przypiętego lokalnie do warstwowego. Jednak ta konwersja nie powinna być częstym zjawiskiem.

### <a name="tiered-to-local-volume-conversion-considerations"></a>Zagadnienia dotyczące konwersji woluminów lokalnych warstwowe do lokalnego

Oto kilka przyczyn konwersji woluminu z warstwowego na przypięty lokalnie:

* Lokalne gwarancje dotyczące dostępności i wydajności danych
* Eliminacja opóźnień w chmurze i problemów z łącznością w chmurze.

Zazwyczaj są to małe istniejące woluminy, do których chcesz często uzyskiwać dostęp. Wolumin przypięty lokalnie jest w pełni aprowizowany podczas jego tworzenia. 

Jeśli konwertujesz wolumin warstwowy na wolumin przypięty lokalnie, StorSimple sprawdza, czy masz wystarczająco dużo miejsca na urządzeniu przed rozpoczęciem konwersji. Jeśli masz za mało miejsca, zostanie wyświetlony błąd, a operacja zostanie anulowana. 

> [!NOTE]
> Przed rozpoczęciem konwersji warstwowej do przypiętych lokalnie upewnij się, że należy wziąć pod uwagę wymagania dotyczące miejsca innych obciążeń. 

Konwersja z woluminu warstwowego na wolumin przypięty lokalnie może negatywnie wpłynąć na wydajność urządzenia. Ponadto następujące czynniki mogą wydłużyć czas potrzebny do ukończenia konwersji:

* Nie ma wystarczającej przepustowości.
* Nie ma bieżącej kopii zapasowej.

Aby zminimalizować skutki, jakie mogą mieć te czynniki:

* Zapoznaj się z zasadami ograniczania przepustowości i upewnij się, że dostępna jest dedykowana przepustowość 40 Mb/s.
* Zaplanuj konwersję poza godzinami szczytu.
* Zrób migawkę w chmurze przed rozpoczęciem konwersji.

Jeśli konwertujesz wiele woluminów (obsługujących różne obciążenia), należy nadać priorytet konwersji woluminów, aby najpierw przekonwertować woluminy o wyższym priorytecie. Na przykład należy przekonwertować woluminy, które hostuje maszyny wirtualne (maszyny wirtualne) lub woluminy z obciążeniami SQL przed konwersją woluminów z obciążeniami udziału plików.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Zagadnienia dotyczące konwersji woluminu lokalnego do warstwowego

Można zmienić wolumin przypięty lokalnie na wolumin warstwowy, jeśli potrzebujesz dodatkowego miejsca do aprowizowania innych woluminów. Podczas konwertowania woluminu przypiętego lokalnie na warstwowy, dostępna pojemność na urządzeniu zwiększa się o rozmiar zwolnionej pojemności. Jeśli problemy z łącznością uniemożliwiają konwersję woluminu z typu lokalnego do typu warstwowego, wolumin lokalny będzie wykazywał właściwości woluminu warstwowego do czasu zakończenia konwersji. Dzieje się tak, ponieważ niektóre dane mogły zostać rozlane do chmury. Te rozlane dane nadal zajmują miejsce lokalne na urządzeniu, które nie może zostać zwolnione, dopóki operacja nie zostanie ponownie uruchomiona i zakończona.

> [!NOTE]
> Konwersja woluminu może zająć trochę czasu i nie można anulować konwersji po jej uruchomieniu. Wolumin pozostaje w trybie online podczas konwersji i można wykonać kopie zapasowe, ale nie można rozszerzyć ani przywrócić woluminu podczas konwersji.


#### <a name="to-change-the-volume-type"></a>Aby zmienić typ woluminu

1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie kliknij pozycję **Urządzenia**. Z tabelarycznej listy urządzeń wybierz urządzenie, które ma wolumin, który ma zostać zmodyfikowany. Kliknij **pozycję Ustawienia > woluminów**.

    ![Przejdź do bloku Woluminy](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Z tabelarycznej listy woluminów wybierz wolumin i kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe. Wybierz **pozycję Modyfikuj**.

    ![Wybierz opcję modyfikuj z menu kontekstowego](./media/storsimple-8000-manage-volumes-u2/changevoltype2.png)

4. W bloku **Modyfikuj wolumin** zmień typ woluminu, wybierając nowy typ z listy rozwijanej **Typ.**
   
   * Jeśli zmieniasz typ na **Lokalnie przypięty,** StorSimple sprawdzi, czy istnieje wystarczająca pojemność.
   * Jeśli zmieniasz typ na **warstwowy** i ten wolumin będzie używany do archiwizacji danych, zaznacz pole wyboru **Użyj tego woluminu dla rzadziej używanych danych archiwalnych.**
   * Jeśli konfigurujesz wolumin przypięty lokalnie jako wolumin warstwowy lub _odwrotnie,_ zostanie wyświetlony następujący komunikat.
   
     ![Zmienianie komunikatu typu woluminu](./media/storsimple-8000-manage-volumes-u2/changevoltype3.png)

7. Kliknij przycisk **Zapisz**, aby zapisać zmiany. Po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak,** aby rozpocząć proces konwersji. 

    ![Zapisz i potwierdź](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

8. Portal Platformy Azure wyświetla powiadomienie o utworzeniu zadania, które zaktualizuje wolumin. Kliknij powiadomienie, aby monitorować stan zadania konwersji woluminu.

    ![Zadanie konwersji woluminów](./media/storsimple-8000-manage-volumes-u2/changevoltype5.png)

## <a name="take-a-volume-offline"></a>Przejań wolumin do trybu offline

Podczas planowania modyfikacji lub usunięcia woluminu może być konieczne przełączeniu woluminu w tryb offline. Gdy wolumin jest w trybie offline, nie jest dostępny dla dostępu do odczytu i zapisu. Wolumin należy przetraktować w trybie offline na hoście i urządzeniu.

#### <a name="to-take-a-volume-offline"></a>Aby przetraktować wolumin w trybie offline

1. Upewnij się, że dany wolumin nie jest używany przed przełączeniem go w tryb offline.
2. Najpierw przejmij wolumin w tryb offline na hoście. Eliminuje to wszelkie potencjalne ryzyko uszkodzenia danych na woluminie. Aby uzyskać szczegółowe kroki, zapoznaj się z instrukcjami dotyczącymi systemu operacyjnego hosta.
3. Po przejadaniu hosta w tryb offline przejmij wolumin na urządzeniu w tryb offline, wykonując następujące czynności:
   
    1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie kliknij pozycję **Urządzenia**. Z tabelarycznej listy urządzeń wybierz urządzenie, które ma wolumin, który ma zostać zmodyfikowany. Kliknij **pozycję Ustawienia > woluminów**.

        ![Przejdź do bloku Woluminy](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

    2. Z tabelarycznej listy woluminów wybierz wolumin i kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe. Wybierz **pozycję Przejmij do trybu offline,** aby przetraktować wolumin w trybie offline.

        ![Wybieranie woluminu i przejmanie woluminu w tryb offline](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. W **bloku Przejmij tryb offline** przejrzyj wpływ przejmowania woluminu w tryb offline i zaznacz odpowiednie pole wyboru. Kliknij **pozycję Przejmij do trybu offline**. 

    ![Przegląd wpływu przejmując wolumin w tryb offline](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)
      
      Użytkownik jest powiadamiany, gdy wolumin jest w trybie offline. Stan woluminu jest również aktualizowany w trybie offline.
      
4. Po zakończeniu pracy woluminu po wybraniu woluminu i kliknięciu prawym przyciskiem myszy opcja **Przetwórdź online** stanie się dostępna w menu kontekstowym.

> [!NOTE]
> Polecenie **Przejmij do trybu offline** wysyła żądanie do urządzenia w celu przejmunia wolumin w tryb offline. Jeśli hosty nadal używają woluminu, powoduje to przerwanie połączeń, ale przełączenie woluminu w tryb offline nie zakończy się niepowodzeniem.

## <a name="delete-a-volume"></a>Usuwanie woluminu

> [!IMPORTANT]
> Wolumin można usunąć tylko wtedy, gdy jest w trybie offline.

Wykonaj następujące kroki, aby usunąć wolumin.

#### <a name="to-delete-a-volume"></a>Aby usunąć wolumin

1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie kliknij pozycję **Urządzenia**. Z tabelarycznej listy urządzeń wybierz urządzenie, które ma wolumin, który ma zostać zmodyfikowany. Kliknij **pozycję Ustawienia > woluminów**.

    ![Przejdź do bloku Woluminy](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Sprawdź stan woluminu, który chcesz usunąć. Jeśli wolumin, który chcesz usunąć, nie jest w trybie offline, należy najpierw przejść do trybu offline. Wykonaj kroki opisane w [obszarze Przejmuj wolumin w tryb offline](#take-a-volume-offline).
4. Po zakończeniu pracy woluminu wybierz wolumin, kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe, a następnie wybierz polecenie **Usuń**.

    ![Wybierz polecenie Usuń z menu kontekstowego](./media/storsimple-8000-manage-volumes-u2/deletevol1.png)

5. W **bloku Usuń** przejrzyj i zaznacz pole wyboru przed wpływem usunięcia woluminu. Po usunięciu woluminu wszystkie dane, które znajdują się na woluminie zostaną utracone. 

    ![Zapisywanie i potwierdzanie zmian](./media/storsimple-8000-manage-volumes-u2/deletevol2.png)

6. Po usunięciu woluminu tabelaryczny listy woluminów zostanie zaktualizowana w celu wskazania usunięcia.

    ![Zaktualizowana lista woluminów](./media/storsimple-8000-manage-volumes-u2/deletevol3.png)
   
   > [!NOTE]
   > Jeśli usuniesz wolumin przypięty lokalnie, miejsce dostępne dla nowych woluminów może nie zostać natychmiast zaktualizowane. Usługa Menedżera urządzeń StorSimple okresowo aktualizuje miejsce lokalne dostępne. Zalecamy odczekanie kilku minut przed próbą utworzenia nowego woluminu.
   >
   > Ponadto jeśli usuniesz wolumin przypięty lokalnie, a następnie natychmiast usuniesz inny wolumin przypięty lokalnie, zadania usuwania woluminu będą uruchamiane sekwencyjnie. Pierwsze zadanie usuwania woluminu musi zakończyć się przed rozpoczęciem następnego zadania usuwania woluminu.

## <a name="monitor-a-volume"></a>Monitorowanie woluminu

Monitorowanie woluminów umożliwia zbieranie statystyk związanych z we/wy dla woluminu. Monitorowanie jest domyślnie włączone dla pierwszych 32 woluminów, które tworzysz. Monitorowanie dodatkowych woluminów jest domyślnie wyłączone. 

> [!NOTE]
> Monitorowanie sklonowanych woluminów jest domyślnie wyłączone.


Wykonaj następujące kroki, aby włączyć lub wyłączyć monitorowanie woluminu.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Aby włączyć lub wyłączyć monitorowanie woluminów

1. Przejdź do usługi Menedżer urządzeń StorSimple, a następnie kliknij pozycję **Urządzenia**. Z tabelarycznej listy urządzeń wybierz urządzenie, które ma wolumin, który ma zostać zmodyfikowany. Kliknij **pozycję Ustawienia > woluminów**.
2. Z tabelarycznej listy woluminów wybierz wolumin i kliknij prawym przyciskiem myszy, aby wywołać menu kontekstowe. Wybierz **pozycję Modyfikuj**.
3. W bloku **Modyfikowanie woluminu** dla **monitorowania** wybierz **włącz** lub **wyłącz,** aby włączyć lub wyłączyć monitorowanie.

    ![Wyłączanie monitorowania](./media/storsimple-8000-manage-volumes-u2/monitorvol1.png) 

4. Kliknij **przycisk Zapisz,** a po wyświetleniu monitu o potwierdzenie kliknij przycisk **Tak**. Portal Platformy Azure wyświetla powiadomienie o zaktualizowaniu woluminu, a następnie komunikat o sukcesie po pomyślnym zaktualizowaniu woluminu.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [sklonować wolumin StorSimple](storsimple-8000-clone-volume-u2.md).
* Dowiedz się, jak [zarządzać urządzeniem StorSimple za pomocą usługi StorSimple Device Manager.](storsimple-8000-manager-service-administration.md)

