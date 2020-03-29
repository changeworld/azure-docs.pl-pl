---
title: Dodawanie encji — usługa LUIS
titleSuffix: Azure Cognitive Services
description: Tworzenie jednostek w celu wyodrębnienia kluczowych danych z wypowiedzi użytkowników w aplikacjach do zrozumienia języka (LUIS). Wyodrębnione dane jednostki są używane przez aplikację kliencką do żądania klienta fullfil.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1f2b293acdc77e25e6b932c47d466cc28a04a2b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220909"
---
# <a name="add-entities-to-extract-data"></a>Dodawanie jednostek w celu wyodrębniania danych 

Tworzenie jednostek w celu wyodrębnienia kluczowych danych z wypowiedzi użytkowników w aplikacjach do zrozumienia języka (LUIS). Wyodrębnione dane encji są używane przez aplikację kliencką do żądania klientów fullfil.

Jednostka reprezentuje wyraz lub frazę wewnątrz wypowiedź, które mają wyodrębnione. Jednostki opisują informacje istotne dla intencji, a czasami są one niezbędne dla aplikacji do wykonywania jej zadania. Można utworzyć jednostki podczas dodawania wypowiedź przykład do intencji lub oprócz (przed lub po) dodanie wypowiedź przykład do intencji.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="plan-entities-then-create-and-label"></a>Planowanie encji, a następnie tworzenie i etykietowanie

Jednostki nauczane maszynowo można utworzyć na podstawie wypowiedzi przykładowych lub utworzone na stronie **Jednostki.** 

Ogólnie rzecz biorąc najlepszym rozwiązaniem jest spędzanie czasu na planowaniu jednostek przed utworzeniem jednostki nauczanych maszynowo w portalu. Następnie utwórz jednostkę nauczoną maszyną z wypowiedź przykład z tyle szczegółów w podskładników i deskryptorów i ograniczeń, jak wiesz w tym czasie. [Samouczek jednostki rozkładalnej](tutorial-machine-learned-entity.md) pokazuje, jak korzystać z tej metody. 

W ramach planowania jednostek może być wiadomo, że potrzebne są jednostki dopasowywania tekstu (takie jak wstępnie utworzone jednostki, jednostki wyrażeń regularnych lub encje listy). Można je utworzyć ze strony **Jednostki,** zanim zostaną oznaczone w wypowiedzi przykład. 

Podczas etykietowania można oznaczyć poszczególne jednostki, a następnie utworzyć do jednostki nadrzędnej jednostki nauczona maszynowo. Można też rozpocząć od jednostki nadrzędnej jednostki nauczającej maszynę i rozkładać się na jednostki podrzędne. 

> [!TIP] 
>Etykieta wszystkich wyrazów, które mogą wskazywać jednostki, nawet jeśli wyrazy nie są używane podczas wyodrębniania w aplikacji klienckiej. 

## <a name="creating-an-entity-before-or-with-labeling"></a>Tworzenie encji przed etykietowaniem lub z etykietą

Poniższa tabela służy do zrozumienia, które jednostki, w których można utworzyć lub dodać każdą encję do aplikacji. 

|Typ jednostki|Gdzie utworzyć encję w portalu usługi LUIS|
|--|--|
|Jednostka nauczona maszynowo|Jednostki lub szczegóły intencji|
|Jednostka listy|Jednostki lub szczegóły intencji|
|Jednostka wyrażenia regularnego|Jednostki|
|Jednostka Pattern.any|Jednostki|
|Wstępnie skompilowana jednostka|Jednostki|
|Wstępnie skompilowana encja domeny|Jednostki|

Można utworzyć wszystkie jednostki ze strony **Jednostki** lub utworzyć kilka jednostek jako część etykietowania jednostki w wypowiedź przykład na **stronie szczegółów intencji.** Jednostkę można _oznaczyć_ tylko w przykładowym wypowiedź ze strony **szczegółów intencji.** 

## <a name="create-a-machine-learned-entity"></a>Tworzenie jednostki nauczającej maszyną

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>Tworzenie encji dopasowywania tekstu

Użyj jednostek dopasowywania tekstu zapewniają kilka sposobów wyodrębniania danych:

|Encje dopasowywania tekstu|Przeznaczenie|
|--|--|
|[Jednostka listy](#add-list-entities-for-exact-matches)|wykaz nazw kanonicznych wraz z synonimami jako alternatywne formy|
|Jednostka wyrażenia regularnego|dopasowywalanie tekstu przy użyciu encji wyrażenia regularnego|
|[Wstępnie skompilowana jednostka](tutorial-machine-learned-entity.md#add-prebuilt-number-to-help-extract-data)|dopasowywuj typowe typy danych, takie jak numer, adres e-mail, data|
|Wstępnie skompilowana encja domeny|dopasowywane przy użyciu wybranych domen tematów|
|[Wzór.any](#add-a-patternany-entity)| aby dopasować jednostki, które można łatwo pomylić z otaczającym tekstem|  

Wstępnie utworzone jednostki działają bez podawania żadnych niestandardowych danych szkoleniowych. Inne jednostki muszą podać dane szkoleniowe klienta (takie jak elementy jednostki listy) lub wyrażenie (takie jak wyrażenie regularne lub pattern.any).

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>Jak utworzyć nową encję niestandardową

1. W portalu usługi LUIS wyjmuje sekcję **Zarządzaj,** a następnie stronę **Jednostki.** 
1. Wybierz **+ Utwórz**, a następnie wybierz typ elementu. 
1. Kontynuuj konfigurowanie encji, a następnie wybierz pozycję **Utwórz** po zakończeniu. 

### <a name="add-list-entities-for-exact-matches"></a>Dodawanie encji listy dla dokładnych dopasowań

Encje listy reprezentują stały, zamknięty zestaw powiązanych słów. Podczas gdy ty, jako autor, można zmienić listę, usługa LUIS nie będzie rosnąć lub zmniejszać listę. Można również zaimportować do istniejącej encji listy przy użyciu [list entity .json format(reference-entity-list.md#example-json-to-import-into-list-entity). 

Na poniższej liście przedstawiono nazwę kanoniczną i synonimy. 

|Kolor — nazwa elementu listy|Kolor - synonimy|
|--|--|
|Red|szkarłatny, krew, jabłko, silnik strażacki|
|Blue|niebo, lazur, kobalt|
|Zielony|kelly, wapno|

Procedura służy do tworzenia encji listy. Po utworzeniu jednostki listy nie trzeba etykiety wypowiedzi przykład w intencji. Elementy listy i synonimy są dopasowywały się do tekstu. 

1. W sekcji **Kompilacja** wybierz **pozycję Elementy** w lewym panelu, a następnie wybierz pozycję **+ Utwórz**.

1. W oknie dialogowym **Tworzenie typu encji** wprowadź nazwę `Colors` encji, na przykład pozycję **Lista**.
1. W oknie **dialogowym Tworzenie encji listy** w **podlisty Dodawanie nowego....** `Green`wprowadź nazwę elementu listy, na przykład , a następnie dodaj synonimy.

    > [!div class="mx-imgBorder"]
    > ![Utwórz listę kolorów jako encję listy na stronie Szczegóły encji.](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. Po zakończeniu dodawania elementów listy i synonimów wybierz pozycję **Utwórz**.

    Po zakończeniu z grupą zmian w aplikacji, należy pamiętać, aby **wyszkolić** aplikację. Nie należy szkolić aplikacji po jednej zmianie. 

    > [!NOTE]
    > Ta procedura pokazuje tworzenie i etykietowanie jednostki listy z wypowiedź przykład na **stronie szczegółów intencji.** Można również utworzyć tę samą encję na stronie **Jednostki.**

## <a name="add-a-role-for-an-entity"></a>Dodawanie roli dla encji

Rola jest nazwanym podtypem jednostki, na podstawie kontekstu. 

### <a name="add-a-role-to-distinguish-different-contexts"></a>Dodawanie roli w celu odróżnienia różnych kontekstów

W poniższej wypowiedź, istnieją dwie lokalizacje, a każda jest określona `to` semantycznie przez słowa wokół niego, takie jak i: `from` 

`Pick up the package from Seattle and deliver to New York City.`

W tej procedurze dodaj `origin` i `destination` role do wstępnie utworzonej jednostki geografiiV2.

1. W sekcji **Kompilacja** wybierz **pozycję Elementy** w lewym panelu.

1. Wybierz **+ Dodaj wstępnie utworzony element**. Wybierz **geografięV2,** a następnie wybierz pozycję **Gotowe**. Spowoduje to dodanie wstępnie utworzonej jednostki do aplikacji.
    
    Jeśli zauważysz, że Twój wzorzec, gdy zawiera jednostkę Pattern.any, niepoprawnie wyodrębnia jednostki, użyj elementu [explicit list](reference-pattern-syntax.md#explicit-lists) (listy jawnej), aby rozwiązać ten problem. 

1. Wybierz nowo dodaną wstępnie utworzonej jednostki geografiiV2 z **listy jednostek jednostki.** 
1. Aby dodać nową rolę, wybierz obok **+** pozycji Brak **dodanych ról**.
1. W polach tekstowych **Typ roli...** wprowadź `Origin` nazwę roli, a następnie wprowadź. Dodaj drugą nazwę `Destination` roli, a następnie wprowadź. 

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający dodawanie roli Pochodzenie do encji Lokalizacja](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    Rola jest dodawana do wstępnie utworzonej jednostki, ale nie jest dodawana do żadnych wypowiedzi przy użyciu tej jednostki. 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Etykieta tekstu z rolą w wypowiedź przykład

1. Przejdź do intencji szczegóły strony, która ma wypowiedzi przykład, które używają roli. 
1. Aby oznaczyć etykietą z rolą, zaznacz etykietę jednostki (linia ciągła pod tekstem) w przykładowym wypowiedź, a następnie wybierz polecenie **Wyświetl w palecie encji** z listy rozwijanej. 

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający wybranie opcji Widok w palecie encji](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    Paleta jednostek zostanie otwarta po prawej stronie. 

1. Wybierz encję, a następnie przejdź do dolnej części palety i wybierz rolę. 

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający wybranie opcji Widok w palecie encji](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>Dodawanie elementu pattern.any

[Pattern.any](luis-concept-entity-types.md) jednostki są prawidłowe tylko w [wzorcach,](luis-how-to-model-intent-pattern.md)nie intencje wypowiedzi przykład. Ten typ jednostki pomaga usługi LUIS znaleźć koniec jednostek o różnej długości i wyboru wyrazów. Ponieważ ta jednostka jest używana we wzorcu, usługa LUIS wie, gdzie znajduje się koniec jednostki w szablonie wypowiedź.

### <a name="steps-to-create-a-patternany-entity"></a>Kroki tworzenia pattern.any encji

1. W sekcji **Kompilacja** wybierz **pozycję Elementy** w lewym panelu, a następnie wybierz pozycję **+ Utwórz**.

1. W oknie **dialogowym Wybieranie typu encji** wprowadź nazwę encji w polu **Nazwa** i wybierz pozycję **Deseń.Dowolny** jako **typ,** a następnie wybierz pozycję **Utwórz**.

    Po [utworzeniu wypowiedź wzorca](luis-how-to-model-intent-pattern.md) przy użyciu tej jednostki, jednostka jest wyodrębniana za pomocą połączonego algorytmu uczenia maszynowego i dopasowywania tekstu. 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>Tworzenie wypowiedź szablonu wzorca w celu użycia pattern.any jednostki

Aby użyć elementu pattern.any, dodaj wzorzec na stronie **Wzorce,** w sekcji **Poprawianie wydajności aplikacji,** `Where is **{HumanResourcesFormTitle}** on the server?`z poprawną składnią nawiasów klamrowych, taką jak .

Jeśli zauważysz, że Twój wzorzec, gdy zawiera jednostkę Pattern.any, niepoprawnie wyodrębnia jednostki, użyj elementu [explicit list](reference-pattern-syntax.md#explicit-lists) (listy jawnej), aby rozwiązać ten problem. 

## <a name="do-not-change-entity-type"></a>Nie zmieniaj typu encji

Usługa LUIS nie pozwala na zmianę typu jednostki, ponieważ nie wie, co dodać lub usunąć, aby utworzyć tę jednostkę. Aby zmienić typ, lepiej jest utworzyć nową jednostkę o poprawnym typie o nieco innej nazwie. Po utworzeniu jednostki w każdym wypowiedź usuń starą nazwę jednostki z etykietą i dodaj nową nazwę jednostki. Po wszystkie wypowiedzi zostały ponownie oznakowane, usuń starą jednostkę. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Korzystanie ze wstępnie utworzonych modeli](howto-add-prebuilt-models.md) 

Dowiedz się więcej o usługach:
* Jak [trenować](luis-how-to-train.md)
* Jak [przetestować](luis-interactive-test.md)
* Jak [publikować](luis-how-to-publish-app.md)
* Wzorców:
    * [Pojęcia](luis-concept-patterns.md)
    * [Składni](reference-pattern-syntax.md)
* [Wstępnie utworzone encje repozytorium GitHub](https://github.com/Microsoft/Recognizers-Text)
* [Pojęcia dotyczące wyodrębniania danych](luis-concept-data-extraction.md)


 
