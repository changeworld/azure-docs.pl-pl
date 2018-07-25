---
title: Dodawanie funkcji w aplikacjach usługi LUIS | Dokumentacja firmy Microsoft
description: Użyj Language Understanding (LUIS), aby dodać funkcje aplikacji, które może poprawić Prognozowanie intencje i podmioty lub wykrywania tej kategorii i wzorce
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/30/2018
ms.author: diberry
ms.openlocfilehash: 5ec75436c7df5c08f5507794229bec1f9adb2804
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222957"
---
# <a name="use-features-to-improve-your-luis-apps-performance"></a>Używanie funkcji w celu zwiększenia wydajności aplikacją usługi LUIS  

Można dodać funkcje do aplikacji usługi LUIS, aby zwiększyć jego dokładność. Funkcje pomocy usługi LUIS, zapewniając wskazówek dotyczących tego określonych słów i fraz należą do kategorii. Jeśli usługa LUIS uczy się, jak rozpoznać jednego członka kategorii, to traktowane innych podobnie.

## <a name="add-phrase-list"></a>Dodawanie listy fraz

1. Otwórz aplikację, klikając jego nazwę **Moje aplikacje** strony, a następnie kliknij przycisk **kompilacji**, następnie kliknij przycisk **frazę list** w panelu po lewej stronie Twojej aplikacji. 

    ![Fraza listy nawigacji](./media/luis-add-features/phrase-list-nav.png)

2. Na **frazę list** kliknij **Utwórz nową listę frazy**. 
 
    ![Tworzenie nowej listy fraz](./media/luis-add-features/create-new-phrase-list.png)
    
3. W **Dodaj frazy listy** okna dialogowego wpisz "Miast" jako nazwy listy fraz. W **wartość** wpisz wartości listy fraz. Wpisz jedną wartość lub zbiór wartości oddzielonych przecinkami, a następnie naciśnij klawisz **Enter**.

    ![Dodaj frazy listę miast](./media/luis-add-features/add-phrase-list-cities.png)

4. Usługa LUIS może zaproponować powiązanych wartości do dodania do listy fraz. Kliknij przycisk **zaleca się** można pobrać grupy proponowane wartości, które są semantycznie związane added value(s). Kliknij dowolną z wartości proponowane, lub kliknij przycisk **Dodaj wszystkie** Aby dodać je wszystkie.

    ![Lista fraz proponowane wartości](./media/luis-add-features/related-values.png)

5. Kliknij przycisk **te wartości są wymienne** Jeśli frazy dodano wartości listy są dostępne opcje alternatywne, które mogą być używane zamiennie.

    ![Lista fraz proponowane wartości](./media/luis-add-features/interchangeable.png)

6. Kliknij pozycję **Zapisz**. Na liście frazę "Miast" zostanie dodany do **frazę list** strony.

    ![Lista fraz dodane](./media/luis-add-features/phrase-list-cities.png)

## <a name="edit-phrase-list"></a>Edytowanie listy fraz

Kliknij nazwę listy fraz **frazę list** strony. W **Edytuj listę frazy** okno dialogowe zostanie otwarte, wprowadzając dowolne wymagane do edycji zmiany, a następnie kliknij przycisk **Zapisz**.

 ![Lista fraz dodane](./media/luis-add-features/edit-phrase-list.png)

## <a name="delete-phrase-list"></a>Usuwanie listy fraz 

Kliknij przycisk wielokropka (***...*** ) znajdujący się na końcu wiersza i wybierz **Usuń**.

 ![Usuń listę dodane](./media/luis-add-features/delete-phrase-list.png)

## <a name="deactivate-phrase-list"></a>Dezaktywuj listy fraz 

Kliknij przycisk wielokropka (***...*** ) znajdujący się na końcu wiersza i wybierz **Dezaktywuj**.

 ![Dezaktywuj listy dodanych](./media/luis-add-features/deactivate-phrase-list.png)

## <a name="pattern-regular-expression-feature"></a>Funkcja wzorca (wyrażenie regularne) 
**Ta funkcja jest przestarzała**. Nie można dodać nowe funkcje wzorzec do usługi LUIS. Wszystkie istniejące funkcje wzorzec są obsługiwane do maja 2018 r. Przyczynia się do standardowych LUIS dopasowania wyrażenia regularnego przy użyciu żądania Ściągnięcia do [repozytorium Github aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text). 

## <a name="next-steps"></a>Kolejne kroki

Po Dodawanie, edytowanie, usuwanie lub dezaktywowanie listę frazy [uczenie i testowanie aplikacji](luis-interactive-test.md) ponownie, aby zobaczyć, czy wydajność się zwiększy.
