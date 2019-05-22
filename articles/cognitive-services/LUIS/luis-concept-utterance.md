---
title: Dobrym przykładem wypowiedzi
titleSuffix: Language Understanding - Azure Cognitive Services
description: Wypowiedzi są dane wejściowe użytkownika, że Twoja aplikacja wymaga, aby zinterpretować. Zbieraj fraz, które uważasz, że użytkownicy będą mogli wprowadzać. Dołącz wypowiedzi, które oznaczają to samo, ale są konstruowane inaczej długość słowa i rozmieszczenie programu word.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: fdf5508475d868ccb8c271daaac7449d3c940301
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2019
ms.locfileid: "65073154"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Zrozumienie, co dobre wypowiedzi związanych z aplikacją usługi LUIS

**Wypowiedzi** są dane wejściowe użytkownika, że Twoja aplikacja wymaga, aby zinterpretować. Szkolenie usługi LUIS do wyodrębnienia z nich intencje i podmioty, jest przechwytywania szereg wypowiedzi w innym przykładzie dla każdego intencji. Aktywna nauka lub proces szkolenie dotyczące nowych wypowiedzi w dalszym ciągu jest niezbędne do analizy maszyny do opanowania, udostępniająca usługi LUIS.

Zbieraj wypowiedzi uważasz, że użytkownicy będą mogli wprowadzać. Obejmują wypowiedzi, które oznaczają to samo, ale są konstruowane na wiele różnych sposobów:

* Wypowiedź długość numeru PIN — krótki, średni i długi dla aplikacji klienckiej
* Długość słowa i frazy 
* Umieszczanie programu Word - jednostkę na początku, środka i na końcu wypowiedź
* Gramatyka 
* Pluralizacja
* Analiza słowotwórcza
* Wybór rzeczownika i zlecenie
* Znaki interpunkcyjne — dobre różnych przy użyciu poprawne, nieprawidłowe i nie gramatyki

## <a name="how-to-choose-varied-utterances"></a>Jak wybrać zróżnicowane wypowiedzi

Gdy użytkownik najpierw początek [Dodawanie wypowiedzi przykład](luis-how-to-add-example-utterances.md) do modelu usługi LUIS, poniżej przedstawiono niektóre zasady, które należy uwzględnić.

### <a name="utterances-arent-always-well-formed"></a>Wypowiedzi nie są zawsze poprawnie sformułowany.

Może być dowolne zdanie, takich jak "Zarezerwuj biletu do Paryża dla mnie" lub fragment zdania, takich jak "Rezerwacji" lub "Paryż lot."  Użytkownicy często wykonują błędy pisowni. Podczas planowania aplikacji należy wziąć pod uwagę, czy używasz [sprawdzania pisowni Bing](luis-tutorial-bing-spellcheck.md) aby poprawić dane wejściowe użytkownika przed przekazaniem go do usługi LUIS. 

Jeśli wypowiedzi użytkownika wyboru nie jest pisowni, możesz później LUIS, na wypowiedzi obejmujących literówki i błędy pisowni.

### <a name="use-the-representative-language-of-the-user"></a>Użyj reprezentatywny języka użytkownika

Podczas wybierania wypowiedzi, należy pamiętać, że co myślisz, jest wspólny termin lub frazę, może nie być poprawne dla typowego użytkownika aplikacji klienckiej. Nie może mieć środowisko domeny. Należy zachować ostrożność, korzystając z terminów ani fraz, czy użytkownik będzie tylko powiedzieć, jeśli znajdowały się ekspertem.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Wybierz zależeć od terminologii, a także właściwej

Znajdziesz, nawet jeśli wprowadzisz działań mających na celu tworzenie zdania zależeć od wzorców, będą nadal powtórzenie niektóre słownika.

Wykonaj te wypowiedzi przykładu:

|Przykładowe wypowiedzi|
|--|
|jak uzyskać komputera?|
|Gdzie uzyskać komputera?|
|Chcę uzyskać komputera, jak przejść na ten temat?|
|Gdy masz, komputer?| 

W tym miejscu termin core "computer" nie jest zróżnicowane. Użyj rozwiązań alternatywnych, takich jak komputerów stacjonarnych, laptopów, stacji roboczej lub nawet po prostu maszyny. Usługa LUIS inteligentnie wnioskuje synonimy z kontekstu, ale podczas tworzenia wypowiedzi szkolenia jest nadal lepiej różnią się w ich.

## <a name="example-utterances-in-each-intent"></a>Przykład wypowiedzi w każdej intencji

Każdy intencji trzeba wypowiedzi przykład co najmniej 15. Jeśli cel, który nie ma wypowiedzi przykład, nie będzie to w opracowywaniu usługi LUIS. W przypadku intencji z jednego lub kilku bardzo wypowiedzi przykład LUIS nie będą dokładnie przewidzieć intencji. 

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Dodaj mniejszym grupom 15 wypowiedzi dla każdej iteracji tworzenia pakietów administracyjnych

W każdej iteracji modelu nie należy dodawać dużej ilości wypowiedzi. Dodawanie wypowiedzi w ilościach 15. [Szkolenie](luis-how-to-train.md), [publikowania](luis-how-to-publish-app.md), i [test](luis-interactive-test.md) ponownie.  

Usługa LUIS kompilacje skuteczne modeli z wypowiedzi dokładnie są wybierane przez tworzenie modelu usługi LUIS. Dodawanie wypowiedzi zbyt wiele nie cenne, ponieważ wprowadza ona pomyłek.  

