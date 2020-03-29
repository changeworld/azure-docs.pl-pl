---
title: Jak działa personalizator - Personalizer
description: _Pętla_ Personalizer używa uczenia maszynowego do tworzenia modelu, który przewiduje najlepsze działanie dla zawartości. Model jest szkolony wyłącznie na podstawie danych, które wysłałeś do niego za pomocą połączeń rangi i nagrody.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 836c207213ac52a60e27da6fc957418187059023
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623746"
---
# <a name="how-personalizer-works"></a>Jak działa usługa Personalizacja

Zasób Personalizer, _pętla uczenia się,_ używa uczenia maszynowego do tworzenia modelu, który przewiduje najlepsze działanie dla zawartości. Model jest szkolony wyłącznie na podstawie danych, które wysłałeś do niego za pomocą połączeń **rangi** i **nagrody.** Każda pętla jest całkowicie niezależna od siebie.

## <a name="rank-and-reward-apis-impact-the-model"></a>Interfejsy API rangi i nagrody mają wpływ na model

Akcje wysyłane _z funkcjami_ i _funkcjami kontekstu_ do interfejsu API rangi. Interfejs API **rangi** decyduje się na użycie:

* _Wykorzystanie:_ Bieżący model, aby zdecydować najlepsze działanie na podstawie danych z przeszłości.
* _Eksploruj_: Wybierz inną akcję zamiast najpopularniejszej akcji. Ten procent dla zasobu Personalizer [można skonfigurować](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt) w witrynie Azure portal.

Określasz wynik nagrody i wysyłasz ten wynik do interfejsu API nagrody. Interfejs API **nagrody:**

* Zbiera dane, aby trenować model, rejestrując funkcje i wyniki nagród każdego wywołania rangi.
* Używa tych danych do aktualizacji modelu na podstawie konfiguracji określonej w _zasadach uczenia się_.

## <a name="your-system-calling-personalizer"></a>Twój system wywołujący Personalizer

Na poniższej ilustracji przedstawiono przepływ architektury wywoływania wywołań rangi i nagrody:

![tekst alternatywny](./media/how-personalizer-works/personalization-how-it-works.png "Jak działa personalizacja")

1. Akcje wysyłane _z funkcjami_ i _funkcjami kontekstu_ do interfejsu API rangi.

    * Personalizator decyduje, czy wykorzystać bieżący model lub eksplorować nowe opcje dla modelu.
    * Wynik rankingu jest wysyłany do eventhub.
1. Najwyższa ranga jest zwracana do systemu jako _identyfikator akcji nagrody._
    System przedstawia tę zawartość i określa wynik nagrody na podstawie własnych reguł biznesowych.
1. System zwraca wynik nagrody do pętli uczenia się.
    * Gdy Personalizer otrzyma nagrodę, nagroda jest wysyłana do EventHub.
    * Ranga i nagroda są skorelowane.
    * Model AI jest aktualizowany na podstawie wyników korelacji.
    * Aparat wnioskowania jest aktualizowany o nowy model.

## <a name="personalizer-retrains-your-model"></a>Personalizator przekwalifikuje twój model

Personalizator przeszkolił model na podstawie ustawienia **aktualizacji częstotliwości modelu** w zasobie Personalizer w witrynie Azure portal.

Personalizator używa wszystkich aktualnie przechowywanech danych na podstawie ustawienia **przechowywania danych** w liczbie dni na zasobie Personalizer w witrynie Azure portal.

## <a name="research-behind-personalizer"></a>Badania za Personalizer

Personalizer opiera się na najnowocześniejszych naukach i badaniach w dziedzinie [uczenia się wzmacniającego,](concepts-reinforcement-learning.md) w tym na pracach, działaniach badawczych i bieżących obszarach badań w programie Microsoft Research.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [najlepszych scenariuszach](where-can-you-use-personalizer.md) personalizatora