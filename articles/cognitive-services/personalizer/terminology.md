---
title: Terminologia - Personalizer
description: Personalizer wykorzystuje terminologię z uczenia się wzmacniającego. Terminy te są używane w witrynie Azure portal i interfejsów API.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: f75437c5afd5d3fd7f7570079be410d3db1ca8db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624276"
---
# <a name="terminology"></a>Terminologia

Personalizer wykorzystuje terminologię z uczenia się wzmacniającego. Terminy te są używane w witrynie Azure portal i interfejsów API.

## <a name="conceptual-terminology"></a>Terminologia konceptualna

* **Pętla uczenia się:** Tworzenie zasobu Personalizer, o nazwie _pętli uczenia się,_ dla każdej części aplikacji, które mogą korzystać z personalizacji. Jeśli masz więcej niż jedno doświadczenie do personalizacji, utwórz pętlę dla każdego.

* **Model:** Model Personalizer przechwytuje wszystkie dane informacje o zachowaniu użytkownika, uzyskiwanie danych szkoleniowych z kombinacji argumentów wysyłanych do wywołań rangi i nagrody oraz z zachowaniem szkoleniowym określonym przez zasady uczenia się.

## <a name="personalizer-configuration"></a>Konfiguracja personalizatora

Personalizer jest skonfigurowany z [witryny Azure portal](https://portal.azure.com).

* **Nagrody:** skonfiguruj domyślne wartości dla czasu oczekiwania na nagrody, domyślnej zasady agregacji nagród i nagród.

* **Eksploracja**: konfigurowanie procentu wywołań rangi, które mają być używane do eksploracji

* **Częstotliwość aktualizacji modelu:** częstotliwość ponownego szkolenia modelu.

* **Przechowywanie danych**: Ile dni warto przechowywać dane. Może to mieć wpływ na oceny w trybie offline, które są używane do poprawy pętli uczenia się.

## <a name="use-rank-and-reward-apis"></a>Korzystanie z interfejsów API rangi i nagród

* **Ranga:** Biorąc pod uwagę akcje z funkcjami i funkcjami kontekstu, użyj eksploruj lub wykorzystaj, aby zwrócić główną akcję (element zawartości).

    * **Akcje:** Akcje to elementy zawartości, takie jak produkty lub promocje, do wyboru. Personalizer wybiera najwyższą akcję (zwrócony identyfikator akcji nagrody), aby pokazać je użytkownikom za pośrednictwem interfejsu API rangi.

    * **Kontekst**: Aby zapewnić dokładniejszą rangę, podaj informacje o kontekście, na przykład:
        * Twój użytkownik.
        * Urządzenie, na które się znajdują.
        * Bieżący czas.
        * Inne dane na temat bieżącej sytuacji.
        * Dane historyczne dotyczące użytkownika lub kontekstu.

        Określona aplikacja może mieć różne informacje kontekstowe.

    * **[Funkcje](concepts-features.md)**: Jednostka informacji o elemencie zawartości lub kontekście użytkownika. Upewnij się, że używasz tylko funkcji, które są agregowane. Nie używaj określonych czasów, identyfikatorów użytkowników ani innych danych nieagregowanych jako funkcji.

        * _Funkcja akcji_ to metadane dotyczące zawartości.
        * _Funkcja kontekstu_ to metadane dotyczące kontekstu, w którym prezentowana jest zawartość.

* **Eksploracja:** Usługa Personalizer eksploruje, gdy zamiast zwracać najlepszą akcję, wybiera inną akcję dla użytkownika. Usługa Personalizer pozwala uniknąć dryfowania, stagnacji i może dostosować się do bieżącego zachowania użytkownika, eksplorując.

* **Wykorzystanie:** Usługa Personalizer używa bieżącego modelu, aby zdecydować o najlepszym działaniu na podstawie przeszłych danych.

* **Czas trwania eksperymentu:** czas oczekiwania usługi Personalizatora na nagrodę, począwszy od momentu wywołania rangi dla tego wydarzenia.

* **Zdarzenia nieaktywne:** Zdarzenie nieaktywne to zdarzenie, w którym nazywasz się Ranga, ale nie masz pewności, że użytkownik kiedykolwiek zobaczy wynik z powodu decyzji aplikacji klienckiej. Nieaktywne zdarzenia umożliwiają tworzenie i przechowywanie wyników personalizacji, a następnie zdecydować się na ich późniejsze odrzucenie bez wpływu na model uczenia maszynowego.


* **Nagroda:** Miara tego, jak użytkownik zareagował na identyfikator akcji zwróconej nagrody interfejsu API rangi, jako wynik od 0 do 1. Wartość od 0 do 1 jest ustawiana przez logikę biznesową, w oparciu o to, w jaki sposób wybór pomógł osiągnąć cele biznesowe personalizacji. Pętla uczenia się nie przechowuje tej nagrody jako historii poszczególnych użytkowników.

## <a name="offline-evaluations"></a>Oceny w trybie offline

* **Ocena:** Ocena w trybie offline określa najlepsze zasady uczenia się dla pętli na podstawie danych pętli.

* **Zasady uczenia się:** Jak Personalizer trenuje model na każdym zdarzeniu będzie określany przez niektóre parametry, które wpływają na działanie algorytmu uczenia maszynowego. Nowa pętla uczenia się rozpoczyna się od domyślnej **zasady uczenia się,** która może przynieść umiarkowaną wydajność. Podczas [uruchamiania oceny,](concepts-offline-evaluation.md)Personalizer tworzy nowe zasady uczenia się specjalnie zoptymalizowane do przypadków użycia pętli. Personalizer będzie działać znacznie lepiej z zasad zoptymalizowanych dla każdej określonej pętli, generowane podczas oceny. Zasady uczenia się nosi nazwę _ustawienia uczenia się_ w **ustawieniach modelu i uczenia się** dla zasobu Personalizer w witrynie Azure portal.