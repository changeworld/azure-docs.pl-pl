---
title: Bezpieczeństwo urządzenia StorSimple | Dokumenty firmy Microsoft
description: W tym artykule opisano konwencje bezpieczeństwa, wskazówki i zagadnienia oraz wyjaśniono, jak bezpiecznie zainstalować i obsługiwać urządzenie StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2017
ms.author: alkohli
ms.openlocfilehash: 66b881ab13e27ee457af4fa1bafb82ad14e9674d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60631684"
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>Bezpieczne instalowanie i obsługa urządzenia StorSimple
![Ikona](./media/storsimple-safety/IC740879.png)
![ostrzeżenia Przeczytaj](./media/storsimple-safety/IC740885.png) ikonę powiadomienia o bezpieczeństwie **PRZECZYTAJ INFORMACJE O BEZPIECZEŃSTWIE I ZDROWIU**

Przeczytaj wszystkie informacje o bezpieczeństwie i kondycji w tym artykule, który ma zastosowanie do urządzenia Microsoft Azure StorSimple. Wszystkie drukowane prowadnice należy przechowywać w urządzeniu StorSimple w przyszłości. Nieprzestrzeganie instrukcji i prawidłowe skonfigurowanie, użycie i pielęgnacja tego produktu może zwiększyć ryzyko poważnych obrażeń lub śmierci lub uszkodzenia urządzenia lub urządzeń. Dostępna jest również [wersja tego przewodnika do pobrania.](https://www.microsoft.com/download/details.aspx?id=44233)

## <a name="safety-icon-conventions"></a>Konwencje ikon bezpieczeństwa
Oto ikony, które można znaleźć podczas przeglądania środków ostrożności, które należy przestrzegać podczas konfigurowania i uruchamiania urządzenia Microsoft Azure StorSimple.

| Ikona | Opis |
|:--- |:--- |
| ![Niebezpieczna](./media/storsimple-safety/IC740879.png) **ikona NIEBEZPIECZEŃSTWO!** |Wskazuje na niebezpieczną sytuację, która, jeśli nie zostanie unikniętą, doprowadzi do śmierci lub poważnych obrażeń. To słowo sygnałowe ma być ograniczone do najbardziej ekstremalnych sytuacji. |
| ![Ikona](./media/storsimple-safety/IC740879.png) ostrzeżenia **OSTRZEŻENIE!** |Wskazuje na niebezpieczną sytuację, która, jeśli nie zostanie unikniętą, może spowodować śmierć lub poważne obrażenia ciała. |
| ![Ikona](./media/storsimple-safety/IC740879.png) ostrzeżenia **UWAGA!** |Wskazuje na niebezpieczną sytuację, która, jeśli nie zostanie unikniętą, może spowodować niewielkie lub umiarkowane obrażenia. |
| ![Uwaga](./media/storsimple-safety/IC740881.png) **Ikona UWAGA:** |Wskazuje informacje uważane za ważne, ale nie związane z zagrożeniami. |
| ![Ikona](./media/storsimple-safety/IC740882.png) **porażenia prądem elektrycznym** |Wysokie napięcie |
| ![Gruba](./media/storsimple-safety/IC740883.png) ikona **wagi ciężkiej** | |
| ![Brak ikony](./media/storsimple-safety/IC740879.png) części z możliwością serwisowania **użytkownika Brak części z możliwością serwisowania przez użytkownika** |Nie należy do nich korzystać, chyba że zostanie odpowiednio przeszkolony. |
| ![Przeczytaj ikonę powiadomienia o bezpieczeństwie](./media/storsimple-safety/IC740885.png)**Przeczytaj najpierw wszystkie instrukcje** | |
| ![Wskazówka](./media/storsimple-safety/IC740886.png) **Niebezpieczeństwo** | |

## <a name="handling-precautions"></a>Środki ostrożności podczas postępowania
![Ostrzeżenie](./media/storsimple-safety/IC740879.png) ![Ikona](./media/storsimple-safety/IC740883.png) wagi ciężkiej **OSTRZEŻENIE!** 

Aby zmniejszyć ryzyko obrażeń:

* W pełni skonfigurowana obudowa może ważyć do 32 kg (70 funtów); nie próbuj podnosić go samodzielnie.
* Przed przeniesieniem obudowy należy zawsze upewnić się, że dwie osoby są dostępne do obsługi wagi. Należy pamiętać, że jedna osoba próbująca podnieść tę wagę może doznać obrażeń.
* Nie podnosić obudowy za uchwyty modułów zasilania i chłodzenia (PCM) znajdujących się z tyłu urządzenia. Nie są one przeznaczone do przybrania ciężaru.

## <a name="connection-precautions"></a>Środki ostrożności dotyczące połączenia
![Ikona](./media/storsimple-safety/IC740879.png) ![ostrzeżenia](./media/storsimple-safety/IC740882.png) Ikona porażenia prądem **elektrycznym OSTRZEŻENIE!**

Aby zmniejszyć prawdopodobieństwo obrażeń, porażenia prądem elektrycznym lub śmierci:

* Po uruchomieniu przez wiele źródeł prądu przemiennego odłącz wszystkie zasilanie zasilania w celu całkowitej izolacji.
* Na stałe odłącz urządzenie przed jego przeniesieniem lub jeśli uważasz, że zostało uszkodzone w jakikolwiek sposób.
* Zapewnić bezpieczne podłączenie uziemienia elektrycznego do przewodów zasilających. Przed zastosowaniem zasilania należy sprawdzić, czy uziemienie obudowy spełnia wymagania krajowe i lokalne.
* Przed wyjęciem pcm z obudowy upewnij się, że połączenie zasilania jest zawsze odłączone.
* Biorąc pod uwagę, że wtyczka na przewodzie zasilającym jest głównym urządzeniem odłączającym, upewnij się, że gniazda znajdują się w pobliżu urządzenia i są łatwo dostępne.

![Ikona](./media/storsimple-safety/IC740879.png) ![ostrzeżenia](./media/storsimple-safety/IC740882.png) Ikona porażenia prądem **elektrycznym OSTRZEŻENIE!**

Aby zmniejszyć prawdopodobieństwo przegrzania lub pożaru z połączeń elektrycznych:

* Zapewnienie odpowiedniego źródła zasilania z zabezpieczeniem przed przeciążeniem elektrycznym, aby spełnić wymagania określone w specyfikacji technicznej.
* Nie używaj rozwidlonych przewodów zasilających ("Y" przewodów).
* Aby spełnić obowiązujące wymagania w zakresie bezpieczeństwa, emisji i ciepła, nie należy zdejmować osłon, a wszystkie wnęki muszą być wypełnione modułami wtykowymi lub półfabrykatami napędu.
* Upewnić się, że urządzenie jest używane w sposób określony przez producenta. Jeżeli to urządzenie jest używane w sposób nieokreślony przez producenta, ochrona zapewniana przez urządzenie może ulec pogorszeniu.

![Uwaga](./media/storsimple-safety/IC740881.png) **Ikona UWAGA:**

W celu prawidłowego działania sprzętu i zapobiegania uszkodzeniom produktu:

* Porty RJ45 z tyłu urządzenia służą wyłącznie do połączenia Ethernet. Nie wolno ich podłączać do sieci telekomunikacyjnej.
* Pamiętaj, aby zainstalować urządzenie w stelażu, który może pomieścić konstrukcję chłodzenia od przodu do tyłu.
* Wszystkie moduły wtykowe i puste płyty są częścią obudowy systemu. Należy je usunąć tylko wtedy, gdy można natychmiast dodać zamiennik. System nie może być uruchamiany bez wszystkich modułów lub półfabrykatów.

## <a name="rack-system-precautions"></a>Środki ostrożności systemu rack
Podczas montażu urządzenia w szafie rack należy wziąć pod uwagę następujące wymagania bezpieczeństwa.

![Ikona](./media/storsimple-safety/IC740879.png) ![ostrzeżenia](./media/storsimple-safety/IC740886.png) Wskazówka Hazard Icon **OSTRZEŻENIE!**

Aby zmniejszyć prawdopodobieństwo obrażeń od przewrócenia się:

* Konstrukcja stelaża powinna wspierać całkowitą masę zainstalowanych obudów i powinna zawierać funkcje stabilizujące odpowiednie, aby zapobiec przewróceniu się stelaża lub przesunięciu podczas instalacji lub normalnego użytkowania.
* Podczas ładowania stelaża napełnij stojak od dołu do góry i opróżnij od góry do dołu.
* Nie wysuwać więcej niż jednej obudowy ze stelaża naraz, aby uniknąć niebezpieczeństwa przewrócenia się stojaka.

![Ikona](./media/storsimple-safety/IC740879.png) ![ostrzeżenia](./media/storsimple-safety/IC740882.png) Ikona porażenia prądem **elektrycznym OSTRZEŻENIE!**

Aby zmniejszyć prawdopodobieństwo obrażeń, porażenia prądem elektrycznym lub śmierci:

* Stojak powinien mieć bezpieczny system dystrybucji energii elektrycznej. Musi zapewniać zabezpieczenie nadprądowe dla obudowy i nie może być przeciążony całkowitą liczbą zainstalowanych obudów. Należy przestrzegać wartości znamionowej zużycia energii elektrycznej podanej na tabliczce znamionowej.
* System dystrybucji energii elektrycznej musi zapewniać niezawodne podłoże dla każdej obudowy w stelażu.
* Konstrukcja systemu dystrybucji energii elektrycznej musi uwzględniać całkowity prąd wycieku gruntu ze wszystkich zasilaczy we wszystkich obudowach. Należy pamiętać, że każdy zasilacz w każdej obudowie ma prąd upływu ziemi 1,0 mA maksymalnie przy 60 Hz, 264 V. Stojak może wymagać etykietowania z "HIGH LEAKAGE CURRENT. Połączenie uziemienia (uziemienia) jest niezbędne przed podłączeniem zasilania."
* Stelaż, skonfigurowany z obudowami, musi spełniać wymagania bezpieczeństwa ul 60950-1 i IEC 60950-1/EN 60950-1.

![Uwaga](./media/storsimple-safety/IC740881.png) **Ikona UWAGA:**

Do prawidłowego chłodzenia systemu stelaża:

* Upewnij się, że konstrukcja stelaża uwzględnia maksymalną temperaturę otoczenia w obudowie 35 stopni Celsjusza (95 stopni Celsjusza).
* System jest obsługiwany przy instalacji niskociśnieniowej z tylnym wydechem (ciśnienie wsteczne tworzone przez drzwi stelaża i przeszkody nieprzekroczą 5 Pascal [0,5 mm wodomierza]).

## <a name="power-cooling-module-pcm-precautions"></a>Środki ostrożności dotyczące modułu chłodzenia zasilania (PCM)
Urządzenie jest przeznaczone do pracy z dwoma PCM. Każdy z pcms posiada zasilacz i wentylator dwuosiowy. W stanie krytycznym system umożliwia awarię jednego zasilacza przy jednoczesnym kontynuowaniu normalnej pracy. Dwa PCM (a tym samym zasilacze) muszą być zawsze zainstalowane. Pojedynczy pcm nie zapewnia nadmiarowego zasilania. W związku z tym awaria nawet jednego PCM może spowodować przestoje lub możliwą utratę danych.

![Ikona](./media/storsimple-safety/IC740879.png) ![ostrzeżenia](./media/storsimple-safety/IC740882.png) Ikona porażenia prądem **elektrycznym OSTRZEŻENIE!**

Aby zmniejszyć prawdopodobieństwo obrażeń, porażenia prądem elektrycznym lub śmierci:

* Nie wyjmuj pokryw z pcm. W środku istnieje niebezpieczeństwo porażenia prądem elektrycznym. Aby zwrócić pcm i uzyskać zamiennik, [skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-contact-microsoft-support.md).

![Uwaga](./media/storsimple-safety/IC740881.png) **Ikona UWAGA:**

W celu prawidłowego działania sprzętu i zapobiegania uszkodzeniom produktu:

* Nie można wymienić nieudanego pcm w ciągu 24 godzin. Po usunięciu PCM do wymiany, wymiana musi zostać zakończona w ciągu 10 minut po usunięciu.
* Nie należy usuwać pcm, chyba że można natychmiast zainstalować zamiennik. Obudowa nie może być eksploatowana bez wszystkich modułów na swoim miejscu.

## <a name="electrostatic-discharge-esd-precautions"></a>Środki ostrożności dotyczące wyładowań elektrostatycznych (ESD)
![Uwaga](./media/storsimple-safety/IC740881.png) **Ikona UWAGA:**

Należy przestrzegać następujących środków ostrożności związanych z ESD.

* Upewnij się, że masz zainstalowany i sprawdzony odpowiedni antystatyczny pasek na nadgarstek lub kostkę.
* Podczas obchodzenia się z modułami i komponentami należy przestrzegać wszystkich konwencjonalnych środków ostrożności ESD.
* Unikać kontaktu z komponentami zasokiwki i złączami modułów.
* Uszkodzenia ESD nie są objęte gwarancją.

## <a name="battery-disposal-precautions"></a>Środki ostrożności dotyczące usuwania baterii
Zasilacz wykorzystuje specjalną baterię do ochrony zawartości pamięci podczas tymczasowych, krótkoterminowych przerw w zasilaniu. Bateria znajduje się w pcm. Należy pamiętać o poniższych informacjach na temat baterii.

![Ikona](./media/storsimple-safety/IC740879.png) ostrzeżenia **OSTRZEŻENIE!**

Aby zmniejszyć ryzyko wystąpienia szortów, pożaru, wybuchu, obrażeń ciała lub śmierci:

* Zużyte baterie należy utylizować zgodnie z przepisami krajowymi/regionalnymi.
* Nie wolno dezasemble, zgniatać ani ogrzewać powyżej 60 stopni Celsjusza (140 stopni Celsjusza) ani nie spalać. Wymień baterię PCM tylko na dostarczoną baterię. Użycie innej baterii może stwarzać ryzyko pożaru lub wybuchu.
* Jeśli są one wyjęte z zasilacza, należy używać ochronnych zaślepek na końcach.

![Uwaga](./media/storsimple-safety/IC740881.png) **Ikona UWAGA:**

W przypadku wysyłki lub innego transportu baterii drogą powietrzną należy postępować zgodnie z dokumentem wskazówek dotyczących baterii litowych IATA dostępnym pod adresem[https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Po zapoznaniu się z tymi uwagami o bezpieczeństwie, następnymi krokami są rozpakowanie, stojak i kabel urządzenia.

## <a name="next-steps"></a>Następne kroki
* W przypadku urządzenia 8100 przejdź do [witryny Instalowanie urządzenia StorSimple 8100](storsimple-8100-hardware-installation.md).
* W przypadku urządzenia 8600 przejdź do [witryny Instalowanie urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md).

