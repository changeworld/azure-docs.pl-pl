---
title: Dobre przykładowe wypowiedzi — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Wypowiedzi to dane wejściowe od użytkownika, które Twoja aplikacja musi zinterpretować. Zbieraj frazy, które, jak myślisz, że użytkownicy wejdą. Dołącz wypowiedzi, które oznaczają to samo, ale są skonstruowane inaczej w długości wyrazów i umieszczaniu wyrazów.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: diberry
ms.openlocfilehash: 7412677773b60a1894a6ece7251e797bfddee091
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219919"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Zrozumienie, jakie są dobre wypowiedzi dla aplikacji usługi LUIS

Wypowiedzi są dane **wejściowe** od użytkownika, który aplikacja musi zinterpretować. Aby przeszkolić usługi LUIS, aby wyodrębnić intencje i jednostki z nich, ważne jest, aby przechwycić różne wypowiedzi przykład dla każdego zamiaru. Aktywne uczenie się lub proces kontynuowania uczenia się na nowe wypowiedzi, jest niezbędna do analizy uczenia się maszynowego, który zapewnia usługi LUIS.

Zbieraj wypowiedzi, które, jak myślisz, że użytkownicy wejdą. Dołącz wypowiedzi, co oznacza to samo, ale są skonstruowane na wiele różnych sposobów:

* Długość wypowiedź - krótki, średni i długi dla aplikacji klienckiej
* Długość wyrazu i frazy 
* Umieszczanie programu Word — encja na początku, w środku i na końcu wypowiedź
* Gramatyka 
* Pluralizm
* Wynikające
* Wybór rzeczownika i czasownika
* Interpunkcja - dobra odmiana przy użyciu poprawne, nieprawidłowe i nie gramatyki

## <a name="how-to-choose-varied-utterances"></a>Jak wybrać różne wypowiedzi

Po pierwszym uruchomieniu przez [dodanie wypowiedzi przykład](luis-how-to-add-example-utterances.md) do modelu usługi LUIS, oto kilka zasad, o których należy pamiętać.

### <a name="utterances-arent-always-well-formed"></a>Wypowiedzi nie zawsze są dobrze sformułowane

Może to być zdanie, takie jak "Zarezerwuj dla mnie bilet do Paryża", lub fragment zdania, taki jak "Rezerwacja" lub "lot do Paryża".  Użytkownicy często popełniają błędy ortograficzne. Podczas planowania aplikacji, należy rozważyć, czy używasz [sprawdzania pisowni Bing,](luis-tutorial-bing-spellcheck.md) aby poprawić dane wejściowe użytkownika przed przekazaniem go do usługi LUIS. 

Jeśli nie pisowni sprawdź wypowiedzi użytkownika, należy trenować usługi LUIS na wypowiedzi, które zawierają literówki i błędy pisowni.

### <a name="use-the-representative-language-of-the-user"></a>Używanie reprezentatywnego języka użytkownika

Wybierając wypowiedzi, należy pamiętać, że to, co uważasz za wspólny termin lub frazę może nie być poprawne dla typowego użytkownika aplikacji klienckiej. Mogą nie mieć doświadczenia domeny. Należy zachować ostrożność podczas używania terminów lub fraz, które użytkownik powiedziałby tylko wtedy, gdy był ekspertem.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Wybierz zróżnicowaną terminologię, a także frazowanie

Przekonasz się, że nawet jeśli dołożysz starań, aby stworzyć różne wzorce zdań, nadal będziesz powtarzać pewne słownictwo.

Weźmy następujące wypowiedzi przykład:

|Przykładowe wypowiedzi|
|--|
|jak uzyskać komputer?|
|Gdzie mogę uzyskać komputer?|
|Chcę dostać komputer, jak go o tym?|
|Kiedy mogę mieć komputer?| 

Podstawowe określenie tutaj, "komputer", nie jest zróżnicowane. Używaj alternatyw, takich jak komputer stacjonarny, laptop, stacja robocza, a nawet tylko maszyna. Usługa LUIS może inteligentnie wywnioskować synonimy z kontekstu, ale podczas tworzenia wypowiedzi dla szkolenia, zawsze lepiej je zmieniać.

