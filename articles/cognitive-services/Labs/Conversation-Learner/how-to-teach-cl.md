---
title: Jak uczy z konwersacji uczeń - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak uczy z uczeń konwersacji.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 639fea64fc8eeb2c1f6e6240c4eb26efc68febbd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348597"
---
# <a name="how-to-teach-with-conversation-learner"></a>Jak uczy z uczeń konwersacji 

W tym dokumencie opisano, co sygnalizuje uczeń konwersacji zna i w tym artykule opisano, jak uzyskuje informacje o.  

Nauczanie może być rozłożone na dwóch różnych kroków: wyodrębniania jednostki i wybór akcji.

## <a name="entity-extraction"></a>Wyodrębnianie jednostki

W obszarze obejmuje, używa uczeń konwersacji [LUIS](https://www.luis.ai) do wyodrębnienia jednostki.  Jeśli znasz LUIS, stosowanym przez środowisko do wyodrębnienia jednostki w uczeń konwersacji.

Modele wyodrębniania jednostki potrafią *zawartości* i *kontekstu* w utterance użytkownika.  Na przykład wyraz "Seattle" ma zostały oznaczone jako Miejscowość w jednym utterance takich jak "Co to jest pogody w Seattle", wyodrębniania jednostka jest w stanie rozpoznawać tej samej zawartości ("Seattle") jako Miejscowość w innym utterance, takie jak "Populacji Seattle", nawet jeśli zniesławiających są bardzo różnią się.  Z drugiej strony jeśli ma "Francis" został rozpoznany jako nazwę w "Zaplanuj spotkanie z Francis", nową nazwę wcześniej niewidocznym rozpoznawany w kontekście podobne, takich jak "Ustaw spotkania z działania okrężnego", a następnie.  Wnioskuje uczenia maszynowego, kiedy należy zająć się zawartość, kontekstu i/lub oparte na przykładów szkoleniowych.

Obecnie wyodrębniania jednostka ma informacje tylko zawartości bieżącego utterance.  W odróżnieniu od wybór akcji (poniżej) nie są znane historii okna dialogowego, takie jak poprzednie włącza system, poprzednie włącza użytkownika lub wcześniej rozpoznany jednostek.  W związku z tym zachowanie jednostki wyodrębniania jest "udostępniony" we wszystkich zniesławiających.  Na przykład jeśli utterance użytkownika "Chcę Apple" ma "Apple" oznaczona jako typu jednostki "Owoce" w jednym utterance użytkownika, modelu jednostki wyodrębniania będzie oczekiwać tego utterance ("Chcę Apple") powinien zawsze mieć "Apple" oznaczone jako "Owoce".

Jeśli jednostka wyodrębniania nie zachowuje się zgodnie z oczekiwaniami, poniżej przedstawiono możliwości:

- Najpierw spróbuj jest dodanie więcej przykładów szkolenia — szczególnie przykłady, których ujawnić kontekst jednostki typowe (otaczającego słowa) lub wyjątków
- W razie potrzeby, należy rozważyć dodanie do właściwości "Oczekiwano jednostki" do akcji.  Aby uzyskać więcej informacji, zobacz samouczek na oczekiwany jednostek.
- Mimo że jest możliwie dodanie ręcznego przetwarzania do `EntityExtractionCallback` do wyodrębnienia jednostek przy użyciu kodu, to jest najmniej zalecane podejście, ponieważ nie będzie korzystna ulepszenia machine learning miarę rozwoju systemu.

## <a name="action-selection"></a>Wybór działania

Wybór działania używa powtarzającego się sieci neuronowej, przyjmująca jako wejściowego wszystkie historii konwersacji.  W związku z tym wybór akcji jest stanowa procesu, który zna poprzedniego zniesławiających użytkownika, wartości jednostki i zniesławiających systemu.  

Niektóre sygnały naturalnie są preferowane przez proces learning.  Innymi słowy uczeń konwersacji opisano decyzja o wyborze akcji za pomocą bardziej sygnałów "preferowane", jego będzie; Jeśli nie, będzie on używać mniej sygnały "preferowane".

W tym miejscu jest tabela zawierająca wszystkie sygnały uczeń konwersacji, oraz te, które są używane przez wybór akcji.  Należy pamiętać, że kolejność format programu word w zniesławiających użytkownika jest ignorowana.

Sygnał | Preferencji (1 = najlepszy możliwy) | Uwagi
--- | --- | --- 
Akcja systemu w poprzednim Włącz | 1 | 
Jednostek znajdujących się w bieżącym Włącz | 1 | 
Czy jest to pierwszy Włącz | 1 |
Dokładnego dopasowania słów w bieżącym utterance użytkownika | 2 | 
Znaczenie podobne słowa w bieżącej utterance użytkownika | 3 | 
Włącz akcje systemu przed poprzedniej | 4 |
Jednostek znajdujących się w włącza przed bieżącym Włącz | 4 | 
Włącz zniesławiających użytkownika przed bieżącym | 5 | 

Należy pamiętać, że wybór akcji stosować zawartość akcji systemu — tekstu, zawartość karty lub nazwa interfejsu API lub zachowania — tylko tożsamości działania systemu.  W związku z tym zmiana zawartości akcji nie zmieni zachowanie modelu zaznaczenia akcji.

Ponadto należy pamiętać, że zawartość/wartości jednostek nie są używane--tylko ich obecność/Brak.

Jeśli wybór akcji nie zachowuje się zgodnie z oczekiwaniami, w tym miejscu są możliwości:

- Dodaj więcej train okien dialogowych, szczególnie okien dialogowych ilustrujące sygnałów, które wybór akcji powinna zwracając uwagę.  Na przykład wybór akcji należy jednak jeden sygnału zamiast innego znajdują się przykłady pokazujące preferowanych sygnał w takim samym stanie, a innymi sygnałami zróżnicowanie.  Niektóre sekwencji może potrwać kilka okien dialogowych szkolenia, aby dowiedzieć się więcej.
- Dodaj "Required" i "Dyskwalifikacji" jednostek do definicji działania.  Ta limity akcje są dostępne i mogą być przydatne dla reguł biznesowych express i niektóre wzorce rozsądkiem. 

## <a name="updates-to-models"></a>Aktualizacje do modeli

Dowolnym momencie dodać lub edytować jednostki, akcji lub okna dialogowego pociągu w interfejsie użytkownika generuje żądanie, aby ponownie uczenia modelu wyodrębniania jednostki i modelu zaznaczenia akcji.  Tego żądania jest umieszczona w kolejce, a następnie ponownie szkolenia jest wykonywane asynchronicznie.  Gdy nowy model jest dostępny, jest używany z tego punktu i jego nowszych wersjach przypadku wyboru akcji i wyodrębniania jednostki.  Ten proces ponownego szkolenia często trwa około 5 sekund, ale może być dłużej, jeśli model jest złożony lub obciążenie usługi szkolenia jest wysoka.

Ponieważ szkolenia jest wykonywane asynchronicznie, jest to możliwe, że zmiany wprowadzone od razu nie są uwzględnione.  Jeśli zaznaczenie wyodrębniania lub akcji jednostki nie zachowuje się zgodnie z oczekiwaniami, na podstawie zmian wprowadzonych w ciągu ostatnich 5 – 10 sekund, może to być przyczyną.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wartości domyślne i granice](./cl-values-and-boundaries.md)
