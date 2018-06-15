---
title: Woluminy często zadawane pytania dotyczące przypięty lokalnie StorSimple | Dokumentacja firmy Microsoft
description: Zawiera odpowiedzi na często zadawane pytania dotyczące woluminów StorSimple przypięty lokalnie.
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
ms.openlocfilehash: 857647e89b7ac5d9bc72bc4b2ace0ea7279353f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875592"
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>Woluminy przypięte lokalnie StorSimple: często zadawane pytania (FAQ)
## <a name="overview"></a>Omówienie
Poniżej przedstawiono pytania i odpowiedzi, które mogą wystąpić podczas tworzenia woluminu StorSimple przypięty lokalnie, konwertowanie woluminu warstwowego do woluminu przypiętego lokalnie (i na odwrót) lub tworzenie kopii zapasowej i przywracania woluminu przypiętego lokalnie.

Pytania i odpowiedzi są rozmieszczone na następujące kategorie

* Tworzenie woluminu przypiętego lokalnie
* Tworzenie kopii zapasowych przypiętych lokalnie
* Konwertowanie woluminu warstwowego na wolumin przypięty lokalnie
* Przywracanie woluminu przypiętego lokalnie
* Awarii woluminu przypiętego lokalnie

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Pytania dotyczące tworzenia woluminu przypiętego lokalnie
**PYTANIE** Co to jest maksymalny rozmiar woluminu przypiętego lokalnie, który można utworzyć w urządzeń z serii 8000?

**A** na urządzeniach z systemem StorSimple 8000 serii aktualizacji 3.0 można alokować woluminy przypięte lokalnie do 8,5 TB lub woluminy warstwowe do 200 TB na urządzeniu 8100. Na większym urządzeniu 8600 można alokować woluminy przypięte lokalnie do 22,5 TB lub woluminy warstwowe do 500 TB.

**PYTANIE** Aktualizacja 3.0 ostatnio uaktualniony urządzenie 8100 i podczas tworzenia woluminu przypiętego lokalnie, maksymalny rozmiar dostępna jest tylko 6 TB i nie 8,5 TB. Dlaczego nie można utworzyć woluminu 8,5 TB?

**A** Jeśli urządzenie korzysta z aktualizacji 3.0, można alokować woluminy przypięte lokalnie do 8.5 woluminami warstwowymi TB lub na urządzeniu 8100 do 200 TB. Jeśli urządzenie z woluminami warstwowymi, a następnie miejsca na potrzeby tworzenia woluminów przypiętych lokalnie będzie proporcjonalnie niższa niż ten maksymalny limit. Na przykład jeśli już zostały udostępnione woluminy warstwowe około 106 TB na urządzeniu 8100 (czyli połowy warstwowych pojemności), następnie maksymalny rozmiar woluminu lokalnego, który można utworzyć na urządzeniu 8100 odpowiednio zmniejszy się do 4 TB (około połowie maksymalnej lokalnie przypięty pojemność woluminu).

Ponieważ lokalne miejsce na urządzeniu jest używana do obsługi zestawu roboczego woluminów warstwowych, jeśli urządzenie z woluminami warstwowymi zostanie zmniejszona miejsce dostępne na potrzeby tworzenia woluminów przypiętych lokalnie. Z drugiej strony tworzenia woluminu przypiętego lokalnie proporcjonalnie zmniejsza miejsce dostępne na potrzeby woluminów warstwowych. Poniższe tabele zawiera podsumowanie dostępnej pojemności warstwowego na urządzeniach 8100 i 8600 podczas tworzenia woluminów przypiętych lokalnie.

#### <a name="update-30"></a>Aktualizacja 3.0 
| Pojemność udostępnione woluminy przypięte lokalnie | Dostępna pojemność mają zostać aprowizowane dla woluminów warstwowych - 8100 | Dostępna pojemność mają zostać aprowizowane dla woluminów warstwowych - 8600 |
| --- | --- | --- |
| 0 |200 TB |500 TB. |
| 1 TB |176.5 TB |477.8 TB |
| 4 TB |105.9 TB |411.1 TB |
| 8.5 TB |0 TB |311.1 TB |
| 10 TB |Nie dotyczy |277.8 TB |
| 15 TB |Nie dotyczy |166.7 TB |
| 22.5 TB |Nie dotyczy |0 TB |

**PYTANIE** Dlaczego jest operacji tworzenia woluminu przypiętego lokalnie operacją wymagającą dużo czasu?