## <a name="example-utterances-in-each-intent"></a>Przykładowe wypowiedzi w każdej intencji

Każdy zamiar musi mieć wypowiedzi przykład, co najmniej 15. Jeśli masz intencji, która nie ma żadnych wypowiedzi przykład, nie będzie można trenować usługi LUIS. Jeśli masz intencji z jednego lub bardzo niewielu wypowiedzi przykład, usługa LUIS nie może dokładnie przewidzieć intencji. 

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Dodawanie małych grup po 15 wypowiedzi dla każdej iteracji tworzenia

W każdej iteracji modelu nie należy dodawać dużą ilość wypowiedzi. Dodaj wypowiedzi w ilości 15. [Pociąg](luis-how-to-train.md), [publikować](luis-how-to-publish-app.md)i [testować](luis-interactive-test.md) ponownie.  

Usługa LUIS tworzy skuteczne modele z wypowiedzi, które są starannie wybrane przez autora modelu usługi LUIS. Dodawanie zbyt wiele wypowiedzi nie jest cenne, ponieważ wprowadza zamieszanie.

Lepiej jest zacząć od kilku wypowiedzi, a następnie [przejrzeć wypowiedzi punktu końcowego](luis-how-to-review-endpoint-utterances.md) dla przewidywania poprawne intencji i wyodrębniania jednostek.

## <a name="utterance-normalization"></a>Normalizacja wypowiedzi

Normalizacja wypowiedź jest procesem ignorowania skutków znaków interpunkcyjnych i znaków diakracyjnych podczas szkolenia i przewidywania.

## <a name="utterance-normalization-for-diacritics-and-punctuation"></a>Normalizacja wypowiedzi dla znaków diakrutycznych i znaków interpunkcyjnych

Normalizacja wypowiedź jest definiowana podczas tworzenia lub importowania aplikacji, ponieważ jest to ustawienie w pliku JSON aplikacji. Ustawienia normalizacji wypowiedź są domyślnie wyłączone. 

Znaki diakrytyczne to znaki lub znaki w tekście, takie jak: 

```
İ ı Ş Ğ ş ğ ö ü
```

Jeśli aplikacja włącza normalizację, wyniki w **okienku testowym,** testy wsadowe i kwerendy punktu końcowego zmienią się dla wszystkich wypowiedzi przy użyciu znaków diakracyjnych lub znaków interpunkcyjnych.

Włącz normalizację wypowiedź dla znaków diakretycznych lub znaków interpunkcyjnych do pliku aplikacji USŁUGI LUIS JSON w parametrze. `settings`

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"},
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

Normalizacja **interpunkcji** oznacza, że zanim modele się uszkodzą i zanim kwerendy punktu końcowego uzyskać przewidywane, znaki interpunkcyjne zostaną usunięte z wypowiedzi. 

Normalizacja **znaków diakrmtycznych** zastępuje znaki znakami znakowymi w wypowiedziach ze zwykłymi znakami. Na `Je parle français` przykład: `Je parle francais`staje się . 

Normalizacja nie oznacza, że nie będzie widać znaków interpunkcyjnych i znaków diakracyjnych w wypowiedzi przykład lub odpowiedzi przewidywania, tylko, że będą one ignorowane podczas szkolenia i przewidywania.


### <a name="punctuation-marks"></a>Znaki interpunkcyjne

Znaki interpunkcyjne jest oddzielny token w usłudze LUIS. Wypowiedź, która zawiera okres na końcu w porównaniu do wypowiedź, która nie zawiera kropki na końcu są dwa oddzielne wypowiedzi i może uzyskać dwa różne prognozy. 

