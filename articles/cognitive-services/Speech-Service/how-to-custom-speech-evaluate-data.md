---
title: Oceny dokładności rozpoznawania mowy niestandardowe — usługi mowy
titlesuffix: Azure Cognitive Services
description: W tym dokumencie dowiesz się, jak ilościowo mierzą jakość modelu mowy na tekst firmy Microsoft lub Twojego niestandardowego modelu. Wymaga aby przetestować dokładność danych transkrypcji audio i oznaczone przez człowieka i powinien zostać podany 30 minut do 5 godzin reprezentatywny audio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 7c1b3602b09c1a129923180c4b1d4a5f8293de2c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65025911"
---
# <a name="evaluate-custom-speech-accuracy"></a>Dokładność mowy niestandardowe obliczyć

W tym dokumencie dowiesz się, jak ilościowo mierzą jakość modelu mowy na tekst firmy Microsoft lub Twojego niestandardowego modelu. Wymaga aby przetestować dokładność danych transkrypcji audio i oznaczone przez człowieka i powinien zostać podany 30 minut do 5 godzin reprezentatywny audio.

## <a name="what-is-word-error-rate-wer"></a>Co to jest współczynnik błędów programu Word (WER, Windows Management Instrumentation)?

To branżowy standard do mierzenia dokładności modelu *współczynnik błędów Word* (WER). Raportowanie błędów systemu Windows zlicza niepoprawne słowa, które zostały zidentyfikowane podczas rozpoznawania, a następnie dzieli przez całkowitą liczbę słów w transkrypcji oznaczone przez człowieka. Na koniec ta liczba jest mnożony przez 100%, aby obliczyć raportowanie błędów systemu Windows.

![Formuła raportowanie błędów systemu Windows](./media/custom-speech/custom-speech-wer-formula.png)

Niepoprawnie identyfikuje fall słów na trzy kategorie:

* Wstawianie (I): Wyrazy, które niepoprawnie zostały dodane w tej transkrypcji hipotezę
* Usunięcie (D): Wyrazy, które są wykryte w transkrypcji hipotezę
* Podstawienie (S): Wyrazy, które zostały zastąpione między odwołania i hipotezę

Oto przykład:

![Przykład niepoprawnie określonych słów](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Usuń błędy i zwiększyć raportowanie błędów systemu Windows

Raportowanie błędów systemu Windows z wyników rozpoznawanie maszyny można użyć do oszacowania jakości modelu, używane z aplikacjami, narzędzia lub produktu. Raportowanie błędów systemu Windows z 5-10% jest uważany za dobrej jakości i jest gotowa do użycia. Raportowanie błędów systemu Windows o 20% jest dopuszczalne, jednak warto wziąć pod uwagę dodatkowe szkolenie. Raportowanie błędów systemu Windows z co najmniej 30% sygnalizuje słabą jakością i wymaga dostosowania i szkolenia.

Ważne jest rozkład błędów. Gdy wystąpi wiele błędów usuwania jest zazwyczaj ze względu na słabe sygnał audio. Aby rozwiązać ten problem, należy zebrać dane audio bliżej do źródła. Błędy wstawiania oznaczać, że audio została zarejestrowana w środowisku generujące dużo alertów, a rozmowy w tle mogą być obecne, powodujące problemy z rozpoznawania. Niewystarczające przykładowe terminów specyficznych dla domeny został podany jako transkrypcje oznaczone przez człowieka lub powiązane często zostaną napotkane błędy podstawianie tekstu.

Analizując poszczególne pliki, można określić, jakiego rodzaju błędy istnieje, a błędy, które są unikatowe dla określonego pliku. Pomoże Ci zrozumieć problemy na poziomie plików docelowych ulepszenia.

## <a name="create-a-test"></a>Tworzenie testu

Jeśli chcesz przetestować jakość modelu odniesienia mowy na tekst firmy Microsoft lub który przeprowadzono już uczenie modelu niestandardowego, można porównać dwa modele obok siebie do oceny dokładności. Porównanie obejmuje raportowanie błędów systemu Windows i rozpoznawania wyników. Zazwyczaj modelu niestandardowego jest porównywany z modelem linii bazowej firmy Microsoft.

Można oceniać modele obok siebie:

1. Przejdź do **mowy na tekst > Custom Speech > testowania**.
2. Kliknij przycisk **Dodaj Test**.
3. Wybierz **oceny dokładności**. Nadaj nazwę, opis, test i wybierz zestaw danych transkrypcji audio i oznaczone przez człowieka.
4. Wybierz maksymalnie dwa modele, które chcesz przetestować.
5. Kliknij pozycję **Utwórz**.

Po pomyślnym utworzeniu testu można porównać wyniki obok siebie.

## <a name="side-by-side-comparison"></a>Porównania Side-by-side

Po zakończeniu testu wskazywanym przez zmianę stanu, aby *Powodzenie*, numer raportowanie błędów systemu Windows znajdują się oba modele zawarte w teście. Kliknij nazwę testu, aby wyświetlić stronę szczegółów testowania. Ta strona szczegółów zawiera listę wszystkich wypowiedzi w zestawie danych, informujący o wyniki rozpoznawania dwa modele obok transkrypcji z przesłano zestawu danych. Aby sprawdzić porównania side-by-side, można przełączać się różne typy błędów, tym wstawiania, usuwania i podstawienia. Nasłuchiwanie audio i porównywania wyników rozpoznawania w każdej kolumnie, który pokazuje transkrypcji oznaczone przez człowieka i wyników dwa modele mowy na tekst, możesz zdecydować, model, który spełnia Twoje wymagania, i gdzie są dodatkowe szkolenie i ulepszenia Wymagane.

## <a name="next-steps"></a>Kolejne kroki

* [Uczenie modelu](how-to-custom-speech-train-model.md)
* [Wdrażanie modelu](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Przygotuj i przetestować danych](how-to-custom-speech-test-data.md)
* [Sprawdzanie danych](how-to-custom-speech-inspect-data.md)
