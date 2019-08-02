---
title: Zagadnienia dotyczące projektowania symulacji akustyki
titlesuffix: Azure Cognitive Services
description: W tym przeglądzie koncepcyjnym wyjaśniono, w jaki sposób akustyczne projektowe obejmuje symulację akustyczną w procesie projektowania dźwięku.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: c7e6f17d3e7b9712dd853bcf309bb73fa10ac156
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704833"
---
# <a name="project-acoustics-design-process-concepts"></a>Pojęcia związane z procesem projektowania akustycznego projektu

W tym omówieniu koncepcji wyjaśniono, w jaki sposób akustyczne projektu polega na rozdzieleniu fizycznej symulacji akustycznej na proces projektowania dźwięku.

## <a name="sound-design-with-audio-dsp-parameters"></a>Projekt dźwiękowy z parametrami DSP audio

interaktywne tytuły 3W osiągają określony dźwięk przy użyciu bloków audio-dźwiękowych (DSP), hostowanych w aparacie audio. Bloki te obejmują złożoność z prostego mieszania, do reverberation, echo, opóźnienia, korektora, kompresji i ograniczania oraz inne efekty. Wybór, porządkowanie i ustawianie parametrów dla tych efektów jest odpowiedzialne za projektanta dźwięku, który kompiluje Graf audio, który osiąga estetyczne i rozgrywkę cele środowiska.

W interaktywnym tytule, jak dźwięki i odbiornik poruszają się w przestrzeni trójwymiarowej, jak te parametry dostosowują się do zmieniających się warunków? Projektant dźwięku często rozmieścić woluminy w całym miejscu, które są programowane w celu wyzwolenia zmian parametrów, aby osiągnąć zmiany w efektach reverberation, na przykład lub do kaczia dźwięków w mieszaninie, gdy odbiornik przejdzie z jednej części sceny do innej. Dostępne są również systemy akustyczne, które mogą zautomatyzować niektóre z tych efektów.

tytuły 3W wykorzystują oświetlenie i Kinematic systemy fizyki, które są fizyko-uzasadnione, ale dostosowane przez projektanta do osiągnięcia różnych celów związanych z zanurzeniem i rozgrywkę. Projektant wizualny nie ustawia pojedynczych wartości pikseli, ale raczej dostosowuje modele 3W, materiały i systemy transportu lekkich, które są fizycznie oparte na wymianie wizualnej i kosztach procesora. Co to jest równoważny proces audio? Dźwięk jest pierwszym krokiem w eksploracji tego pytania. Najpierw będziemy korzystać z tego, co oznacza, aby transportować energię akustyczną za pośrednictwem miejsca.