Jeśli znaki interpunkcyjne nie są znormalizowane, usługa LUIS domyślnie nie ignoruje znaków interpunkcyjnych, ponieważ niektóre aplikacje klienckie mogą mieć znaczenie dla tych znaków. Upewnij się, że wypowiedzi przykład używać zarówno znaki interpunkcyjne i bez znaków interpunkcyjnych w celu obu stylów, aby zwrócić te same względne wyniki. 

Upewnij się, że model obsługuje znaki interpunkcyjne albo w wypowiedzi przykład (o i nie o znaki interpunkcyjne) lub w [wzorcach,](luis-concept-patterns.md) gdzie łatwiej jest zignorować znaki interpunkcyjne ze specjalną składnią:`I am applying for the {Job} position[.]`

Jeśli znaki interpunkcyjne nie ma określonego znaczenia w aplikacji klienckiej, należy rozważyć [ignorowanie znaków interpunkcyjnych](#utterance-normalization) przez normalizację interpunkcji. 

### <a name="ignoring-words-and-punctuation"></a>Ignorowanie słów i znaków interpunkcyjnych

Jeśli chcesz zignorować określone wyrazy lub znaki interpunkcyjne we wzorcach, użyj [wzorca](luis-concept-patterns.md#pattern-syntax) ze składnią _ignorowania_ nawiasów kwadratowych, `[]`. 

## <a name="training-utterances"></a>Wypowiedzi szkoleniowe

Szkolenie jest zazwyczaj niedeterministyczne: przewidywanie wypowiedź może się nieznacznie różnić w różnych wersjach lub aplikacjach. Szkolenie niedeterministyczne można usunąć, aktualizując interfejs `UseAllTrainingData` API ustawień [wersji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) przy użyciu pary nazwa/wartość w celu użycia wszystkich danych szkoleniowych.

## <a name="testing-utterances"></a>Testowanie wypowiedzi 

Deweloperzy powinni rozpocząć testowanie aplikacji usługi LUIS z rzeczywistym ruchem, wysyłając wypowiedzi do adresu URL [punktu końcowego przewidywania.](luis-how-to-azure-subscription.md) Te wypowiedzi są używane do poprawy wydajności intencji i jednostek z [przejrzyj wypowiedzi.](luis-how-to-review-endpoint-utterances.md) Testy przesłane za pomocą okienka testowania witryny sieci LUIS nie są wysyłane za pośrednictwem punktu końcowego, a więc nie przyczyniają się do aktywnego uczenia się. 

## <a name="review-utterances"></a>Przeglądanie wypowiedzi

Po modelu jest uczony, opublikowane i odbieranie zapytań [punktu końcowego,](luis-glossary.md#endpoint) [przejrzyj wypowiedzi sugerowane](luis-how-to-review-endpoint-utterances.md) przez usługę LUIS. Usługa LUIS wybiera wypowiedzi punktów końcowych, które mają niskie wyniki dla intencji lub jednostki. 

## <a name="best-practices"></a>Najlepsze rozwiązania

Przejrzyj [najlepsze praktyki](luis-concept-best-practices.md) i zastosuj je w ramach regularnego cyklu tworzenia.

## <a name="label-for-word-meaning"></a>Etykieta dla znaczenia słowa

Jeśli wybór wyrazu lub rozmieszczenie wyrazów jest takie samo, ale nie oznacza tego samego, nie oznaczaj go jednostką. 

Następujące wypowiedzi, słowo `fair` jest homografem. Jest napisane tak samo, ale ma inne znaczenie:

|Wypowiedź|
|--|
|Jakie targi powiatu dzieje się w obszarze Seattle tego lata?|
|Czy aktualna ocena recenzji w Seattle jest sprawiedliwa?|

Jeśli chcesz, aby jednostka zdarzenia znaleźć wszystkie `fair` dane zdarzeń, oznaczyć wyraz w pierwszym wypowiedź, ale nie w drugim.


## <a name="next-steps"></a>Następne kroki
Zobacz [Dodawanie wypowiedzi przykład informacji](luis-how-to-add-example-utterances.md) na temat szkolenia aplikacji usługi LUIS, aby zrozumieć wypowiedzi użytkowników.

