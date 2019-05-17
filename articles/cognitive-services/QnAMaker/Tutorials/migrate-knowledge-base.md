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
ms.openlocfilehash: 8ff3c497372a761bd8a02ae81bc897c8ee297bd0
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794871"
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
Zmiany bez uwzględniania wielkości liter (synonimy) nie są importowane automatycznie. Użyj [interfejsów API w wersji 4](https://go.microsoft.com/fwlink/?linkid=2092179) Aby przenieść zmiany w nowej bazie wiedzy knowledge base.

Nie ma możliwości migracji dzienniki czatu, ponieważ nowe bazy wiedzy przy użyciu usługi Application Insights dzienniki czatu. 

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Edytowanie wiedzy](../How-To/edit-knowledge-base.md)
