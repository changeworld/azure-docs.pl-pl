---
title: Migrowanie baz wiedzy — QnA Maker
titleSuffix: Azure Cognitive Services
description: Migrowanie bazy wiedzy wymaga eksportowania z jednej bazy wiedzy, a następnie importowania do innej.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/28/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b441eb1e6531030a998fe628ae833b29a5d9fe5a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902054"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrowanie bazy wiedzy przy użyciu eksportu / importu

Migrowanie bazy wiedzy wymaga eksportowania z jednej bazy wiedzy, a następnie importowania do innej.

## <a name="prerequisites"></a>Wymagania wstępne

* Zanim rozpoczniesz, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Skonfiguruj nową [usługę QNA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Migrowanie bazy wiedzy z QnA Maker
1. Zaloguj się do [portalu QNA Maker](https://qnamaker.ai).
1. Wybierz bazę wiedzy źródłowej, którą chcesz zmigrować.

1. Na stronie **Ustawienia** wybierz pozycję **Eksportuj bazę wiedzy** , aby pobrać plik. tsv, który zawiera zawartość bazy wiedzy o pochodzeniu, odpowiedzi, metadane, monity monitujące oraz nazwy źródeł danych, z których zostały wyodrębnione.

1. Wybierz pozycję **Utwórz bazę wiedzy** z górnego menu, a następnie Utwórz _pustą_ bazę wiedzy. Jest ona pusta, ponieważ podczas jej tworzenia nie będzie można dodawać żadnych adresów URL ani plików. Są one dodawane podczas kroku importowania po utworzeniu.

    Skonfiguruj bazę wiedzy. Ustaw tylko nową nazwę bazy wiedzy. Zduplikowane nazwy są obsługiwane i znaki specjalne są również obsługiwane.

    Nie zaznaczaj niczego z kroku 4, ponieważ te wartości zostaną nadpisywane podczas importowania pliku.

1. W kroku 5 wybierz pozycję **Utwórz**.

1. W tej nowej bazie wiedzy Otwórz kartę **Ustawienia** , a następnie wybierz pozycję **Importuj bazę wiedzy**. Spowoduje to zaimportowanie pytań, odpowiedzi, metadanych, monitów, a następnie zachowanie nazw źródeł danych, z których zostały wyodrębnione.

   > [!div class="mx-imgBorder"]
   > [![importować bazy wiedzy](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Test** bazie wiedzy za pomocą panelu testu. Dowiedz się, jak [test bazy wiedzy](../How-To/test-knowledge-base.md).
1. **Publikowanie** bazy wiedzy knowledge base. Dowiedz się, jak [Opublikuj bazę wiedzy](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).
1. Użyj punktu końcowego w aplikacji lub kodzie bot. Zobacz tutaj jak [tworzenia bota pytań i odpowiedzi](../Tutorials/create-qna-bot.md).

    ![Usługa QnA Maker wartości](../media/qnamaker-how-to-migrate-kb/qnamaker-settings-kbid-key.png)

    W tym momencie całą wiedzy zawartość — pytania i odpowiedzi metadanych, wraz z nazwy plików źródłowych oraz w adresach URL, zostaną zaimportowane do nowej bazy wiedzy.

## <a name="chat-logs-and-alterations"></a>Dzienniki i zmiany rozmowy
Zmiany bez uwzględniania wielkości liter (synonimy) nie są importowane automatycznie. Użyj [interfejsów API v4](https://go.microsoft.com/fwlink/?linkid=2092179) , aby przenieść zmiany w nowej bazie wiedzy.

Nie ma możliwości migrowania dzienników rozmowy, ponieważ nowa baza wiedzy używa Application Insights do przechowywania dzienników rozmowy.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Edytowanie wiedzy](../How-To/edit-knowledge-base.md)
