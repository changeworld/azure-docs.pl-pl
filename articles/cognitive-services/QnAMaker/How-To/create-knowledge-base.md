---
title: Tworzenie bazy wiedzy
titleSuffix: QnA Maker - Azure Cognitive Services
description: Użyj portalu narzędzia QnA Maker można dodać za pomocą czatu chit tworzenie bazy wiedzy. To sprawia, że aplikacja interesujące. Dodaj zestaw wstępnie wypełnionych najważniejsze rozmowy chit do wiedzy jako punkt wyjścia do czatu internetowego chit Twój bot i zaoszczędzić czas i pieniądze, zapisywania ich od podstaw.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 12/11/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 4a4f3d44454e7851f74bbeb072a9d4dcd600a6ef
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338128"
---
# <a name="quickstart-create-a-knowledge-base-using-the-qna-maker-portal"></a>Szybki Start: Tworzenie bazy wiedzy przy użyciu portalu narzędzia QnA Maker

Usługa QnA Maker pozwala w prosty sposób dodać istniejących źródeł danych, tworząc wiedzy. Możesz utworzyć nowe narzędzie QnA Maker wiedzy z następujących typów dokumentów:

<!-- added for scanability -->
* Często zadawane pytania dotyczące strony
* Podręczniki produktów
* Dokumenty ze strukturą

Obejmują osobowość chit rozmowy, aby wiedzę na temat więcej angażowanie użytkowników.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="create-a-new-knowledge-base"></a>Tworzenie nowej bazy wiedzy

1. Zaloguj się do [portalu narzędzia QnA Maker](https://qnamaker.ai) przy użyciu poświadczeń platformy Azure i wybierz **tworzenie bazy wiedzy**.

1. Jeśli nie utworzono już usługę QnA Maker, wybierz opcję **tworzenie pytań i odpowiedzi usługi**. 

1. Wybierz dzierżawy platformy Azure, Nazwa subskrypcji platformy Azure i nazwę zasobu platformy Azure skojarzoną z usługi QnA Maker z list w **kroku 2** w portalu narzędzia QnA Maker. Wybierz usługę Azure QnA Maker, który będzie hostem bazy wiedzy Knowledge Base.

    ![Usługa QnA Instalatora](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

1. Wprowadź nazwę bazy wiedzy i źródła danych dla nowej bazy wiedzy.

    ![Zestaw źródeł danych](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Nadaj usługi **nazwy.** Zduplikowane nazwy i znaki specjalne są obsługiwane.
    - Dodaj adresy URL dla danych, które mają zostać wyodrębnione. Zobacz więcej informacji na temat typów źródła obsługiwane [tutaj](../Concepts/data-sources-supported.md).
    - Przekaż pliki danych, które mają zostać wyodrębnione. Zobacz [informacje o cenach](https://aka.ms/qnamaker-pricing) aby zobaczyć, jak wiele dokumentów można dodać.
    - Jeśli chcesz ręcznie dodać znacznie, możesz pominąć **kroku 4** na poprzednim obrazie.

1. Dodaj **rozmowy Chit** do wiedzy. Możliwość obsługę rozmowy chit bota, wybierając jedną z osobowości 3. 

    ![Dodaj rozmowy chit KB ](../media/qnamaker-how-to-create-kb/create-kb-chit-chat.png)

1. Wybierz **tworzenie wiedzy**.

    ![Tworzenie bazy wiedzy](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Trwa kilka minut, zanim dane do wyodrębnienia.

    ![Wyodrębniania](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. Po pomyślnym utworzeniu wiedzy, nastąpi przekierowanie do **wiedzy** strony.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy skończysz z bazy wiedzy knowledge base, usuń go w portalu narzędzia QnA Maker.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dodaj osobiste chit rozmowy](./chit-chat-knowledge-base.md)
