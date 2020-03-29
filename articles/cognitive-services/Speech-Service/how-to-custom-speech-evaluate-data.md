---
title: Ocena dokładności dla mowy niestandardowej — usługa mowy
titleSuffix: Azure Cognitive Services
description: W tym dokumencie dowiesz się, jak ilościowo mierzyć jakość naszego modelu zamiany mowy na tekst lub modelu niestandardowego. Audio + dane transkrypcji oznaczone przez człowieka jest wymagane do testowania dokładności i 30 minut do 5 godzin reprezentatywnego dźwięku powinny być dostarczone.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: f710b8bfdd4dcfd3b7a63aa0b457036ab7037016
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806100"
---
# <a name="evaluate-custom-speech-accuracy"></a>Ocena niestandardowej dokładności mowy

W tym dokumencie dowiesz się, jak ilościowo mierzyć jakość modelu mowy na tekst firmy Microsoft lub modelu niestandardowego. Audio + dane transkrypcji oznaczone przez człowieka jest wymagane do testowania dokładności i 30 minut do 5 godzin reprezentatywnego dźwięku powinny być dostarczone.

## <a name="what-is-word-error-rate-wer"></a>Co to jest poziom błędu programu Word (WER)?

Standardem branżowym do pomiaru dokładności modelu jest *poziom błędu programu Word* (WER). WER zlicza liczbę niepoprawnych słów zidentyfikowanych podczas rozpoznawania, a następnie dzieli przez całkowitą liczbę słów podanych w transkrypcji oznaczonej przez człowieka. Na koniec liczba ta jest mnożona przez 100% do obliczenia WER.

![Formuła WER](./media/custom-speech/custom-speech-wer-formula.png)

Nieprawidłowo zidentyfikowane słowa dzielą się na trzy kategorie:

* Wstawienie (I): Słowa, które są nieprawidłowo dodane do transkrypcji hipotezy
* Usunięcie (D): wyrazy, które nie zostały skryte w transkrypcji hipotezy
* Substytucja (S): Wyrazy, które zostały zastąpione między odniesieniem a hipotezą

Oto przykład:

![Przykład błędnie zidentyfikowanych słów](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Rozwiązywanie problemów i ulepszanie programu WER

Za pomocą narzędzia WER z wyników rozpoznawania maszyn można ocenić jakość modelu używanego za pomocą aplikacji, narzędzia lub produktu. WER 5%-10% jest uważany za dobrej jakości i jest gotowy do użycia. WER 20% jest dopuszczalne, jednak warto rozważyć dodatkowe szkolenia. WER 30% lub więcej sygnalizuje niską jakość i wymaga dostosowania i szkolenia.

Sposób dystrybucji błędów jest ważne. Gdy wystąpi wiele błędów usuwania, zwykle z powodu słabej siły sygnału audio. Aby rozwiązać ten problem, musisz zebrać dane audio bliżej źródła. Błędy wstawiania oznaczają, że dźwięk został nagrany w hałaśliwym otoczeniu i może występować przesłuch, co powoduje problemy z rozpoznawaniem. Błędy podstawiania są często napotykane, gdy niewystarczająca próbka terminów specyficznych dla domeny została dostarczona jako transkrypcje oznaczone przez człowieka lub powiązany tekst.

Analizując poszczególne pliki, można określić, jaki typ błędów istnieje i które błędy są unikatowe dla określonego pliku. Zrozumienie problemów na poziomie pliku pomoże Ci udoskonalić cel.

## <a name="create-a-test"></a>Tworzenie testu

Jeśli chcesz przetestować jakość modelu linii bazowej mowy do tekstu firmy Microsoft lub modelu niestandardowego, który został przeszkolony, możesz porównać dwa modele obok siebie, aby ocenić dokładność. Porównanie obejmuje WER i wyniki rozpoznawania. Zazwyczaj model niestandardowy jest porównywany z modelem bazowym firmy Microsoft.

Aby ocenić modele obok siebie:

1. Zaloguj się do [portalu mowy niestandardowej](https://speech.microsoft.com/customspeech).
2. Przejdź do **> testowania mowy niestandardowej >** mowy mowy mowy .
3. Kliknij **pozycję Dodaj test**.
4. Wybierz **oceń dokładność**. Nadaj testowi nazwę, opis i wybierz zestaw danych transkrypcyjnych audio + oznaczonych przez człowieka.
5. Wybierz maksymalnie dwa modele, które chcesz przetestować.
6. Kliknij przycisk **Utwórz**.

Po pomyślnym utworzeniu testu można porównać wyniki obok siebie.

## <a name="side-by-side-comparison"></a>Porównanie obok siebie

Po zakończeniu testu, wskazanym przez zmianę stanu na *Zakończone pomyślnie,* znajdziesz numer WER dla obu modeli uwzględnionych w teście. Kliknij nazwę testu, aby wyświetlić stronę szczegółów testowania. Ta strona szczegółów zawiera listę wszystkich wypowiedzi w zestawie danych, wskazując wyniki rozpoznawania dwóch modeli wraz z transkrypcji z przesłanego zestawu danych. Aby sprawdzić porównanie side-by-side, można przełączać różne typy błędów, w tym wstawianie, usuwanie i podstawianie. Słuchając dźwięku i porównując wyniki rozpoznawania w każdej kolumnie, która pokazuje transkrypcję z etykietą człowieka i wyniki dla dwóch modeli zamiany mowy na tekst, możesz zdecydować, który model spełnia Twoje potrzeby i gdzie są dodatkowe szkolenia i ulepszenia Wymagane.

## <a name="next-steps"></a>Następne kroki

* [Trenowanie modelu](how-to-custom-speech-train-model.md)
* [Wdrażanie modelu](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Przygotowanie i przetestowanie danych](how-to-custom-speech-test-data.md)
* [Sprawdź swoje dane](how-to-custom-speech-inspect-data.md)
