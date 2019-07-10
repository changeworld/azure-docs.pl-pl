---
title: Wynik za wynagrodzeniem — Personalizer
titleSuffix: Azure Cognitive Services
description: Wynik za wynagrodzeniem wskazuje, jak dobrze personalizacji wybór, RewardActionID, w wyniku dla użytkownika. Wartość wyniku nagradzania jest określana przez logikę biznesową, na podstawie obserwacji zachowania użytkowników. Personalizer szkolenie modeli uczenia modeli poprzez ocenę korzyści maszynowego.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: edjez
ms.openlocfilehash: 39db28cd7e11d77362a2aefcf4ad8d2748db59c2
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722509"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>Nagrody wyniki wskazują sukces personalizacji

Wynik za wynagrodzeniem wskazuje, jak dobrze wybór personalizacji [RewardActionID](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/rank/rank#response), wynikowa dla użytkownika. Wartość wyniku nagradzania jest określana przez logikę biznesową, na podstawie obserwacji zachowania użytkowników.

Personalizer szkolenie modeli uczenia modeli poprzez ocenę korzyści maszynowego. 

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Wysyłać nagradzania wynikiem Personalizer za pomocą interfejsu API nagrody

Korzyści są wysyłane do Personalizer przez [interfejsu API za wynagrodzeniem](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/events/reward). Wynagrodzenia to liczba z przedziału od -1 do 1. Personalizer przygotowuje modelu, aby osiągnąć najwyższą sumę rewards wraz z upływem czasu.

Korzyści są wysyłane po zachowania użytkowników miało miejsce, co może być dni później. Maksymalna ilość czasu Personalizer zaczeka na zdarzenie została uznana za nie za wynagrodzeniem lub nagradzania domyślne jest skonfigurowany z [czas oczekiwania nagradzania](#reward-wait-time) w witrynie Azure portal.

Jeśli wynik za wynagrodzeniem zdarzenia nie otrzymał w ciągu **czas oczekiwania nagradzania**, a następnie **domyślne nagradzania** zostaną zastosowane. Zazwyczaj **[domyślne nagradzania](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** jest skonfigurowany jako zero.


## <a name="behaviors-and-data-to-consider-for-rewards"></a>Zachowania i danych należy wziąć pod uwagę, aby uzyskać korzyści

Należy wziąć pod uwagę te sygnały i zachowania kontekstu wynik za wynagrodzeniem:

* Bezpośrednie dane wejściowe użytkownika w przypadku sugestii, w przypadku opcji ("Czy chodziło Ci o X?").
* Długość sesji.
* Czas między sesjami.
* Analiza tonacji interakcji użytkownika.
* Bezpośrednie pytania i mini ankiet, gdzie bot prosi użytkownika o opinię o przydatności, dokładności.
* Odpowiedzi na alerty lub opóźnienie do odpowiedzi na alerty.

## <a name="composing-reward-scores"></a>Tworzenie oceny nagrody

Wynik za wynagrodzeniem musi być obliczone w logice biznesowej. Wynik może być reprezentowana jako:

* Pojedyncza liczba wysłanych raz 
* Wynik wysłana natychmiast (na przykład 0,8) i score dodatkowe wysyłane później (zazwyczaj 0,2).

## <a name="default-rewards"></a>Domyślne nagrody

Jeśli nie wynagrodzenie została odebrana w [czas oczekiwania nagradzania](#reward-wait-time), czas trwania od ranga wywołania, stosuje niejawnie Personalizer **domyślne nagradzania** na to zdarzenie rangi.

## <a name="building-up-rewards-with-multiple-factors"></a>Budowanie rewards o wiele czynników  

Na potrzeby personalizacji skuteczne może tworząc wynik za wynagrodzeniem (dowolną liczbę od -1 do 1) na podstawie wielu czynników. 

Na przykład można stosować te zasady do personalizacji listę zawartości wideo:

|Zachowania użytkowników|Częściowe ocena wartości|
|--|--|
|Użytkownik kliknął na pierwszy element.|+0.5 nagrody|
|Użytkownik otworzy rzeczywista zawartość tego elementu.|+0.3 nagrody|
|Użytkownik 5 minut zawartości lub 30%, którzy oglądali transmisje stacji, która kwota jest większa.|+0.2 nagrody|
|||

Następnie możesz wysłać łączna liczba osób trzecich do interfejsu API.

## <a name="calling-the-reward-api-multiple-times"></a>Wywoływanie interfejsu API programu nagradzania wiele razy

Można również wywołać interfejs API nagrody, przy użyciu tego samego Identyfikatora zdarzeń, wysyłanie nagradzania różne wyniki. Gdy Personalizer pobiera te korzyści, określa ostateczny nagrody dla tego zdarzenia przez agregowanie ich, jak określono w ustawieniach Personalizer.

Ustawienia agregacji:

*  **Pierwszy**: Pobiera pierwszą ocenę za wynagrodzeniem odbierane w wydarzeniu i odrzuca wszystkie pozostałe.
* **Suma**: Pobiera wszystkie wyniki nagradzania zbierane na potrzeby eventId i dodaje je ze sobą.

Wszystkie korzyści dla zdarzenia, które są odbierane po **czas oczekiwania nagradzania**, są odrzucane, a nie wpływają na szkolenie modeli.

Sumując wyniki nagradzania użytkownika końcowego za wynagrodzeniem może być wyższy niż 1 lub mniejsza niż -1. Nie będzie to, że usługa się nie powieść.

<!--
@edjez - is the number ignored if it is outside the acceptable range?
-->

## <a name="best-practices-for-calculating-reward-score"></a>Najlepsze rozwiązania dotyczące nagradzania wynikiem obliczania

* **Należy wziąć pod uwagę true wskaźniki pomyślnych personalizacji**: Łatwo myślą w kategoriach kliknięć, ale dobrym za wynagrodzeniem opiera się na co chcesz użytkownikom *osiągnąć* zamiast co ma być *czy*.  Na przykład nagradzanie na kliknięcia może prowadzić do wybierając zawartość, która jest podatny na clickbait.

* **Użyj wynagrodzenia wynik jak dobre personalizacji przepracowanych**: Personalizowanie sugestię filmu miejmy nadzieję spowodowałaby użytkownika oglądania filmu, a następnie podając go wysoką oceną. Ponieważ Klasyfikacja filmu prawdopodobnie jest zależna od wiele elementów (jakość stanowiącej nastrój użytkownika), nie jest sygnał dobrych nagradzania jak dobrze *personalizacji* zadziałała. Użytkownika, oglądając pierwszych kilku minut filmu, jednak może być sygnał lepszej efektywności personalizacji i wysyłanie nagradzania 1, po 5 minut będzie lepiej sygnału.

* **Korzyści dotyczą tylko RewardActionID**: Personalizer dotyczy nagrody, aby zrozumieć skuteczności z akcją określoną w RewardActionID. Jeśli chcesz wyświetlić inne akcje i użytkownik klika polecenie na nich nagrody powinna wynosić zero.

* **Należy wziąć pod uwagę niezamierzone konsekwencje**: Tworzenie funkcji nagradzania, które prowadzą do wyników odpowiedzialny za pomocą [etyki i użyj odpowiada](ethics-responsible-use.md).

* **Użyj przyrostowe Rewards**: Dodawanie częściowe korzyści dla mniejszych zachowania użytkowników ułatwia Personalizer osiągania lepszych nagrody. To przyrostowe nagradzania umożliwia algorytm wiedzieć, że stają się coraz bliższe wykonują użytkownika końcowego żądane zachowanie.
    * Lista filmy, są wyświetlane, gdy użytkownik zatrzyma na pierwszy z nich od pewnego czasu uzyskać więcej informacji, można określić, czy wystąpiły pewne zwiększania zaangażowania użytkowników. To zachowanie może być traktowane z wynikiem nagradzania 0,1. 
    * Jeśli użytkownik otworzyć stronę, a następnie został zakończony, wynik za wynagrodzeniem może być wymagane 0,2. 

## <a name="reward-wait-time"></a>Czas oczekiwania nagrody

Personalizer zostaną skorelowane informacje rangę wywołanie z nagrody wysyłane w wywołaniach nagradzania do nauczenia modelu. Mogą one pochodzić w różnym czasie. Personalizer czeka przez ograniczony czas, rozpoczynając od rangi wywołanie się stało, nawet jeśli rangi wywołanie zostało wprowadzone jako nieaktywne zdarzenie i aktywowany później.

Jeśli **czas oczekiwania nagradzania** wygaśnie i nie było żadnych informacji nagrody, nagradzania domyślna jest stosowana do tego zdarzenia do trenowania. Maksymalna czasu trwania wynosi 6 dni.

## <a name="best-practices-for-setting-reward-wait-time"></a>Najlepsze rozwiązania dotyczące ustawienie nagradzania czas oczekiwania

Wykonaj te zalecenia pozwalają uzyskać lepsze wyniki.

* Należy nagradzania czas oczekiwania możliwie krótki, jak to możliwe, przy równoczesnym zachowaniu wystarczająco dużo czasu, aby uzyskiwać opinie użytkowników. 

<!--@Edjez - storage quota? -->

* Nie należy wybierać czas trwania, który jest krótszy niż czas potrzebny na uzyskiwanie informacji zwrotnych. Na przykład, jeśli część nagrody przychodzą po użytkownik ma obserwowane 1 minutę film wideo, eksperymentu długość powinna być co najmniej dwukrotnie.

## <a name="next-steps"></a>Następne kroki

* [Uczenia przez wzmacnianie](concepts-reinforcement-learning.md) 
* [Spróbuj ranga interfejsu API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [Spróbuj za wynagrodzeniem interfejsu API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)
