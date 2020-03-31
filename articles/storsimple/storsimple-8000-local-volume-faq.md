---
title: StorSimple lokalnie przypięte woluminy często zadawane pytania| Dokumenty firmy Microsoft
description: Zawiera odpowiedzi na często zadawane pytania dotyczące woluminów przypiętych lokalnie storem.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60319551"
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple woluminy przypięte lokalnie: często zadawane pytania (CZĘSTO ZADAWANE PYTANIA)
## <a name="overview"></a>Omówienie
Poniżej przedstawiono pytania i odpowiedzi, które mogą mieć podczas tworzenia woluminu przypiętego lokalnie storsimple, konwertowania woluminu warstwowego na wolumin przypięty lokalnie (i odwrotnie) lub tworzenia kopii zapasowej i przywracania woluminu przypiętego lokalnie.

Pytania i odpowiedzi są podzielone na następujące kategorie

* Tworzenie woluminu przypiętego lokalnie
* Tworzenie kopii zapasowej przypiętego lokalnie
* Konwertowanie woluminu warstwowego na wolumin przypięty lokalnie
* Przywracanie woluminu przypiętego lokalnie
* W przypadku przepełnienie woluminu przypiętego lokalnie

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Pytania dotyczące tworzenia woluminu przypiętego lokalnie
**PYTANIE** Jaki jest maksymalny rozmiar woluminu przypiętego lokalnie, który można utworzyć na urządzeniach z serii 8000?

**A** Na urządzeniach z aktualizacją 3.0 z serii StorSimple 8000 można aprowizować woluminy przypięte lokalnie o pojemności do 8,5 TB lub woluminy warstwowe o pojemności do 200 TB na urządzeniu 8100. Na większym urządzeniu 8600 można alokować woluminy przypięte lokalnie do 22,5 TB lub woluminy warstwowe do 500 TB.

**PYTANIE** Niedawno uaktualniłem urządzenie 8100 do aktualizacji 3.0, a gdy próbuję utworzyć lokalnie przypięty wolumin, maksymalny dostępny rozmiar to tylko 6 TB, a nie 8,5 TB. Dlaczego nie mogę utworzyć woluminu o pojemności 8,5 TB?

**A** Jeśli na urządzeniu jest uruchomiona aktualizacja 3.0, można aprowizować woluminy przypięte lokalnie do 8,5 TB lub woluminów warstwowych o pojemności do 200 TB na urządzeniu 8100. Jeśli urządzenie ma już woluminy warstwowe, miejsce dostępne do utworzenia woluminu przypiętego lokalnie będzie proporcjonalnie niższe niż ten maksymalny limit. Na przykład, jeśli na urządzeniu 8100 udostępniono już około 106 TB woluminów warstwowych (co stanowi połowę pojemności warstwowej), maksymalny rozmiar woluminu lokalnego, który można utworzyć na urządzeniu 8100, zostanie odpowiednio zmniejszony do 4 TB (około połowę maksymalnej pojemności woluminu przypiętego lokalnie).

Ponieważ niektóre miejsca lokalnego na urządzeniu jest używany do obsługi zestawu roboczego woluminów warstwowych, dostępne miejsce do tworzenia woluminu przypięte lokalnie jest zmniejszona, jeśli urządzenie ma woluminów warstwowych. Z drugiej strony utworzenie woluminu przypiętego lokalnie proporcjonalnie zmniejsza dostępne miejsce dla woluminów warstwowych. W poniższych tabelach podsumowano dostępne pojemność warstwową na urządzeniach 8100 i 8600 podczas tworzenia woluminów przypiętych lokalnie.

#### <a name="update-30"></a>Aktualizacja 3.0 

| Lokalnie przypięta pojemność obsługi administracyjnej woluminów | Dostępna pojemność do obsługi administracyjnej dla woluminów warstwowych - 8100 | Dostępna pojemność do obsługi administracyjnej dla woluminów warstwowych - 8600 |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176,5 TB |477,8 TB |
| 4 TB |105,9 TB |411,1 TB |
| 8,5 TB |0 TB |311,1 TB |
| 10 TB |Nie dotyczy |277,8 TB |
| 15 TB |Nie dotyczy |166,7 TB |
| 22,5 TB |Nie dotyczy |0 TB |

**PYTANIE** Dlaczego lokalnie przypięty wolumin jest długotrwałą operacją?

