---
title: Zagadnienia dotyczące projektowania symulacji akustyki
titlesuffix: Azure Cognitive Services
description: W tym przeglądzie koncepcyjnym wyjaśniono, w jaki sposób projekt Akustyka uwzględnia symulację akustyczną w procesie projektowania dźwięku.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 11e1e3f45b5198ddedb6c31fcd354185adef445d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854308"
---
# <a name="project-acoustics-design-process-concepts"></a>Koncepcje procesu projektowania akustyki projektu

W tym przeglądzie koncepcyjnym wyjaśniono, w jaki sposób projekt Akustyka włącza fizyczną symulację akustyczną do procesu projektowania dźwięku.

## <a name="sound-design-with-audio-dsp-parameters"></a>Konstrukcja dźwięku z parametrami DSP audio

Interaktywne tytuły 3D osiągają swój szczególny dźwięk za pomocą bloków przetwarzania cyfrowego sygnału audio (DSP) hostowanych w silniku audio. Bloki te zakres złożoności od prostego mieszania, do pogłosu, echo, opóźnienie, wyrównywanie, kompresji i ograniczania i innych efektów. Wybór, aranżowanie i ustawianie parametrów tych efektów jest obowiązkiem projektanta dźwięku, który buduje wykres audio, który osiąga cele estetyczne i rozgrywki doświadczenia.

W interaktywnym tytule, w miarę jak dźwięki i słuchacz poruszają się po przestrzeni 3D, w jaki sposób te parametry dostosowują się do zmieniających się warunków? Projektant dźwięku często rozmieszcza woluminy w całej przestrzeni, które są zaprogramowane do wyzwalania zmian parametrów w celu osiągnięcia zmian w efektach pogłosu, na przykład, lub do kaczą dźwięków w miksie, gdy odbiornik przenosi się z jednej części sceny do drugiej. Dostępne są również systemy akustyki, które mogą zautomatyzować niektóre z tych efektów.

Tytuły 3D wykorzystują systemy oświetlenia i fizyki kinematycznej, które są motywowane fizyką, ale dostosowane do projektantów, aby osiągnąć mieszankę celów zanurzenia i rozgrywki. Projektant wizualny nie ustawia wartości pojedynczych pikseli, ale dostosowuje modele 3D, materiały i lekkie systemy transportowe, które są fizycznie oparte na celu zrekontekcji estetyki wizualnej i kosztów procesora. Jaki byłby równoważny proces dla dźwięku? Projekt Akustyka jest pierwszym krokiem w badaniu tego pytania. Najpierw dotkniemy, co to znaczy transport energii akustycznej przez przestrzeń.

