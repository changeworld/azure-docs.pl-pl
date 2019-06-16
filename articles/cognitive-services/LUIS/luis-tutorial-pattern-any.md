---
title: Jednostka Pattern.any
titleSuffix: Azure Cognitive Services
description: Jednostka Pattern.any służy do wyodrębniania danych z wypowiedzi, gdzie wypowiedź jest prawidłowo sformatowana i gdzie koniec danych można łatwo pomylić z pozostałymi wyrazami wypowiedzi.
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/12/2019
ms.author: diberry
ms.openlocfilehash: 6007f88af4d1049a87851b3808c66693173a648a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069248"
---
# <a name="tutorial-extract-free-form-data-with-patternany-entity"></a>Samouczek: wyodrębnianie danych o formacie swobodnym za pomocą jednostki Pattern.any

W tym samouczku używamy jednostki Pattern.any do wyodrębniania danych z wypowiedzi, gdzie wypowiedzi są prawidłowo sformatowane i gdzie koniec danych można łatwo pomylić z pozostałymi wyrazami wypowiedzi. 

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Importowanie aplikacji przykładowej
> * Dodawanie przykładowych wypowiedzi do istniejącej jednostki
> * Tworzenie jednostki Pattern.any
> * Tworzenie wzorca
> * Szkolenie
> * Testowanie nowego wzorca

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="using-patternany-entity"></a>Używanie jednostki Pattern.any

Jednostka Pattern.any umożliwia wyszukiwanie danych o swobodnym formacie, gdzie treść jednostki sprawia, że trudno rozróżnić koniec jednostki od reszty wypowiedzi. 

Ta aplikacja zarządzania zasobami ludzkimi pomaga pracownikom znajdować formularze firmy. 

|Wypowiedź|
|--|
|Where is **HRF-123456**? (Gdzie jest formularz HRF-123456?)|
|Who authored **HRF-123234**? (Kto jest autorem formularza HRF-123456?)|
|**HRF-456098** is published in French? (Czy formularz HRF-123456 jest publikowany w języku francuskim?)|

Jednak każdy formularz ma zarówno nazwę sformatowaną, używaną w powyższej tabeli, jak i nazwę przyjazną, taką jak `Request relocation from employee new to the company 2018 version 5`. 

Wypowiedzi z przyjazną formą wyglądają następująco:

|Wypowiedź|
|--|
|Where is **Request relocation from employee new to the company 2018 version 5**? (Gdzie jest formularz Żądanie przeniesienia nowego pracownika do firmy 2018 wersja 5?)|
|Who authored **Request relocation from employee new to the company 2018 version 5**? (Kto jest autorem formularza Żądanie przeniesienia nowego pracownika do firmy 2018 wersja 5?)|
|**Request relocation from employee new to the company 2018 version 5** is published in French? (Czy formularz Żądanie przeniesienia nowego pracownika do firmy 2018 wersja 5 jest publikowany w języku francuskim?)|

Różna długość zawiera wyrazy, które mogą wprowadzać usługę LUIS w błąd co do tego, gdzie kończy się dana jednostka. Użycie jednostki Pattern.any we wzorcu umożliwia określenie początku i końca nazwy formularza, dzięki czemu usługa LUIS poprawnie wyodrębnia nazwę formularza.

|Przykładowa wypowiedź szablonu|
|--|
|Where is {FormName}[?] (Gdzie jest {Nazwa_formularza}[?])|
|Who authored {FormName}[?] (Kto jest autorem {Nazwa_formularza}[?])|
|{FormName} is published in French[?] (Czy {Nazwa_formularza} jest publikowany w języku francuskim [?])|

## <a name="import-example-app"></a>Importowanie aplikacji przykładowej

1. Pobierz i zapisz [plik JSON aplikacji](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-pattern-roles-HumanResources.json).