**A.** Woluminy przypięte lokalnie są aprowizacji są gęsto aprowizacji. Aby utworzyć miejsce na warstwach lokalnych urządzenia, niektóre dane z istniejących woluminów warstwowych mogą być wypychane do chmury podczas procesu inicjowania obsługi administracyjnej. A ponieważ zależy to od rozmiaru woluminu, który jest aprowizacjowo, istniejących danych na urządzeniu i dostępnej przepustowości chmury, czas pracy do utworzenia woluminu lokalnego może wynosić kilka godzin.

**PYTANIE** Jak długo trwa tworzenie woluminu przypiętego lokalnie?

**A.** Ponieważ woluminy przypięte lokalnie są aprowizacji grubo, niektóre istniejące dane z woluminów warstwowych może być wypychane do chmury podczas procesu inicjowania obsługi administracyjnej. W związku z tym czas pracy w celu utworzenia woluminu przypiętego lokalnie zależy od wielu czynników, w tym rozmiaru woluminu, danych na urządzeniu i dostępnej przepustowości. Na świeżo zainstalowanym urządzeniu, które nie ma woluminów, czas utworzenia woluminu przypiętego lokalnie wynosi około 10 minut na terabajt danych. Jednak tworzenie woluminów lokalnych może potrwać kilka godzin na podstawie czynników wyjaśnionych powyżej na urządzeniu, które jest w użyciu.

**PYTANIE** Chcę utworzyć wolumin przypięty lokalnie. Czy są jakieś najlepsze rozwiązania, o których muszę wiedzieć?

**A.** Woluminy przypięte lokalnie są odpowiednie dla obciążeń, które wymagają lokalnych gwarancji danych przez cały czas i są wrażliwe na opóźnienia w chmurze. Biorąc pod uwagę użycie woluminów lokalnych dla dowolnego obciążenia, należy pamiętać o następujących czynnościach:

* Woluminy przypięte lokalnie są gęsto aprowizacji, a tworzenie woluminów lokalnych wpływa na dostępne miejsce dla woluminów warstwowych. W związku z tym zalecamy, aby rozpocząć od mniejszych woluminów i skalować w górę wraz ze wzrostem zapotrzebowania na magazynowanie.
* Inicjowanie obsługi administracyjnej woluminów lokalnych jest długotrwałą operacją, która może obejmować wypychanie istniejących danych z woluminów warstwowych do chmury. W rezultacie może wystąpić zmniejszona wydajność na tych woluminach.
* Inicjowanie obsługi administracyjnej woluminów lokalnych jest operacją czasochłonną. Rzeczywisty czas zależy od wielu czynników: rozmiaru aprowizowanego woluminu, danych na urządzeniu i dostępnej przepustowości. Jeśli nie wykonasz kopii zapasowej istniejących woluminów w chmurze, tworzenie woluminu jest wolniejsze. Zalecamy zrobienie migawek w chmurze istniejących woluminów przed aprowizją wolumin lokalny.
* Istniejące woluminy warstwowe można konwertować na woluminy przypięte lokalnie, a ta konwersja obejmuje inicjowanie obsługi administracyjnej miejsca na urządzeniu dla woluminu przypiętego lokalnie (oprócz obniżania danych warstwowych, jeśli takie istnieją, z chmury). Ponownie, jest to długotrwała operacja, która zależy od czynników, które omówiliśmy powyżej. Sugerujemy utworzenie kopii zapasowej istniejących woluminów przed konwersją, ponieważ proces będzie jeszcze wolniejszy, jeśli istniejące woluminy nie zostaną utworzone. Podczas tego procesu urządzenie może również zmniejszyć wydajność.