**ODPOWIEDŹ** Woluminów przypiętych lokalnie jest alokowany nieelastycznie. Aby utworzyć miejsce w warstwach lokalnych urządzenia, dane z istniejące woluminy warstwowe może zostać przeniesiony do chmury podczas procesu inicjowania obsługi administracyjnej. I ponieważ to zależy od rozmiaru woluminu obsługiwana administracyjnie istniejące dane na urządzeniu i dostępną przepustowość do chmury, czas tworzenia woluminu lokalnego może być kilka godzin.

**PYTANIE** Jak długo trwa tworzenie woluminu przypiętego lokalnie?

**ODPOWIEDŹ** Ponieważ woluminów przypiętych lokalnie jest alokowany nieelastycznie, niektóre dane z woluminów warstwowych może zostać przeniesiony do chmury podczas procesu inicjowania obsługi administracyjnej. W związku z tym czas tworzenia woluminu przypiętego lokalnie zależy od wielu czynników, takich jak rozmiar woluminu, danych na urządzeniu i dostępną przepustowość. Na urządzeniu świeżo zainstalowanym ma nie woluminów czas, aby utworzyć wolumin przypięty lokalnie jest około 10 minut na każdą terabajtów danych. Jednak tworzenie lokalnych woluminów może zająć wiele godzin, w oparte na czynnikach przedstawionych powyżej na urządzeniu, który jest używany.

**PYTANIE** Chcę utworzyć wolumin przypięty lokalnie. Czy istnieją najlepszych rozwiązań, które należy pamiętać o?

**ODPOWIEDŹ** Woluminów przypiętych lokalnie są odpowiednie dla obciążeń, które wymagają lokalnych gwarancji danych przez cały czas i wrażliwy na opóźnienia w chmurze. Rozważając użycie woluminów lokalnych dla każdego z obciążeń, należy pamiętać, z następujących czynności:

* Woluminów przypiętych lokalnie jest alokowany nieelastycznie i tworzenia woluminów lokalnych ma wpływ na miejsce dostępne na potrzeby woluminów warstwowych. W związku z tym zaleca się rozpoczynać mniejszym woluminów i skalowanie w górę wraz ze wzrostem zapotrzebowania Twojego magazynu.
* Inicjowanie obsługi administracyjnej lokalnych woluminów jest operacją wymagającą dużo czasu, które mogą dotyczyć wypychanie istniejących danych z woluminy warstwowe do chmury. W związku z tym może wystąpić obniżona wydajność na tych woluminach.
* Inicjowanie obsługi woluminów lokalnych to czasochłonna operacja. Czasu rzeczywistego, związanych zależy od wielu czynników: rozmiar woluminu zainicjowanie obsługi, danych na urządzeniu i dostępną przepustowość. Jeśli nie została utworzona kopia zapasowa istniejących woluminów do chmury, tworzenia woluminu jest wolniejsze. Sugerujemy tworzenia migawek chmury istniejących woluminów przed udostępnieniem woluminu lokalnego.
* Można przekonwertować istniejące woluminy warstwowe do woluminów przypiętych lokalnie, a ta konwersja wymaga obsługi miejsca na urządzeniu na potrzeby wynikowy woluminu przypiętego lokalnie (oprócz wyłączania warstwowych danych z chmury). Ponownie jest operacją wymagającą dużo czasu, który jest zależny od czynników, które zostały omówione powyżej. Zaleca się utworzyć kopię zapasową istniejących woluminów przed konwersji jako proces będzie nawet wolniejsze, jeśli istniejące woluminy nie kopii zapasowej. Urządzenia mogą też wystąpić obniżona wydajność podczas tego procesu.

