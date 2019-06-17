---
title: Usługa StorSimple lokalnie przypięte woluminy — często zadawane pytania | Dokumentacja firmy Microsoft
description: Zawiera odpowiedzi na często zadawane pytania dotyczące usługi StorSimple lokalnie przypięte woluminy.
services: storsimple
documentationcenter: NA
author: manuaery
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2017
ms.author: manuaery
ms.openlocfilehash: aa69d8b07d31b5cf0386e34c113475cbf4191891
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60319551"
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>Usługa StorSimple lokalnie przypięte woluminy: często zadawane pytania (FAQ)
## <a name="overview"></a>Omówienie
Poniżej przedstawiono pytania i odpowiedzi, które mogą wystąpić podczas tworzenia woluminu StorSimple, przypięty lokalnie, konwertowanie woluminu warstwowego na wolumin przypięty lokalnie (i odwrotnie) lub tworzenie kopii zapasowej i przywracanie woluminu przypiętego lokalnie.

Pytania i odpowiedzi są zestawiane w następujące kategorie

* Tworzenie woluminu przypiętego lokalnie
* Tworzenie kopii zapasowych lokalnie przypięte
* Konwertowanie woluminu warstwowego na wolumin przypięty lokalnie
* Przywracanie woluminu przypiętego lokalnie
* Przechodzenie w tryb failover wolumin przypięty lokalnie

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Pytania dotyczące tworzenia woluminu przypiętego lokalnie
**PYTANIE** Co to jest maksymalny rozmiar woluminu przypiętego lokalnie, które można utworzyć na urządzeniach z serii 8000?

**A** na urządzeniach z systemem StorSimple 8000 Series Update w wersji 3.0 można alokować woluminy przypięte lokalnie do 8,5 TB lub woluminy warstwowe do 200 TB na urządzeniu 8100. Na większym urządzeniu 8600 można alokować woluminy przypięte lokalnie do 22,5 TB lub woluminy warstwowe do 500 TB.

**PYTANIE** Update w wersji 3.0 niedawno uaktualniony urządzenie 8100 i podczas tworzenia woluminu przypiętego lokalnie, maksymalny rozmiar dostępny jest tylko do 6 TB i nie 8,5 TB. Dlaczego nie można utworzyć woluminu o rozmiarze 8,5 TB

**A** Jeśli urządzenie działa aktualizacja 3.0, można alokować woluminy przypięte lokalnie do 8,5 TB lub woluminy warstwowe do 200 TB na urządzeniu 8100. Jeśli urządzenie woluminami warstwowymi, miejsce dostępne do tworzenia woluminu przypiętego lokalnie będzie proporcjonalnie mniejszy niż ten maksymalny limit. Na przykład jeśli już zostały udostępnione woluminy warstwowe około 106 TB na urządzeniu 8100 (czyli połowę pojemności woluminu warstwowego), następnie maksymalny rozmiar woluminu lokalnego, który można utworzyć na urządzeniu 8100 odpowiednio zmniejszy się do 4 TB (około połowie maksymalnej lokalnie przypięty pojemność woluminu).

Ponieważ lokalne miejsce na urządzeniu są używane do hostowania zestawu roboczego woluminów warstwowych, dostępne miejsce w przypadku tworzenia woluminu przypiętego lokalnie jest ograniczona, jeśli urządzenie ma woluminy warstwowe. Z drugiej strony tworzenia woluminu przypiętego lokalnie proporcjonalnie zmniejsza dostępne miejsce na woluminach warstwowych. Poniższych tabelach zestawiono dostępne pojemności woluminu warstwowego na urządzeniach 8100 i 8600 podczas tworzenia woluminów przypiętych lokalnie.

#### <a name="update-30"></a>Update 3.0 

| Woluminy przypięte lokalnie zaprowizowaną pojemnością | Dostępna pojemność do aprowizowania woluminów warstwowych — 8100 | Dostępna pojemność do aprowizowania woluminów warstwowych — 8600 |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176.5 TB |477.8 TB |
| 4 TB |105.9 TB |411.1 TB |
| 8,5 TB |0 TB |311.1 TB |
| 10 TB |Nie dotyczy |277.8 TB |
| 15 TB |Nie dotyczy |166.7 TB |
| 22,5 TB |Nie dotyczy |0 TB |

**PYTANIE** Dlaczego jest utworzenie woluminu przypiętego lokalnie długotrwałej operacji?

