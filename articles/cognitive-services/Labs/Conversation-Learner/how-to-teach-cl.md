---
title: Jak do nauki o uczeń konwersacji — Microsoft Cognitive Services | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Dowiedz się, jak do nauki o uczeń konwersacji.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 9e5e6594a74219a22d67af18827bfe2b7cbd0fb8
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66385271"
---
# <a name="how-to-teach-with-conversation-learner"></a>Nauczanie z użyciem Ucznia konwersacji 

W tym dokumencie opisano, co sygnalizuje uczeń konwersacji zna i w tym artykule opisano, jak uczy się.  

Nauczanie może być rozłożone na dwa etapy: działania funkcji wydobywania podmiotów i wybieranie akcji.

## <a name="entity-extraction"></a>Wyodrębnianie jednostek

Dzieje się w tle, używa uczeń konwersacji [LUIS](https://www.luis.ai) dla działania funkcji wydobywania podmiotów.  Osoby zaznajomione z użyciem usługi LUIS, tego, że ma zastosowanie do działania funkcji wydobywania podmiotów w uczeń konwersacji.

Modele Ekstrakcja jednostek zdawali sobie sprawę z *zawartości* i *kontekstu* w ramach wypowiedź użytkownika.  Na przykład, jeśli słowo "Seattle" ma zostały oznaczone jako miasta w jedną wypowiedź takich jak "Co to jest pogody w Seattle", wyodrębnianie jednostek jest w stanie rozpoznawać tej samej zawartości ("Seattle") jako Miasto w innym wypowiedź, takie jak "Populacji Seattle", nawet wtedy, gdy wypowiedzi różnią się bardzo.  Z drugiej strony Jeśli "Francis" ma została uznana za nazwę w "Zaplanuj spotkanie z Francis", a następnie nazwę wcześniej niewidzianych rozpoznawany w kontekście podobnie, takich jak "Ustaw spotkanie wstępne przy użyciu działania okrężnego".  Wnioskuje uczenia maszynowego, kiedy należy weź udział w zawartości, kontekstu lub oba, na podstawie przykładów szkolenia.

Obecnie działania funkcji wydobywania podmiotów ma informacje tylko zawartości bieżącego wypowiedź.  Inaczej niż w przypadku wyboru akcji (poniżej) nie ma informacji o historii okna dialogowego, takich jak poprzednie włącza system, poprzednie włącza użytkownika lub jednostki wcześniej rozpoznany.  W rezultacie zachowanie działania funkcji wydobywania podmiotów jest "udostępnione" wszystkie wypowiedzi.  Na przykład jeśli wypowiedź użytkownika "Chcę Apple" zawiera "Apple" oznaczona jako typu jednostki "Owoce" w jednym wypowiedź użytkownika, model wyodrębniania jednostki będą oczekiwać, że ta wypowiedź ("Chcę Apple") zawsze powinien mieć "Apple" oznaczona jako "Owoce".

Jeśli działania funkcji wydobywania podmiotów nie zachowuje się zgodnie z oczekiwaniami, poniżej przedstawiono możliwe środki zaradcze:

- Najpierw należy spróbować jest dodanie więcej przykładów szkolenia — szczególnie te przykłady, które ujawniają kontekstu typowych jednostki (otaczającego słów) lub wyjątki
- Jeśli to stosowne, należy rozważyć dodanie do właściwości "Oczekiwaniami Entity" do akcji.  Aby uzyskać więcej informacji, zobacz samouczek na oczekiwany jednostek.
- Choć jest możliwe do ręcznego przetwarzania w celu dodania `EntityExtractionCallback` do wyodrębnienia jednostki przy użyciu kodu, to jest najmniej zalecane podejście, ponieważ nie będzie korzystna ulepszenia usługi machine learning jak dojrzewa systemu.

## <a name="action-selection"></a>Wybór akcji

Wybór akcji używa powtarzającego się sieci neuronowych, która przyjmuje jako danych wejściowych wszystkie historię konwersacji.  Dlatego wybór akcji jest procesów stanowych, który zna poprzedniego wypowiedzi użytkowników, wartości jednostki i wypowiedzi systemu.  

Niektóre sygnały naturalnie są preferowane przez proces uczenia.  Innymi słowy uczeń konwersacji może wyjaśnić, przy użyciu bardziej "preferowane" sygnały decyzja o wyborze akcji, jej będzie; Jeśli jest ona nieosiągalna, będzie ona używać mniej "preferowane" sygnałów.

Poniżej przedstawiono tabelę przedstawiającą wszystkie sygnały w uczeń konwersacji, oraz te, które są używane przez wybór akcji.  Należy zauważyć, że wyraz zamówienia w wypowiedzi użytkowników jest ignorowany.

Sygnał | Preferencji (1 = najbardziej preferowaną) | Uwagi
--- | --- | --- 
Akcja systemu poprzedniej pozycji. | 1 | 
Jednostki znajdujące się w bieżącym Włącz | 1 | 
Czy jest to pierwsze Włącz | 1 |
Dokładnego dopasowania słów w bieżącym wypowiedź użytkownika | 2 | 
Podobne znaczenie słowa w bieżącej wypowiedź użytkownika | 3 | 
Włącz akcji systemu przed poprzedniej | 4 |
Jednostki znajdujące się w włącza przed bieżącym Włącz | 4 | 
Włącz wypowiedzi użytkowników przed bieżącą | 5 | 

> [!NOTE]
> Wybór akcji nie przyjmuje zawartości akcji systemu — tekst, zawartość karty lub nazwa interfejsu API lub zachowanie — tożsamość działania systemu.  W wyniku zmiany zawartości akcji nie powodują zmianę zachowania modelu wybór akcji.
>
> Ponadto zawartość/wartości jednostki nie są używane — tylko ich obecności/Brak.

Jeśli wybór akcji nie zachowuje się zgodnie z oczekiwaniami, poniżej przedstawiono możliwe środki zaradcze:

- Dodaj więcej train wyświetlanymi w oknach dialogowych, szczególnie wyświetlanymi w oknach dialogowych, które ilustrują sygnałów, które wybór akcji powinna zwracając uwagę.  Na przykład wybór akcji powinna jednak jeden sygnał zamiast innego zapewniają przykładami pokazującymi preferowanych sygnał znajdujące się w tym samym stanie, a innymi sygnałami zmieniającego się.  Kilka sekwencji może trwać kilka okien dialogowych szkolenia, aby dowiedzieć się więcej.
- Dodaj "Required" i "Dyskwalifikacji" jednostki do definicji działania.  Ta limity akcje są dostępne, gdy może być przydatne do reguł biznesowych express i niektóre wzorce rozsądkiem. 

## <a name="updates-to-models"></a>Aktualizacje do modeli

Ilekroć dodawania lub edytowania jednostek, akcji lub pociągu okno dialogowe, w interfejsie użytkownika, spowoduje to wygenerowanie żądania do ponownego szkolenia modelu wyodrębniania jednostki i modelu wybór akcji.  To żądanie jest umieszczany w kolejce, a następnie ponowne szkolenie są wykonywane asynchronicznie.  Po udostępnieniu nowego modelu jest używany z tego punktu i nowszych wersjach do wyboru akcji i wyodrębniania jednostki.  Ten proces ponownego szkolenia często zajmuje około 5 sekund, ale może być dłuższa, czy model jest złożony, czy obciążenie usługi programu szkolenia jest wysoka.

Ponieważ szkoleń są wykonywane asynchronicznie, jest możliwe, że zmiany wprowadzone nie są natychmiast odzwierciedlane.  Wybór jednostki wyodrębniania lub akcji nie zachowuje się zgodnie z oczekiwaniami, w oparciu o zmiany wprowadzone w ciągu ostatnich 5 – 10 sekund, może to być przyczyną.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wartości domyślne i granice](./cl-values-and-boundaries.md)
