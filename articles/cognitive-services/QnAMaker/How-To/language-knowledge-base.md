---
title: Tworzenie usług kognitywnych Azure wiedzy z systemem innym niż angielski - Maker — strona główna - | Dokumentacja firmy Microsoft
description: Jak utworzyć bazę wiedzy z systemem innym niż angielski.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 3fbd590229044af0daa60968fd8d556d539a58c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348652"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Obsługa języka zawartości wiedzy Maker — strona główna
Maker — strona główna obsługuje wiedzy zawartości w wielu językach. Jednak każdej usługi Maker — strona główna ma być zarezerwowana dla jednego języka. Pierwszy wiedzy utworzone przeznaczonych dla określonej usługi Maker — strona główna ustawia język tej usługi. Zobacz [tutaj](../Overview/languages-supported.md) listę obsługiwanych języków.

Język jest rozpoznawany automatycznie z zawartości wyodrębniania źródeł danych. Po utworzeniu nowej usługi Maker — strona główna i nowej wiedzy tej usługi, można sprawdzić, czy język został poprawnie ustawiony.

1. Przejdź do [portalu Azure](https://portal.azure.com/).

2. Wybierz **grup zasobów** i przejdź do grupy zasobów, w którym usługa Maker — strona główna jest wdrożone i wybierz **usługi Azure Search** zasobów.

    ![Wybierz zasób usługi Azure Search](../media/qnamaker-how-to-language-kb/select-azsearch.png)

3. Wybierz **testkb** indeksu. Ten indeks usługi Azure Search jest zawsze pierwszy utworzony i zawiera zapisane zawartości wszystkich zasad wiedzy w tej usłudze. 

    ![Wybierz Test KB](../media/qnamaker-how-to-language-kb/select-testkb.png)

4. Wybierz **pola** zawierającego szczegóły testkb sekcji.

    ![Wybierz pola](../media/qnamaker-how-to-language-kb/selectfields.png)

5. Pole wyboru dla **analizator** Aby wyświetlić szczegółowe dane języków.

    ![Wybierz analizatora](../media/qnamaker-how-to-language-kb/select-analyzer.png)

6. Powinien znajdować się analizatora ustawiono konkretnego języka. Podczas wykonywania kroku tworzenia wiedzy automatycznie wykryto tego języka. Ten język nie można zmienić po utworzeniu zasobu.

    ![Wybrane analizatora](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie za pomocą usługi Azure Bot bot — strona główna](../Tutorials/create-qna-bot.md)
