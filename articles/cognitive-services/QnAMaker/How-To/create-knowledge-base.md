---
title: Tworzenie bazy wiedzy — QnA Maker
titleSuffix: Azure Cognitive Services
description: Dodawanie chit czatu do bota sprawia, że konwersacji i bardziej interesujące. Usługa QnA Maker umożliwia łatwe dodawanie wstępnie wypełnionych zbiór najważniejsze rozmowy chit, do wiedzy. To jest punkt wyjścia dla czatu internetowego chit Twój bot i zaoszczędzić czas i pieniądze, zapisywania ich od podstaw.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 274c2289c75f44c5a1c8dd3799612a23f46a6d67
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037698"
---
# <a name="create-a-knowledge-base"></a>Tworzenie bazy wiedzy

Usługa QnA Maker pozwala w prosty sposób dodać istniejących źródeł danych, tworząc wiedzy. Możesz utworzyć nowe narzędzie QnA Maker wiedzy z następujących typów dokumentów:

<!-- added for scanability -->
* Często zadawane pytania dotyczące strony
* Podręczniki produktów
* Dokumenty ze strukturą

## <a name="steps"></a>Kroki

1. Zaloguj się do [portalu narzędzia QnA Maker](https://qnamaker.ai) przy użyciu poświadczeń platformy Azure i wybierz **Utwórz nową usługę**.

    ![Tworzenie bazy wiedzy ](../media/qnamaker-how-to-create-kb/create-new-service.png)

2. Jeśli nie utworzono już usługę QnA Maker, wybierz opcję **tworzenie pytań i odpowiedzi usługi**. 

3. Wybierz dzierżawy platformy Azure, Nazwa subskrypcji platformy Azure i nazwę zasobu platformy Azure skojarzoną z usługi QnA Maker z list w **kroku 2** w portalu narzędzia QnA Maker. Wybierz usługę Azure QnA Maker, który będzie hostem bazy wiedzy Knowledge Base.

    ![Usługa QnA Instalatora](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

4. Wprowadź nazwę bazy wiedzy i źródła danych dla nowej bazy wiedzy.

    ![Zestaw źródeł danych](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Nadaj usługi **nazwy.** Zduplikowane nazwy i znaki specjalne są obsługiwane.
    - Dodaj adresy URL dla danych, które mają zostać wyodrębnione. Zobacz więcej informacji na temat typów źródła obsługiwane [tutaj](../Concepts/data-sources-supported.md).
    - Przekaż pliki danych, które mają zostać wyodrębnione. Zobacz [informacje o cenach](https://aka.ms/qnamaker-pricing) aby zobaczyć, jak wiele dokumentów można dodać.
    - Jeśli chcesz ręcznie dodać znacznie, możesz pominąć **kroku 4** na poprzednim obrazie.

5. Dodaj **rozmowy Chit** do wiedzy. Możliwość obsługę rozmowy chit bota, wybierając jedną z 3 osobowości wstępnie zdefiniowane. 

    <!-- TBD: add back in when chit chat how-to is merged
    ![Add chit-chat to KB ](../media/qnamaker-how-to-chitchat/create-kb-chit-chat.png)
    -->

6. Wybierz **tworzenie wiedzy**.

    ![Tworzenie bazy wiedzy](../media/qnamaker-how-to-create-kb/create-kb.png)

7. Trwa kilka minut, zanim dane do wyodrębnienia.

    ![Wyodrębniania](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

8. Po pomyślnym utworzeniu wiedzy, nastąpi przekierowanie do **wiedzy** strony.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dodaj osobiste chit rozmowy](./chit-chat-knowledge-base.md)
