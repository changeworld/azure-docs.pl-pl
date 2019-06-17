---
title: Zagadnienia dotyczące projektowania symulacji akustyki
titlesuffix: Azure Cognitive Services
description: To omówienie pojęć dotyczących wyjaśnia, jak projekt Akustyka dołącza akustyczny symulację do procesu projektowania dźwięku.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 4a1a0b15da091a1c020eb132f6b14b9ee14d334c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61335421"
---
# <a name="project-acoustics-design-process-concepts"></a>Pojęcia procesu projektowania Akustyka projektu

To omówienie pojęć dotyczących wyjaśnia, jak projekt Akustyka dołącza fizycznych symulacji akustyczny procesem zaprojektowaniu.

## <a name="sound-design-with-audio-dsp-parameters"></a>Zaprojektowaniu parametrami DSP audio

Tytuły interaktywne 3D osiągnąć ich określonego dźwięku, za pomocą cyfrowego sygnału audio przetwarzania bloków (DSP) hostowanych w aparacie audio. Te zakres bloków rozwiązania od prostego mieszania, reverberation, echo, opóźnienie, wyrównywania, kompresji i ograniczania i innych skutków. Wybierając, rozmieszczanie i ustawianie parametrów w tych skutków odpowiada projektanta dźwięk, który tworzy audio programu graph, która osiąga cele estetycznych i gra środowiska.

W tytule interaktywne dźwięki i odbiornika przeniesieniu w całej przestrzeni 3D jak te parametry dostosowanie do zmieniających się warunków? Projektant dźwięku często zorganizuje woluminy w całej przestrzeni zaprogramowane do wyzwolenia zmiany parametrów do osiągnięcia zmiany w reverberation skutki, na przykład lub dźwięków kaczka w asortymencie przemieszcza się odbiornika z jednej strony sceny do innego. Systemy Akustyka są również dostępne, można zautomatyzować niektóre z tych skutków.

Tytuły 3D używać udziału oświetlenia i kinematyczna fizyki systemami są uzasadnione fizyki, ale Projektant dostosowane do różnych celów — szczegółowe informacje i gra osiągnięcia. Projektant wizualny nie zostały ustawione wartości poszczególnych pikseli, ale raczej dostosowuje modele 3D, materiały i światła systemów, które są wszystkie fizycznie oparte na handlu visual wyglądu i koszty procesora CPU. Jaki byłyby równoważne proces dźwięk? Akustyka projektu jest pierwszym etapem eksplorowania to pytanie. Firma Microsoft będzie dotknij najpierw na znaczenia transportu akustyczne energii poprzez spację.