**ODPOWIEDŹ** Woluminy przypięte lokalnie są aprowizowane. Aby utworzyć miejsce w warstwach lokalnych, urządzenia, pewne dane z istniejące woluminy warstwowe może zostać przeniesiony do chmury podczas procesu inicjowania obsługi administracyjnej. A ponieważ to zależy od rozmiaru woluminu aprowizowane, istniejące dane na urządzeniu i dostępną przepustowość do chmury, czas tworzenia woluminu lokalnego może być kilka godzin.

**PYTANIE** Jak długo trwa Utwórz wolumin przypięty lokalnie?

**ODPOWIEDŹ** Ponieważ lokalnie przypięte woluminy są aprowizowane, niektóre istniejące dane z woluminów warstwowych może zostać przeniesiony do chmury podczas procesu inicjowania obsługi administracyjnej. W związku z tym czas tworzenia woluminu przypiętego lokalnie zależy od wielu czynników, takich jak rozmiar woluminu, dane na urządzeniu i dostępną przepustowość. Na urządzeniu świeżo zainstalowanym ma woluminów czas tworzenia woluminu przypiętego lokalnie jest około 10 minut za terabajt danych. Jednak tworzenie woluminów lokalnych może potrwać kilka godzin na podstawie czynników wyjaśniono powyżej na urządzeniu, który jest używany.

**PYTANIE** Chcę utworzyć woluminu przypiętego lokalnie. Czy istnieją najlepsze rozwiązania, które należy wiedzieć o?

**ODPOWIEDŹ** Woluminy przypięte lokalnie są odpowiednie dla obciążeń, które wymagają lokalnych gwarancji danych przez cały czas i są wrażliwe na opóźnienia w chmurze. Rozważając użycie woluminów lokalnych dla dowolnego obciążenia, należy mieć świadomość następujących czynności:

* Woluminy przypięte lokalnie są aprowizowane i tworzenie woluminów lokalnych ma wpływ na dostępne miejsce na woluminach warstwowych. Dlatego zalecamy rozpoczynać mniejszym woluminów i skalowanie w górę wzrostem zapotrzebowania magazynu.
* Inicjowanie obsługi woluminów lokalnych jest operacją wymagającą dużo czasu, który może obejmować wypychanie istniejących danych z woluminów warstwowych do chmury. W rezultacie mogą występować zmniejszenie wydajności na tych woluminach.
* Inicjowanie obsługi woluminów lokalnych jest czasochłonna operacja. Musisz tracić czasu rzeczywistego zależy od wielu czynników: rozmiar woluminu aprowizowane, dane na urządzeniu i dostępną przepustowość. Jeśli nie masz kopię istniejących woluminów do chmury, Tworzenie woluminu jest wolniejsze. Sugerujemy, aby wykonać migawki w chmurze istniejących woluminów przed udostępnieniem lokalnym woluminie.
* Można przekonwertować istniejące woluminy warstwowe woluminy przypięte lokalnie, a ta konwersja obejmuje Inicjowanie obsługi administracyjnej miejsca na urządzeniu na potrzeby wynikowego woluminu przypiętego lokalnie (oprócz wyłączania dane warstwowe, jeśli dowolny z chmury). Ponownie jest to długotrwała operacja, która jest zależna od czynników, które zostały omówione powyżej. Zaleca się tworzenie kopii zapasowej przed konwersji istniejących woluminów jako proces będzie jeszcze niższa, jeśli istniejące woluminy są nie kopię zapasową. Urządzenia mogą wystąpić również zmniejszenie wydajności w trakcie tego procesu.