![Zrzut ekranu przedstawiający scenę AltSpace z Reverb strefami](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Impulsy odpowiedzi: Akustyczne łączenie dwóch punktów w miejscu

Jeśli znasz już projekt audio, możesz poznać odpowiedzi na impulsy akustyczne. Odpowiedź impulsu akustycznego modeluje transport dźwięku ze źródła do odbiornika. W związku z tym odpowiedzi impulsów mogą przechwytywać każdy interesujący efekt hałasu z pokoju, takiego jak zamknięcia i reverberation. Odpowiedzi impulsów mają również pewne zaawansowane właściwości, które umożliwiają skalowanie efektów Audio DSP. Dodanie dwóch sygnałów audio razem i przetwarzania z odpowiedzią impulsu daje ten sam wynik, co stosowanie impulsu odpowiedzi dla każdego sygnału i Dodawanie wyników. Propagacja akustyczna i impulsowe odpowiedzi nie zależą również od przetwarzania dźwięku, tylko na modelowanej scenie i lokalizacji źródłowej i odbiornika. W skrócie, impuls odpowiedzi pozostanie efektem sceny dotyczącej propagacji dźwięku.

Odpowiedź impulsu przechwytuje każdy interesujący efekt akustyczny pokoju i możemy zastosować ją do wydajnego dźwięku za pomocą filtru, a firma Microsoft może uzyskać odpowiedzi na miarę pomiaru lub symulacji. Ale co zrobić, jeśli nie chcesz, aby akustyczne dokładnie pasowały do fizyki, ale Mold je w celu dopasowania do emocjonalnejych wymagań sceny? Podobnie jak w przypadku wartości pikseli, odpowiedzi impulsów jest tylko lista tysięcy cyfr, jak można dostosować ją do potrzeb estetycznych? I co zrobić, jeśli chcemy mieć zamknięcia/przeszkodę, która zmienia się w sposób płynny i przechodzą przez Doorways lub za przeszkody, ile impulsów potrzebujemy, aby uzyskać gładki efekt? Co zrobić, jeśli źródło jest przenoszone szybko? Jak możemy przeprowadzić interpolację?

Trudno jest używać symulacji i impulsów odpowiedzi dla niektórych aspektów akustycznych w tytułach interaktywnych. Jednak nadal możemy utworzyć system transportu audio, który obsługuje dostosowania projektanta, jeśli możemy połączyć nasze impulsy z symulacji przy użyciu znanych parametrów efektów Audio DSP.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>Łączenie symulacji z procesorem DSP audio z parametrami

Odpowiedź impulsu zawiera każdy interesujący (i każdy nieinteresujący) efekt akustyczny. Bloki DSP audio, gdy ich parametry są ustawione prawidłowo, mogą renderować interesujący efekt akustyczny. Korzystanie z symulacji akustycznej w celu wypróbowania bloku Audio DSP w celu zautomatyzowania transportu audio w scenie 3D jest tylko kwestią mierzenia parametrów Audio DSP z odpowiedzi impulsu. Pomiary te są dobrze zrozumiałe dla niektórych typowych i ważnych efektów akustycznych, takich jak zamknięcia, przeszkoda, portaler i reverberation.

Ale jeśli Symulacja jest połączona bezpośrednio z parametrami DSP audio, gdzie jest dopasowanie projektanta? Jakie korzyści dodaliśmy? Dodatkowo zyskuje znaczną ilość pamięci dzięki odrzucaniu impulsów i zachowaniu kilku parametrów DSP. I aby dać projektantowi pewną moc w stosunku do końcowego wyniku, musimy tylko znaleźć sposób wstawiania projektanta między symulacją a procesorem DSP audio.

![Graf z założonym impulsem odpowiedzi z parametrami](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>Projektowanie dźwięku przez transformowanie parametrów DSP audio z symulacji

Weź pod uwagę wpływ efektów ubocznych na Twój widok świata. Na krótkim dzień szklanki mogą zmniejszyć komfort do czegoś bardziej wygodnego. W ciemnym pokoju może nie być możliwe wyświetlenie wszystkiego. Okulary nie ustawiają pewnego poziomu jasności we wszystkich sytuacjach. właśnie sprawiają, że wszystko jest ciemne.

Jeśli korzystamy z symulacji w celu napędu naszego DSP audio przy użyciu parametrów zamknięcia i reverberation, możemy dodać filtr po symulatorze, aby dostosować parametry, które obsługuje procesor DSP ". Filtr nie wymusił określonego poziomu zamknięcia lub Reverb długość ogona, podobnie jak okulary słoneczne nie sprawiają, że każdy pokój ma tę samą jasność. Filtr może po prostu dokonywać occluder occlude. Lub occlude więcej. Dodanie i dostosowanie jednego "przyciemniania" filtru parametrów zamknięcia, duże, otwarte pokoje nadal będzie miało niewielki wpływ na zamknięcia, podczas gdy Doorways zwiększy się od średniego do silnego efektu zamknięcia, zachowując gładkość w przejściach efektu zapewnia symulację.

W tym modelu zadania projektanta zmieniają się przed wybraniem parametrów akustycznych dla każdej i każdej sytuacji, aby wybrać i dostosować filtry do zastosowania do najważniejszych parametrów DSP pochodzących z symulacji. Podnosi ona działania projektanta od wąskich obaw związanych z konfigurowaniem płynnych przejść do wyższego wpływu na intensywność efektów zamknięcia i reverberation oraz obecność źródeł w mieszaninie. Oczywiście, gdy zajdzie taka potrzeba, jeden filtr zawsze jest dostępny po prostu z powrotem do wybierania parametrów DSP dla określonego źródła w określonej sytuacji.

## <a name="sound-design-in-project-acoustics"></a>Projekt dźwięku w przypadku hałasu w projekcie

Pakiet akustyczny projektu integruje poszczególne składniki opisane powyżej: symulator, koder, który wyodrębnia parametry i kompiluje element zawartości akustycznej, DSP audio i wybór filtrów. Projekt dźwiękowy z Akustycznością projektu obejmuje wybór parametrów filtrów, które dostosowują parametry zamknięcia i reverberation wynikające z symulacji i stosowane do DSP audio, z kontrolkami dynamicznymi widocznymi w edytorze gier i aparacie audio.

## <a name="next-steps"></a>Kolejne kroki
* Wypróbuj model projektowy, korzystając z [przewodnika Szybki Start dla środowiska Unity](unity-quickstart.md) lub [projektu — Przewodnik Szybki Start dla Unreal](unreal-quickstart.md)
* Eksploruj kontrolki projektowe akustyczne projektu [dla aparatu Unity](unity-workflow.md) lub [formanty układu akustycznego projektu dla Unreal](unreal-workflow.md)

