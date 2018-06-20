---
title: Zniesławiających w aplikacjach LUIS na platformie Azure | Dokumentacja firmy Microsoft
description: Dodaj zniesławiających w aplikacji usługi inteligentnego opis języka (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/13/2018
ms.author: v-geberr
ms.openlocfilehash: 7c2cd84df8f1eccbd30a7c8054ec8d06336cf2dd
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264650"
---
# <a name="utterances-in-luis"></a>Zniesławiających w LUIS

**Zniesławiających** są dane wejściowe użytkownika, które aplikacji należy interpretować. Uczenia LUIS można wyodrębnić intencje i jednostki z nich, jest szereg różnych komponentów dla każdego zamiar przechwytywania. Active learning lub kontynuowanie uczenia na nowe zniesławiających, proces jest niezbędne do analizy rozpoznawane maszyny, zapewniająca LUIS.

Zbieraj wyrażeń, które uważasz, że użytkownicy będą mogli wprowadzać. Dołącz zniesławiających, które oznaczają to samo, ale są zbudowane inaczej długość słowa i umieszczania programu word. 

## <a name="how-to-choose-varied-utterances"></a>Wybieranie zróżnicowane zniesławiających
Należy najpierw pobrać uruchomienia przez [Dodawanie zniesławiających przykład] [ add-example-utterances] modelu LUIS, poniżej przedstawiono niektóre zasady, które należy wziąć pod uwagę.

### <a name="utterances-arent-always-well-formed"></a>Zniesławiających nie są zawsze poprawnie sformułowany.
Może być zdania, takich jak "W Book mnie bilet, aby Paryża" lub fragmentu zdania, takich jak "Rezerwacji" lub "Paryża transmitowane."  Użytkownicy często wykonują błędów pisowni. Podczas planowania aplikacji, należy wziąć pod uwagę czy możesz sprawdzanie danych wejściowych użytkownika przed przekazaniem go do LUIS. [API sprawdzania pisowni usługi Bing] [ BingSpellCheck] integruje się z LUIS. LUIS aplikacji można skojarzyć z kluczem zewnętrznym dla API sprawdzania pisowni usługi Bing podczas jego publikowania. Jeśli nie zostało wpisane wyboru użytkownika zniesławiających, powinien uczenia LUIS na zniesławiających, obejmujące błędów pisowni i błędów pisowni.

### <a name="use-the-representative-language-of-the-user"></a>Użyj reprezentatywny języka użytkownika
Podczas wybierania zniesławiających, należy pamiętać, co myślisz jest wspólny termin lub frazę może nie być użytkownikowi typowych aplikacji klienta. Nie może mieć środowisko domeny. Dlatego należy zachować ostrożność, podczas korzystania z warunków lub wyrażeń, że użytkownik będzie tylko powiedzieć, jakby były eksperta.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Wybierz zróżnicowane terminologii, a także właściwej
Znajdziesz się nawet wtedy, gdy wysiłki utworzyć wzorce zróżnicowane zdania, będą nadal powtórzenie niektórych słownika.

Wykonaj te zniesławiających przykładzie:
```
how do I get a computer?
Where do I get a computer?
I want to get a computer, how do I go about it?
When can I have a computer? 
```
Termin core w tym miejscu "komputer", nie jest zmieniane. One powiedzieć komputerów stacjonarnych, laptopów, stacji roboczej lub nawet po prostu maszyny. LUIS inteligentnie wnioskuje synonimy z kontekstu, ale po utworzeniu zniesławiających szkolenia jest nadal lepiej różnią się je.

## <a name="example-utterances-in-each-intent"></a>Przykład zniesławiających w każdym polu Opcje
Każda próba trzeba przykład zniesławiających, co najmniej 10 – 15. Jeśli masz zamiar, który nie ma żadnych zniesławiających przykład nie można uczenia LUIS. Jeśli masz zamiar z jednego lub kilku bardzo zniesławiających przykład LUIS nie zostanie dokładnie przewidzieć celem. 

## <a name="add-small-groups-of-10-15-utterances-for-each-authoring-iteration"></a>Dodaj małych grup zniesławiających 10 – 15 dla każdej iteracji tworzenia pakietów administracyjnych
W każdej iteracji modelu nie należy dodawać dużej ilości zniesławiających. Dodaj zniesławiających w ilości dziesiątki. [Train](luis-how-to-train.md), [publikowania](publishapp.md), i [test](interactive-test.md) ponownie.  

LUIS kompilacje skuteczne modeli z zniesławiających wybranych uważnie. Dodawanie zbyt wiele zniesławiających nie jest przydatna ponieważ wprowadza ona pomyłek.  

Zaleca się rozpoczynać się od kilku zniesławiających, następnie [Przejrzyj zniesławiających punktu końcowego](label-suggested-utterances.md) poprawne konwersji ekstrakcji prognozowania i jednostek.

## <a name="training-utterances"></a>Zniesławiających szkolenia
Szkolenie jest deterministyczna: prognozowania utterance mogą się nieco różnić między wersjami lub aplikacji.

## <a name="testing-utterances"></a>Testowanie zniesławiających 

Deweloperzy powinien rozpocząć testowanie ich aplikacji LUIS przy użyciu rzeczywistego ruchu, wysyłając zniesławiających do punktu końcowego. Zniesławiających te są wykorzystywane do ulepszania wydajności intencje i jednostki z [Przejrzyj zniesławiających](label-suggested-utterances.md). Testy przesłane z witryną internetową LUIS testowania okienku nie są wysyłane za pośrednictwem punktu końcowego, a nie współtworzenia do uczenia active. 

## <a name="review-utterances"></a>Przejrzyj zniesławiających
Po modelu przeszkolone opublikowanych i odbierania [punktu końcowego](luis-glossary.md#endpoint) zapytań, [Przejrzyj zniesławiających](label-suggested-utterances.md) zasugerowany przez LUIS. LUIS wybiera zniesławiających punktu końcowego, mających niski wyniki zamiar lub jednostki. 

## <a name="best-practices"></a>Najlepsze praktyki
Przegląd [najlepsze rozwiązania](luis-concept-best-practices.md) Aby dowiedzieć się więcej.

## <a name="next-steps"></a>Kolejne kroki
Zobacz [dodać zniesławiających przykład] [ add-example-utterances] uzyskać informacji dotyczących szkoleń aplikacji LUIS, aby zrozumieć zniesławiających użytkownika.

[add-example-utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-add-example-utterances
[BingSpellCheck]: https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/proof-text