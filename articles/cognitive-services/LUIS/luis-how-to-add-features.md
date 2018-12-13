---
title: Listy fraz
titleSuffix: Language Understanding - Azure Cognitive Services
description: Użyj Language Understanding (LUIS), aby dodać funkcje aplikacji, które może poprawić Prognozowanie intencje i podmioty lub wykrywania tej kategorii i wzorce
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 905970c3ab449f139710cf974111618f16da141a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086644"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>Użyj frazy Wyświetla sygnału boost listy programu word

Można dodać funkcje do aplikacji usługi LUIS, aby zwiększyć jego dokładność. Funkcje pomocy usługi LUIS, zapewniając wskazówek dotyczących tego określonych słów i fraz są częścią słownictwa domeny aplikacji. 

A [listy fraz](luis-concept-feature.md) znajduje się grupa wartości (słów i fraz), które należą do tej samej klasy i muszą być traktowane w podobny sposób (na przykład nazwy miasta lub produktów). Usługa LUIS się o jeden z nich jest automatycznie stosowany do pozostałych. Ta lista nie jest jednostką listy zamknięte (pasuje do tekstu do dokładnego dopasowania) dopasowane słów.

Lista fraz dodaje do słownictwa używanego w domenie aplikacji jako drugi sygnał do usługi LUIS o tych słów.

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
> Usuń lub dezaktywowanie listy fraz z paska narzędzi kontekstowych na **frazę list** strony.

## <a name="next-steps"></a>Kolejne kroki

Po Dodawanie, edytowanie, usuwanie lub dezaktywowanie listę frazy [uczenie i testowanie aplikacji](luis-interactive-test.md) ponownie, aby zobaczyć, czy wydajność się zwiększy.