Więcej informacji na temat sposobu [Utwórz wolumin przypięty lokalnie](storsimple-8000-manage-volumes-u2.md#add-a-volume)

**PYTANIE** Czy można utworzyć wiele woluminów przypiętych lokalnie, w tym samym czasie?

**ODPOWIEDŹ** Tak, ale wszystkie zadania tworzenia i rozszerzenia woluminu przypiętego lokalnie są przetwarzane sekwencyjnie.

Woluminy przypięte lokalnie jest alokowany nieelastycznie i wymaga utworzenia lokalne miejsce na urządzeniu (co może prowadzić do istniejących danych z woluminów warstwowych, które ma zostać wypchnięty do chmury podczas procesu inicjowania obsługi administracyjnej). W związku z tym jeśli zadanie inicjowania obsługi administracyjnej jest w toku, inne zadania tworzenia woluminu lokalnego zostanie umieszczona w kolejce do momentu ukończenia zadania.

Podobnie jeśli jest rozszerzana istniejącego woluminu lokalnego lub wolumin warstwowy jest przekształcany na wolumin przypięty lokalnie, utworzenia nowego woluminu przypiętego lokalnie jest kolejkowana ukończenie poprzedniego zadania. Rozmiar woluminu przypiętego lokalnie obejmuje rozszerzenie istniejące lokalne miejsce dla danego woluminu. Konwersja z warstwowego na lokalnie przypięty woluminów obejmuje również tworzenie lokalnego miejsca dla wynikowy lokalnie przypiętych woluminów. W obu tych operacji, tworzenia lub rozszerzania lokalne miejsce bardzo długo działa zadanie.

Możesz wyświetlić te zadania w **zadań** bloku usługi Menedżer urządzeń StorSimple. Zadanie, które jest aktywnie przetwarzany jest stale aktualizowany w celu odzwierciedlenia postęp aprowizowania miejsca. Pozostałe zadania woluminu przypiętego lokalnie są oznaczone jako uruchomiona, ale ich postęp został wstrzymany i pobrane w kolejności, w jakiej one znajdują się w kolejce.

**PYTANIE** Czy mogę usunąć woluminu przypiętego lokalnie. Dlaczego nie widzę odzyskiwanego miejsca, które zostaną uwzględnione w dostępnego miejsca, przy próbie utworzenia nowego woluminu

**ODPOWIEDŹ** Jeśli usuniesz wolumin przypięty lokalnie, miejsce dostępne do nowych woluminów nie może natychmiast zaktualizować. Usługa Menedżer urządzeń StorSimple aktualizuje lokalne miejsce dostępne mniej więcej co godzinę. Sugerujemy, zaczekaj godzinę przed próbą utworzenia nowego woluminu.

**PYTANIE** Woluminy przypięte lokalnie obsługiwanych na urządzeniu w chmurze?

**ODPOWIEDŹ** Woluminy przypięte lokalnie nie są obsługiwane na urządzeniu w chmurze (8010 i 8020 urządzenia wcześniej określonych jako urządzenie wirtualne StorSimple).

**PYTANIE** Tworzenie i zarządzanie nimi woluminów przypiętych lokalnie może używać poleceń cmdlet programu Azure PowerShell?

**ODPOWIEDŹ** Nie, nie można utworzyć woluminy przypięte lokalnie za pomocą poleceń cmdlet programu Azure PowerShell (warstwowa woluminów utworzonych za pomocą programu Azure PowerShell). Również Sugerujemy, że należy używać poleceń cmdlet programu Azure PowerShell Aby zmodyfikować dowolne właściwości woluminu przypiętego lokalnie, ponieważ miałoby niepożądane efekt modyfikowanie typu woluminu do warstwowego.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Pytania dotyczące tworzenia kopii zapasowej woluminu przypiętego lokalnie
**PYTANIE** Czy na lokalne migawki woluminów przypiętych lokalnie obsługiwana?

**ODPOWIEDŹ** Tak, możesz korzystać z lokalnych migawek woluminów przypiętych lokalnie. Jednak zdecydowanie zaleca się regularne tworzenie kopii zapasowej woluminów przypiętych lokalnie z migawkami w chmurze, upewnij się, że Twoje dane są chronione w przypadku możliwości awarii.

Pamiętaj, że lokalne migawki woluminów przypiętych lokalnie może również warstwy się do chmury i nie ma gwarancji pozostanie w warstwie lokalnej urządzenia.

**PYTANIE** Czy istnieją jakiekolwiek wskazówki dotyczące zarządzania lokalne migawki dla woluminów przypiętych lokalnie?

**ODPOWIEDŹ** Częste lokalne migawki obok wysoki współczynnik zmian danych w woluminu przypiętego lokalnie może spowodować lokalne miejsce na urządzeniu w celu szybkie zajmowanie i spowodować dane z woluminów warstwowych wypychania w chmurze. W związku z tym sugerujemy zminimalizować liczbę lokalnych migawek.

**PYTANIE** Został wyświetlony alert informujący, może unieważniony Moje lokalne migawki woluminów przypiętych lokalnie. Jeśli to możliwe?

**ODPOWIEDŹ** Częste lokalne migawki obok wysoki współczynnik zmian danych w woluminu przypiętego lokalnie może spowodować lokalne miejsce na urządzeniu, aby szybko wykorzystać. Jeśli warstwach lokalnych, które urządzenia są intensywnie używane, awaria rozszerzonej chmury może prowadzić do urządzenia zapełnianiu i przychodzące zapisu do woluminu może spowodować unieważniania migawek (jak miejsce nie istnieje w celu aktualizacji migawek do odwoływania się do starszych bloki konstrukcyjne dane, które zostały zastąpione). W takiej sytuacji operacje zapisu do woluminu będą nadal ma być obsługiwana, ale migawki lokalne mogą być nieprawidłowe. Nie ma to wpływu na istniejące migawki w chmurze.

Jest alertu ostrzeżenie informujące, że taka sytuacja może wystąpić i upewnij się, że taki sam adres w sposób terminowy albo przeglądanie harmonogramy lokalnych migawek do podjęcia mniej częste lokalne migawki lub usuwając starsze lokalne migawki, które nie są już wymagane.

Migawki lokalne są unieważniane, otrzymasz alert informacyjny informujący o tym, że lokalne migawki dla określonych zasad tworzenia kopii zapasowej zostały unieważnione obok listy czasowe lokalne migawki, które zostały unieważnione. Migawki te będą usuwane automatycznie i nie będzie można wyświetlić je w **katalogi kopii zapasowej** bloku w witrynie Azure portal.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Pytania dotyczące Konwertowanie woluminu warstwowego na wolumin przypięty lokalnie
**PYTANIE** Czy mogę jestem obserwowania niektórych powolność na urządzeniu podczas konwertowania woluminu warstwowego woluminu przypiętego lokalnie. Dlaczego tak się dzieje?

**ODPOWIEDŹ** Proces konwersji obejmuje dwa kroki:

1. Inicjowanie obsługi administracyjnej miejsca na urządzeniu na potrzeby wkrótce do--skonwertowania lokalnie przypięte woluminu.
2. Pobieranie wszelkie dane warstwowe z chmury w celu zapewnienia lokalnych gwarancji.

Oba te kroki są za długie długotrwałych operacji, które są zależne od rozmiaru woluminu konwertowane dane na urządzeniu i dostępną przepustowość. Ponieważ pewne dane z istniejące woluminy warstwowe mogą zostaną przeniesione do chmury w ramach procesu inicjowania obsługi administracyjnej, urządzenie może wystąpić zmniejszenie wydajności, w tym czasie. Ponadto, proces konwersji może być wolniejsze jeśli:

* Istniejące woluminy nie utworzone kopie zapasowe w chmurze; Dlatego zalecamy utworzenie kopii zapasowej woluminów przed zainicjowaniem konwersji.
* Zastosowano zasady ograniczania przepustowości, który może stanowić ograniczenie dostępną przepustowość w chmurze. Firma Microsoft zaleca w związku z tym, że masz dedykowane MB/s 40 lub więcej połączeń w chmurze.
* Proces konwersji może potrwać kilka godzin, ze względu na wiele czynników opisane powyżej. Dlatego zaleca się wykonanie tej operacji w czasie, bez szczytów lub w weekendy, aby uniknąć wpływu na użytkowników końcowych.

Więcej informacji na temat sposobu [Konwertowanie woluminu warstwowego woluminu przypiętego lokalnie](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)

**PYTANIE** Czy można anulować operacji konwersji woluminu?

**ODPOWIEDŹ** Nie, nie jest możliwe anulowanie operacji konwersji po zainicjowaniu. Zgodnie z opisem z poprzedniego pytania, należy mieć świadomość potencjalnych problemów z wydajnością, mogą wystąpić podczas procesu i stosuj najlepsze rozwiązania wymienione powyżej, podczas planowania usługi konwersji.

**PYTANIE** Co się dzieje Moje woluminu w przypadku niepowodzenia operacji konwersji?

**ODPOWIEDŹ** Konwersja woluminu może zakończyć się niepowodzeniem z powodu problemów z łącznością chmury. Urządzenie po pewnym czasie może zatrzymać procesu konwersji po kilku nieudanych prób obniżyć dane warstwowe w chmurze. W takiej sytuacji nadal będzie typ woluminu źródłowego, przed konwersji typu woluminu oraz:

* Zostanie wygenerowany alert krytyczny, informujące o błąd konwersji woluminu. Więcej informacji na temat [alerty związane z woluminów przypiętych lokalnie](storsimple-8000-manage-alerts.md#locally-pinned-volume-alerts)
* Jeśli konwertujesz warstwowego woluminu przypiętego lokalnie, wolumin będzie nadal wykazują właściwości woluminu warstwowego, jak dane nadal może znajdować się w chmurze. Zaleca się rozwiązać problemy z łącznością, a następnie spróbuj ponownie wykonać operację konwersji.
* Podobnie gdy konwersja lokalnie przypięte do woluminu warstwowego zakończy się niepowodzeniem, chociaż wolumin zostanie oznaczona jako wolumin przypięty lokalnie, będzie działać jako woluminu warstwowego (ponieważ można mieć rozlane dane do chmury). Jednak będą nadal zajmują miejsce w warstwach lokalnych urządzenia. To miejsce, nie będą dostępne dla innych woluminów przypiętych lokalnie. Zaleca się, ponów próbę wykonania tej operacji, aby upewnić się, że konwersji woluminu zostało ukończone i lokalne miejsce na urządzeniu można odzyskać.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Pytania dotyczące przywracania woluminu przypiętego lokalnie
**PYTANIE** Są natychmiast przywrócić woluminy przypięte lokalnie?

**ODPOWIEDŹ** Tak, woluminy przypięte lokalnie zostaną przywrócone natychmiast. Tak szybko, jak informacje o metadanych dla woluminu zostanie ściągnięty z chmury w ramach operacji przywracania, wolumin w tryb online i może zostać oceniony przez hosta. Jednak lokalnych gwarancji dla woluminu, że dane nie będą obecne, dopiero po pobraniu wszystkich danych z chmury i może wystąpić obniżona wydajność na tych woluminach czasie trwania operacji przywracania.

**PYTANIE** Jak długo trwa przywracanie woluminu przypiętego lokalnie?

**ODPOWIEDŹ** Woluminy przypięte lokalnie są natychmiast przywrócić, a następnie przełączone w tryb online tak szybko, jak informacje o metadanych woluminu jest pobierana z chmury, danych woluminu nadal mają być pobrane w tle. Ten ostatni część operacji przywracania — powrót lokalnych gwarancji dla woluminu danych — jest operacją wymagającą dużo czasu i może potrwać kilka godzin dla wszystkich danych, które ma zostać wykonane ponownie lokalnego. Czas potrzebny do ukończenia takie same zależy od wielu czynników, takich jak rozmiar woluminu, przywracana i dostępną przepustowość. Usunięcie oryginalnego woluminu, który jest przywracana dodatkowy czas zostaną wykonane, aby utworzyć lokalne miejsce na urządzeniu w ramach operacji przywracania.

**PYTANIE** Musisz przywrócić Mój istniejący wolumin przypięty lokalnie do starszych migawki (gdy wolumin został warstwy). Wolumin przywrócone zostaną jako warstwy w tym przypadku?

**ODPOWIEDŹ** Nie, zostaną przywrócone wolumin jako wolumin przypięty lokalnie. Mimo że dat migawek do chwili, gdy został warstwowego woluminu, podczas przywracania istniejące woluminy, StorSimple zawsze używa typu woluminu na dysku zgodnie z jego lokalizacją obecnie.

**PYTANIE** Czy mogę ostatnio rozszerzony Moje woluminu przypiętego lokalnie, ale teraz potrzebne do przywrócenia danych do czasu, gdy wolumin był mniejszy rozmiar. Będzie przywracania rozmiar bieżącego woluminu i będzie konieczne rozszerzenie rozmiaru woluminu po zakończeniu przywracania?

**ODPOWIEDŹ** Tak, przywracanie spowoduje zmianę rozmiaru woluminu i będzie konieczne rozszerzenie rozmiaru woluminu po zakończeniu przywracania.

**PYTANIE** Typ woluminu można zmienić podczas przywracania?

**ODPOWIEDŹ** Nie, nie można zmienić typ woluminu podczas przywracania.

* Woluminy, które zostały usunięte zostaną przywrócone jako typ w migawce.
* Istniejące woluminy są przywracane na podstawie ich bieżącego typu, niezależnie od tego, z ich typem w migawce (zobacz poprzednie dwa pytania).

**PYTANIE** Chcę przywrócić Moje woluminu przypiętego lokalnie, ale wybrano nieprawidłowy punkt w czasie migawki. Czy można anulować bieżącą operację przywracania?

**ODPOWIEDŹ** Tak, można anulować operacji przywracania w toku. Stan woluminu zostanie wycofana do stanu na początku przywracania. Jednak wszelkie operacje zapisu, które zostały wprowadzone do woluminu, podczas przywracania jest w toku zostaną utracone.

**PYTANIE** Zacząłem operacji przywracania na jednej z moich woluminy przypięte lokalnie, a teraz zobaczyć migawkę w katalogu Moje zaległości, które nie zebrać tworzenia. To do czego służy?

**ODPOWIEDŹ** Jest to tymczasowe migawkę, która jest tworzona przed operacji przywracania i jest używany na potrzeby wycofywania w przypadku przywracania została anulowana lub nie powiedzie się. Nie usuwaj tej migawki; zostanie automatycznie usunięty po zakończeniu przywracania. To zachowanie może wystąpić, jeśli zadanie przywracania tylko lokalnie ma przypięte woluminy lub kombinacji lokalnie woluminów przypiętych i podzielonych na warstwy. Jeśli zadanie przywracania zawiera tylko woluminy warstwowe, to zachowanie nie będzie występować.

**PYTANIE** Czy można sklonować woluminu przypiętego lokalnie?

**ODPOWIEDŹ** Tak, możesz. Jednak woluminu przypiętego lokalnie będzie można sklonować jako wolumin warstwowy, domyślnie. Więcej informacji na temat sposobu [klonowania woluminu przypiętego lokalnie](storsimple-8000-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Pytania dotyczące przechodzenia w tryb failover wolumin przypięty lokalnie
**PYTANIE** Muszę mojego urządzenia z innego urządzenia fizycznego pracy awaryjnej. Moje woluminy przypięte lokalnie nie powiedzie się ponad przypięty lokalnie lub warstwy?

**ODPOWIEDŹ** Woluminy przypięte lokalnie są przełączone w tryb failover, przypięty lokalnie, jeśli na urządzeniu docelowym jest uruchomiona StorSimple 8000 z aktualizacją update 3 lub nowszej.

Więcej informacji na temat [trybu failover i odzyskiwanie po awarii z różnych wersjach lokalnie przypięte woluminy](storsimple-8000-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**PYTANIE** Woluminy przypięte lokalnie natychmiast przywracania podczas odzyskiwania po awarii (DR)?

**ODPOWIEDŹ** Tak, woluminy przypięte lokalnie zostaną przywrócone natychmiast podczas pracy awaryjnej. Tak szybko, jak informacje o metadanych dla woluminu zostanie ściągnięty z chmury w ramach operacji trybu failover, wolumin w tryb online na urządzeniu docelowym i może zostać oceniony przez hosta. W tym samym czasie dane woluminu będą w dalszym ciągu pobierania w tle i może wystąpić zmniejszenie wydajności na tych woluminach na czas trwania pracy w trybie failover.

**PYTANIE** Czy mogę zobaczyć ukończone zadanie trybu failover, jak śledzić postęp woluminu przypiętego lokalnie, która jest przywracana na urządzeniu docelowym?

**ODPOWIEDŹ** Podczas operacji trybu failover zadanie trybu failover jest oznaczony jako wykonać jeden raz, wszystkie woluminy w zestaw trybu failover została natychmiast przywrócić i przełączyć do trybu online na urządzeniu docelowym. W tym woluminy przypięte lokalnie, które być może zostały przełączone w tryb failover; jednak lokalnych gwarancji dane tylko będą dostępne po pobraniu wszystkich danych dla woluminu. Możesz śledzić ten postęp dla każdego woluminu przypiętego lokalnie, przełączoną przez monitorowanie odpowiedniego zadania przywracania, utworzonych jako część pracy w trybie failover. Te zadania indywidulane Przywracanie będzie można tworzyć tylko dla woluminów przypiętych lokalnie.

**PYTANIE** Czy można zmienić typ woluminu, podczas trybu failover?

**ODPOWIEDŹ** Nie, nie można zmienić typ woluminu podczas przejścia w tryb failover. Jeśli nie możesz za pośrednictwem na inne urządzenie fizyczne, uruchomionym StorSimple 8000 series update 3, woluminy są w trybie Failover na podstawie typu woluminu w migawce.

**PYTANIE** Czy mogę w trybie Failover kontenera woluminów z woluminy przypięte lokalnie do urządzenia w chmurze?

**ODPOWIEDŹ** Tak, możesz. Woluminy przypięte lokalnie zostaną przełączone w tryb failover jako woluminy warstwowe. Więcej informacji na temat [trybu failover i odzyskiwanie po awarii z różnych wersjach lokalnie przypięte woluminy](storsimple-8000-device-failover-disaster-recovery.md#common-considerations-for-device-failover)

