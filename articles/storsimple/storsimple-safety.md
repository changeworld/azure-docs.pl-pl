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
ms.openlocfilehash: a178e8880bcbcada9d66eaacf5ccbdb7c55957cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875865"
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>Bezpiecznie zainstalowania i obsługi urządzenia StorSimple
![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png)
![odczytu ikony powiadomień bezpieczeństwa](./media/storsimple-safety/IC740885.png) **przeczytać informacje dotyczące bezpieczeństwa i kondycji**

Przeczytać wszystkie informacje DS w tym artykule, która ma zastosowanie do Twojego urządzenia Microsoft Azure StorSimple. Zachowaj wszystkie prowadnice drukowanymi dostarczone z urządzeniem StorSimple do użytku w przyszłości. Niepowodzenie postępuj zgodnie z instrukcjami i poprawnie skonfigurować, używanie i szczególną uwagę na ten produkt można zwiększyć ryzyko wystąpienia poważnej szkody lub śmierci lub uszkodzenie urządzenie lub urządzenia. A [w wersji do pobrania tego przewodnika](http://www.microsoft.com/download/details.aspx?id=44233) jest również dostępna.

## <a name="safety-icon-conventions"></a>Konwencje ikona bezpieczeństwa
Oto ikony, które znajdują się po przejrzeniu ostrożności należy przestrzegać podczas konfigurowania i uruchamiania urządzenia Microsoft Azure StorSimple.

| Ikona | Opis |
|:--- |:--- |
| ![Ikona zagrożenie](./media/storsimple-safety/IC740879.png) **zagrożenia!** |Wskazuje niebezpiecznych sytuacji, który nie uniknąć spowoduje śmierci lub poważnej szkody. Ten wyraz sygnału jest ograniczony do najbardziej skrajne sytuacji. |
| ![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png) **ostrzeżenie!** |Wskazuje niebezpiecznych sytuację, w której, jeśli nie jest to uniknąć, może spowodować śmierci lub poważnej szkody. |
| ![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png) **Uwaga!** |Wskazuje sytuację zagrożenia co, jeśli nie jest to uniknąć, może spowodować szkody drobne lub średniego. |
| ![Ikony](./media/storsimple-safety/IC740881.png) **POWIADOMIEŃ:** |Wskazuje informacje uznawane za ważne, ale nie zagrożenia związane z. |
| ![Ikona uderzenia elektrycznego](./media/storsimple-safety/IC740882.png) **elektrycznego uderzenia zagrożenia** |Wysokie napięcie |
| ![Ikona ciężki](./media/storsimple-safety/IC740883.png) **ciężki** | |
| ![Żaden użytkownik nie obsługiwanych części ikona](./media/storsimple-safety/IC740879.png) **żadnych części obsługiwanych użytkownika** |Brak dostępu do chyba, że poprawnie uczony. |
| ![Przeczytaj ikonę powiadomienia bezpieczeństwa](./media/storsimple-safety/IC740885.png)**najpierw przeczytać wszystkie instrukcje** | |
| ![Ikona zagrożenie porady](./media/storsimple-safety/IC740886.png) **Porada zagrożenia** | |

## <a name="handling-precautions"></a>Obsługa środki ostrożności
![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png) ![ikona ciężki](./media/storsimple-safety/IC740883.png) **ostrzeżenie!** 

Aby zmniejszyć ryzyko uszkodzenia:

* Obudowa pełni skonfigurowany można porównać do 32 kg (70 kg); nie należy próbować podnieś go samodzielnie.
* Przed przeniesieniem obudowa, należy zawsze upewnij się, że dwie osoby są dostępne do obsługi wagą. Należy pamiętać, że próba przyrostu to wagi kilku osób może wytrzymać uszkodzenia.
* Obudowa nie powodują podniesienie uchwytami zasilania i chłodzenia modułów (PCMs) znajdujący się na tylnej części urządzenia. Te nie mają wagi.

## <a name="connection-precautions"></a>Środki ostrożności połączenia
![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png) ![ikona uderzenia elektrycznego](./media/storsimple-safety/IC740882.png) **ostrzeżenie!**

Aby zmniejszyć prawdopodobieństwo szkody, elektrycznego uderzenia lub śmierci:

* Gdy zasilane z wielu źródeł AC, odłącz wszystkie power dostaw dla pełnej izolacji.
* Trwale odłączyć jednostki przed jego przeniesieniem lub jeśli uważasz, że ma uszkodzony w dowolny sposób.
* Podaj połączenie bezpieczne ziemi elektrycznego kable dostaw. Sprawdź, czy uziemiający spełnia bardzo komory spełnia wymagania krajowych i lokalnych przed zastosowaniem zasilania.
* Upewnij się, że przed usunięciem PCM z obudowy zawsze następuje rozłączenie połączenia zasilania.
* Biorąc pod uwagę, że wtyczka na dostaw zasilający jest głównym odłączyć urządzenie, upewnij się, że gniazda znajdują się w pobliżu urządzenia i są łatwo dostępne.

![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png) ![ikona uderzenia elektrycznego](./media/storsimple-safety/IC740882.png) **ostrzeżenie!**

Aby zmniejszyć prawdopodobieństwo przegrzaniu lub uruchomienie z elektrycznego połączeń:

* Zapewnić odpowiednie zasilania elektrycznego przeciążenia protection, aby spełniać wymagania wyszczególnione w specyfikacji technicznej.
* Nie należy używać bifurcated kable (potencjalnych klientów "Y").
* Do wykonania odpowiednich bezpieczeństwa, emisji i cieplna wymagania, należy usunąć nie obejmuje i wszystkie kieszeni musi być wypełniane przy użyciu wtyczki lub dysk puste wartości.
* Upewnij się, że urządzenie jest używana w sposób określony przez producenta. Jeśli urządzenie jest używany w sposób, który nie został określony przez producenta tego komputera, może być ograniczony zapewniany przez urządzenie.

![Ikony](./media/storsimple-safety/IC740881.png) **POWIADOMIEŃ:**

Aby zapewnić prawidłowe działanie sprzętu i aby zapobiec uszkodzeniu produktu:

* Porty RJ45 z tyłu urządzenia są tylko połączenia Ethernet. Te muszą nie być połączony z siecią telekomunikacyjną.
* Należy zainstalować w stojaku, które może obsłużyć projektu chłodzenia przodu do tyłu na urządzeniu.
* Wszystkie wtyczki i pustych płyt są częścią załącznik systemowy. Takie tylko muszą zostać usunięte, gdy natychmiast dodawane zastępczy. System nie muszą być uruchamiane bez wszystkich modułów lub puste wartości w miejscu.

## <a name="rack-system-precautions"></a>Środki ostrożności systemu stojak
W przypadku zainstalowania urządzenia w stojaku cabinet należy rozważyć następujące wymagania bezpieczeństwa.

![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png) ![zagrożenia ikona porady](./media/storsimple-safety/IC740886.png) **ostrzeżenie!**

Aby zmniejszyć prawdopodobieństwo szkody w etykietce przez:

* Projekt stojak powinien obsługi wagą całkowitą zainstalowanych obudów i powinien dołączyć funkcje utrwalający może uniemożliwić stojaku wyrzucanie lub przekazywanej za pośrednictwem podczas instalacji lub użytkowania.
* Podczas ładowania stojak, wypełnij stojak od dołu do góry, a pusty od góry w dół.
* Nie slajd więcej niż jeden załącznik poza stojaku w czasie, aby uniknąć zagrożenia toppling stojaku.

![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png) ![ikona uderzenia elektrycznego](./media/storsimple-safety/IC740882.png) **ostrzeżenie!**

Aby zmniejszyć prawdopodobieństwo szkody, elektrycznego uderzenia lub śmierci:

* Stojaku powinny mieć system bezpiecznej dystrybucji elektrycznego. Należy zapewnić ochronę uwierzytelniając bieżącego obudowa, a nie musi być przeciążony przez całkowitą liczbę obudów zainstalowane. Ocena zużycia energii elektrycznej wyświetlany na tablica umieszczona powinien być uwzględniony.
* System elektrycznego dystrybucji należy podać niezawodnej podstaw każdej obudowy w stojaku.
* Podczas projektowania systemu elektrycznego dystrybucji musi wziąć pod uwagę wycieku całkowita podstaw bieżącego z wszystkich zasilacze w obudowach do wszystkich. Pamiętaj, że każdy zasilacz w każdej obudowie ma bieżącej wycieku podstaw 1.0 maksymalnego mA w 60 Hz 264 v. Stojaku może wymagać etykietowania z "BIEŻĄCĄ WYCIEKU wysoki. Zwarcie (ziemi) jest przed połączeniem dostawy."
* Stojak, gdy skonfigurowano obudów, musi spełniać wymagania dotyczące zabezpieczeń UL 60950-1 i IEC 60950-1/EN 60950-1.

![Ikony](./media/storsimple-safety/IC740881.png) **POWIADOMIEŃ:**

Do prawidłowego chłodzenia systemu stojaku:

* Upewnij się, że projekt stojak uwzględnia obudowa maksymalna temperatura otoczenia 35 stopni c (95 stopni f).
* System jest obsługiwany z instalacją Niskociśnieniowa, spalin tyłu (utworzony przez drzwi stojaku i przeszkód nie przekracza 5 Pascal [0,5 mm słupa wody] wykorzystania wstecz).

## <a name="power-cooling-module-pcm-precautions"></a>Środki ostrożności chłodzenia modułu (PCM) zasilania
Urządzenie jest przeznaczony do działania z dwiema PCMs. Każdy PCMs ma źródło zasilania i wentylator podwójnego osi. Podczas krytycznego stanu systemu umożliwia awarii jednego zasilania, pozostawiając normalnych operacji. Dwa PCMs (i dlatego zasilania) zawsze musi być zainstalowany. Pojedynczy PCM nie zapewnia nadmiarowy. W związku z tym niepowodzenia PCM nawet co może spowodować przestoje lub możliwa utrata danych.

![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png) ![ikona uderzenia elektrycznego](./media/storsimple-safety/IC740882.png) **ostrzeżenie!**

Aby zmniejszyć prawdopodobieństwo szkody, elektrycznego uderzenia lub śmierci:

* Nie usuwaj obejmuje z PCM. Brak zagrożenie uderzenia elektrycznego wewnątrz. Zwraca PCM i uzyskanie zastąpienia, [skontaktuj się z Microsoft Support](storsimple-contact-microsoft-support.md).

![Ikony](./media/storsimple-safety/IC740881.png) **POWIADOMIEŃ:**

Aby zapewnić prawidłowe działanie sprzętu i aby zapobiec uszkodzeniu produktu:

* Należy zastąpić PCM nie powiodło się w ciągu 24 godzin. Po usunięciu PCM do zastąpienia zastąpienia należy wykonać w ciągu 10 minut po usunięciu.
* Nie usuwaj PCM, o ile nie zastępuje ona instalowana natychmiast. Nie należy uruchamiać obudowa bez wszystkich modułów w miejscu.

## <a name="electrostatic-discharge-esd-precautions"></a>Środki ostrożności elektrostatyczne (ESD)
![Ikony](./media/storsimple-safety/IC740881.png) **POWIADOMIEŃ:**

Sprawdź następujące ostrzeżenia dotyczące ESD.

* Sprawdź zainstalowane i sprawdzane odpowiedniego antistatic paska nadgarstka lub kostki.
* Sprawdź wszelkie środki ostrożności z konwencjonalnej ESD podczas obsługi modułów i składniki.
* Unikaj kontaktować się z modułu łączników i składników systemu backplane.
* Uszkodzenie ESD nie pasuje do żadnego gwarancji.

## <a name="battery-disposal-precautions"></a>Środki ostrożności usuwania baterii
Zasilacz używa baterii specjalne do ochrony zawartości pamięci podczas awarie zasilania tymczasowego, krótkoterminowej. Bateria jest umieszczona w PCM. Należy pamiętać o baterii następujące informacje.

![Ikona ostrzeżenia](./media/storsimple-safety/IC740879.png) **ostrzeżenie!**

Aby zmniejszyć ryzyko szorty fire, masowego, szkody albo śmierci:

* Usuwa używane baterie zgodnie z przepisami krajowych i regionalnych.
* Nie dezasemblować, rozdrobnić, ogrzewać ponad 60 stopni c (140 stopni f) lub spalania. Zastąp baterii PCM tylko podany baterii. Użyj innego poziomu energii baterii może stanowić ryzyko fire lub rozłożenia.
* Użyj ochronnych zakończenia z baterii, jeśli są one usuwane z zasilania.

![Ikony](./media/storsimple-safety/IC740881.png) **POWIADOMIEŃ:**

Podczas wysyłania lub w przeciwnym razie transportowania baterie lotniczego, wykonaj IATA litu baterii wytycznych dostępne pod adresem [http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Po przejrzeniu tych ogłoszeniach bezpieczeństwa następne kroki są Rozpakowywanie, należy zainstalować w stojaku i Podłączanie kabli do urządzenia.

## <a name="next-steps"></a>Następne kroki
* Przejdź do urządzenia 8100 [zainstalować do urządzenia StorSimple 8100](storsimple-8100-hardware-installation.md).
* Przejdź do urządzenia 8600 [zainstalować do urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md).

