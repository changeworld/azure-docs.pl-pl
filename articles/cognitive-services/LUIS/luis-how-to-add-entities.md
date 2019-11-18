---
title: Dodawanie jednostek — LUIS
titleSuffix: Azure Cognitive Services
description: Utwórz jednostki, aby wyodrębnić dane z wyrażenia długości użytkownika w aplikacjach Language Understanding (LUIS). Wyodrębnione dane jednostki są używane przez aplikację kliencką do fullfil żądań klientów.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 7de1a1e24c2863b90fe5f1f3ff19124318912cff
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132691"
---
# <a name="add-entities-to-extract-data"></a>Dodawanie jednostek do wyodrębniania danych 

Utwórz jednostki, aby wyodrębnić dane z wyrażenia długości użytkownika w aplikacjach Language Understanding (LUIS). Wyodrębnione dane jednostki są używane przez aplikację kliencką do fullfil żądań klientów.

Jednostka reprezentuje wyraz lub frazę w polu wypowiedź, który ma zostać wyodrębniony. Jednostki opisują informacje istotne dla intencji i czasami są istotne dla aplikacji w celu wykonania swojego zadania. Można utworzyć jednostki po dodaniu przykładu wypowiedź do zamiaru lub od (przed lub po) dodania przykładu wypowiedź do zamiaru.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="plan-entities-then-create-and-label"></a>Planowanie jednostek, a następnie Tworzenie i etykietowanie

Jednostki z możliwością uczenia maszynowego można utworzyć na podstawie przykładu wyrażenia długości lub utworzyć na stronie **jednostki** . 

Ogólnie rzecz biorąc, najlepszym rozwiązaniem jest poświęcanie czasu na planowanie jednostek przed utworzeniem jednostki, która jest wyszukiwana przez maszynę w portalu. Następnie utwórz jednostkę uczenia maszynowego na podstawie przykładu wypowiedź z jak najwięcej szczegółów w podskładnikach i deskryptorach i ograniczeniach, jak wiadomo w czasie. [Samouczek dotyczący tworzenia jednostki](tutorial-machine-learned-entity.md) pokazuje, jak używać tej metody. 

W ramach planowania jednostek, możesz mieć pewność, że potrzebujesz obiektów pasujących do tekstu (takich jak wstępnie skompilowane jednostki, jednostki wyrażeń regularnych lub jednostki listy). Można je utworzyć na podstawie strony **jednostki** , zanim zostaną one oznaczone jako przykładowe wyrażenia długości. 

Podczas etykietowania można oznaczyć poszczególne jednostki, a następnie skompilować do jednostki nadrzędnej. Można też rozpocząć od nadrzędnej maszyny i rozłożyć na jednostki podrzędne. 

> [!TIP] 
>Oznacz wszystkie słowa, które mogą wskazywać na jednostkę, nawet jeśli wyrazy nie są używane podczas wyodrębniania w aplikacji klienckiej. 

## <a name="creating-an-entity-before-or-with-labeling"></a>Tworzenie jednostki przed lub z etykietami

W poniższej tabeli przedstawiono jednostki, w których można utworzyć lub dodać każdą jednostkę do aplikacji. 

|Typ jednostki|Gdzie można utworzyć jednostkę w portalu LUIS|
|--|--|
|Jednostka nauczona maszynowo|Szczegóły jednostek lub zamiaru|
|Jednostka listy|Szczegóły jednostek lub zamiaru|
|Jednostka wyrażenia regularnego|Jednostki|
|Jednostka Pattern.any|Jednostki|
|Wstępnie utworzone jednostki|Jednostki|
|Prebudowana jednostka domeny|Jednostki|

Wszystkie jednostki można utworzyć ze strony **jednostki** lub można utworzyć kilka jednostek w ramach etykietowania jednostki w przykładowym wypowiedź na stronie **szczegółów** elementu. Można _oznaczyć_ tylko jednostkę w przykładowym wypowiedź na stronie **szczegółów zamierzenia** . 

## <a name="create-a-machine-learned-entity"></a>Tworzenie jednostki o Poznaniu maszynowym

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>Tworzenie jednostki dopasowania tekstu

Użycie jednostek dopasowania tekstu zapewnia kilka sposobów wyodrębnienia danych:

