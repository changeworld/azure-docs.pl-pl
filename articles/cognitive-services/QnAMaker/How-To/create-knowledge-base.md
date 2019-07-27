---
title: Tworzenie bazy wiedzy — QnA Maker
titleSuffix: Azure Cognitive Services
description: Użyj portalu usługi interfejs API usługi QnA Maker, aby dodać Tworzenie bazy wiedzy z Chit-Chat. Spowoduje to zaangażowanie aplikacji. Dodaj wstępnie wypełniony zestaw najpopularniejszych Chit-Chat do swojej bazy wiedzy jako punkt wyjścia dla Chit — rozmowy i Oszczędź czas i koszt ich zapisania od podstaw.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/10/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: dfd0663c09b78539a1d6b78aa7820385b9ddbdca
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563086"
---
# <a name="quickstart-create-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>Szybki start: Tworzenie bazy wiedzy przy użyciu portalu usługi interfejs API usługi QnA Maker

Portal usługi interfejs API usługi QnA Maker ułatwia dodawanie istniejących źródeł danych podczas tworzenia bazy wiedzy. Możesz utworzyć nowe narzędzie QnA Maker wiedzy z następujących typów dokumentów:

<!-- added for scanability -->
* Często zadawane pytania dotyczące strony
* Podręczniki produktów
* Dokumenty ze strukturą

Uwzględnij osobowość chit-chat, aby Twoja wiedza była bardziej interesująca dla użytkowników.

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

1. Dodaj **rozmowy Chit** do wiedzy. Wybierz, aby dodać obsługę Chit-Chat dla bot, wybierając jedną z osobistych. 

    ![Dodawanie Chit-Chat do bazy wiedzy](../media/qnamaker-how-to-create-kb/create-kb-chit-chat.png)

1. Wybierz **tworzenie wiedzy**.

    ![Tworzenie bazy wiedzy](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Trwa kilka minut, zanim dane do wyodrębnienia.

    ![Wyodrębniania](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. Po pomyślnym utworzeniu wiedzy, nastąpi przekierowanie do **wiedzy** strony.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu pracy z bazą wiedzy usuń ją w portalu QnA Maker.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać środki oszczędnościowe, możesz [udostępnić](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker) kilka zasobów platformy Azure utworzonych dla QNA Maker.

> [!div class="nextstepaction"]
> [Dodaj osobiste chit rozmowy](./chit-chat-knowledge-base.md)
