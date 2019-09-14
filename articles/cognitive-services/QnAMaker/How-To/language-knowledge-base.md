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
ms.date: 09/12/2019
ms.author: diberry
ms.openlocfilehash: 5e50c814fef24aa799549d055ad6496f5bdf05e0
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70961475"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Obsługa języka zawartości bazy wiedzy dla QnA Maker

QnA Maker obsługuje zawartość bazy wiedzy w wielu językach. Jednak każda usługa QnA Maker powinna być zarezerwowana dla jednego języka. Pierwsza baza wiedzy utworzona jako przeznaczona do konkretnej usługi QnA Maker ustawia język tej usługi. Zobacz [tutaj](../Overview/languages-supported.md) , aby zapoznać się z listą obsługiwanych języków.

Język jest rozpoznawany automatycznie z zawartości wyodrębnianych źródeł danych. Po utworzeniu nowej usługi QnA Maker i nowej bazy wiedzy w tej usłudze można sprawdzić, czy język został poprawnie ustawiony.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/).

1. Wybierz pozycję **grupy zasobów** i przejdź do grupy zasobów, w której wdrożono usługę QNA Maker, a następnie wybierz zasób **Azure Search** .

    ![Wybierz zasób Azure Search](../media/qnamaker-how-to-language-kb/select-azsearch.png)

1. Wybierz pozycję **indeksy**, a następnie wybierz indeks **testkb** . Jest to pierwszy utworzony indeks Azure Search i zawiera zapisaną zawartość wszystkich baz wiedzy w tej usłudze. 

1. Wybierz **pola** , aby wyświetlić pola w indeksie.

1. `answer` Kolumna _analizatora_ pól ijestustawionanaokreślonyjęzyk.`questions` Ten język został automatycznie wykryty podczas kroku tworzenia bazy wiedzy z zaimportowanych plików i adresów URL. Nie można zmienić tego języka po utworzeniu zasobu.

    ![Wybrany Analizator](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie QnA bot z Azure Bot Service](../Tutorials/create-qna-bot.md)
