---
title: Migrowanie baz wiedzy — QnA Maker
titleSuffix: Azure Cognitive Services
description: Migrowanie bazy wiedzy wymaga eksportowanie z jednej bazy wiedzy knowledge base, a następnie zaimportować do innego.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/08/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: eac5e43c69cc09c5945316827a35f729c158d47a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61431246"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrowanie bazy wiedzy przy użyciu eksportu / importu

Migrowanie bazy wiedzy wymaga eksportowanie z jednej bazy wiedzy knowledge base, a następnie zaimportować do innego. 

## <a name="prerequisites"></a>Wymagania wstępne

* Zanim rozpoczniesz, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Skonfiguruj nowy [usługę QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Migrowanie bazy wiedzy z usługi QnA Maker
1. Zaloguj się do [portalu narzędzia QnA Maker](https://qnamaker.ai).
1. Wybierz wiedzy, które mają zostać zmigrowane.

1. Na **ustawienia** wybierz opcję **eksportowania bazy wiedzy knowledge base** Aby pobrać plik tsv z zawartością bazy wiedzy — pytań, odpowiedzi, metadane, oraz nazwy źródła danych, z których zostały wyodrębnione.

1. Wybierz **tworzenie bazy wiedzy** z górnego menu następnie utworzyć pusty bazy wiedzy knowledge base. 

    ![Zestaw źródeł danych](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Nadaj usługi **nazwy.** Zduplikowane nazwy są obsługiwane i znaki specjalne są również obsługiwane.

1. Wybierz pozycję **Utwórz**.

    ![Tworzenie bazy wiedzy](../media/qnamaker-how-to-create-kb/create-kb.png)

1. W tej nowej bazy wiedzy knowledge base, otwórz **ustawienia** kartę, a następnie wybierz pozycję **importu wiedzy**. Importuje pytań, odpowiedzi i metadanych i przechowuje nazwy źródeł danych, z których zostały wyodrębnione.

   ![Importowanie bazy wiedzy](../media/qnamaker-how-to-migrate-kb/Import.png)

1. **Test** bazie wiedzy za pomocą panelu testu. Dowiedz się, jak [test bazy wiedzy](../How-To/test-knowledge-base.md).
1. **Publikowanie** bazy wiedzy knowledge base. Dowiedz się, jak [Opublikuj bazę wiedzy](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).
1. W kodzie aplikacji lub dowolnemu botowi, należy używać punktu końcowego. Zobacz tutaj jak [tworzenia bota pytań i odpowiedzi](../Tutorials/create-qna-bot.md).

    ![Usługa QnA Maker wartości](../media/qnamaker-how-to-migrate-kb/qnamaker-settings-kbid-key.png)

    W tym momencie całą wiedzy zawartość — pytania i odpowiedzi metadanych, wraz z nazwy plików źródłowych oraz w adresach URL, zostaną zaimportowane do nowej bazy wiedzy. 

## <a name="chat-logs-and-alterations"></a>Dzienniki czatu i zmiany
Zmiany bez uwzględniania wielkości liter (synonimy) nie są importowane automatycznie. Użyj [interfejsów API w wersji 2](https://aka.ms/qnamaker-v2-apis) Eksportowanie zmiany ze starego wiedzy i [interfejsów API w wersji 4](https://aka.ms/qnamaker-v4-apis) Aby przenieść zmiany w nowej bazie wiedzy knowledge base.

Nie ma możliwości migracji dzienniki czatu, ponieważ nowe bazy wiedzy przy użyciu usługi Application Insights dzienniki czatu. 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Edytowanie wiedzy](../How-To/edit-knowledge-base.md)