1. W [portal usługi LUIS](https://www.luis.ai)na **Moje aplikacje** strony, zaimportuj plik JSON do nowej aplikacji.

1. W sekcji **Manage** (Zarządzanie) na karcie **Versions** (Wersje) sklonuj wersję i nadaj jej nazwę `patt-any`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. Ponieważ nazwa wersji jest używana jako część trasy adresu URL, nie może ona zawierać żadnych znaków, które są nieprawidłowe w adresie URL.

## <a name="add-example-utterances"></a>Dodawanie przykładowych wypowiedzi 

1. Wybierz pozycję **Build** (Kompiluj) na górnym pasku nawigacyjnym i wybierz pozycję **Intents** (Intencje) na lewym pasku nawigacyjnym.

1. Z listy intencji wybierz pozycję **FindForm** (Znajdź formularz).

1. Dodaj przykładowe wypowiedzi:

    |Przykładowa wypowiedź|
    |--|
    |Where is the form **What to do when a fire breaks out in the Lab** and who needs to sign it after I read it? (Gdzie jest formularz Postępowanie w razie pożaru w laboratorium i kto musi go podpisać, gdy go przeczytam?)|
    |Where is **Request relocation from employee new to the company 2018 version 5** on the server? (Gdzie jest formularz Żądanie przeniesienia nowego pracownika do firmy 2018 wersja 5 na serwerze?)|
    |Who authored "**Health and wellness requests on the main campus**" and what is the most current version? (Kto jest autorem formularza „Wymagania dotyczące zdrowia i dobrego samopoczucia na głównym kampusie” i jaka jest najbardziej aktualna wersja?)|
    |I'm looking for the form named "**Office move request including physical assets**". (Szukam formularza o nazwie „Żądanie przeniesienia biura łącznie z zasobami fizycznymi”.) |

    Bez jednostki Pattern.any usługa LUIS może mieć trudność ze zrozumieniem, gdzie kończy się tytuł formularza ze względu na wiele różnych nazw formularzy.

## <a name="create-a-patternany-entity"></a>Tworzenie jednostki Pattern.any
Jednostka Pattern.any wyodrębnia jednostki o różnej długości. Działa tylko we wzorcu, ponieważ wzorzec oznacza początek i koniec jednostki.  

1. Wybierz pozycję **Entities** (Jednostki) na lewym pasku nawigacyjnym.

1. Wybierz polecenie **Create new entity** (Utwórz nową jednostkę), wprowadź nazwę `FormName` i wybierz jednostkę **Pattern.any** jako typ. Wybierz pozycję **Done** (Gotowe). 

    Jednostki w wypowiedzi przykład celem nie etykiety, ponieważ Pattern.any obowiązuje tylko we wzorcu. 

    Jeśli chcesz, aby wyodrębnione dane uwzględniały inne jednostki, takie jak liczba lub jednostka datetimeV2, musisz utworzyć złożoną jednostkę, która obejmuje jednostkę Pattern.any, a także liczbę i jednostkę datetimeV2.

## <a name="add-a-pattern-that-uses-the-patternany"></a>Dodawanie wzorca, który używa jednostki Pattern.any

1. Wybierz pozycję **Patterns** (Wzorce) na lewym pasku nawigacyjnym.

1. Wybierz intencję **FindForm** (Znajdź formularz).

1. Wprowadź następujące wypowiedzi szablonu, które używają nowej jednostki:

    |Wypowiedzi szablonu|
    |--|
    |Where is the form ["]{FormName}["] and who needs to sign it after I read it[?] (Gdzie jest formularz [„]{Nazwa_formularza}[”] i kto musi go podpisać, gdy go przeczytam[?])|
    |Where is ["]{FormName}["] on the server[?] (Gdzie jest [„]{Nazawa_formularza}[”] na serwerze[?])|
    |Who authored ["]{FormName}["] and what is the most current version[?] (Kto jest autorem formularza [„]{Nazwa_formularza}[”] i jaka jest najbardziej aktualna wersja[?])|
    |I'm looking for the form named ["]{FormName}["][.] (Szukam formularza o nazwie [„]{Nazwa_formularza}[”][.])|

    Jeśli chcesz uwzględnić różne warianty w formularzu, takie jak pojedyncze cudzysłowy zamiast cudzysłowów podwójnych lub kropka zamiast znaku zapytania, utwórz nowy wzór dla każdego wariantu.

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testowanie nowego wzorca do wyodrębnienia danych o formacie swobodnym
1. Na górnym pasku wybierz pozycję **Test** (Testuj), aby otworzyć panel testu. 

1. Wprowadź następującą wypowiedź: 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

1. W obszarze wyniku wybierz pozycję **Inspect** (Sprawdź), aby zobaczyć wyniki testu jednostki i intencji.

    Najpierw znaleziono jednostkę `FormName`, następnie znaleziono wzorzec określający intencję. Jeśli otrzymano wynik testu, w którym jednostki nie zostały wykryte, a w związku z tym nie znaleziono wzorca, należy dodać więcej przykładowych wypowiedzi do intencji (nie wzorca).

1. Zamknij panel testu, wybierając przycisk **Test** (Testuj) na górnym pasku nawigacyjnym.

## <a name="using-an-explicit-list"></a>Używanie jawnych list

Jeśli zauważysz, że Twój wzorzec, gdy zawiera jednostkę Pattern.any, niepoprawnie wyodrębnia jednostki, użyj elementu [explicit list](luis-concept-patterns.md#explicit-lists) (listy jawnej), aby rozwiązać ten problem.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku dodano przykładowe wypowiedzi do istniejącej intencji, a następnie utworzono nową jednostkę Pattern.any dla nazwy formularza. Następnie w samouczku utworzono wzorzec dla istniejącej intencji z nowymi przykładowymi wypowiedziami i jednostką. Testowanie interaktywne wykazało, że wzorzec i jego intencja zostały przewidziane, ponieważ znaleziono jednostkę. 

> [!div class="nextstepaction"]
> [Dowiedz się, jak używać ról ze wzorcem](luis-tutorial-pattern-roles.md)
