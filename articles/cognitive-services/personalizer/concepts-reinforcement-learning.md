---
title: Uczenie się wzmacniające - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer wykorzystuje informacje o działaniach i bieżącym kontekście, aby lepiej sugestie rankingu. Informacje o tych akcjach i kontekście są atrybuty lub właściwości, które są określane jako funkcje.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 36071cdee25cfa99fc54b0e5c0c0aa822cb5fe2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68662835"
---
# <a name="what-is-reinforcement-learning"></a>Co to jest uczenie się wzmacniania?

Uczenie wzmacniające to podejście do uczenia maszynowego, które uczy się zachowań, uzyskując informacje zwrotne z jego użycia.
 
Wzmocnienie Nauka działa poprzez:

* Zapewnienie możliwości lub stopnia swobody w wprowadzaniu w życie zachowania - takiego jak podejmowanie decyzji lub wyborów.
* Dostarczanie informacji kontekstowych o środowisku i wyborach.
* Przekazywanie opinii na temat tego, jak dobrze zachowanie osiąga określony cel.

Chociaż istnieje wiele podtypów i stylów uczenia się wzmacniania, jest to, jak działa koncepcja w Personalizer:

* Aplikacja umożliwia wyświetlenie jednej zawartości z listy alternatyw.
* Aplikacja zawiera informacje o każdej alternatywy i kontekście użytkownika.
* Aplikacja oblicza _wynik nagrody_.

W przeciwieństwie do niektórych podejść do uczenia się wzmacniania, Personalizer nie wymaga symulacji do pracy. Jego algorytmy uczenia się są przeznaczone do reagowania na świat zewnętrzny (w porównaniu z kontrolą go) i uczyć się z każdego punktu danych ze zrozumieniem, że jest to wyjątkowa okazja, która kosztuje czas i pieniądze, aby stworzyć, i że istnieje non-zero żal (utrata ewentualnej nagrody) jeśli nieoptymalne działanie.

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>Jakiego rodzaju algorytmów uczenia wzmacniania używa Personalizer?

Obecna wersja Personalizer wykorzystuje **kontekstowych bandytów**, podejście do uczenia się wzmocnienia, które jest sformułowane wokół podejmowania decyzji lub wyborów między dyskretnymi działaniami, w danym kontekście.

_Pamięć decyzyjna_, model, który został przeszkolony do przechwytywania najlepszą możliwą decyzję, biorąc pod uwagę kontekst, używa zestawu modeli liniowych. Te wielokrotnie wykazały wyniki biznesowe i są sprawdzonym podejściem, częściowo dlatego, że mogą uczyć się od świata rzeczywistego bardzo szybko bez konieczności szkolenia wieloprzebiegowego, a częściowo dlatego, że mogą uzupełniać nadzorowane modele uczenia się i głębokie neuronalne modeli sieciowych.

Alokacja ruchu eksplorowania/wykorzystania odbywa się losowo po wartości procentowej ustawionej dla eksploracji, a domyślnym algorytmem eksploracji jest epsilon-chciwy.

### <a name="history-of-contextual-bandits"></a>Historia kontekstowych bandytów

John Langford ukuł nazwę Contextual Bandits (Langford i Zhang [2007]), aby opisać liczną podzbiór uczenia się wzmocnienia i pracował nad pół tuzina prac poprawiających nasze zrozumienie tego, jak uczyć się w tym paradygmacie:

* Beygelzimer i wsp.
* Dudík i wsp.
* Agarwal i wsp. [2014, 2012]
* Beygelzimer i Langford [2009]
* Li i wsp.

John dał również kilka tutoriali wcześniej na tematy, takie jak wspólne przewidywanie (ICML 2015), Kontekstowa teoria bandytów (NIPS 2013), Aktywne uczenie się (ICML 2009) i Granice złożoności próbki (ICML 2003)

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>Jakich struktur uczenia maszynowego używa Personalizer?

Personalizer obecnie używa [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki) jako podstawy dla uczenia maszynowego. Ta struktura pozwala na maksymalną przepływność i najniższe opóźnienie podczas tworzenia szeregów personalizacji i szkolenia modelu ze wszystkimi zdarzeniami.

## <a name="references"></a>Dokumentacja

* [Podejmowanie decyzji kontekstowych przy niskim zadłużeniu technicznym](https://arxiv.org/abs/1606.03966)
* [Podejście do sprawiedliwej klasyfikacji redukcji](https://arxiv.org/abs/1803.02453)
* [Efektywne kontekstowe bandytów w światach niesklatkowych](https://arxiv.org/abs/1708.01799)
* [Przewidywanie strat resztkowych: Wzmocnienie: nauka bez przyrostowych sprzężeń zwrotnych](https://openreview.net/pdf?id=HJNMYceCW)
* [Instrukcje mapowania i obserwacje wizualne do działań z uczeniem się wzmacniania](https://arxiv.org/abs/1704.08795)
* [Nauka wyszukiwania lepiej niż twój nauczyciel](https://arxiv.org/abs/1502.02206)

## <a name="next-steps"></a>Następne kroki

[Ocena w trybie offline](concepts-offline-evaluation.md) 