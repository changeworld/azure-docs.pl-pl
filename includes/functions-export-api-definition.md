---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 49ac1a7585ddf2a6500c7e9382880109c3f7f431
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534272"
---
## <a name="export-an-api-definition"></a>Eksportowanie definicji interfejsu API
Twoja funkcja ma definicję OpenAPI, od [tworzenia definicji openapi dla funkcji](../articles/azure-functions/functions-openapi-definition.md). Następny krok w tym procesie polega na wyeksportowaniu definicji interfejsu API, aby usługa PowerApps i usługa Microsoft Flow mogły korzystać z niej w niestandardowym interfejsie API.

> [!IMPORTANT]
> Należy pamiętać, że użytkownik musi być zalogowany na platformie Azure przy użyciu tych samych poświadczeń, które są używane dla dzierżawy usługi PowerApps i Microsoft Flow. Dzięki temu platforma Azure może utworzyć niestandardowy interfejs API i udostępnić go dla usług PowerApps i Microsoft Flow.

1. W [Azure Portal](https://portal.azure.com)kliknij nazwę aplikacji funkcji (na przykład **Function-Demonstracja-Energy**) >**definicji interfejsu API** **funkcji** > platformy.

    ![Definicja interfejsu API](media/functions-export-api-definition/api-definition.png)

1. Kliknij pozycję **Eksportuj do usługi powerapps i Flow**.

    ![Źródło definicji interfejsu API](media/functions-export-api-definition/export-api-1.png)

1. W okienku po prawej stronie Użyj ustawień określonych w tabeli.

    |Ustawienie|Opis|
    |--------|------------|
    |**Tryb eksportu**|Wybierz pozycję **Express** , aby automatycznie wygenerować niestandardowy interfejs API. Wybranie opcji **ręcznie** Eksportuje definicję interfejsu API, ale należy zaimportować ją do usługi PowerApps i Microsoft Flow ręcznie. Aby uzyskać więcej informacji, zobacz [Eksportowanie do usługi powerapps i Microsoft Flow](../articles/azure-functions/app-service-export-api-to-powerapps-and-flow.md).|
    |**Środowisko**|Wybierz środowisko, w którym ma zostać zapisany niestandardowy interfejs API. Aby uzyskać więcej informacji, zobacz [Omówienie środowisk (powerapps)](https://powerapps.microsoft.com/tutorials/environments-overview/) lub [środowisk (Microsoft Flow)](https://us.flow.microsoft.com/documentation/environments-overview-admin/).|
    |**Nazwa niestandardowego interfejsu API**|Wprowadź nazwę, `Turbine Repair`na przykład.|
    |**Nazwa klucza interfejsu API**|Wprowadź nazwę, którą powinni zobaczyć konstruktory aplikacji i przepływu w interfejsie użytkownika niestandardowego interfejsu API. Należy zauważyć, że przykład zawiera przydatne informacje.|
 
    ![Eksportowanie do usług PowerApps i Microsoft Flow](media/functions-export-api-definition/export-api-2.png)

1. Kliknij przycisk **OK**. Niestandardowy interfejs API jest teraz skompilowany i dodawany do określonego środowiska.