Więcej informacji na temat sposobu [tworzenia woluminu przypiętego lokalnie](storsimple-8000-manage-volumes-u2.md#add-a-volume)

**PYTANIE** Czy można utworzyć wiele woluminów przypiętych lokalnie, w tym samym czasie?

**ODPOWIEDŹ** Tak, ale wszystkie zadania tworzenia i rozszerzania woluminów przypiętych lokalnie są przetwarzane sekwencyjnie.

Woluminów przypiętych lokalnie jest alokowany nieelastycznie i wymaga utworzenia lokalne miejsce na urządzeniu (co może doprowadzić do istniejących danych z woluminów warstwowych, aby zostać przeniesiony do chmury podczas procesu inicjowania obsługi administracyjnej). W związku z tym jeśli zadanie inicjowania obsługi administracyjnej jest w toku, inne zadania tworzenia woluminu lokalnego zostaną umieszczone w kolejce do momentu zakończenia tego zadania.

Podobnie jeśli jest rozszerzana istniejącego woluminu lokalnego lub wolumin warstwowy jest przekształcany na wolumin przypięty lokalnie, utworzenia nowego woluminu przypiętego lokalnie jest Zakolejkowane ukończenie poprzedniego zadania. Zwiększenie rozmiaru woluminu przypiętego lokalnie obejmuje rozszerzenie istniejących lokalne miejsce dla tego woluminu. Konwersja z warstwową przypięty lokalnie woluminu obejmuje również tworzenie lokalne miejsce dla powstałe w ten sposób lokalnie przypięty woluminu. W obu tych operacji, tworzenie lub rozszerzenie lokalne miejsce długi działa zadanie.

Można wyświetlić te zadania w **zadania** bloku usługi Menedżer StorSimple urządzenia. Zadanie, które jest aktywnie przetwarzanych stale zostało zaktualizowane do odzwierciedlać postęp inicjowania obsługi miejsca. Pozostałe zadania woluminu przypiętego lokalnie została oznaczona jako uruchomiona, ale ich postępu jest zablokowany i pobrane w kolejności, w jakiej zostały umieszczone w kolejce.

**PYTANIE** Po usunięciu woluminu przypiętego lokalnie. Dlaczego nie widzę regeneracji miejsce odzwierciedlone w dostępne miejsce podczas próby utworzenia nowego woluminu?

**ODPOWIEDŹ** Jeśli usuniesz woluminu przypiętego lokalnie, miejsce dostępne dla nowych woluminów mogły nie zostać zaktualizowane od razu. Aktualizacje usługi Menedżer StorSimple urządzenia lokalne miejsce dostępne mniej więcej co godzinę. Sugerujemy, zaczekaj godzinę przed próbą utworzenia nowego woluminu.

**PYTANIE** Woluminów przypiętych lokalnie są obsługiwane przez urządzenia chmury?

**ODPOWIEDŹ** Woluminów przypiętych lokalnie nie są obsługiwane na urządzeniu chmury (8010 i 8020 urządzenia wcześniej określonych jako urządzenie wirtualne StorSimple).

**PYTANIE** Tworzenie i zarządzanie nimi woluminów przypiętych lokalnie może używać poleceń cmdlet programu Azure PowerShell?

**ODPOWIEDŹ** Nie, nie można utworzyć woluminów przypiętych lokalnie za pomocą poleceń cmdlet programu Azure PowerShell (warstwowa każdym woluminie tworzonych za pomocą programu Azure PowerShell). Również sugerować czy należy używać poleceń cmdlet programu Azure PowerShell Aby zmodyfikować dowolne właściwości woluminu przypiętego lokalnie, ponieważ miałoby niepożądane skutki modyfikowanie typ woluminu do warstwowego.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Pytania dotyczące tworzenia kopii zapasowej woluminu przypiętego lokalnie
**PYTANIE** Czy lokalne migawek woluminów przypiętych lokalnie obsługiwane?

**ODPOWIEDŹ** Tak, można wykonać migawki lokalnych woluminów przypiętych lokalnie. Jednak zdecydowanie zaleca się regularne tworzenie kopii zapasowych woluminów przypiętych lokalnie z migawki w chmurze, upewnij się, że dane są chronione w możliwości awarii.

Zauważ, że migawki lokalne woluminów przypiętych lokalnie może również warstwy się w chmurze i nie ma gwarancji pozostanie w warstwie lokalnej urządzenia.

**PYTANIE** Czy są jakieś wskazówki dotyczące zarządzania lokalnego migawek dla woluminów przypiętych lokalnie?

**ODPOWIEDŹ** Częste migawki lokalne obok wysokie tempo zmian danych w ramach woluminu przypiętego lokalnie może spowodować lokalne miejsce na urządzeniu, aby szybko zużycia i spowodować danych z woluminów warstwowych przekazywanej do chmury. W związku z tym sugerujemy zminimalizować liczbę migawek lokalnych.

**PYTANIE** Dotarło do mnie alert informujący, czy Moje migawki lokalne woluminów przypiętych lokalnie może unieważniona. Gdy to się zdarzyć?

**ODPOWIEDŹ** Częste migawki lokalne obok wysokie tempo zmian danych w ramach woluminu przypiętego lokalnie może spowodować lokalne miejsce na urządzeniu, aby szybko zużycia. Jeśli używane są mocno warstwach lokalnych urządzenia, awarii rozszerzonej chmurze może spowodować urządzenia zapełnianiu, i przychodzące zapisu w woluminie może spowodować unieważnienie migawek (jak miejsca nie można zaktualizować migawki do odwoływania się do starszych bloki danych, które zostały zastąpione). W takiej sytuacji będzie można zrealizować zapisu w woluminie, ale migawki lokalne może być nieprawidłowy. Nie ma to wpływu na istniejące migawki w chmurze.

Alert ostrzeżenia jest wyświetlona, taka sytuacja może wystąpić i upewnij się, że taki sam adres w odpowiednim czasie recenzowania planów migawki lokalne do rzadziej migawek lokalnych lub usuwanie starszych migawki lokalne, które nie są już wymagane.

Jeśli migawki lokalne zostały unieważnione, zostanie wyświetlony alert informacji z powiadomieniem o migawek lokalnych dla określonych zasad tworzenia kopii zapasowej zostały unieważnione obok listy sygnatury czasowe migawek lokalnych, które zostały unieważnione. Te migawki zostaną usunięte automatycznie i nie będzie mogła wyświetlać je w **katalogi kopii zapasowej** bloku w portalu Azure.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Pytania dotyczące Konwertowanie woluminu warstwowego na wolumin przypięty lokalnie
**PYTANIE** Jestem I obserwowania niektórych powolność na urządzeniu podczas konwertowania woluminu warstwowego na wolumin przypięty lokalnie. Dlaczego jest sytuacja?

**ODPOWIEDŹ** Proces konwersji obejmuje dwa kroki:

1. Inicjowanie obsługi administracyjnej miejsca na urządzeniu na potrzeby tylko do--skonwertowania lokalnie przypięty woluminu.
2. Pobieranie warstwowych danych z chmury do zapewnienia lokalnym gwarancji.

Oba te kroki są za długie uruchamiania operacji, które są zależne od rozmiaru woluminu konwertowanej danych na urządzeniu i dostępną przepustowość. Jak dane z istniejące woluminy warstwowe może zostaną przeniesione do chmury w trakcie procesu zastrzegania, urządzenie może wystąpić zmniejszenie wydajności w tym czasie. Ponadto, proces konwersji może przebiegać wolniej jeśli:

* Istniejące woluminy nie wykonano kopii w chmurze; Dlatego zalecamy wykonywać kopie zapasowe woluminów przed zainicjowaniem konwersji.
* Zastosowano zasady ograniczania przepustowości, który może stanowić ograniczenie przepustowości w chmurze; Firma Microsoft zaleca w związku z tym, że masz dedykowane MB/s 40 lub więcej połączeń do chmury.
* Proces konwersji może zająć kilka godzin, ze względu na wiele czynników przedstawionych powyżej; Dlatego zaleca się wykonanie tej operacji w czasie bez szczytów lub w weekendy, aby wyeliminować wpływ na użytkowników końcowych.

Więcej informacji na temat sposobu [Konwertowanie woluminu warstwowego na wolumin przypięty lokalnie](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)

**PYTANIE** Czy można anulować operacji konwersji woluminu?

**ODPOWIEDŹ** Nie, nie możesz anulowanie operacji konwersji raz zainicjowane. Zgodnie z opisem w poprzedniego pytania, należy mieć świadomość potencjalnych problemów z wydajnością czy mogą wystąpić podczas procesu, a najlepsze praktyki wymienionych powyżej, planując z konwersji.

**PYTANIE** Co się dzieje z mojej woluminu w przypadku niepowodzenia operacji konwersji?

**ODPOWIEDŹ** Konwersja woluminu może zakończyć się niepowodzeniem z powodu problemów z łącznością chmury. Urządzenie ostatecznie może przestać proces konwersji po kilku nieudanych prób Przenieś w dół warstwowych danych z chmury. W takiej sytuacji typ woluminu będzie nadal typ woluminu źródłowego przed konwersji, oraz:

* Zostanie wygenerowany alert krytyczny, aby powiadamiać błąd konwersji woluminu. Więcej informacji na temat [alerty powiązane z woluminów przypiętych lokalnie](storsimple-8000-manage-alerts.md#locally-pinned-volume-alerts)
* Jeśli zmieniasz warstwowych do woluminu przypiętego lokalnie, wolumin będzie wykazują właściwości wolumin warstwowy, jak dane może nadal znajdować się w chmurze. Zalecamy, aby rozwiązać problemy z łącznością, a następnie ponów próbę operacji konwersji.
* Podobnie podczas konwersji przypiętych lokalnie na wolumin warstwowy nie powiedzie się, mimo że wolumin zostanie oznaczony jako wolumin przypięty lokalnie, będzie działać jako wolumin warstwowy (ponieważ danych może mieć rozrzucone w chmurze). Jednak będą nadal zajmują miejsce w warstwach lokalnych urządzenia. Ta przestrzeń nie będą dostępne dla innych woluminów przypiętych lokalnie. Zaleca się, ponów próbę wykonania tej operacji, aby upewnić się, czy konwersji woluminu są kompletne i będzie można odzyskać lokalne miejsce na urządzeniu.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Pytania dotyczące przywracania woluminu przypiętego lokalnie
**PYTANIE** Woluminów przypiętych lokalnie natychmiast przywrócona?

**ODPOWIEDŹ** Tak, woluminów przypiętych lokalnie zostaną przywrócone natychmiast. Jak informacje o metadanych dla woluminu są pobierane z chmury w ramach operacji przywracania, wolumin jest przełączany w tryb online i jest dostępny przez hosta. Jednak lokalnych gwarancji woluminu się, że dane nie będą obecne dopiero po pobraniu wszystkich danych z chmury i może wystąpić zmniejszenie wydajności na tych woluminach, w czasie trwania przywracania.

**PYTANIE** Jak długo trwa przywracanie woluminu przypiętego lokalnie?

**ODPOWIEDŹ** Woluminów przypiętych lokalnie są natychmiast przywrócona i trybu online, jak metadanych informacji o woluminie są pobierane z chmury, wolumin danych kontynuuje pobieranie w tle. Ten ostatni część operacji przywracania — odzyskać lokalnych gwarancji dla danych woluminu — jest operacją wymagającą dużo czasu i może potrwać kilka godzin dla wszystkich danych, które ma być wykonane ponownie lokalnego. Czas potrzebny na zakończenie takie same zależy od wielu czynników, takich jak rozmiar woluminu przywracana i dostępną przepustowość. Usunięcie oryginalnego woluminu, który jest przywracana dodatkowy czas nastąpi przekierowanie do tworzenia lokalne miejsce na urządzeniu w ramach operacji przywracania.

**PYTANIE** Musisz przywrócić Mój istniejący wolumin przypięty lokalnie starsze migawką (gdy wolumin został warstwowa). Wolumin będzie można przywrócić ponieważ w takim przypadku do warstwy?

**ODPOWIEDŹ** Nie, zostaną przywrócone woluminu jako woluminu przypiętego lokalnie. Mimo że daty migawki do czasu, gdy wolumin został warstwowy, podczas przywracania istniejące woluminy, StorSimple zawsze używa typ woluminu na dysku, ponieważ istnieje on.

**PYTANIE** Moje woluminu przypiętego lokalnie I rozszerzony ostatnio, ale teraz należy przywrócić dane na godzinę, kiedy był mniejszy rozmiar woluminu. Zmieni rozmiar przywracania bieżącego woluminu i będzie konieczne rozszerzenie rozmiaru woluminu po zakończeniu przywracania?

**ODPOWIEDŹ** Tak, przywracania zmieni się rozmiar woluminu i będzie konieczne rozszerzenie rozmiaru woluminu po ukończeniu przywracania.

**PYTANIE** Typ woluminu można zmienić podczas przywracania?

**A.** nie, nie można zmienić typu woluminu podczas przywracania.

* Woluminy, które zostały usunięte zostaną przywrócone jako typ przechowywane w migawce.
* Istniejące woluminy są przywracane na podstawie ich bieżącego typu, niezależnie od typu przechowywanych w migawce (zobacz poprzednie dwa pytania).

**PYTANIE** Należy przywrócić Moje woluminu przypiętego lokalnie, ale wybrano niepoprawny punkt w czasie migawki. Czy można anulować bieżącą operację przywracania?

**ODPOWIEDŹ** Tak, możesz anulować operacji przywracania w toku. Stan woluminu zostanie cofnięta na początku przywracania stanu. Jednak wszystkie zapisy, które zostały wprowadzone do woluminu, podczas przywracania jest w toku, zostaną utracone.

**PYTANIE** Rozpoczęciu operacji przywracania na jednym z mojej woluminów przypiętych lokalnie, a teraz wyświetlać migawki w katalogu Moje zaległości nie zebrać tworzenia. To do czego służy?

**ODPOWIEDŹ** Jest to tymczasowe migawki, która jest tworzona przed operacją przywracania i jest używany na potrzeby wycofywania na wypadek przywracania została anulowana lub nie powiedzie się. Nie należy usuwać tej migawki; go zostaną automatycznie usunięte po zakończeniu przywracania. Ten problem może wystąpić, jeśli zadanie przywracania ma przypięty tylko lokalnie woluminów lub mieszane lokalnie woluminów przypiętych i warstwowego. Jeśli zadanie przywracania zawiera tylko woluminów warstwowych, ten problem nie zostanie przeprowadzona.

**PYTANIE** Czy można sklonować woluminu przypiętego lokalnie?

**ODPOWIEDŹ** Tak, można. Jednak woluminu przypiętego lokalnie zostaną sklonowane jako wolumin warstwowy domyślnie. Więcej informacji na temat sposobu [sklonować woluminu przypiętego lokalnie](storsimple-8000-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Pytania dotyczące awarii woluminu przypiętego lokalnie
**PYTANIE** Musisz przełączyć urządzenia do innego urządzenia fizycznego. Moje woluminów przypiętych lokalnie nie powiedzie się powyżej przypięty lokalnie lub do warstwy?

**ODPOWIEDŹ** Woluminów przypiętych lokalnie są Failover przypięty lokalnie, jeśli urządzenie działa StorSimple 8000 z aktualizacją update 3 lub nowszej.

Więcej informacji na temat [trybu failover i odzyskiwania po awarii z przypięty lokalnie woluminach między wersjami](storsimple-8000-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**PYTANIE** Woluminów przypiętych lokalnie natychmiast przywrócono podczas odzyskiwania awaryjnego (DR)?

**ODPOWIEDŹ** Tak, woluminów przypiętych lokalnie zostaną przywrócone natychmiast podczas pracy awaryjnej. Jak informacje o metadanych dla woluminu są pobierane z chmury w ramach operacji trybu failover, wolumin jest przełączany w tryb online na urządzeniu docelowym i jest dostępny przez hosta. W tym samym czasie danych woluminu będzie kontynuować pobieranie w tle i może wystąpić zmniejszenie wydajności na tych woluminach na czas trwania pracy awaryjnej.

**PYTANIE** Zakończone zadanie trybu failover, jak śledzić postęp woluminu przypiętego lokalnie, która jest przywracana na urządzeniu docelowym są widoczne?

**ODPOWIEDŹ** Podczas operacji trybu failover zadanie trybu failover jest oznaczony jako raz wszystkie woluminy w zestawie trybu failover zostały natychmiast przywrócona i przełączony w tryb online na urządzeniu docelowym. Dotyczy to również wszelkich woluminów przypiętych lokalnie, które mogą nie jednak lokalnych gwarancji danych tylko będą dostępne po pobraniu wszystkich danych dla woluminu. Można śledzić postęp tej dla każdego woluminu przypiętego lokalnie, która nie powiodła się przez monitorowanie odpowiedniego zadania przywracania, które są tworzone w ramach trybu failover. Te zadania przywracania poszczególnych zostanie utworzone tylko dla woluminów przypiętych lokalnie.

**PYTANIE** Typ woluminu można zmienić w trybie failover?

**ODPOWIEDŹ** Nie, nie można zmienić typu woluminu, podczas pracy w trybie failover. Jeśli kończą się niepowodzeniem przez do innego fizycznego urządzenia z systemem StorSimple 8000 serii Aktualizacja 3, woluminy są awaryjnie na podstawie typu woluminu przechowywane w migawce.

**PYTANIE** Można nie przez kontener woluminów z woluminów przypiętych lokalnie na urządzeniu chmury?

**ODPOWIEDŹ** Tak, można. Woluminów przypiętych lokalnie będzie można przełączyć jako woluminy warstwowe. Więcej informacji na temat [trybu failover i odzyskiwania po awarii z przypięty lokalnie woluminach między wersjami](storsimple-8000-device-failover-disaster-recovery.md#common-considerations-for-device-failover)

