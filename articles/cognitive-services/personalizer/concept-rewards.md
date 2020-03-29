---
title: Wynik nagrody - Personalizer
description: Wynik nagrody wskazuje, jak dobrze wybór personalizacji, RewardActionID, spowodowało dla użytkownika. Wartość wyniku nagrody zależy od logiki biznesowej, na podstawie obserwacji zachowania użytkownika. Personalizer szkoli swoje modele uczenia maszynowego, oceniając nagrody.
ms.date: 02/20/2020
ms.topic: conceptual
ms.openlocfilehash: 734e4d0fdcec25884f8535ec61ccd10569fa8890
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219369"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>Wyniki nagród wskazują na sukces personalizacji

Wynik nagrody wskazuje, jak dobrze wybór personalizacji, [RewardActionID](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/rank/rank#response), wynik dla użytkownika. Wartość wyniku nagrody zależy od logiki biznesowej, na podstawie obserwacji zachowania użytkownika.

Personalizer szkoli swoje modele uczenia maszynowego, oceniając nagrody.

Dowiedz [się, jak skonfigurować](how-to-settings.md#configure-rewards-for-the-feedback-loop) domyślny wynik nagrody w witrynie Azure portal dla zasobu Personalizer.

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Użyj interfejsu API nagrody, aby wysłać wynik nagrody do Personalizer

Nagrody są wysyłane do Personalizer przez [API nagrody.](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/events/reward) Zazwyczaj nagrodą jest liczba od 0 do 1. Ujemna nagroda, o wartości -1, jest możliwa w niektórych scenariuszach i powinna być używana tylko wtedy, gdy masz doświadczenie w uczeniu się wzmocnienia (RL). Personalizer trenuje model, aby osiągnąć najwyższą możliwą sumę nagród w czasie.

Nagrody są wysyłane po wystąpieniu zachowania użytkownika, co może nastąpić kilka dni później. Maksymalny czas, przez który personalizator będzie czekał, aż zdarzenie zostanie uznane za nienagrodzone lub domyślna nagroda jest skonfigurowana z [czasem oczekiwania na nagrody](#reward-wait-time) w witrynie Azure portal.

Jeśli wynik nagrody za wydarzenie nie został odebrany w **ramach czasu oczekiwania na nagrody,** zostanie zastosowana nagroda **domyślna.** Zazwyczaj nagroda **[domyślna](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** jest skonfigurowana jako zero.


## <a name="behaviors-and-data-to-consider-for-rewards"></a>Zachowania i dane, które należy wziąć pod uwagę w przypadku nagród

Rozważmy te sygnały i zachowania w kontekście wyniku nagrody:

* Bezpośrednie wprowadzanie sugestii przez użytkownika, gdy dostępne są opcje ("Czy masz na myśli X?").
* Długość sesji.
* Czas między sesjami.
* Analiza tonacji interakcji użytkownika.
* Bezpośrednie pytania i mini ankiety, w których bot prosi użytkownika o opinię na temat użyteczności, dokładności.
* Reagowanie na alerty lub opóźnienie w odpowiedzi na alerty.

## <a name="composing-reward-scores"></a>Komponowanie wyników nagród

Wynik nagrody musi być obliczony w logice biznesowej. Wynik może być reprezentowany jako:

* Pojedynczy numer wysłany raz
* Wynik wysłany natychmiast (np. 0,8) i dodatkowy wynik wysłany później (zazwyczaj 0,2).

## <a name="default-rewards"></a>Nagrody domyślne

Jeśli w czasie oczekiwania na nagrodę nie zostanie odebrana [żadna](#reward-wait-time)nagroda , czas trwania od wezwania rangi, Personalizator w sposób dorozumiany zastosuje **nagrodę domyślną** do tego wydarzenia rangi.

## <a name="building-up-rewards-with-multiple-factors"></a>Budowanie nagród z wieloma czynnikami

Aby uzyskać skuteczną personalizację, możesz zbudować wynik nagrody na podstawie wielu czynników.

Można na przykład zastosować następujące reguły do personalizacji listy treści wideo:

|Zachowanie użytkownika|Częściowa wartość wyniku|
|--|--|
|Użytkownik kliknął górny element.|+0,5 nagrody|
|Użytkownik otworzył rzeczywistą zawartość tego elementu.|+0,3 nagrody|
|Użytkownik oglądał 5 minut treści lub 30%, w zależności od tego, która z tych wartości jest dłuższa.|+0,2 nagrody|
|||

Następnie można wysłać całkowitą nagrodę do interfejsu API.

## <a name="calling-the-reward-api-multiple-times"></a>Wielokrotne wywoływanie interfejsu API nagród

Możesz również wywołać interfejs API nagrody przy użyciu tego samego identyfikatora zdarzenia, wysyłając różne wyniki nagród. Gdy Personalizer otrzymuje te nagrody, określa ostateczną nagrodę dla tego wydarzenia, agregując je zgodnie z konfiguracją Personalizatora.

Wartości agregacji:

*  **Po pierwsze:** Bierze pierwszą nagrodę otrzymaną za wydarzenie, a resztę odrzuca.
* **Suma:** Pobiera wszystkie wyniki nagród zebrane dla eventId i dodaje je razem.

Wszystkie nagrody za wydarzenie, które są odbierane po **czasie oczekiwania na nagrody,** są odrzucane i nie mają wpływu na szkolenie modeli.

Sumując wyniki nagród, twoja ostateczna nagroda może być poza oczekiwanym zakresem punktów. To nie spowoduje awarii usługi.

## <a name="best-practices-for-calculating-reward-score"></a>Najlepsze praktyki dotyczące obliczania wyniku nagrody

* **Rozważ prawdziwe wskaźniki udanej personalizacji**: Łatwo jest myśleć w kategoriach kliknięć, ale dobra nagroda opiera się na tym, co chcesz, aby użytkownicy *osiągnęli* zamiast tego, co chcesz, aby ludzie *robili.*  Na przykład nagradzanie kliknięć może prowadzić do wyboru treści, która jest podatna na kliknięcia.

* **Użyj wynik nagrody za to, jak dobrze działała personalizacja**: Personalizacja sugestii filmu, miejmy nadzieję, doprowadzi do tego, że użytkownik obejrzy film i nadaniu mu wysokiej oceny. Ponieważ ocena filmu prawdopodobnie zależy od wielu rzeczy (jakość aktorstwa, nastrój użytkownika), nie jest to dobry sygnał nagrody za to, jak dobrze *działała personalizacja.* Użytkownik oglądający pierwsze minuty filmu może być jednak lepszym sygnałem skuteczności personalizacji i wysłanie nagrody 1 po 5 minutach będzie lepszym sygnałem.

* **Nagrody dotyczą tylko RewardActionID**: Personalizer stosuje nagrody, aby zrozumieć skuteczność działania określonego w RewardActionID. Jeśli zdecydujesz się wyświetlić inne akcje, a użytkownik kliknie je, nagroda powinna wynosić zero.

* **Rozważ niezamierzone konsekwencje:** Twórz funkcje nagradzania, które prowadzą do odpowiedzialnych wyników dzięki [etyce i odpowiedzialnemu użyciu.](ethics-responsible-use.md)

* **Użyj nagród przyrostowych:** Dodanie częściowych nagród za zachowania mniejszych użytkowników pomaga Personalizerowi osiągnąć lepsze nagrody. Ta przyrostowa nagroda pozwala algorytmowi wiedzieć, że jest coraz bliżej do angażowania użytkownika w ostateczne pożądane zachowanie.
    * Jeśli wyświetlasz listę filmów, jeśli użytkownik najedzie kursorem na pierwszy z nich przez jakiś czas, aby wyświetlić więcej informacji, możesz ustalić, że doszło do pewnego zaangażowania użytkownika. Zachowanie może liczyć z wynikiem nagrody 0,1.
    * Jeśli użytkownik otworzył stronę, a następnie zakończył, wynik nagrody może wynosić 0,2.

## <a name="reward-wait-time"></a>Czas oczekiwania na nagrody

Personalizator skoreluje informacje o zaproszeniu do rangi z nagrodami wysyłanymi w zaproszeniach do nagradzania, aby wyszkolić model. Mogą one nastąpić w różnym czasie. Personalizator czeka przez ograniczony czas, począwszy od wywołania rangi, nawet jeśli wywołanie rangi zostało wykonane jako nieaktywne zdarzenie i aktywowane później.

Jeśli **czas oczekiwania na nagrody** wygaśnie i nie ma żadnych informacji o nagrodzie, do tego wydarzenia zostanie naliczona nagroda domyślna. Maksymalny czas oczekiwania wynosi 6 dni.

## <a name="best-practices-for-reward-wait-time"></a>Najlepsze praktyki dotyczące czasu oczekiwania na nagrody

Postępuj zgodnie z tymi zaleceniami, aby uzyskać lepsze wyniki.

* Spraw, aby czas oczekiwania na nagrodę był tak krótki, jak to tylko możliwe, pozostawiając wystarczająco dużo czasu na uzyskanie opinii użytkowników.

* Nie wybieraj czasu trwania krótszego niż czas potrzebny na uzyskanie opinii. Jeśli na przykład część twoich nagród pojawi się po obejrzeniu filmu przez użytkownika przez 1 minutę, długość eksperymentu powinna być co najmniej dwukrotnie większa.

## <a name="next-steps"></a>Następne kroki

* [Uczenie przez wzmacnianie](concepts-reinforcement-learning.md)
* [Wypróbuj interfejs API rangi](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [Wypróbuj interfejs API nagród](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)
