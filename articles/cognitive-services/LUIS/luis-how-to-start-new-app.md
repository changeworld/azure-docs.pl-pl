---
title: Tworzenie nowej aplikacji — USŁUGA LUIS
titleSuffix: Azure Cognitive Services
description: Tworzenie aplikacji i zarządzanie nimi na stronie internetowej language understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d3f8696388a33a8ea112aae438c6bbe9af520c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220832"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Tworzenie nowej aplikacji usługi LUIS w portalu usługi LUIS
Istnieje kilka sposobów tworzenia aplikacji usługi LUIS. Aplikację usługi LUIS można utworzyć w portalu usługi LUIS lub za pośrednictwem [interfejsów API](developer-reference-resource.md)tworzenia usługi LUIS .

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="using-the-luis-portal"></a>Korzystanie z portalu usługi LUIS

Nową aplikację można utworzyć w portalu w wersji zapoznawczej na kilka sposobów:

* Zacznij od pustej aplikacji i utwórz intencje, wypowiedzi i jednostki.
* Zacznij od pustej aplikacji i dodaj [wstępnie skompilowany plik .](luis-how-to-use-prebuilt-domains.md)
* Importowanie aplikacji usługi `.lu` `.json` LUIS z pliku lub pliku, który zawiera już intencje, wypowiedzi i encje.

## <a name="using-the-authoring-apis"></a>Korzystanie z interfejsów API tworzenia
Możesz utworzyć nową aplikację za pomocą interfejsów API tworzenia na kilka sposobów:

* [Dodaj aplikację](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) — zacznij od pustej aplikacji i utwórz intencje, wypowiedzi i jednostki.
* [Dodaj wstępnie skompilowana aplikacja](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) — rozpocznij od wstępnie utworzonej domeny, w tym intencji, wypowiedzi i jednostek.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Tworzenie nowej aplikacji w usłudze LUIS

1. Na stronie **Moje aplikacje** wybierz subskrypcję i zasób tworzenia, a następnie **+ Utwórz**. Jeśli używasz bezpłatnego klucza próbnego, dowiedz się, jak [utworzyć zasób autora](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal).

    ![Lista aplikacji LUIS](./media/create-app-in-portal.png)


1. W oknie dialogowym wprowadź nazwę aplikacji, `Pizza Tutorial`na przykład .

    ![Tworzenie nowego okna dialogowego aplikacji](./media/create-pizza-tutorial-app-in-portal.png)

1. Wybierz kulturę aplikacji, a następnie wybierz pozycję **Gotowe**. Opis i zasób przewidywania są opcjonalne w tym momencie. Następnie można ustawić w dowolnym momencie w sekcji **Zarządzaj** portalu.

    > [!NOTE]
    > Kultury nie można zmienić po utworzeniu aplikacji. 

    Po utworzeniu aplikacji portal usługi LUIS pokazuje listę `None` **intencji** z zamiarem już utworzonym dla Ciebie. Masz teraz pustą aplikację. 
    
    > [!div class="mx-imgBorder"]
    > ![Lista intencji z brak intencji utworzony bez wypowiedzi przykład.](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available"></a>Inne dostępne działania

Pasek narzędzi kontekstu zawiera inne akcje:

* Zmienianie nazwy aplikacji
* Importowanie z `.lu` pliku przy użyciu lub`.json`
* Eksportowanie `.lu` aplikacji jako (dla `.zip` [LUDown)](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown) `.json`lub (dla [kontenera usługi LUIS)](luis-container-howto.md)
* Importowanie dzienników punktu końcowego kontenera, aby przejrzeć wypowiedzi punktów końcowych
* Eksportowanie dzienników punktów `.csv`końcowych jako analizy w trybie offline
* Usuń aplikację

## <a name="next-steps"></a>Następne kroki

Jeśli projekt aplikacji zawiera wykrywanie intencji, [utwórz nowe intencje](luis-how-to-add-intents.md)i dodaj przykładowe wypowiedzi. Jeśli projekt aplikacji jest tylko wyodrębnianie danych, dodać wypowiedzi przykład do None intencji, a następnie [utworzyć jednostki](luis-how-to-add-example-utterances.md)i etykiety wypowiedzi przykład z tych jednostek. 