![Zrzut ekranu AltSpace sceny nałożony ze strefami pogłosu](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Impulsów odpowiedzi: Pod względem akustycznym łączenie dwóch punktów w przestrzeni

Jeśli znasz audio projektu może być zna akustycznych impulsów odpowiedzi. Odpowiedź akustyczny impulsów modeli transportu dźwięk ze źródła do odbiornika. W związku z tym odpowiedź impulsów można przechwycić co ciekawe efekt Akustyka pomieszczenia, takie jak zamknięcia i reverberation. Odpowiedzi impulsów ma również niektórych zaawansowanych właściwości, które umożliwia efekty dźwiękowe DSP skalowania. Dodanie dwóch sygnałów audio i przetwarzania o odpowiedź impulsów daje ten sam wynik jako stosowanie odpowiedzi impulsów oddzielnie poszczególnych sygnałów i dodanie wyników. Propagacja akustyczne i odpowiedzi impulsów również nie należy polegać na dźwięk przetwarzane tylko na scenie są modelowane i lokalizacja źródłowa i odbiornika. Krótko mówiąc odpowiedź impulsów przetwarza sceny wpływu na propagację dźwięku.

Odpowiedź impulsów przechwytuje co ciekawe pokoju akustyczny efekt, możemy zastosować go do audio wydajnie za pomocą filtru i uzyskujemy impulsów odpowiedzi z pomiaru lub symulacji. Ale co zrobić, jeśli firma Microsoft nie ma dość Akustyka, aby dokładnie dopasować fizyki, ale raczej mold jej odpowiadający wymaganiom emocjonalnej sceny? Ale znacznie takich jak wartości pikseli odpowiedź impulsów się tylko listami tysięcy liczb znajdujących się, jak firma Microsoft prawdopodobnie je dostosować do potrzeb estetycznych? I co zrobić, jeśli chcemy mieć zamknięcia. / przeszkoda, który jest różny płynnie podczas przekazywania za pomocą drzwi lub za zaporą przeszkód, ile odpowiedzi impulsów chcemy uzyskać efekt smooth? Co zrobić, jeśli źródłowy przenosi się szybko? Jak możemy interpolacji?

Wydaje się to trudne w użyciu symulacji i impulsów odpowiedzi dotyczących niektórych aspektów Akustyka w tytułach interaktywne. Ale możemy ją tworzyć nadal systemem audio transportu, który obsługuje projektanta dopasowania, jeśli połączymy naszych odpowiedzi impuls z symulacji parametrami naszych znanych audio DSP efekt.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>Symulacja nawiązywania połączenia z DSP audio z parametrami

Odpowiedź impulsów zawiera każdy interesujące (i każdy postrzegać) akustyczne efekt. Audio DSP bloki, podczas ich parametry są ustawione poprawnie, można renderować akustyczne efekty. Dysk blokiem DSP audio do zautomatyzowania transportu audio w scenie 3D przy użyciu symulacji akustyczne polega tylko na pomiaru audio parametrów DSP z odpowiedzi impulsów. Ten pomiar jest zrozumiały dla niektórych typowych i ważnych skutków akustyczne tym zamknięcia "," przeszkoda "," portalling "i" reverberation.

Ale jeśli Symulacja jest podłączony bezpośrednio do audio parametrów DSP, gdzie jest dostosowanie projektanta? Co ich? Dobrze ponownie, odrzucając impulsów odpowiedzi i utrzymywanie kilku parametrów DSP ich znacznej ilości pamięci. I zapewnienie projektanta nieco energii w wyniku końcowego, należy tylko znaleźliśmy sposób wstawiania projektanta między symulacji i dźwięk DSP.

![Wykres z odpowiedzią stylizowane impuls z parametrami nałożony](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>Zaprojektowaniu poprzez przekształcanie audio DSP parametry symulacji

Należy wziąć pod uwagę wpływ posiadane przez użytkownika okularów przeciwsłonecznych w nakierowaniu pomniejszyć widok świata. W dniu jasny okularów może zmniejszyć lśnienia na coś bardziej komfortowo, jednocześnie. W pokoju ciemny może nie mieć możliwość znajdziesz już niczego w ogóle. Okulary nie należy ustawiać poziom jasności we wszystkich sytuacjach; po prostu dokonają wszystko, czego ciemniejszy.

Jeśli używamy symulacji można dostarczać naszym audio DSP przy użyciu parametrów zamknięcia i reverberation możemy dodać filtr po symulator, aby dostosować parametry, które DSP "widzi". Filtr nie wymuszają określony poziom zamknięcia lub pogłosu długość tail podobnie jak okularów przeciwsłonecznych w nakierowaniu nie pomieścić co jasności ten sam. Filtr po prostu wprowadzać co occluder occlude mniejsza. Lub occlude więcej. Przez dodawanie i dostosowywanie filtr parametr zamknięcia. "ściemniające", pokoje dużych, otwórz nadal będzie miał mały do zamknięcia efektu, gdy drzwi wydłuży ze środowiska w celu zamknięcia silne., przy zachowaniu największy obowiązuje przejścia zapewnia symulacji.

W tym modelu projektanta zadania zmienia się wybór parametrów akustycznych w każdej sytuacji do wybierania i Dostosowywanie filtrów do zastosowania najważniejsze parametry DSP pochodzące z symulacji. Jego eksponuje projektanta działań z wąskie dotyczy konfigurowania przejścia na wyższy zainteresowania intensywność skutki zamknięcia i reverberation i obecność źródeł w zestawie. Oczywiście gdy wymaga tego sytuacja, jeden filtr zawsze dostępna jest po prostu przejdź wstecz do wybierania parametry DSP dla określonego źródła w konkretnej sytuacji.

## <a name="sound-design-in-project-acoustics"></a>Zaprojektowaniu w Akustyka projektu

Pakiet Akustyka projektu integruje poszczególne składniki opisane powyżej: symulatora, kodera, który wyodrębnia parametrów i tworzy zasób Akustyka, procesor DSP audio i wyboru filtrów. Dźwięk projektu z projektu Akustyka pociąga za sobą parametry wybierając filtry, które dostosować parametry zamknięcia i reverberation pochodną symulacji i stosowane do audio DSP, za pomocą kontrolek dynamicznych udostępniane wewnątrz edytora gry i aparat audio.

## <a name="next-steps"></a>Kolejne kroki
* Wypróbuj przy użyciu modelu projektu [Akustyka projektu Przewodnik Szybki start dotyczący Unity](unity-quickstart.md) lub [Akustyka projektu Przewodnik Szybki start dotyczący Unreal](unreal-quickstart.md)
* Zapoznaj się z [Akustyka projektu projektowania formanty programu Unity](unity-workflow.md) lub [Akustyka projektu projektowania formanty Unreal](unreal-workflow.md)

