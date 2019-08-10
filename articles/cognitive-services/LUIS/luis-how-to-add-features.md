---
title: Listy fraz — LUIS
titleSuffix: Azure Cognitive Services
description: Użyj Language Understanding (LUIS), aby dodać funkcje aplikacji, które może poprawić Prognozowanie intencje i podmioty lub wykrywania tej kategorii i wzorce
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: f51f5a8583a73219ffb419c76fcd009d102f6ffb
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932911"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>Użyj frazy Wyświetla sygnału boost listy programu word

Można dodać funkcje do aplikacji usługi LUIS, aby zwiększyć jego dokładność. Funkcje pomocy usługi LUIS, zapewniając wskazówek dotyczących tego określonych słów i fraz są częścią słownictwa domeny aplikacji. 

A [listy fraz](luis-concept-feature.md) znajduje się grupa wartości (słów i fraz), które należą do tej samej klasy i muszą być traktowane w podobny sposób (na przykład nazwy miasta lub produktów). Usługa LUIS się o jeden z nich jest automatycznie stosowany do pozostałych. Ta lista nie jest taka sama jak [Jednostka listy](reference-entity-list.md) (dokładne dopasowanie tekstu) pasujących wyrazów.

Lista fraz dodaje do słownictwa używanego w domenie aplikacji jako drugi sygnał do usługi LUIS o tych słów.

## <a name="add-phrase-list"></a>Dodawanie listy fraz

LUIS umożliwia maksymalnie 10 list fraz na aplikację. 

1. Otwórz aplikację, klikając jego nazwę **Moje aplikacje** strony, a następnie kliknij przycisk **kompilacji**, następnie kliknij przycisk **frazę list** w panelu po lewej stronie Twojej aplikacji. 

1. Na **frazę list** kliknij **Utwórz nową listę frazy**. 
 
1. W oknie dialogowym **Dodawanie listy fraz** wpisz `Cities` jako nazwę listy fraz. W **wartość** wpisz wartości listy fraz. Wpisz jedną wartość lub zbiór wartości oddzielonych przecinkami, a następnie naciśnij klawisz **Enter**.

    ![Dodaj frazy listę miast](./media/luis-add-features/add-phrase-list-cities.png)

1. Usługa LUIS może zaproponować powiązanych wartości do dodania do listy fraz. Kliknij przycisk **zaleca się** można pobrać grupy proponowane wartości, które są semantycznie związane added value(s). Kliknij dowolną z wartości proponowane, lub kliknij przycisk **Dodaj wszystkie** Aby dodać je wszystkie.

    ![Proponowane wartości listy fraz — Dodaj wszystko](./media/luis-add-features/related-values.png)

1. Kliknij przycisk **te wartości są wymienne** Jeśli frazy dodano wartości listy są dostępne opcje alternatywne, które mogą być używane zamiennie.

    ![Proponowane wartości na liście fraz — wybierz pole, które jest zamienne](./media/luis-add-features/interchangeable.png)

1. Kliknij przycisk **Gotowe**. Na liście frazę "Miast" zostanie dodany do **frazę list** strony.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Usuń lub dezaktywowanie listy fraz z paska narzędzi kontekstowych na **frazę list** strony.

## <a name="next-steps"></a>Kolejne kroki

Po Dodawanie, edytowanie, usuwanie lub dezaktywowanie listę frazy [uczenie i testowanie aplikacji](luis-interactive-test.md) ponownie, aby zobaczyć, czy wydajność się zwiększy.
