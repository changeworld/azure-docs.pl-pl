---
title: Samouczek za pomocą jednostki pattern.any usprawniających prognoz usługi LUIS — Azure | Dokumentacja firmy Microsoft
titleSuffix: Cognitive Services
description: W tym samouczku Użyj jednostki pattern.any w celu przewidywania intencji i jednostki usługi LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 58a1871774683e2af66e12a03bdaf8d6bcea831c
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39493262"
---
# <a name="tutorial-improve-app-with-patternany-entity"></a>Samouczek: Ulepszaniu aplikacji z jednostką pattern.any

W tym samouczku Użyj jednostki pattern.any, aby zwiększyć prognozowania intencji i jednostek.  

> [!div class="checklist"]
* Dowiedz się, kiedy i jak używać pattern.any
* Tworzenie wzorca, który używa pattern.any
* Jak zweryfikować ulepszenia prognoz

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem
Jeśli nie masz zarządzania zasobami ludzkimi firmy [ról wzorca](luis-tutorial-pattern-roles.md) samouczek, [zaimportować](luis-how-to-start-new-app.md#import-new-app) dane JSON do nowej aplikacji w [LUIS](luis-reference-regions.md#luis-website) witryny sieci Web. Aplikację do zaimportowania znajduje się w [przykłady LUIS](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-roles-HumanResources.json) repozytorium GitHub.

Jeśli chcesz zachować oryginalną aplikację Human Resources, sklonuj tę wersję na stronie [Settings](luis-how-to-manage-versions.md#clone-a-version) (Ustawienia) i nadaj jej nazwę `patt-any`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. 

## <a name="the-purpose-of-patternany"></a>Celem pattern.any
Jednostka pattern.any umożliwia wyszukiwanie danych dowolnej postaci, w którym treść jednostki sprawia, że trudno ustalić koniec jednostki od reszty wypowiedź. 

Ta aplikacja zarządzania zasobami ludzkimi pomaga pracownikom Znajdź formy firmy. Formularze zostały dodane w [samouczek wyrażenia regularnego](luis-quickstart-intents-regex-entity.md). Nazwy formularza z tego samouczka używane wyrażeń regularnych do wyodrębniania nazwa formularza, który został prawidłowo sformatowaną, takich jak nazwy formularza pogrubiona w poniższej tabeli wypowiedź:

|Wypowiedź|
|--|
|Gdzie jest **HRF 123456**?|
|Autora **HRF 123234**?|
|**HRF 456098** jest publikowany w języku francuskim?|

Jednak każdy formularz zarówno o nazwie sformatowane, używane w powyższej tabeli, a także przyjazną nazwę, taką jak `Request relocation from employee new to the company 2018 version 5`. 

Wypowiedzi o nazwie przyjazna formularza wyglądać następująco:

|Wypowiedź|
|--|
|Gdzie jest **żądania przeniesienia z pracowników jest nowym składnikiem w wersji 2018 firmy 5**?|
|Autora **"Żądania relokacji z nowym składnikiem w wersji 2018 firmy 5 pracownika"**?|
|**Żądanie przeniesienia z nowym składnikiem w wersji 2018 firmy 5 pracownika** jest publikowany w języku francuskim?|

Różnej długości obejmuje fraz, które mogą mylić LUIS, o którym kończy się jednostki. Przy użyciu jednostki Pattern.any we wzorcu umożliwia określenie początku i na końcu nazwy formularza, dzięki czemu usługa LUIS poprawnie wyodrębnia nazwę formularza.

**Gdy wzorce umożliwiają mniej wypowiedzi przykład, jeśli obiekty nie są wykrywane, wzorzec jest niezgodny.**

## <a name="add-example-utterances-to-the-existing-intent-findform"></a>Dodawanie wypowiedzi przykładzie do istniejącego intencji FindForm 
Usuń keyPhrase wstępnie utworzone jednostki, jeśli jest trudny do tworzenia i oznaczanie jednostek Nazwa_formularza. 

1. Wybierz **kompilacji** w górnym menu nawigacyjnym i w wybierz **intencji** nawigacji po lewej stronie.

2. Wybierz **FindForm** z listy opcji.

3. Dodawanie wypowiedzi niektóre przykładowe:

    |Przykład wypowiedź|
    |--|
    |Gdzie jest to forma **o postępowaniu pożar dzieli w środowisku laboratoryjnym** i kogo należy podpisać go po jego odczytu?|
    |Gdzie jest **żądania przeniesienia z nowym użytkownikiem firmy pracownika** na serwerze?|
    |Autora "**kondycji i uroda żądań na uczelniach głównego**" i co to jest najbardziej aktualną wersję?|
    |Szukam formularz o nazwie "**Office przenieść żądanie, w tym zasoby fizyczne**". |

    Bez jednostka Pattern.any go może sprawiać trudności dla usługi LUIS dowiedzieć się, których tytuł formularza kończy się ze względu na wiele zmian nazw formularzy.

## <a name="create-a-patternany-entity"></a>Tworzenie jednostki Pattern.any
Jednostka Pattern.any wyodrębnia jednostki o różnej długości. Działa tylko we wzorcu ponieważ wzorca oznacza początek i koniec jednostki. Jeśli zauważysz, że Twoje wzorzec, gdy zawiera on Pattern.any, wyodrębnia jednostek, użyj [jawną listę](luis-concept-patterns.md#explicit-lists) Aby rozwiązać ten problem. 

1. Wybierz **jednostek** w nawigacji po lewej stronie.

2. Wybierz **Utwórz nową jednostkę**, wprowadź nazwę `FormName`i wybierz **Pattern.any** jako typu. Wybierz pozycję **Done** (Gotowe). 

    Jednostki w celu nie etykiety, ponieważ Pattern.any obowiązuje tylko we wzorcu. 

    Wyodrębnione dane, aby uwzględnić inne jednostki, takie jak liczba lub datetimeV2, należy utworzyć złożone jednostki, która obejmuje Pattern.any, a także liczbę i datetimeV2.

## <a name="add-a-pattern-that-uses-the-patternany"></a>Dodawanie wzorca, który używa Pattern.any

1. Wybierz **wzorców** w lewym obszarze nawigacji.

2. Wybierz **FindForm** intencji.

3. Wprowadź następujące wypowiedzi szablonu, które używają nowej jednostki:

    |Szablon wypowiedzi|
    |--|
    |Gdzie jest to forma ["] {Nazwa_formularza} ["] i kogo należy podpisać go po czy mogę odczytywać je [?]|
    |Gdzie to ["] {Nazwa_formularza} ["] na serwerze [?]|
    |Autora ["] {Nazwa_formularza} ["] i co to jest najbardziej aktualnej wersji [?]|
    |Szukam formularz o nazwie ["] {Nazwa_formularza} ["] [.]|

    Jeśli chcesz uwzględnić zmiany w formularzu, takie jak apostrofy zamiast podwójne cudzysłowy lub okres zamiast znaku zapytania, należy utworzyć nowy szablon dla poszczególnych odmian.

4. Usunięcie jednostki keyPhrase należy dodać go do aplikacji. 

5. Uczenie aplikacji.

## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testowanie nowego wzorca do wyodrębnienia dowolnych danych
1. Wybierz **Test** z paskiem górnym, aby otworzyć panel testu. 

2. Wprowadź następujące wypowiedź: 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

3. Wybierz **Sprawdź** w obszarze wynik, aby zobaczyć wyniki testu jednostki i przeznaczenie.

    Jednostka `FormName` znajduje się najpierw, a następnie zostanie odnaleziony wzorzec określania intencji. Jeśli wynik testu, w którym obiekty nie są wykrywane, a w związku z tym nie zostanie odnaleziony wzorzec, należy dodać więcej wypowiedzi przykład na intencji (nie wzorzec).

4. Zamknij panel testu, wybierając **Test** przycisku w górnym menu nawigacyjnym.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak użyć ról z wzorcem](luis-tutorial-pattern-roles.md)