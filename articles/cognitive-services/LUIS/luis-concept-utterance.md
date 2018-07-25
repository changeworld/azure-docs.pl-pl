---
title: Wypowiedzi w aplikacjach usługi LUIS na platformie Azure | Dokumentacja firmy Microsoft
description: Dodawanie wypowiedzi w aplikacjach Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/13/2018
ms.author: diberry
ms.openlocfilehash: 6f962d0aaf631051c841be29d2854a89bf58ac25
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224419"
---
# <a name="utterances-in-luis"></a>Wypowiedzi w usługi LUIS

**Wypowiedzi** są dane wejściowe użytkownika, że Twoja aplikacja wymaga, aby zinterpretować. Szkolenie usługi LUIS do wyodrębnienia z nich intencje i podmioty, jest przechwytywania szereg różnych danych wejściowych dla poszczególnych intencji. Aktywna nauka lub proces szkolenie dotyczące nowych wypowiedzi w dalszym ciągu jest niezbędne do analizy maszyny do opanowania, udostępniająca usługi LUIS.

Zbieraj fraz, które uważasz, że użytkownicy będą mogli wprowadzać. Dołącz wypowiedzi, które oznaczają to samo, ale są konstruowane inaczej długość słowa i rozmieszczenie programu word. 

## <a name="how-to-choose-varied-utterances"></a>Jak wybrać zróżnicowane wypowiedzi
Gdy użytkownik najpierw początek [Dodawanie wypowiedzi przykład](luis-how-to-add-example-utterances.md) do modelu usługi LUIS, poniżej przedstawiono niektóre zasady, które należy uwzględnić.

### <a name="utterances-arent-always-well-formed"></a>Wypowiedzi nie są zawsze poprawnie sformułowany.
Może być dowolne zdanie, takich jak "W Zarezerwuj mnie biletu do Paryża" lub fragment zdania, takich jak "Rezerwacji" lub "Paryż lot."  Użytkownicy często wykonują błędy pisowni. Podczas planowania aplikacji należy wziąć pod uwagę informację określającą, czy możesz sprawdzanie pisowni dla danych wejściowych użytkownika przed przekazaniem go do usługi LUIS. [Interfejsu API sprawdzania pisowni Bing] [ BingSpellCheck] integruje się z użyciem usługi LUIS. Aplikacją usługi LUIS można skojarzyć z klucza zewnętrznego do API sprawdzania pisowni Bing podczas publikowania go. Jeśli wypowiedzi użytkownika wyboru nie jest pisowni, możesz później LUIS, na wypowiedzi obejmujących literówki i błędy pisowni.

### <a name="use-the-representative-language-of-the-user"></a>Użyj reprezentatywny języka użytkownika
Podczas wybierania wypowiedzi, należy pamiętać, co myślisz, jest wspólny termin lub frazę, może nie być użytkownikowi typowej aplikacji klienckiej. Nie może mieć środowisko domeny. Dlatego należy zachować ostrożność, korzystając z terminów ani fraz, czy użytkownik będzie tylko powiedzieć, jeśli znajdowały się ekspertem.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Wybierz zależeć od terminologii, a także właściwej
Znajdziesz, nawet jeśli wprowadzisz działań mających na celu tworzenie zdania zależeć od wzorców, będą nadal powtórzenie niektóre słownika.

Wykonaj te wypowiedzi przykładu:
```
how do I get a computer?
Where do I get a computer?
I want to get a computer, how do I go about it?
When can I have a computer? 
```
W tym miejscu termin core nie zróżnicowane "computer". Można mówią, komputerów stacjonarnych, laptopów, stacji roboczej lub nawet po prostu maszyny. Usługa LUIS inteligentnie wnioskuje synonimy z kontekstu, ale podczas tworzenia wypowiedzi szkolenia jest nadal lepiej różnią się w ich.

## <a name="example-utterances-in-each-intent"></a>Przykład wypowiedzi w każdej intencji
Każdy intencji trzeba wypowiedzi przykładu, co najmniej 10 do 15. Jeśli cel, który nie ma wypowiedzi przykład, nie będzie to w opracowywaniu usługi LUIS. W przypadku intencji z jednego lub kilku bardzo wypowiedzi przykład LUIS nie będą dokładnie przewidzieć intencji. 

## <a name="add-small-groups-of-10-15-utterances-for-each-authoring-iteration"></a>Dodaj mniejszym grupom wypowiedzi 10 – 15 dla każdej iteracji tworzenia pakietów administracyjnych
W każdej iteracji modelu nie należy dodawać dużej ilości wypowiedzi. Dodawanie wypowiedzi w ilościach dziesiątki. [Szkolenie](luis-how-to-train.md), [publikowania](luis-how-to-publish-app.md), i [test](luis-interactive-test.md) ponownie.  

Usługa LUIS opiera się skuteczne modeli z wypowiedzi wybranych dokładnie. Dodawanie wypowiedzi zbyt wiele nie jest przydatne ponieważ wprowadza ona pomyłek.  

Zaleca się rozpoczynać kilka wypowiedzi następnie [Przejrzyj wypowiedzi punktu końcowego](luis-how-to-review-endoint-utt.md) poprawne intencji ekstrakcji prognoz i jednostek.

## <a name="ignoring-words-and-punctuation"></a>Ignorowanie słów i znaki interpunkcyjne
Ignorowanie konkretnych słów lub znaki interpunkcyjne w wypowiedź przykładu, należy użyć [wzorzec](luis-concept-patterns.md#pattern-syntax) z _Ignoruj_ składni. 

## <a name="training-utterances"></a>Szkolenie wypowiedzi
Szkolenie jest niejednoznaczne: prognozowanie wypowiedź może się nieco różnić między wersjami lub aplikacji.

## <a name="testing-utterances"></a>Wypowiedzi testowania 

Deweloperzy powinni uruchomić testowanie ich aplikacji LUIS, przy użyciu rzeczywistego ruchu, wysyłając wypowiedzi do punktu końcowego. Wypowiedzi te są wykorzystywane do ulepszania wydajności intencje i podmioty, za pomocą [Przejrzyj wypowiedzi](luis-how-to-review-endoint-utt.md). Przesłane za pomocą usługi LUIS witryny sieci Web, w okienku testowania testy nie są wysyłane za pośrednictwem punktu końcowego, a więc nie przyczyniają się do aktywne uczenie. 

## <a name="review-utterances"></a>Przejrzyj wypowiedzi
Po model jest uczony opublikowane i odbieranie [punktu końcowego](luis-glossary.md#endpoint) zapytań, [Przejrzyj wypowiedzi](luis-how-to-review-endoint-utt.md) zaproponowana przez usługi LUIS. Usługa LUIS wybiera wypowiedzi punkt końcowy ma niskie oceny przeznaczenie lub jednostki. 

## <a name="best-practices"></a>Najlepsze praktyki
Przegląd [najlepsze praktyki](luis-concept-best-practices.md) Aby dowiedzieć się więcej.

## <a name="next-steps"></a>Kolejne kroki
Zobacz [Dodawanie wypowiedzi przykład](luis-how-to-add-example-utterances.md) uzyskać informacji na temat szkoleń aplikacją usługi LUIS, aby zrozumieć wypowiedzi użytkowników.

[BingSpellCheck]: https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/proof-text