---
title: 'Szybki start: dodawanie pytań i odpowiedzi w portalu programu QnA Maker'
description: Ten przewodnik Szybki start pokazuje, jak dodać zestawy pytań i odpowiedzi z metadanymi, aby użytkownicy mogli znaleźć właściwą odpowiedź na swoje pytanie.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: f067bae55c38fc783c12bf9d0bc6fbcdf881e4e4
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756687"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Szybki start: dodawanie pytań i odpowiadanie za pomocą portalu QnA Maker

Po utworzeniu bazy wiedzy dodaj zestawy pytań i odpowiedzi (QnA) z metadanymi, aby filtrować odpowiedź. Pytania w poniższej tabeli dotyczą limitów usług platformy Azure, ale każdy ma do czynienia z inną usługą platformy Azure.

<a name="qna-table"></a>

|Set|Pytania|Odpowiedź|Metadane|
|--|--|--|--|
|1.|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|2.|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

Po dodaniu metadanych do pary QnA aplikacja kliencka może:

* Żądaj odpowiedzi, które pasują tylko do określonych metadanych.
* Otrzymuj wszystkie odpowiedzi, ale po przetworze odpowiedzi w zależności od metadanych dla każdej odpowiedzi.


## <a name="prerequisites"></a>Wymagania wstępne

* Ukończ [poprzedni szybki start](./create-publish-knowledge-base.md)

## <a name="sign-in-to-the-qna-maker-portal"></a>Zaloguj się do portalu QnA Maker

1. Zaloguj się do [portalu QnA Maker](https://www.qnamaker.ai).

1. Wybierz istniejącą bazę wiedzy z [poprzedniego przewodnika Szybki start](../how-to/create-knowledge-base.md).

## <a name="add-additional-alternatively-phrased-questions"></a>Dodawanie dodatkowych pytań z wyrażeniami alternatywnymi

Aktualna baza wiedzy ma QnA Maker rozwiązywania problemów z parami QnA. Te zestawy zostały utworzone, gdy adres URL został dodany do bazy wiedzy podczas procesu tworzenia.

Podczas importowania tego adresu URL utworzono tylko jedno pytanie z jedną odpowiedzią. W tej procedurze dodaj dodatkowe pytania.

1. Na stronie **Edycja** użyj pola tekstowego wyszukiwania nad zestawami pytań i odpowiedzi, aby znaleźć pytanie`How large a knowledge base can I create?`

1. W kolumnie **Pytanie** wybierz + **Dodaj zwroty alternatywne,** a następnie dodaj każde nowe sformułowanie podane w poniższej tabeli.

    |Alternatywne zwroty|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. Wybierz **zapisz i trenuj,** aby przeszkolić bazę wiedzy.

1. Wybierz **opcję Testuj**, a następnie wprowadź pytanie, które jest bliskie jednemu z nowych alternatywnych zwrotów, ale nie jest dokładnie tym samym sformułowaniem:

    `What GB size can a knowledge base be?`

    Poprawna odpowiedź jest zwracana w formacie znaczników:

    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    Jeśli wybierzesz **Inspect** pod zwróconą odpowiedzią, możesz zobaczyć więcej odpowiedzi na pytanie, ale nie z tym samym wysokim poziomem zaufania.

    Nie należy dodawać każdej możliwej kombinacji alternatywnych zwrotów. Po włączeniu [aktywnego uczenia się](../how-to/improve-knowledge-base.md)QnA Maker znajdzie alternatywne zwroty, które najlepiej pomogą Twojej bazie wiedzy zaspokoić potrzeby użytkowników.

1. Wybierz **opcję Testuj** ponownie, aby zamknąć okno testu.

## <a name="add-metadata-to-filter-the-answers"></a>Dodawanie metadanych w celu filtrowania odpowiedzi

Dodawanie metadanych do zestawu pytań i odpowiedzi umożliwia aplikacji klienckiej żądanie filtrowanych odpowiedzi. Ten filtr jest stosowany przed zastosowaniem [pierwszej i drugiej rangi.](../concepts/query-knowledge-base.md#ranker-process)

1. Dodaj drugie pytanie i zestaw odpowiedzi, bez metadanych, z [pierwszej tabeli w tym przewodniku Szybki start,](#qna-table)a następnie przejdź do następujących kroków.

1. Wybierz pozycję **Opcje widoku**, a następnie wybierz pozycję **Pokaż metadane**.

1. Dla właśnie dodanej pary QnA wybierz **pozycję Dodaj tagi metadanych,** a następnie dodaj nazwę `service` i wartość . `search` Wygląda to tak: `service:search`.

1. Dodaj kolejny tag metadanych `link_in_answer` o `false`nazwie i wartości pliku . Wygląda to tak: `link_in_answer:false`.

1. Wyszukaj pierwszą odpowiedź `How large a knowledge base can I create?`w tabeli, .

1. Dodawanie par metadanych dla tych samych dwóch tagów metadanych:

    `link_in_answer` : `true`<br>
    `server`: `qna_maker`

    Masz teraz dwa pytania z tymi samymi metadanymi tagami o różnych wartościach.

1. Wybierz **zapisz i trenuj,** aby przeszkolić bazę wiedzy.

1. Wybierz **pozycję Publikuj** w górnym menu, aby przejść do strony publikowania.
1. Wybierz przycisk **Publikuj,** aby opublikować bieżącą bazę wiedzy w punkcie końcowym.
1. Po opublikowaniu bazy wiedzy przejdź do następnego przewodnika Szybki start, aby dowiedzieć się, jak wygenerować odpowiedź z bazy wiedzy.

## <a name="what-did-you-accomplish"></a>Co udało ci się osiągnąć?

Edytowałeś swoją bazę wiedzy, aby obsługiwać więcej pytań i podałeś pary nazw/wartości do obsługi filtrowania podczas wyszukiwania odpowiedzi górnej lub postprocessingu po zwróceniu odpowiedzi lub odpowiedzi.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie kontynuujesz następnego przewodnika Szybki start, usuń zasoby platformy QnA Maker i Bot w witrynie Azure portal.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uzyskiwanie odpowiedzi przy użyciu narzędzia Postman lub cURL](get-answer-from-knowledge-base-using-url-tool.md)