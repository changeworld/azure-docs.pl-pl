---
title: Tworzenie bazy wiedzy — QnA Maker
titleSuffix: Azure Cognitive Services
description: Dodawanie chit czatu do bota sprawia, że konwersacji i bardziej interesujące. Usługa QnA Maker umożliwia łatwe dodawanie wstępnie wypełnionych zbiór najważniejsze rozmowy chit, do wiedzy. To jest punkt wyjścia dla czatu internetowego chit Twój bot i zaoszczędzić czas i pieniądze, zapisywania ich od podstaw.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: 66705a0afdcdb04fe49bea0bfc03286df3611071
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2018
ms.locfileid: "45543362"
---
# <a name="create-a-knowledge-base"></a>Tworzenie bazy wiedzy

Narzędzie QnA Maker upraszcza bardzo dołączanie istniejących źródeł danych w taki sposób, aby utworzyć wiedzy. Można utworzyć nowej usługi QnA Maker wiedzy z stron — często zadawane pytania, podręczników produktów, ze strukturą dokumenty lub dodane przez jej tradycyjne zredagowanie.

## <a name="steps"></a>Kroki

1. Aby rozpocząć, zaloguj się do celu [portalu narzędzia QnA Maker](https://qnamaker.ai) przy użyciu poświadczeń platformy azure i kliknij pozycję **Utwórz nową usługę**.

    ![Tworzenie bazy wiedzy ](../media/qnamaker-how-to-create-kb/create-new-service.png)

2. Jeśli nie utworzono już usługę QnA Maker, wybierz opcję **tworzenie pytań i odpowiedzi usługi**. W przeciwnym razie wybierz usługę QnA Maker z listy rozwijane w kroku 2. Wybierz usługę QnA Maker, który będzie hostem bazy wiedzy Knowledge Base.

    ![Usługa QnA Instalatora](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

3. Wprowadź następujące informacje, aby można było utworzyć w bazie wiedzy knowledge base.

    ![Zestaw źródeł danych](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Nadaj usługi **nazwy.** Zduplikowane nazwy są obsługiwane i znaki specjalne są również obsługiwane.
    - Wklej w adresach URL, do których zostaną wyodrębnione z. Zobacz więcej informacji na temat typów źródła obsługiwane [tutaj](../Concepts/data-sources-supported.md).
    - Alternatywnie można przekazać pliki, z których dane są wyodrębniane. Zobacz [informacje o cenach](https://aka.ms/qnamaker-pricing
) aby zobaczyć, jak wiele dokumentów można dodać.
    - Jeśli chcesz ręcznie dodać znacznie, można pominąć, łączenia plików.

4. Wybierz pozycję **Utwórz**.

    ![Tworzenie bazy wiedzy](../media/qnamaker-how-to-create-kb/create-kb.png)

5. Trwa kilka minut, zanim dane do wyodrębnienia.

    ![Wyodrębniania](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

6. Jeśli wiedzy został utworzony pomyślnie, nastąpi przekierowanie do **wiedzy** strony.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Importowanie bazy wiedzy](../Tutorials/migrate-knowledge-base.md)
