---
title: Wyświetla frazy, aby poprawić wykrywanie przez jednostki
titleSuffix: Azure Cognitive Services
description: Użyj Language Understanding (LUIS), aby dodać funkcje aplikacji, które może poprawić Prognozowanie intencje i podmioty lub wykrywania tej kategorii i wzorce
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: 0fe4e1c64d1d443148f1d0a8ba2a9856e3566f30
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44052619"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>Użyj frazy Wyświetla sygnału boost listy programu word

Można dodać funkcje do aplikacji usługi LUIS, aby zwiększyć jego dokładność. Funkcje pomocy usługi LUIS, zapewniając wskazówek dotyczących tego określonych słów i fraz są częścią słownictwa domeny aplikacji. 

## <a name="add-phrase-list"></a>Dodawanie listy fraz

1. Otwórz aplikację, klikając jego nazwę **Moje aplikacje** strony, a następnie kliknij przycisk **kompilacji**, następnie kliknij przycisk **frazę list** w panelu po lewej stronie Twojej aplikacji. 

2. Na **frazę list** kliknij **Utwórz nową listę frazy**. 
 
3. W **Dodaj frazy listy** okna dialogowego wpisz "Miast" jako nazwy listy fraz. W **wartość** wpisz wartości listy fraz. Wpisz jedną wartość lub zbiór wartości oddzielonych przecinkami, a następnie naciśnij klawisz **Enter**.

    ![Dodaj frazy listę miast](./media/luis-add-features/add-phrase-list-cities.png)

4. Usługa LUIS może zaproponować powiązanych wartości do dodania do listy fraz. Kliknij przycisk **zaleca się** można pobrać grupy proponowane wartości, które są semantycznie związane added value(s). Kliknij dowolną z wartości proponowane, lub kliknij przycisk **Dodaj wszystkie** Aby dodać je wszystkie.

    ![Lista fraz proponowane wartości](./media/luis-add-features/related-values.png)

5. Kliknij przycisk **te wartości są wymienne** Jeśli frazy dodano wartości listy są dostępne opcje alternatywne, które mogą być używane zamiennie.

    ![Lista fraz proponowane wartości](./media/luis-add-features/interchangeable.png)

6. Kliknij pozycję **Zapisz**. Na liście frazę "Miast" zostanie dodany do **frazę list** strony.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Edytuj, usuń lub dezaktywowanie listy fraz z wielokropek (***...*** ) przycisk na końcu wiersza eac frazy listy.

## <a name="pattern-regular-expression-feature"></a>Funkcja wzorca (wyrażenie regularne) 
**Ta funkcja jest przestarzała**. Nie można dodać nowe funkcje wzorzec do usługi LUIS. Wszystkie istniejące funkcje wzorzec są obsługiwane do maja 2018 r. Przyczynia się do standardowych LUIS dopasowania wyrażenia regularnego przy użyciu żądania Ściągnięcia do [repozytorium Github aparatów rozpoznawania tekstu](https://github.com/Microsoft/Recognizers-Text). 

## <a name="next-steps"></a>Kolejne kroki

Po Dodawanie, edytowanie, usuwanie lub dezaktywowanie listę frazy [uczenie i testowanie aplikacji](luis-interactive-test.md) ponownie, aby zobaczyć, czy wydajność się zwiększy.