|Jednostki pasujące do tekstu|Przeznaczenie|
|--|--|
|[Jednostka listy](#add-list-entities-for-exact-matches)|Lista nazw kanonicznych wraz z synonimami jako alternatywnymi formularzami|
|Jednostka wyrażenia regularnego|dopasowanie tekstu przy użyciu jednostki wyrażenia regularnego|
|[Wstępnie utworzona jednostka](tutorial-machine-learned-entity.md#add-prebuilt-number-to-app-to-help-extract-data)|dopasowuje typowe typy danych, takie jak Number, email, Date|
|Prebudowana jednostka domeny|Dopasuj przy użyciu wybranych domen podmiotu|
|[Pattern.any](#add-a-patternany-entity)| Aby dopasować jednostki, które można łatwo pomylić z otaczającym tekstem|  

Wstępnie utworzone jednostki działają bez udostępniania niestandardowych danych szkoleniowych. Inne jednostki muszą podawać dane szkolenia klienta (takie jak elementy jednostki listy) lub wyrażenie (takie jak wyrażenie regularne lub wzorzec. any).

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>Jak utworzyć nową jednostkę niestandardową

1. W portalu LUIS przejdź do sekcji **Zarządzanie** , a następnie strony **jednostki** . 
1. Wybierz pozycję **+ Utwórz**, a następnie wybierz typ jednostki. 
1. Kontynuuj Konfigurowanie jednostki, a następnie wybierz pozycję **Utwórz** po zakończeniu. 

### <a name="add-list-entities-for-exact-matches"></a>Dodaj jednostki listy dla dokładnych dopasowań

Lista jednostek reprezentują stały, zamknięte zbiór powiązanych słów. Chociaż autor może zmienić listę, LUIS nie zostanie powiększony ani zmniejszony. Można również zaimportować do istniejącej jednostki listy przy użyciu formatu [list Entity. JSON (odwołanie-Entity-List. MD # example-JSON-to-list-Entity-on 

Na poniższej liście pokazano kanoniczną nazwę i synonimy. 

|Nazwa elementu listy kolorów|Kolory — synonimy|
|--|--|
|Czerwony|Użycie Crismon, krew, Apple, ogień|
|Niebieski|, platforma Azure, kobalt|
|Zielony|Kelly, limonowa|

Użyj procedury, aby utworzyć jednostkę listy. Po utworzeniu jednostki listy nie musisz oznaczać przykładu wyrażenia długości w zamiarze. Elementy listy i synonimy są dopasowywane przy użyciu dokładnego tekstu. 

1. W sekcji **kompilacja** wybierz pozycję **jednostki** w lewym panelu, a następnie wybierz pozycję **+ Utwórz**.

1. W oknie dialogowym **Tworzenie typu jednostki** wprowadź nazwę jednostki, np. `Colors` i wybierz pozycję **Lista**.
1. W oknie dialogowym **Tworzenie jednostki listy** , w polu **Dodaj nową podlistę.** ....., wprowadź nazwę elementu listy, taką jak `Green`, a następnie Dodaj synonimy.

    > [!div class="mx-imgBorder"]
    > ![utworzyć listę kolorów jako jednostkę listy na stronie szczegółów jednostki.](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. Po zakończeniu dodawania elementów listy i synonimów wybierz pozycję **Utwórz**.

    Po zakończeniu pracy z grupą zmian w aplikacji Pamiętaj, aby **nauczyć** aplikację. Nie szkol aplikacji po pojedynczej zmianie. 

    > [!NOTE]
    > Ta procedura ilustruje tworzenie i etykietowanie jednostki listy na podstawie przykładowej wypowiedź na stronie **szczegółów intencji** . Możesz również utworzyć tę samą jednostkę na stronie **jednostki** .

## <a name="add-a-role-for-an-entity"></a>Dodawanie roli dla jednostki

Rola jest nazwanym podtypem jednostki na podstawie kontekstu. 

### <a name="add-a-role-to-distinguish-different-contexts"></a>Dodawanie roli w celu rozróżnienia różnych kontekstów

W poniższym wypowiedź istnieją dwie lokalizacje, a każda z nich jest określona semantycznie przez wyrazy, takie jak `to` i `from`: 

`Pick up the package from Seattle and deliver to New York City.`

W tej procedurze Dodaj role `origin` i `destination` do wstępnie skompilowanej jednostki geographyV2.

1. Z **kompilacji** zaznacz **jednostek** w panelu po lewej stronie.

1. Wybierz pozycję **+ Dodaj wstępnie utworzoną jednostkę**. Wybierz pozycję **geographyV2** , a następnie wybierz pozycję **gotowe**. Spowoduje to dodanie wstępnie skompilowanej jednostki do aplikacji.
    
    Jeśli zauważysz, że Twój wzorzec, gdy zawiera jednostkę Pattern.any, niepoprawnie wyodrębnia jednostki, użyj elementu [explicit list](reference-pattern-syntax.md#explicit-lists) (listy jawnej), aby rozwiązać ten problem. 

1. Wybierz nowo dodaną wbudowaną jednostkę geographyV2 z listy **jednostki** jednostki. 
1. Aby dodać nową rolę, zaznacz opcję **+** obok pozycji **nie dodano żadnych ról**.
1. W polu tekstowym **Typ roli..** . Wprowadź nazwę roli, `Origin` następnie wprowadź wartość. Dodaj drugą nazwę roli `Destination` następnie wprowadź. 

    > [!div class="mx-imgBorder"]
    > ![zrzut ekranu przedstawiający Dodawanie roli pochodzenia do jednostki lokalizacji](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    Rola jest dodawana do wstępnie skompilowanej jednostki, ale nie jest dodawana do żadnych wyrażenia długości przy użyciu tej jednostki. 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Etykieta tekstu z rolą w przykładzie wypowiedź

1. Przejdź do strony szczegółów intencji, która ma przykład wyrażenia długości, który korzysta z roli. 
1. Aby oznaczyć rolę, wybierz etykietę jednostki (linię ciągłą w obszarze tekst) w przykładzie wypowiedź, a następnie wybierz pozycję **Wyświetl w palecie jednostek** z listy rozwijanej. 

    > [!div class="mx-imgBorder"]
    > ![zrzut ekranu przedstawiający wybór widoku w palecie jednostek](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    Paleta jednostek zostanie otwarta po prawej stronie. 

1. Wybierz jednostkę, a następnie przejdź do dolnej części palety i wybierz rolę. 

    > [!div class="mx-imgBorder"]
    > ![zrzut ekranu przedstawiający wybór widoku w palecie jednostek](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>Dodaj wzorzec. dowolna jednostka

[Wzorzec. wszystkie](luis-concept-entity-types.md) jednostki są prawidłowe tylko w [wzorcach](luis-how-to-model-intent-pattern.md), a nie na przykład wyrażenia długości. Ten typ jednostki pomaga LUIS Znajdź koniec jednostki o różnej długości i wybór programu word. Ponieważ tej jednostki są używane we wzorcu, LUIS wie, gdzie jest koniec jednostki w szablonie wypowiedź.

### <a name="steps-to-create-a-patternany-entity"></a>Procedura tworzenia wzorca. dowolna jednostka

1. W sekcji **kompilacja** wybierz pozycję **jednostki** w lewym panelu, a następnie wybierz pozycję **+ Utwórz**.

1. W oknie dialogowym **Wybierz typ jednostki** wprowadź nazwę jednostki w polu **Nazwa** , a następnie wybierz pozycję **wzorzec. dowolny** **Typ** , a następnie wybierz pozycję **Utwórz**.

    Po [utworzeniu wypowiedź wzorca](luis-how-to-model-intent-pattern.md) przy użyciu tej jednostki jednostka jest wyodrębniana z połączonym algorytmem zgodnym z maszyną i tekstem. 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>Utwórz szablon wzorca wypowiedź, aby użyć wzorca. dowolna jednostka

Aby użyć jednostki pattern.any, dodawanie wzorca na **wzorców** strony w **lepsza wydajność aplikacji** sekcji ze składnią poprawne nawiasów klamrowych, takich jak `Where is **{HumanResourcesFormTitle}** on the server?`.

Jeśli zauważysz, że Twój wzorzec, gdy zawiera jednostkę Pattern.any, niepoprawnie wyodrębnia jednostki, użyj elementu [explicit list](reference-pattern-syntax.md#explicit-lists) (listy jawnej), aby rozwiązać ten problem. 

## <a name="do-not-change-entity-type"></a>Nie zmieniaj typu jednostki

Usługa LUIS nie pozwala zmienić typ jednostki, ponieważ nie ma informacji, co należy dodać lub usunąć do skonstruowania tej jednostki. Aby zmienić typ, lepiej jest utworzyć nową jednostkę poprawnego typu o nazwie nieco inne. Po utworzeniu jednostki w każdym wypowiedź usuń starą nazwę jednostki oznaczone i dodać nową nazwę jednostki. Gdy ma zostać relabeled wszystkich wypowiedzi, należy usunąć stare jednostki. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o wzorcach:

* [Koncepcje wzorców](luis-concept-patterns.md)
* [Składnia wzorców](reference-pattern-syntax.md)

Aby uzyskać więcej informacji na temat wstępnie utworzonych jednostek, zobacz [aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text) projektu. 

Aby uzyskać informacji na temat sposobu wyświetlania jednostki w odpowiedzi na zapytanie punkt końcowy JSON, zobacz [wyodrębnianie danych](luis-concept-data-extraction.md)

Teraz, że dodano intencje, wypowiedzi i podmioty, masz podstawową aplikację usługi LUIS. Dowiedz się, jak [szkolenie](luis-how-to-train.md), [test](luis-interactive-test.md), i [publikowania](luis-how-to-publish-app.md) aplikacji.
 