Zaleca się rozpoczynać kilka wypowiedzi następnie [Przejrzyj wypowiedzi punktu końcowego](luis-how-to-review-endpoint-utterances.md) poprawne intencji ekstrakcji prognoz i jednostek.

## <a name="utterance-normalization"></a>Normalizacja wypowiedź

Wypowiedź normalizacji jest proces ignorowania skutki Interpunkcja i znaki diakrytyczne podczas uczenia i przewidywania.

## <a name="utterance-normalization-for-diacritics-and-punctuation"></a>Normalizacja wypowiedź znaki diakrytyczne i znaki interpunkcyjne

Wypowiedź normalizacji jest definiowany podczas tworzenia lub importowania aplikacji, ponieważ jest on ustawienie w pliku JSON aplikacji. Ustawienia normalizacji wypowiedź są domyślnie wyłączone. 

Znaki diakrytyczne są znaki lub znaków w tekście, takie jak: 

```
İ ı Ş Ğ ş ğ ö ü
```

Jeśli aplikacja włącza normalizacji, ocenia w **testu** okienka, testy usługi batch i zapytania punktu końcowego zmieni się dla wszystkich wypowiedzi przy użyciu znaków diakrytycznych lub znaki interpunkcyjne.

Włącz normalizacji wypowiedź znaków diakrytycznych lub znaki interpunkcyjne do pliku JSON usługi LUIS aplikacji w `settings` parametru.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"},
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

Normalizowanie **znaków interpunkcyjnych** oznacza, że przed swoje modele, skorzystaj ze szkoleń i przed punktu końcowego uzyskać przewidzieć zapytań, znaki interpunkcyjne zostaną usunięte z wypowiedzi. 

Normalizowanie **znaków diakrytycznych** zastępuje znaki przy użyciu znaków diakrytycznych w wypowiedzi znakami regularne. Na przykład: `Je parle français` staje się `Je parle francais`. 

Normalizacja nie oznacza, że wykonasz nie Zobacz interpunkcja i znaki diakrytyczne w przykładzie wypowiedzi lub odpowiedzi prognozowania jedynie, zostaną one zignorowane podczas uczenia i przewidywania.


### <a name="punctuation-marks"></a>Znaki interpunkcyjne

Jeśli nie jest znormalizowana znaków interpunkcyjnych, LUIS nie zignorować znaków interpunkcyjnych, domyślnie, ponieważ niektóre aplikacje klienckie mogą umieścić istotności na te znaczniki. Upewnij się, że swoje wypowiedzi przykład używać zarówno znaki interpunkcyjne, jak i nie znaków interpunkcyjnych aby obu stylów do zwrócenia tej samej względnej wyniki. 

Jeśli znaki interpunkcyjne nie ma znaczenia określonych w aplikacji klienckiej, należy wziąć pod uwagę [ignoruje znaki interpunkcyjne](#utterance-normalization) przez normalizowanie znaki interpunkcyjne. 

### <a name="ignoring-words-and-punctuation"></a>Ignorowanie słów i znaki interpunkcyjne

Ignorowanie słów lub znaki interpunkcyjne we wzorcach, należy użyć [wzorzec](luis-concept-patterns.md#pattern-syntax) z _Ignoruj_ składni z nawiasami kwadratowymi `[]`. 

## <a name="training-utterances"></a>Szkolenie wypowiedzi

Szkolenie jest zazwyczaj niedeterministyczna: prognozowanie wypowiedź może się nieco różnić między wersjami lub aplikacji. Możesz usunąć szkolenia deterministyczna, aktualizując [ustawień wersji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) interfejsu API za pomocą `UseAllTrainingData` nazwa/wartość pary na używanie wszystkich danych szkoleniowych.

## <a name="testing-utterances"></a>Wypowiedzi testowania 

Deweloperzy powinni uruchomić testowanie ich aplikacji LUIS, przy użyciu rzeczywistego ruchu, wysyłając wypowiedzi do [endpoint prognoz](luis-how-to-azure-subscription.md) adresu URL. Wypowiedzi te są wykorzystywane do ulepszania wydajności intencje i podmioty, za pomocą [Przejrzyj wypowiedzi](luis-how-to-review-endpoint-utterances.md). Przesłane za pomocą usługi LUIS witryny sieci Web, w okienku testowania testy nie są wysyłane za pośrednictwem punktu końcowego, a więc nie przyczyniają się do aktywne uczenie. 

## <a name="review-utterances"></a>Przejrzyj wypowiedzi

Po model jest uczony opublikowane i odbieranie [punktu końcowego](luis-glossary.md#endpoint) zapytań, [Przejrzyj wypowiedzi](luis-how-to-review-endpoint-utterances.md) zaproponowana przez usługi LUIS. Usługa LUIS wybiera wypowiedzi punkt końcowy ma niskie oceny przeznaczenie lub jednostki. 

## <a name="best-practices"></a>Najlepsze rozwiązania

Przegląd [najlepsze praktyki](luis-concept-best-practices.md) i zastosować je jako część regularnych cykl tworzenia pakietów administracyjnych.

## <a name="next-steps"></a>Kolejne kroki
Zobacz [Dodawanie wypowiedzi przykład](luis-how-to-add-example-utterances.md) uzyskać informacji na temat szkoleń aplikacją usługi LUIS, aby zrozumieć wypowiedzi użytkowników.

