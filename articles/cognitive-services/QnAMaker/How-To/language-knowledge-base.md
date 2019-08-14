---
title: Baza wiedzy w wersji innej niż angielska — QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker obsługuje zawartość bazy wiedzy w wielu językach. Jednak każda usługa QnA Maker powinna być zarezerwowana dla jednego języka. Pierwsza baza wiedzy utworzona jako przeznaczona do konkretnej usługi QnA Maker ustawia język tej usługi.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: diberry
ms.openlocfilehash: 26792246267ced6d9fff50fe4fea11cc8d280d6a
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966684"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Obsługa języka zawartości bazy wiedzy dla QnA Maker
QnA Maker obsługuje zawartość bazy wiedzy w wielu językach. Jednak każda usługa QnA Maker powinna być zarezerwowana dla jednego języka. Pierwsza baza wiedzy utworzona jako przeznaczona do konkretnej usługi QnA Maker ustawia język tej usługi. Zobacz [tutaj](../Overview/languages-supported.md) , aby zapoznać się z listą obsługiwanych języków.

Język jest rozpoznawany automatycznie z zawartości wyodrębnianych źródeł danych. Po utworzeniu nowej usługi QnA Maker i nowej bazy wiedzy w tej usłudze można sprawdzić, czy język został poprawnie ustawiony.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/).

2. Wybierz pozycję **grupy zasobów** i przejdź do grupy zasobów, w której wdrożono usługę QNA Maker, a następnie wybierz zasób **Azure Search** .

    ![Wybierz zasób Azure Search](../media/qnamaker-how-to-language-kb/select-azsearch.png)

3. Wybierz indeks **testkb** . Ten indeks Azure Search jest zawsze pierwszym utworzonym i zawiera zapisaną zawartość wszystkich baz wiedzy w tej usłudze. 

    ![Wybierz test KB](../media/qnamaker-how-to-language-kb/select-testkb.png)

4. Wybierz sekcję **pól** pokazującą szczegóły testkb.

    ![Wybierz pola](../media/qnamaker-how-to-language-kb/selectfields.png)

5. Zaznacz pole wyboru **Analizator** , aby wyświetlić szczegółowe informacje o języku.

    ![Wybór analizatora](../media/qnamaker-how-to-language-kb/select-analyzer.png)

6. Należy się dowiedzieć, że analizator jest ustawiony na określony język. Ten język został automatycznie wykryty podczas kroku tworzenia bazy wiedzy. Nie można zmienić tego języka po utworzeniu zasobu.

    ![Wybrany Analizator](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie QnA bot z Azure Bot Service](../Tutorials/create-qna-bot.md)