![Zrzut ekranu przedstawiający scenę AltSpace nałożącą na strefy pogłosu](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Reakcje impulsowe: Akustycznie łączące dwa punkty w przestrzeni

Jeśli znasz projekt dźwięku, możesz znać reakcje impulsów akustycznych. Reakcja impulsu akustycznego modeluje transport dźwięku ze źródła do odbiornika. Dlatego reakcja impulsowa może uchwycić każdy interesujący efekt akustyki pomieszczenia, takich jak okluzja i pogłos. Odpowiedzi impulsowe mają również pewne zaawansowane właściwości, które umożliwiają skalowanie efektów DSP audio. Dodanie dwóch sygnałów audio razem i przetwarzania z odpowiedzią impulsu daje taki sam wynik jak zastosowanie odpowiedzi impulsu oddzielnie do każdego sygnału i dodanie wyników. Propagacji akustycznej i impuls odpowiedzi również nie zależą od dźwięku przetwarzane, tylko na scenie modelowane, a lokalizacja źródła i odbiornika. Krótko mówiąc, reakcja impulsowa destyluje wpływ sceny na propagację dźwięku.

Reakcja impulsowa rejestruje każdy interesujący efekt akustyczny pomieszczenia i możemy go skutecznie zastosować do dźwięku za pomocą filtra, a my możemy uzyskać odpowiedzi impulsowe z pomiaru lub symulacji. Ale co, jeśli nie chcemy, aby akustyka dokładnie pasowała do fizyki, ale raczej kształtuje ją, aby dopasować ją do emocjonalnych wymagań sceny? Ale podobnie jak wartości pikseli, reakcja impulsowa to tylko lista tysięcy liczb, jak możemy ją ewentualnie dostosować do potrzeb estetycznych? A co, jeśli chcemy mieć okluzji / przeszkody, która zmienia się płynnie podczas przechodzenia przez drzwi lub za przeszkodami, ile impulsów odpowiedzi musimy uzyskać płynny efekt? Co zrobić, jeśli źródło porusza się szybko? Jak interpolujemy?

Trudno jest użyć symulacji i impulsów do niektórych aspektów akustyki w interaktywnych tytułach. Ale nadal możemy zbudować system transportu audio, który obsługuje regulacje projektanta, jeśli możemy połączyć nasze reakcje impulsowe z symulacji z naszymi znanymi parametrami efektu DSP audio.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>Łączenie symulacji z audio DSP z parametrami

Reakcja impulsowa zawiera każdy interesujący (i każdy nieciekawy) efekt akustyczny. Bloki DSP audio, gdy ich parametry są prawidłowo ustawione, mogą renderować interesujący efekt akustyczny. Używanie symulacji akustycznej do napędzania bloku DSP audio w celu zautomatyzowania transportu audio w scenie 3D to tylko kwestia pomiaru parametrów DSP audio z odpowiedzi impulsowej. Pomiar ten jest dobrze zrozumiany dla niektórych wspólnych i ważnych efektów akustycznych, w tym okluzji, niedrożności, portallingu i pogłosu.

Ale jeśli symulacja jest podłączona bezpośrednio do parametrów DSP audio, gdzie jest regulacja projektanta? Co zyskaliśmy? Cóż, zyskujemy znaczną ilość pamięci z powrotem, odrzucając odpowiedzi impulsowe i zachowując kilka parametrów DSP. Aby dać projektantowi pewną moc nad końcowym wynikiem, wystarczy znaleźć sposób na wstawienie projektanta między symulacją a audio DSP.

![Wykres ze stylizowaną reakcją impulsu z nałożonymi parametrami](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>Projektowanie dźwięku poprzez przekształcanie parametrów DSP audio z symulacji

Zastanów się nad wpływem okularów przeciwsłonecznych na widok świata. W jasny dzień okulary mogą zmniejszyć połysk do czegoś bardziej komfortowego. W ciemnym pokoju możesz nie być w stanie nic zobaczyć. Okulary nie ustawiają pewnego poziomu jasności we wszystkich sytuacjach; po prostu sprawiają, że wszystko ciemniejsze.

Jeśli używamy symulacji do kierowania naszym audio DSP przy użyciu parametrów okluzji i pogłosu, możemy dodać filtr po symulatorze, aby dostosować parametry, które "widzi" DSP. Filtr nie wymusiłby pewnego poziomu niedrożności lub długości ogona pogłosu, podobnie jak okulary przeciwsłoneczne nie sprawiają, że każdy pokój ma taką samą jasność. Filtr może po prostu sprawić, że każde okludy mniej. Lub occlude więcej. Dodając i dostosowując jeden filtr parametru okluzji "ciemnienie", duże, otwarte pomieszczenia nadal miałyby niewielki lub żaden efekt okluzji, podczas gdy drzwi wzrosłyby ze średniego do silnego efektu okluzji, zachowując jednocześnie płynność w przejściach efektu które zapewnia symulacja.

W tym paradygmacie zadanie projektanta zmienia się od wyboru parametrów akustycznych dla każdej sytuacji, do wybierania i dostosowywania filtrów w celu zastosowania do najważniejszych parametrów DSP pochodzących z symulacji. Podnosi działania projektanta z wąskich problemów związanych z tworzeniem płynnych przejść do wyższych obaw związanych z intensywnością efektów okluzji i pogłosu oraz obecnością źródeł w miksie. Oczywiście, gdy wymaga sytuacji, jeden filtr zawsze dostępne jest po prostu wrócić do wybierania parametrów DSP dla określonego źródła w określonej sytuacji.

## <a name="sound-design-in-project-acoustics"></a>Projektowanie dźwięku w akustyki projektu

Pakiet Project Acoustics integruje każdy z opisanych powyżej składników: symulator, koder, który wyodrębnia parametry i buduje zasób akustyki, audio DSP i wybór filtrów. Projekt dźwięku z akustyką projektu wiąże się z wyborem parametrów filtrów, które dostosowują parametry okluzji i pogłosu wynikające z symulacji i zastosowane do audio DSP, z dynamicznymi elementami sterującymi ujawnionymi wewnątrz edytora gier i silnika audio.

## <a name="next-steps"></a>Następne kroki
* Wypróbuj paradygmat projektowania za pomocą [przewodnika Szybki start akustyki projektu dla unity](unity-quickstart.md) lub [szybkiego startu akustyki projektu dla unreal](unreal-quickstart.md)
* Zapoznaj się z [kontrolkami projektu Akustyka projektu dla unity](unity-workflow.md) lub [formanty projektowe Akustyka projektu dla Unreal](unreal-workflow.md)

