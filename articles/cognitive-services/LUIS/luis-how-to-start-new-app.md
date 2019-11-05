---
title: Utwórz nową aplikację — LUIS
titleSuffix: Azure Cognitive Services
description: Twórz aplikacje i zarządzaj nimi na stronie Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 227efcdbcb7d8e776dd77b38c5d1dedd54d71b6b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500308"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Tworzenie nowej aplikacji LUIS w portalu LUIS
Istnieje kilka sposobów tworzenia aplikacji LUIS. Aplikację LUIS można utworzyć w portalu [Luis](https://www.luis.ai) lub za pomocą [interfejsów API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)tworzenia Luis.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="using-the-luis-portal"></a>Korzystanie z portalu LUIS

Nową aplikację w portalu LUIS można utworzyć na kilka sposobów:

* Zacznij od pustej aplikacji i Utwórz intencje, wyrażenia długości i jednostki.
* Zacznij od pustej aplikacji i Dodaj [prezbudowaną domenę](luis-how-to-use-prebuilt-domains.md).
* Zaimportuj aplikację LUIS z pliku JSON, który zawiera już intencje, wyrażenia długości i jednostki.

## <a name="using-the-authoring-apis"></a>Korzystanie z interfejsów API tworzenia
Nową aplikację można utworzyć za pomocą interfejsów API tworzenia na kilka sposobów:

* [Zacznij](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) od pustej aplikacji i Utwórz intencje, wyrażenia długości i jednostki.
* [Zacznij](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/59104e515aca2f0b48c76be5) od prekompilowanej domeny.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Utwórz nową aplikację w LUIS

1. Na stronie **Moje aplikacje** wybierz pozycję **+ Utwórz**.

    ![Lista aplikacji LUIS](./media/luis-create-new-app/apps-list.png)


2. W oknie dialogowym Nadaj nazwę aplikacji "TravelAgent".

    ![Okno dialogowe Tworzenie nowej aplikacji](./media/luis-create-new-app/create-app.png)

3. Wybierz kulturę aplikacji (dla aplikacji TravelAgent, wybierz pozycję angielski), a następnie wybierz pozycję **gotowe**. 

    > [!NOTE]
    > Kultury nie można zmienić po utworzeniu aplikacji. 

## <a name="import-an-app-from-file"></a>Importuj aplikację z pliku

1. Na stronie **Moje aplikacje** wybierz pozycję **Importuj nową aplikację**.
1. W podręcznym oknie dialogowym Wybierz prawidłowy plik JSON aplikacji, a następnie wybierz pozycję **gotowe**.

### <a name="import-errors"></a>Błędy importowania

Możliwe błędy to: 

* Aplikacja o tej nazwie już istnieje. Aby rozwiązać ten problem, zaimportuj ponownie aplikację i ustaw nową nazwę jako **nazwę opcjonalną** . 

## <a name="export-app-for-backup"></a>Eksportuj aplikację do kopii zapasowej

1. Na stronie **Moje aplikacje** wybierz pozycję **Eksportuj**.
1. Wybierz pozycję **Eksportuj jako plik JSON**. Przeglądarka pobiera aktywną wersję aplikacji.
1. Dodaj ten plik do systemu kopii zapasowej, aby zarchiwizować model.

## <a name="export-app-for-containers"></a>Eksportowanie aplikacji dla kontenerów

1. Na stronie **Moje aplikacje** wybierz pozycję **Eksportuj**.
1. Wybierz pozycję **Eksportuj jako kontener,** a następnie wybierz opublikowane gniazdo (produkcyjne lub etap), które chcesz wyeksportować.
1. Użyj tego pliku z [kontenerem Luis](luis-container-howto.md). 

    Jeśli interesuje Cię eksportowanie przeszkolonego, ale jeszcze nie opublikowanego modelu do użycia z kontenerem LUIS, przejdź do strony **wersje** i wyeksportuj z tego miejsca. 

## <a name="delete-app"></a>Usuń aplikację

1. Na stronie **Moje aplikacje** wybierz trzy kropki (...) na końcu wiersza aplikacji.
1. Z menu wybierz pozycję **Usuń** .
1. W oknie potwierdzenia wybierz pozycję **OK** .

## <a name="next-steps"></a>Następne kroki

Twoje pierwsze zadanie w aplikacji ma na celu [dodanie intencji](luis-how-to-add-intents.md).
