---
title: Utwórz nową aplikację — LUIS
titleSuffix: Azure Cognitive Services
description: Utwórz aplikacje i zarządzaj nimi na stronie sieci Web Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 87244169aa8e50ddd503086121dd84f5d50c5df4
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932772"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Tworzenie nowej aplikacji LUIS w portalu LUIS
Istnieje kilka sposobów, aby utworzyć aplikację usługi LUIS. Można utworzyć aplikację usługi LUIS w [LUIS](https://www.luis.ai) portalu lub za pomocą usługi LUIS tworzenia [interfejsów API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

## <a name="using-the-luis-portal"></a>Za pomocą portalu usługi LUIS
Można utworzyć nową aplikację w portalu usługi LUIS na kilka sposobów:

* Rozpocznij od pusta aplikacja i tworzyć intencji, wypowiedzi i jednostek.
* Start z pustą aplikacją, a następnie dodaj [ze wstępnie utworzonych domen](luis-how-to-use-prebuilt-domains.md).
* Importowanie aplikacji usługi LUIS z pliku JSON, który zawiera już intencji, wypowiedzi i jednostek.

## <a name="using-the-authoring-apis"></a>Za pomocą tworzenia interfejsów API
Można utworzyć nową aplikację za pomocą tworzenia interfejsów API na kilka sposobów:

* [Rozpocznij](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) z pustą aplikację i utworzyć intencji, wypowiedzi i jednostek.
* [Rozpocznij](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/59104e515aca2f0b48c76be5) przy użyciu wbudowanych domeny.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

## <a name="create-new-app-in-luis"></a>Utwórz nową aplikację w usługi LUIS

1. Na **Moje aplikacje** wybierz opcję **Utwórz nową aplikację**.

    ![Lista aplikacji LUIS](./media/luis-create-new-app/apps-list.png)


2. W oknie dialogowym Nazwa aplikacji "TravelAgent".

    ![Utwórz nowe okno dialogowe aplikacji](./media/luis-create-new-app/create-app.png)

3. Wybierz Twojej kulturze aplikacji (TravelAgent aplikacji, wybierz język angielski), a następnie wybierz pozycję **gotowe**. 

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

## <a name="delete-app"></a>Usuwanie aplikacji

1. Na stronie **Moje aplikacje** wybierz trzy kropki (...) na końcu wiersza aplikacji.
1. Z menu wybierz pozycję **Usuń** .
1. W oknie potwierdzenia wybierz pozycję **OK** .

## <a name="next-steps"></a>Następne kroki

Pierwsze zadanie w aplikacji jest [Dodawanie intencji](luis-how-to-add-intents.md).
