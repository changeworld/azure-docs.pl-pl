---
title: 'Samouczek 5: Jednostki Pattern.any tekst w dowolnej postaci'
titleSuffix: Azure Cognitive Services
description: Służy do wyodrębniania danych z wypowiedzi jednostki pattern.any, gdzie jest prawidłowo sformatowaną wypowiedzi i gdzie końca danych może łatwo pomylić z pozostałych wyrazów wypowiedź.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.technology: language-understanding
ms.topic: article
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: a5b964877f1c089015812abdf365b22c57267bb0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031374"
---
# <a name="tutorial-5-extract-free-form-data"></a>Samouczek: 5. Wyodrębnianie danych o formacie swobodnym

W tym samouczku Użyj jednostki pattern.any do wyodrębniania danych z wypowiedzi, gdzie są prawidłowo sformatowaną wypowiedzi i gdzie końca danych może łatwo pomylić z pozostałych wyrazów wypowiedź. 

Jednostka pattern.any umożliwia wyszukiwanie dowolnych danych, w którym treść jednostki sprawia, że trudno ustalić koniec jednostki od reszty wypowiedź. 

Ta aplikacja zarządzania zasobami ludzkimi pomaga pracownikom Znajdź formy firmy. 

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

Różnej długości zawiera wyrazy, które mogą mylić LUIS, o którym kończy się jednostki. Przy użyciu jednostki Pattern.any we wzorcu umożliwia określenie początku i na końcu nazwy formularza, dzięki czemu usługa LUIS poprawnie wyodrębnia nazwę formularza.

|Przykład wypowiedź szablonu|
|--|
|Gdzie jest {Nazwa_formularza} [?]|
|Autora {Nazwa_formularza} [?]|
|{Nazwa formularza} jest publikowany w języku francuskim [?]|

**W tym samouczku dowiesz się, jak:**

> [!div class="checklist"]
> * Użyj istniejącego samouczek aplikacji
> * Dodawanie wypowiedzi przykład do istniejącej jednostki
> * Tworzenie jednostki Pattern.any
> * Tworzenie wzorca
> * Szkolenie
> * Wzór nowego testu

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Użyj istniejącej aplikacji
Kontynuuj aplikację utworzoną w samouczku ostatni o nazwie **kadry**. 

Jeśli nie masz aplikacji kadry z poprzedniego samouczka, należy użyć następujących czynności:

1.  Pobierz i Zapisz [pliku JSON aplikacji](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-roles-HumanResources.json).

2. Importuj dane JSON do nowej aplikacji.

3. Z **Zarządzaj** sekcji na **wersji** kartę, klonowanie wersji i nadaj mu nazwę `patt-any`. Klonowanie to dobry sposób na testowanie różnych funkcji usługi LUIS bez wpływu na oryginalną wersję aplikacji. Ponieważ nazwa wersji jest używany jako część trasy adresu URL, nazwa nie może zawierać żadnych znaków, które nie są prawidłowe w adresie URL.

## <a name="add-example-utterances"></a>Dodawanie wypowiedzi przykład 
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

## <a name="train-the-luis-app"></a>Uczenie aplikacji LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testowanie nowego wzorca do wyodrębnienia dowolnych danych
1. Wybierz **Test** z paskiem górnym, aby otworzyć panel testu. 

2. Wprowadź następujące wypowiedź: 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

3. Wybierz **Sprawdź** w obszarze wynik, aby zobaczyć wyniki testu jednostki i przeznaczenie.

    Jednostka `FormName` znajduje się najpierw, a następnie zostanie odnaleziony wzorzec określania intencji. Jeśli wynik testu, w którym obiekty nie są wykrywane, a w związku z tym nie zostanie odnaleziony wzorzec, należy dodać więcej wypowiedzi przykład na intencji (nie wzorzec).

4. Zamknij panel testu, wybierając **Test** przycisku w górnym menu nawigacyjnym.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku dodany przykład wypowiedzi do istniejących intencji, a następnie tworzone nowe Pattern.any nazwa formularza. Następnie samouczka utworzona szablon dla istniejącej intencji z nowy przykład wypowiedzi i jednostek. Testowanie interaktywne wykazało, że wzorzec i jego celem były przewidzieć, ponieważ znaleziono jednostki. 

> [!div class="nextstepaction"]
> [Dowiedz się, jak użyć ról z wzorcem](luis-tutorial-pattern-roles.md)