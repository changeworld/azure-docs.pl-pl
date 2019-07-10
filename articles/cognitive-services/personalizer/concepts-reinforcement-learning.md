---
title: Uczenia przez wzmacnianie - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer używa informacji dotyczących działań i bieżący kontekst sugestii dotyczących lepszej klasyfikacji. Informacje na temat tych akcji i kontekstu są atrybuty lub właściwości, które są określane jako funkcje.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 26f8348bc2de9cb56110c1cc7ce896934bb46ef7
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722457"
---
# <a name="what-is-reinforcement-learning"></a>Co to jest uczenia przez wzmacnianie?

Uczenia przez wzmacnianie to podejście do usługi machine learning, która uczy zachowań, uzyskiwanie opinii od jego użycia.
 
Uczenia przez wzmacnianie polega na:

* Zapewnianie szansy sprzedaży lub stopień swobody wprowadzenie zachowanie - takie decyzje lub opcji.
* Zapewnienie informacje kontekstowe dotyczące środowiska i opcje.
* Przekazywanie opinii o jak dobrze zachowanie osiąga celu.

Dostępnych jest wiele podtypy i stylów uczenia przez wzmacnianie, jest to, jak pojęcie działa w Personalizer:

* Aplikacja udostępnia szansy sprzedaży, aby wyświetlić jeden element zawartości z listy alternatyw.
* Aplikacja zawiera informacje dotyczące każdego rozwiązania alternatywnego i kontekście użytkownika.
* Twoja aplikacja oblicza _nagradzaj wynik_.

W odróżnieniu od niektórych metod do uczenia przez wzmacnianie Personalizer nie wymaga symulacji pracę w. Jego algorytmów uczenia zaprojektowano w celu reagowania na zewnątrz (i sterować nim) i ucz się od każdego punktu danych w zrozumieniu jest unikatową szansę kosztów czasu i pieniędzy na tworzenie i że istnieje żal różna od zera (utraty nagradzania możliwe), jeśli odbywa się nieoptymalne wydajności.

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>Jakiego rodzaju algorytmów uczenia przez wzmacnianie używa Personalizer?

Korzysta z bieżącej wersji Personalizer **kontekstowych bandits**, podejście do wzmocnienia nauka, to znaczy w ramce wokół wprowadzeniem decyzji lub wyborami między akcjami dyskretne, w danym kontekście.

_Decyzji pamięci_, model, który przeszkoleni do przechwytywania najlepsze możliwe decyzji, biorąc pod uwagę w kontekście, korzysta z zestawu modeli liniowych. Te wielokrotnie wykazały wyników biznesowych i są sprawdzone podejście, częściowo ponieważ informacji ze środowisk rzeczywistych bardzo szybko bez konieczności używania wielu — dostęp próbny szkolenia i częściowo mogą uzupełniać nadzorowanego uczenia modeli i neuronowej modele sieci.

Eksploruj/wykorzystać alokacji ruch jest po dokonaniu losowo procent ustawiony dla eksploracji, a domyślny algorytm potrzeby eksploracji jest zachłanne epsilon.

### <a name="history-of-contextual-bandits"></a>Historia Bandits kontekstowych

John Langford ukuł nazwa kontekstowych Bandits (Langford i Zhang [2007]) do opisywania podzbiór tractable uczenia przez wzmacnianie i pracował nad half-dozen dokumentów, poprawy naszych wiedzą, jak i ucz się w tym modelu:

* Beygelzimer et al. [2011
* Dudík et al. [2011a,b]
* Agarwal et al. [2014, 2012]
* Beygelzimer i Langford [2009 r]
* Li et al. [2010]

John jest również podana kilka samouczków wcześniej omawiającymi zagadnienia, takie jak wspólnego prognozowania (ICML 2015), kontekstowe teorii Bandit (NIPS 2013), aktywne uczenie (ICML 2009) i przykładowe złożoności granice (ICML 2003)

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>Jakie struktury uczenia maszynowego używa Personalizer?

Aktualnie używa personalizer [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki) jako podstawa dla usługi machine learning. Ta platforma pozwala na maksymalną przepływność i najniższe opóźnienia podczas wprowadzania personalizacji szereguje i uczenia modelu ze wszystkimi zdarzeniami.

## <a name="references"></a>Odwołania

* [Podejmowanie decyzji kontekstowych o niskim długu technicznego](https://arxiv.org/abs/1606.03966)
* [Obniżki sposobem uczciwe klasyfikacji](https://arxiv.org/abs/1803.02453)
* [Wydajne Bandits kontekstowe w stacjonarnych innych cech](https://arxiv.org/abs/1708.01799)
* [Pozostałe utraty prognozowania: Wzmocnienie: uczenie z nie przyrostowe opinii](https://openreview.net/pdf?id=HJNMYceCW)
* [Mapowanie instrukcje i obserwacji do działania za pomocą uczenia przez wzmacnianie](https://arxiv.org/abs/1704.08795)
* [Nauka wyszukiwania lepiej niż usługi dla nauczycieli](https://arxiv.org/abs/1502.02206)

## <a name="next-steps"></a>Kolejne kroki

[Ocena w trybie offline](concepts-offline-evaluation.md) 