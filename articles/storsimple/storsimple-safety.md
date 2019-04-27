---
title: Bezpieczeństwa dla urządzenia StorSimple | Dokumentacja firmy Microsoft
description: W tym artykule opisano konwencje bezpieczeństwa, wskazówki i uwagi i wyjaśniono, jak bezpiecznie zainstalowania i obsługi urządzenia StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: dae6d535-1ca2-4d2b-b221-6819043aa068
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: alkohli
ms.openlocfilehash: 9d0c8c01ae0d87c944922dece74e13f00879b02f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629234"
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>Bezpiecznie zainstalowania i obsługi urządzenia StorSimple
![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png)
![odczytu ikonę powiadomienia bezpieczeństwa](./media/storsimple-safety/IC740885.png) **odczytu bezpieczeństwa i informacje o kondycji**

Odczyt wszystkich informacji bezpieczeństwa i ochrony zdrowia w tym artykule, który ma zastosowanie do Twojego urządzenia Microsoft Azure StorSimple. Zachowaj wszystkie prowadnice drukowane są dostarczane z urządzenia StorSimple do użytku w przyszłości. Nie powiodło się postępuj zgodnie z instrukcjami i poprawnie skonfigurować, użycia i istotne dla tego produktu można zwiększyć ryzyko poważne szkody lub śmierci lub uszkodzenie urządzenie lub urządzenia. A [wersji do pobrania tego przewodnika](https://www.microsoft.com/download/details.aspx?id=44233) jest również dostępna.

## <a name="safety-icon-conventions"></a>Konwencje ikonę bezpieczeństwa
Poniżej przedstawiono ikony, które znajdują się po sprawdzeniu środki bezpieczeństwa, należy przestrzegać podczas konfigurowania i uruchamiania urządzenia Microsoft Azure StorSimple.

| Ikona | Opis |
|:--- |:--- |
| ![Ikona zagrożenie](./media/storsimple-safety/IC740879.png) **zagrożenia!** |Wskazuje niebezpiecznych sytuację, która nie jest to uniknąć, spowoduje śmierci lub poważnemu. Ten wyraz sygnał jest ograniczona do najbardziej ekstremalnych przypadkach. |
| ![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png) **ostrzeżenie!** |Wskazuje sytuację zagrożenia, jeśli nie jest to uniknąć, może spowodować śmierci lub poważne szkody. |
| ![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png) **Uwaga!** |Wskazuje sytuację zagrożenia, jeśli nie jest to uniknąć, może spowodować szkody drobnych lub Średni. |
| ![Zwróć uwagę, ikona](./media/storsimple-safety/IC740881.png) **powiadomienia:** |Wskazuje informacje uznawane za ważne, ale niepowiązane zagrożenia. |
| ![Ikona elektrycznych wstrząsy](./media/storsimple-safety/IC740882.png) **elektrycznych wstrząsy zagrożenia** |Wysokie napięcie |
| ![Ikona ciężki](./media/storsimple-safety/IC740883.png) **ciężki** | |
| ![Żaden użytkownik nie zdatne do użytku części ikonę](./media/storsimple-safety/IC740879.png) **nie części zdatne do użytku użytkownika** |Nie ma dostępu, chyba że odpowiednio przeszkoleni. |
| ![Przeczytaj ikonę powiadomienia bezpieczeństwa](./media/storsimple-safety/IC740885.png)**najpierw przeczytać wszystkie instrukcje** | |
| ![Ikona zagrożenie porady](./media/storsimple-safety/IC740886.png) **Porada zagrożenia** | |

## <a name="handling-precautions"></a>Obsługa środki ostrożności
![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png) ![ikonę ciężki](./media/storsimple-safety/IC740883.png) **ostrzeżenie!** 

Aby zmniejszyć ryzyko uszkodzenia:

* W pełni skonfigurowany obudowy można porównać do 32 kg (70 lbs); nie należy próbować przenoś je samodzielnie.
* Przed przeniesieniem obudowa, zawsze upewnij się, że dwie osoby mogą obsługiwać wagi. Należy pamiętać, że próby, celu zniesienia tego wagi kilku osób może wytrzymać uszkodzenia.
* Nie przenoś obudowa uchwytami zasilania i chłodzenia modułów (PCMs) znajdujący się na tylnej części urządzenia. Te nie są przeznaczone do wykonania wagi.

## <a name="connection-precautions"></a>Środki ostrożności połączenia
![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png) ![ikonę elektrycznych wstrząsy](./media/storsimple-safety/IC740882.png) **ostrzeżenie!**

Aby zmniejszyć prawdopodobieństwo szkody, elektryczny wstrząsy lub śmierci:

* Gdy zasilane z wielu źródeł AC, odłącz wszystkie energii dostaw do kompletnej izolacji.
* Trwale Odłącz jednostki, przed jego przeniesieniem lub jeśli uważasz, że ma ulec uszkodzeniu w dowolny sposób.
* Podaj ziemi elektrycznych bezpieczne połączenie kable dostaw. Sprawdź, czy zakazu lotu określonego komory spełnia wymagania krajowych i lokalnych, przed zastosowaniem zasilania.
* Upewnij się, czy połączenie power zawsze jest odłączony przed jej usunięciem PCM obudowę.
* Biorąc pod uwagę, że wtyczka na dostaw zasilający jest głównym odłączyć urządzenie, upewnij się, że gniazda znajdują się w pobliżu sprzętu i są łatwo dostępne.

![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png) ![ikonę elektrycznych wstrząsy](./media/storsimple-safety/IC740882.png) **ostrzeżenie!**

Aby zmniejszyć prawdopodobieństwo przegrzaniu lub ognia na podstawie połączeń elektrycznego:

* Zapewnić źródła zasilania odpowiednie przeciążenie elektrycznych protection, aby spełniać wymagania wyszczególnione w specyfikacji technicznej.
* Nie należy używać przewodów zasilania bifurcated (potencjalnych klientów "Y").
* Do wykonania odpowiednich bezpieczeństwa, emisji i termiczny wymagania, nie obejmuje powinny zostać usunięte, a wszystkie kieszenie muszą być wypełnione przy użyciu wtyczki lub wartości pustych dysków.
* Upewnij się, że urządzenie jest używany w sposób określony przez producenta. Jeśli urządzenie jest używany w sposób, który nie jest określony przez producenta, ochrony zapewnianej przez urządzenie może zostać zakłócona.

![Zwróć uwagę, ikona](./media/storsimple-safety/IC740881.png) **powiadomienia:**

Aby zapewnić sprawne działanie sprzętu i uniknięcia uszkodzenia produktu:

* Porty RJ-45 tyłu urządzenia dotyczą tylko połączenia Ethernet. Te nie musi być połączony z siecią telekomunikacyjnych.
* Należy zainstalować na urządzeniu w stojaku, która może pomieścić projektu chłodzenia przodu do tyłu.
* Wszystkie wtyczki i pustych płyt należą załącznik systemowy. Takie tylko muszą zostać usunięte, gdy natychmiast dodawane zastępczy. System nie muszą być uruchamiane bez wszystkich modułów lub puste wartości w miejscu.

## <a name="rack-system-precautions"></a>Środki ostrożności system stojak
Następujące wymagania bezpieczeństwa należy rozważyć w przypadku zainstalowania urządzenia w stojaku cabinet.

![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png) ![Porada ikony zagrożenia](./media/storsimple-safety/IC740886.png) **ostrzeżenie!**

Aby zmniejszyć prawdopodobieństwo szkody z Porada ciągu:

* Projekt stojak powinien obsługiwać wagą całkowitą zainstalowanych obudowy i powinno zawierać utrwalający funkcji może uniemożliwić stojaku wyrzucanie lub wypychania za pośrednictwem podczas instalacji lub normalnego użytkowania.
* Podczas ładowania perspektywy regału sprzętowego, wypełnij stojak od dołu do góry i puste od góry w dół.
* Więcej niż jeden załącznik poza stojaku nie Przesuń suwak w czasie, aby uniknąć zagrożenia tor toppling za pośrednictwem.

![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png) ![ikonę elektrycznych wstrząsy](./media/storsimple-safety/IC740882.png) **ostrzeżenie!**

Aby zmniejszyć prawdopodobieństwo szkody, elektryczny wstrząsy lub śmierci:

* Stojaku powinien mieć system bezpiecznej dystrybucji elektrycznego. Go podać nadmiernego bieżącą ochronę obudowa i nie musi być przeciążony przez łączną liczbę zainstalowano obudowach. Należy przestrzegać ocenę użycia energii elektrycznej na tablica umieszczona.
* System elektrycznych dystrybucji należy podać niezawodne podstaw każdej obudowie w stojaku.
* Projekt systemu elektrycznego dystrybucji musi wziąć pod uwagę wycieku podstaw łączna liczba bieżącego ze wszystkich zasilacze we wszystkich plików. Zwróć uwagę że każdego zasilacz w każdej obudowie zawiera bieżącej wycieku podstaw 1.0 maksymalnego mA na 60 Hz 264 v. Stojaku może wymagać etykietowania oraz "WYSOKA CURRENT WYCIEKOM. Zwarcie (ziemi) jest niezbędne przed nawiązaniem połączenia z dostaw."
* Stojak, gdy skonfigurowano obudów, musi spełniać wymagania bezpieczeństwa UL 60950-1 i IEC 60950-1/EN 60950-1.

![Zwróć uwagę, ikona](./media/storsimple-safety/IC740881.png) **powiadomienia:**

Do prawidłowego chłodzenia systemu stojak:

* Upewnij się, że projekt stojak uwzględnia obudowa maksymalna temperatura otoczenia 35 stopniach Celsjusza (95 stopniach Fahrenheita).
* System jest świadczona Niskociśnieniowa, spalin tyłu instalacji (ciśnienia utworzonych przez drzwi stojaku oraz przeszkód, aby nie przekroczyć Pascal 5 [0,5 mm słupa wody]).

## <a name="power-cooling-module-pcm-precautions"></a>Różne środki ostrożności Power chłodzenie modułu PCM)
Urządzenie jest zaprojektowana do pracy z dwóch PCMs. Każdy PCMs ma źródło zasilania i wentylator dwóch osi. Podczas krytycznego stanu systemu umożliwia awarii jednego zasilacz przerywając normalnych operacji. Dwa PCMs (i dlatego zasilaczach) musi zawsze być zainstalowana. Pojedynczy PCM nie zapewnia nadmiarowy. W związku z tym awarii PCM nawet co może spowodować przestój lub możliwej utracie danych.

![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png) ![ikonę elektrycznych wstrząsy](./media/storsimple-safety/IC740882.png) **ostrzeżenie!**

Aby zmniejszyć prawdopodobieństwo szkody, elektryczny wstrząsy lub śmierci:

* Nie usuwaj okładki z modułu PCM. Istnieje zagrożenie wstrząsy electric wewnątrz. Zwróć modułu PCM i mogą zastąpić [skontaktuj się z Microsoft Support](storsimple-contact-microsoft-support.md).

![Zwróć uwagę, ikona](./media/storsimple-safety/IC740881.png) **powiadomienia:**

Aby zapewnić sprawne działanie sprzętu i uniknięcia uszkodzenia produktu:

* Należy zastąpić PCM zakończonych niepowodzeniem w ciągu 24 godzin. Po usunięciu PCM do zastąpienia zastąpienia należy wykonać w ciągu 10 minut, po usunięciu.
* Nie usuwaj PCM, o ile nie zastępuje ona instalowana natychmiast. Obudowa nie muszą być obsługiwane bez wszystkie moduły w miejscu.

## <a name="electrostatic-discharge-esd-precautions"></a>Środki ostrożności elektrostatyczne (ESD)
![Zwróć uwagę, ikona](./media/storsimple-safety/IC740881.png) **powiadomienia:**

Sprawdź następujące środki ostrożności powiązane ESD.

* Upewnij się, że zainstalowane i sprawdzić odpowiednie, antistatic taśmy nadgarstka lub więcej.
* Sprawdź wszystkie środki ostrożności konwencjonalne ESD podczas obsługi modułów i składników.
* Należy unikać kontaktować się z płyty montażowej składników i modułu.
* Uszkodzenie ESD nie jest objęta gwarancją.

## <a name="battery-disposal-precautions"></a>Środki ostrożności usuwania baterii
Zasilacz używa baterii specjalne do ochrony zawartości pamięci podczas awarii zasilania tymczasowego, krótkoterminowej. Baterii jest umieszczona w module PCM. Pamiętać o następujących informacji o baterii.

![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png) **ostrzeżenie!**

Aby zmniejszyć ryzyko szorty, pożaru, rozłożenie, szkód lub śmierci:

* Usuwa używane baterie zgodnie z przepisami krajowych i regionalnych.
* Nie dezasemblować, z łatwością Wypełniaj normy, cieplnej powyżej 60 stopni Celsjusza (140 stopniach Fahrenheita) lub spalania. Zastąp bateria w module PCM tylko dostarczony baterii. Użyj innego baterii może stanowić ryzyko pożaru lub rozłożenia.
* Użyj ochronne zakończenia z baterii, jeśli są one usuwane z zasilania.

![Zwróć uwagę, ikona](./media/storsimple-safety/IC740881.png) **powiadomienia:**

Podczas wysyłania lub inny sposób transportowania baterie lotniczego, postępuj zgodnie z dokumentu IATA Lithium baterii wskazówki dostępne pod adresem [https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Po przejrzeniu tych ogłoszeniach bezpieczeństwa następne kroki są rozpakować, stojaku i Podłączanie kabli do urządzenia.

## <a name="next-steps"></a>Kolejne kroki
* Aby urządzenie 8100, przejdź do [instalowania urządzenia StorSimple 8100](storsimple-8100-hardware-installation.md).
* Dla urządzenia 8600, przejdź do [instalowania urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md).

