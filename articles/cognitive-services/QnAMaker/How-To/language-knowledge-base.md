---
title: Innej niż angielska bazy wiedzy knowledge base — QnA Maker
titleSuffix: Azure Cognitive Services
description: Usługa QnA Maker obsługuje zawartość bazy wiedzy w wielu językach. Jednak każda usługa QnA Maker ma być zarezerwowana dla jednego języka. Pierwszy wiedzy utworzone przeznaczonych dla określonej usługi QnA Maker określa język tej usługi.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.openlocfilehash: f6c317cc1281a5a9bc18a2057fa12b7b61bb7689
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61372061"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Obsługa języków w bazie wiedzy knowledge base zawartości usługi QnA Maker
Usługa QnA Maker obsługuje zawartość bazy wiedzy w wielu językach. Jednak każda usługa QnA Maker ma być zarezerwowana dla jednego języka. Pierwszy wiedzy utworzone przeznaczonych dla określonej usługi QnA Maker określa język tej usługi. Zobacz [tutaj](../Overview/languages-supported.md) uzyskać listę obsługiwanych języków.

Język jest rozpoznawany automatycznie z zawartości źródła danych wyodrębniania. Po utworzeniu nowego usługa QnA Maker i nowej wiedzy w tej usłudze, możesz sprawdzić, czy język został prawidłowo ustawiony.

1. Przejdź do [witryny Azure Portal](https://portal.azure.com/).

2. Wybierz **grup zasobów** i przejdź do grupy zasobów, w których ta usługa QnA Maker jest wdrożone i wybierz **usługi Azure Search** zasobów.

    ![Wybierz zasób usługi Azure Search](../media/qnamaker-how-to-language-kb/select-azsearch.png)

3. Wybierz **testkb** indeksu. Ten indeks usługi Azure Search jest zawsze pierwszej utworzone i zawiera zapisane treści baz wiedzy w tej usłudze. 

    ![Wybierz Test KB](../media/qnamaker-how-to-language-kb/select-testkb.png)

4. Wybierz **pola** wyświetlania szczegółów testkb sekcji.

    ![Wybierz pola](../media/qnamaker-how-to-language-kb/selectfields.png)

5. Pole wyboru dla **analizatora** Aby wyświetlić szczegółowe dane języków.

    ![Wybierz Analizator](../media/qnamaker-how-to-language-kb/select-analyzer.png)

6. Powinien znajdować się ustawiono analizatora określonego języka. Ten język został automatycznie wykryty podczas wykonywania kroku tworzenie bazy wiedzy knowledge base. Ten język nie można zmienić po utworzeniu zasobu.

    ![Wybrane analizatora](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Utwórz bota pytań i odpowiedzi z usługi Azure Bot Service](../Tutorials/create-qna-bot.md)
