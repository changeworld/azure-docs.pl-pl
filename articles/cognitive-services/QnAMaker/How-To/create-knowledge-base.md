---
title: Tworzenie usług kognitywnych Azure bazy wiedzy knowledge base - Maker — strona główna - | Dokumentacja firmy Microsoft
description: Jak utworzyć bazę wiedzy
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 93b64948ecc52feeb0f862f2b76ea898dce2333a
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "35355939"
---
# <a name="create-a-knowledge-base"></a>Tworzenie bazy wiedzy

Maker — strona główna ułatwia bardzo proste do dołączenia wykorzystywanie istniejących źródeł danych, aby utworzyć bazę wiedzy. Można utworzyć nowej wiedzy Maker — strona główna z często zadawane pytania dotyczące stron, podręczniki produktów, strukturalnych dokumentów lub dodaj je redakcyjna.

## <a name="steps"></a>Kroki

1. Aby rozpocząć, zaloguj się do [Maker — strona główna portalu](https://qnamaker.ai) przy użyciu poświadczeń platformy azure i kliknij przycisk **Utwórz nową usługę**.

    ![Tworzenie bazy wiedzy ](../media/qnamaker-how-to-create-kb/create-new-service.png)

2. Jeśli nie utworzono już Maker — strona główna usługi, wybierz **Tworzenie usługi — strona główna**. W przeciwnym razie wybierz usługę Maker — strona główna listach rozwijanych w kroku 2. Wybierz usługę — strona główna Maker, który będzie hostem bazy wiedzy Knowledge Base.

    ![Skonfiguruj usługę — strona główna](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

3. Wprowadź następujące informacje w celu utworzenia bazy wiedzy knowledge base.

    ![Zestaw źródeł danych](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Nadaj usługi **nazwy.** Zduplikowane nazwy są obsługiwane i znaki specjalne są również obsługiwane.
    - Wklej w adresach URL, które zostaną wyodrębnione ze. Wyświetlić więcej informacji na typy źródeł obsługiwane [tutaj](../Concepts/data-sources-supported.md).
    - Alternatywnie przekazać pliki, z których dane są wyodrębniane. Zobacz [uzyskać informacje o cenach](https://aka.ms/qnamaker-pricing
) aby zobaczyć, jak wiele dokumentów można dodać.
    - Jeśli chcesz ręcznie dodać QnAs, można pominąć, łączenia plików.

4. Wybierz pozycję **Utwórz**.

    ![Tworzenie bazy wiedzy](../media/qnamaker-how-to-create-kb/create-kb.png)

5. Trwa kilka minut wyodrębnić dane.

    ![Wyodrębniania](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

6. Po pomyślnym utworzeniu bazy wiedzy nastąpi przekierowanie do **wiedzy** strony.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Importowanie bazy wiedzy](../Tutorials/migrate-knowledge-base.md)