Więcej informacji na temat [tworzenia woluminu przypiętego lokalnie](storsimple-8000-manage-volumes-u2.md#add-a-volume)

**PYTANIE** Czy można utworzyć wiele woluminów przypiętych lokalnie w tym samym czasie?

**A.** Tak, ale wszystkie zadania tworzenia i rozszerzania woluminów przypiętych lokalnie są przetwarzane sekwencyjnie.

Woluminy przypięte lokalnie są aprowizacji są gęsto aprowizacji i wymaga utworzenia miejsca lokalnego na urządzeniu (co może spowodować istniejące dane z woluminów warstwowych, które mają być wypychane do chmury podczas procesu inicjowania obsługi administracyjnej). W związku z tym jeśli zadanie inicjowania obsługi administracyjnej jest w toku, inne zadania tworzenia woluminu lokalnego będą umieszczane w kolejce do zakończenia tego zadania.

Podobnie jeśli istniejący wolumin lokalny jest rozszerzany lub wolumin warstwowy jest konwertowany na wolumin przypięty lokalnie, tworzenie nowego woluminu przypiętego lokalnie jest umieszczane w kolejce do momentu ukończenia poprzedniego zadania. Rozszerzenie rozmiaru woluminu przypiętego lokalnie obejmuje rozszerzenie istniejącej przestrzeni lokalnej dla tego woluminu. Konwersja z woluminu warstwowego na wolumin przypięty lokalnie obejmuje również utworzenie przestrzeni lokalnej dla wynikowego woluminu przypiętego lokalnie. W obu tych operacjach tworzenie lub rozszerzanie przestrzeni lokalnej jest długotrwałym zadaniem.

Można wyświetlić te zadania w **zadaniach** bloku usługi StorSimple Device Manager. Zadanie, które jest aktywnie przetwarzane jest stale aktualizowana, aby odzwierciedlić postęp inicjowania obsługi administracyjnej miejsca. Pozostałe zadania woluminu przypięte lokalnie są oznaczone jako uruchomione, ale ich postęp jest zablokowany i są pobierane w kolejności, w jakiej zostały umieszczone w kolejce.

**PYTANIE** Usunięto wolumin przypięty lokalnie. Dlaczego nie widzę odzyskanego miejsca odzwierciedlonego w dostępnym miejscu podczas próby utworzenia nowego woluminu?

**A.** Jeśli usuniesz wolumin przypięty lokalnie, miejsce dostępne dla nowych woluminów może nie zostać natychmiast zaktualizowane. Usługa Menedżera urządzeń StorSimple aktualizuje lokalne miejsce dostępne około co godzinę. Zalecamy odczekanie godziny przed próbą utworzenia nowego woluminu.

**PYTANIE** Czy woluminy przypięte lokalnie są obsługiwane na urządzeniu w chmurze?

**A.** Woluminy przypięte lokalnie nie są obsługiwane w urządzeniu w chmurze (urządzenia 8010 i 8020 wcześniej określane jako urządzenie wirtualne StorSimple).

**PYTANIE** Czy można używać poleceń cmdlet programu Azure PowerShell do tworzenia woluminów przypiętych lokalnie i zarządzania nimi?

**A.** Nie, nie można tworzyć woluminów przypiętych lokalnie za pośrednictwem poleceń cmdlet programu Azure PowerShell (każdy wolumin utworzony za pośrednictwem programu Azure PowerShell jest warstwowy). Firma Microsoft sugeruje również, że nie używasz poleceń cmdlet programu Azure PowerShell do modyfikowania żadnych właściwości woluminu przypiętego lokalnie, ponieważ będzie to miało niepożądany efekt modyfikowania typu woluminu do warstwowego.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Pytania dotyczące tworzenia kopii zapasowej woluminu przypiętego lokalnie
**PYTANIE** Czy lokalne migawki woluminów przypiętych lokalnie są obsługiwane?

**A.** Tak, możesz robić lokalne migawki woluminów przypiętych lokalnie. Jednak zdecydowanie zalecamy regularne tworzenie kopii zapasowych woluminów przypiętych lokalnie za pomocą migawek w chmurze, aby zapewnić ochronę danych w przypadku awarii.

Należy pamiętać, że lokalne migawki woluminów przypiętych lokalnie można również warstwy do chmury i nie są gwarantowane, aby pozostać w warstwie lokalnej urządzenia.

**PYTANIE** Czy istnieją jakieś wytyczne dotyczące zarządzania migawkami lokalnymi dla woluminów przypiętych lokalnie?

**A.** Częste migawki lokalne wraz z dużą szybkością zmian danych w woluminie przypiętym lokalnie może spowodować, że miejsce lokalne na urządzeniu zostanie szybko wykorzystane i spowoduje, że dane z woluminów warstwowych zostaną wypychane do chmury. W związku z tym zalecamy zminimalizowanie liczby migawek lokalnych.

**PYTANIE** Otrzymałem alert informujący, że moje lokalne migawki woluminów przypiętych lokalnie mogą zostać unieważnione. Kiedy to możliwe?

**A.** Częste migawki lokalne wraz z dużą szybkością zmian danych w woluminie przypiętym lokalnie może spowodować, że miejsce lokalne na urządzeniu zostanie szybko wykorzystane. Jeśli warstwy lokalne urządzenia są intensywnie używane, rozszerzona awaria chmury może spowodować, że urządzenie stanie się pełne, a przychodzące zapisy do woluminu mogą spowodować unieważnienie migawek (ponieważ nie ma miejsca na zaktualizowanie migawek w celu odniesienia się do starszych bloków dane, które zostały zastąpione). W takiej sytuacji zapisy do woluminu będą nadal obsługiwane, ale migawki lokalne mogą być nieprawidłowe. Nie ma wpływu na istniejące migawki w chmurze.

Ostrzeżenie alertu jest powiadomienie, że taka sytuacja może wystąpić i upewnij się, że adres ten sam w odpowiednim czasie przez przeglądanie harmonogramów migawek lokalnych do podejmowania rzadszych migawek lokalnych lub usuwanie starszych migawek lokalnych, które nie są już wymagane.

Jeśli migawki lokalne są unieważnione, otrzymasz alert informacyjny informujący, że migawki lokalne dla określonych zasad tworzenia kopii zapasowych zostały unieważnione wraz z listą sygnatur czasowych migawek lokalnych, które zostały unieważnione. Te migawki zostaną automatycznie usunięte i nie będzie już można wyświetlić je w **bloku katalogów kopii zapasowych** w witrynie Azure portal.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Pytania dotyczące konwertowania woluminu warstwowego na wolumin przypięty lokalnie
**PYTANIE** Obserwuję pewną powolność na urządzeniu podczas konwersji woluminu warstwowego na wolumin przypięty lokalnie. Dlaczego tak się dzieje?

**A.** Proces konwersji obejmuje dwa etapy:

1. Inicjowanie obsługi administracyjnej miejsca na urządzeniu dla woluminu wstępnie przekonwertowanego lokalnie.
2. Pobieranie dowolnych danych warstwowych z chmury w celu zapewnienia gwarancji lokalnych.

Oba te kroki są długotrwałe operacje, które są zależne od rozmiaru woluminu konwertowane, dane na urządzeniu i dostępnej przepustowości. Ponieważ niektóre dane z istniejących woluminów warstwowych mogą wyciekać do chmury w ramach procesu inicjowania obsługi administracyjnej, urządzenie może w tym czasie zmniejszyć wydajność. Ponadto proces konwersji może być wolniejszy, jeśli:

* Istniejące woluminy nie zostały utworzone w chmurze; zalecamy tworzenie kopii zapasowych woluminów przed rozpoczęciem konwersji.
* Zastosowano zasady ograniczania przepustowości, które mogą ograniczać dostępną przepustowość do chmury; dlatego zalecamy, aby mieć dedykowane 40 Mbps lub więcej połączenia z chmurą.
* Proces konwersji może potrwać kilka godzin ze względu na wiele czynników wyjaśnionych powyżej; w związku z tym zalecamy wykonanie tej operacji w godzinach poza szczytami lub w weekend, aby uniknąć wpływu na konsumentów końcowych.

Więcej informacji na temat [konwertowania woluminu warstwowego na wolumin przypięty lokalnie](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)

**PYTANIE** Czy mogę anulować operację konwersji woluminu?

**A.** Nie, nie można anulować operacji konwersji po zainicjowaniu. Jak omówiono w poprzednim pytaniu, należy pamiętać o potencjalnych problemach z wydajnością, które mogą wystąpić podczas procesu, i postępować zgodnie z najlepszymi praktykami wymienionymi powyżej podczas planowania konwersji.

**PYTANIE** Co się stanie z moim woluminem, jeśli operacja konwersji nie powiedzie się?

**A.** Konwersja woluminów może zakończyć się niepowodzeniem z powodu problemów z łącznością w chmurze. Urządzenie może ostatecznie zatrzymać proces konwersji po serii nieudanych prób obniżenia warstwowych danych z chmury. W takim scenariuszu typ woluminu będzie nadal typem woluminu źródłowego przed konwersją i:

* Alert krytyczny zostanie podniesiony, aby powiadomić Cię o niepowodzeniu konwersji woluminu. Więcej informacji o [alertach związanych z woluminami przypiętymi lokalnie](storsimple-8000-manage-alerts.md#locally-pinned-volume-alerts)
* Jeśli konwertujesz wolumin warstwowy na wolumin przypięty lokalnie, wolumin będzie nadal wykazywał właściwości woluminu warstwowego, ponieważ dane mogą nadal znajdować się w chmurze. Sugerujemy, aby rozwiązać problemy z łącznością, a następnie ponowić próbę wykonania operacji konwersji.
* Podobnie, gdy konwersja z lokalnie przypięty do woluminu warstwowego kończy się niepowodzeniem, chociaż wolumin zostanie oznaczony jako wolumin przypięty lokalnie, będzie działać jako wolumin warstwowy (ponieważ dane mogły rozlać się do chmury). Jednak nadal będzie zajmować miejsce na lokalnych warstwach urządzenia. To miejsce nie będzie dostępne dla innych woluminów przypiętych lokalnie. Sugerujemy, aby ponowić próbę tej operacji, aby upewnić się, że konwersja woluminu została zakończona i można odzyskać miejsce lokalne na urządzeniu.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Pytania dotyczące przywracania woluminu przypiętego lokalnie
**PYTANIE** Czy woluminy przypięte lokalnie są natychmiast przywracane?

**A.** Tak, woluminy przypięte lokalnie są natychmiast przywracane. Tak szybko, jak informacje o metadanych dla woluminu jest pobierana z chmury w ramach operacji przywracania, wolumin jest przenoszony do trybu online i mogą być dostępne dla hosta. Jednak lokalne gwarancje dla danych woluminu nie będą obecne, dopóki wszystkie dane nie zostaną pobrane z chmury i może wystąpić zmniejszona wydajność na tych woluminach na czas trwania przywracania.

**PYTANIE** Jak długo trwa przywracanie woluminu przypiętego lokalnie?

**A.** Woluminy przypięte lokalnie są przywracane natychmiast i przenoszone do trybu online, gdy tylko informacje o metadanych woluminu zostaną pobrane z chmury, podczas gdy dane woluminu są nadal pobierane w tle. Ta ostatnia część operacji przywracania — odzyskiwanie lokalnych gwarancji dla danych woluminu — jest długotrwałą operacją i może potrwać kilka godzin, aby wszystkie dane zostały ponownie udostępnione lokalnie. Czas, który trzeba było ukończyć, zależy od wielu czynników, takich jak rozmiar przywracanego woluminu i dostępna przepustowość. Jeśli oryginalny wolumin, który jest przywracany został usunięty, zostanie pobrany dodatkowy czas na utworzenie miejsca lokalnego na urządzeniu w ramach operacji przywracania.

**PYTANIE** Muszę przywrócić istniejący wolumin przypięty lokalnie do starszej migawki (wykonanej, gdy wolumin był warstwowy). Czy wolumin zostanie przywrócony jako warstwowy w tym przypadku?

**A.** Nie, wolumin zostanie przywrócony jako wolumin przypięty lokalnie. Mimo że migawka datuje się na czas, w którym wolumin był warstwowy, podczas przywracania istniejących woluminów StorSimple zawsze używa typu woluminu na dysku w obecnym kształcie.

**PYTANIE** Niedawno rozszerzyłem wolumin przypięty lokalnie, ale teraz muszę przywrócić dane do czasu, gdy wolumin był mniejszy. Czy przywrócić rozmiar bieżącego woluminu i czy po zakończeniu przywracania będzie musiał rozszerzyć rozmiar woluminu?

**A.** Tak, przywracanie zmieni rozmiar woluminu i trzeba będzie rozszerzyć rozmiar woluminu po zakończeniu przywracania.

**PYTANIE** Czy można zmienić typ woluminu podczas przywracania?

**A.** Nie, nie można zmienić typu woluminu podczas przywracania.

* Woluminy, które zostały usunięte są przywracane jako typ przechowywany w migawce.
* Istniejące woluminy są przywracane na podstawie ich bieżącego typu, niezależnie od typu przechowywanego w migawce (patrz dwa poprzednie pytania).

**PYTANIE** Muszę przywrócić wolumin przypięty lokalnie, ale wybrałem niepoprawny punkt w migawce czasu. Czy można anulować bieżącą operację przywracania?

**A.** Tak, można anulować operację przywracania w toku. Stan woluminu zostanie przywrócony do stanu na początku przywracania. Jednak wszelkie zapisy, które zostały wprowadzone do woluminu podczas przywracania był w toku zostaną utracone.

**PYTANIE** Rozpocząłem operację przywracania na jednym z moich woluminów przypiętych lokalnie, a teraz widzę migawkę w moim katalogu zaległości, których nie przypominam sobie podczas tworzenia. Do czego służy?

**A.** Jest to migawka tymczasowa, która jest tworzona przed operacją przywracania i jest używana do wycofywania w przypadku anulowania przywracania lub niepowodzenia. Nie należy usuwać tej migawki; zostanie automatycznie usunięty po zakończeniu przywracania. To zachowanie może wystąpić, jeśli zadanie przywracania ma tylko lokalnie przypięte woluminy lub kombinację woluminów przypiętych lokalnie i warstwowych. Jeśli zadanie przywracania zawiera tylko woluminy warstwowe, to zachowanie nie wystąpi.

**PYTANIE** Czy mogę sklonować wolumin przypięty lokalnie?

**A.** Tak, można. Jednak wolumin przypięty lokalnie zostanie domyślnie sklonowany jako wolumin warstwowy. Więcej informacji na temat [klonowania woluminu przypiętego lokalnie](storsimple-8000-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Pytania dotyczące awarii woluminu przypiętego lokalnie
**PYTANIE** Muszę przewinąć urządzenie w stan fail over na innym urządzeniu fizycznym. Czy moje woluminy przypięte lokalnie zostaną przejęte awaryjnie jako przypięte lokalnie lub warstwowe?

**A.** Woluminy przypięte lokalnie są przełączone w tryb fail, ponieważ lokalnie przypięte, jeśli na urządzeniu docelowym jest uruchomiona aktualizacja serii StorSimple 8000 3 lub nowsza.

Więcej informacji na temat pracy awaryjnej i odzyskiwania po awarii [woluminów przypiętych lokalnie w różnych wersjach](storsimple-8000-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**PYTANIE** Czy woluminy przypięte lokalnie są natychmiast przywracane podczas odzyskiwania po awarii?Are locally pinned volumes tam restored during disaster recovery (DR)?

**A.** Tak, woluminy przypięte lokalnie są przywracane natychmiast podczas pracy awaryjnej. Tak szybko, jak informacje o metadanych dla woluminu jest pobierana z chmury w ramach operacji pracy awaryjnej, wolumin jest przenoszony do trybu online na urządzeniu docelowym i mogą być dostępne dla hosta. W międzyczasie dane woluminu będą nadal pobierane w tle i może wystąpić zmniejszona wydajność na tych woluminach na czas pracy awaryjnej.

**PYTANIE** Widzę, że zadanie pracy awaryjnej zostało ukończone, jak mogę śledzić postęp lokalnie przypiętego woluminu, który jest przywracany na urządzeniu docelowym?

**A.** Podczas operacji pracy awaryjnej zadanie pracy awaryjnej jest oznaczone jako ukończone, gdy wszystkie woluminy w zestawie pracy awaryjnej zostały natychmiast przywrócone i przesunęły do trybu online na urządzeniu docelowym. Obejmuje to wszystkie woluminy przypięte lokalnie, które mogły zostać przejęte awaryjnie; jednak lokalne gwarancje danych będą dostępne tylko wtedy, gdy wszystkie dane dla woluminu zostały pobrane. Można śledzić ten postęp dla każdego woluminu przypiętego lokalnie, który został przejęty awaryjnie, monitorując odpowiednie zadania przywracania, które są tworzone w ramach pracy awaryjnej. Te poszczególne zadania przywracania zostaną utworzone tylko dla woluminów przypiętych lokalnie.

**PYTANIE** Czy można zmienić typ woluminu podczas pracy awaryjnej?

**A.** Nie, nie można zmienić typu woluminu podczas pracy awaryjnej. Jeśli po awarii do innego urządzenia fizycznego, który jest uruchomiony StorSimple 8000 serii aktualizacji 3, woluminy są przepełnione w trybie failed na podstawie typu woluminu przechowywane w migawce.

**PYTANIE** Czy można przewinąć kontener woluminu z lokalnie przypiętymi woluminami do urządzenia w chmurze?

**A.** Tak, można. Woluminy przypięte lokalnie zostaną przejęte awaryjnie jako woluminy warstwowe. Więcej informacji na temat pracy awaryjnej i odzyskiwania po awarii [woluminów przypiętych lokalnie w różnych wersjach](storsimple-8000-device-failover-disaster-recovery.md#common-considerations-for-device-failover)

