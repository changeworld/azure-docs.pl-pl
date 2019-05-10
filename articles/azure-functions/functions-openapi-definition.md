---
title: Tworzenie definicji interfejsu OpenAPI dla funkcji usługi Azure API Management
description: Utwórz definicję interfejsu OpenAPI, która umożliwia innym aplikacjom i usługom wywołanie Twojej funkcji na platformie Azure.
services: functions
keywords: interfejs OpenAPI, Swagger, aplikacje w chmurze, usługi w chmurze,
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: tutorial
ms.date: 05/08/2019
ms.author: glenga
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 255a7c9d0b9da15176fca90c6934a84fa0f863ed
ms.sourcegitcommit: 1d257ad14ab837dd13145a6908bc0ed7af7f50a2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65501868"
---
# <a name="create-an-openapi-definition-for-a-function-with-azure-api-management"></a>Tworzenie definicji interfejsu OpenAPI dla funkcji usługi Azure API Management

Interfejsy API REST są często opisywane przy użyciu definicji interfejsu OpenAPI. Ta definicja zawiera informacje o operacjach dostępnych w interfejsie API i wymaganej strukturze danych żądań i odpowiedzi dla interfejsu API.

W ramach tego samouczka utworzysz funkcję, która pozwoli określić, czy awaryjna naprawa turbiny wiatrowej jest opłacalna. Następnie utworzysz definicję interfejsu OpenAPI dla aplikacji funkcji korzystającej [usługi Azure API Management](../api-management/api-management-key-concepts.md) tak, aby funkcja może zostać wywołana z innych aplikacji i usług.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie funkcji na platformie Azure
> * Generowanie definicji interfejsu OpenAPI za pomocą usługi Azure API Management
> * Testowanie definicji przez wywołanie funkcji

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Do obsługi wykonywania funkcji potrzebna jest aplikacja funkcji. Aplikacja funkcji umożliwia grupowanie funkcji jako jednostki logicznej aby ułatwić zarządzanie, wdrażanie, skalowanie i udostępnianie zasobów.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>Tworzenie funkcji

Ten samouczek używa funkcji wyzwalanej przez protokół HTTP, która przyjmuje dwa parametry:

* Szacowany czas naprawy w godzinach turbiny.
* Moc turbiny, w kilowatach. 

Następnie funkcja oblicza koszt naprawy oraz przychody, które mogą zostać uzyskane dzięki turbinie w ciągu 24 godzin. Aby utworzyć HTTP funkcja wyzwalana przez w [witryny Azure portal](https://portal.azure.com).

1. Rozwiń aplikację funkcji i wybierz przycisk **+** obok pozycji **Funkcje**. Wybierz **w portalu** > **nadal**.

1. Wybierz **więcej szablonów...** , a następnie wybierz **Zakończ i Wyświetl szablony**

1. Wybieranie wyzwalacza HTTP, typ `TurbineRepair` funkcji **nazwa**, wybierz `Function` dla  **[poziom uwierzytelniania](functions-bindings-http-webhook.md#http-auth)**, a następnie wybierz pozycję  **Utwórz**.  

    ![Tworzenie funkcji protokół HTTP dla interfejsu OpenAPI](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. Zastąp zawartość run.csx C# skryptu pliku następującym kodem, a następnie wybierz **Zapisz**:

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    const double revenuePerkW = 0.12;
    const double technicianCost = 250;
    const double turbineCost = 100;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        // Get query strings if they exist
        int tempVal;
        int? hours = Int32.TryParse(req.Query["hours"], out tempVal) ? tempVal : (int?)null;
        int? capacity = Int32.TryParse(req.Query["capacity"], out tempVal) ? tempVal : (int?)null;
    
        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
    
        // Use request body if a query was not sent
        capacity = capacity ?? data?.capacity;
        hours = hours ?? data?.hours;
    
        // Return bad request if capacity or hours are not passed in
        if (capacity == null || hours == null){
            return new BadRequestObjectResult("Please pass capacity and hours on the query string or in the request body");
        }
        // Formulas to calculate revenue and cost
        double? revenueOpportunity = capacity * revenuePerkW * 24;  
        double? costToFix = (hours * technicianCost) +  turbineCost;
        string repairTurbine;
    
        if (revenueOpportunity > costToFix){
            repairTurbine = "Yes";
        }
        else {
            repairTurbine = "No";
        };
    
        return (ActionResult)new OkObjectResult(new{
            message = repairTurbine,
            revenueOpportunity = "$"+ revenueOpportunity,
            costToFix = "$"+ costToFix
        });
    }
    ```

    Ten kod funkcji zwraca komunikat `Yes` lub `No` wskazujący, czy awaryjna naprawa turbiny jest opłacalna, a także wartość ewentualnego przychodu, który można uzyskać dzięki turbinie, oraz koszt naprawy turbiny.

1. Aby przetestować tę funkcję, kliknij przycisk **Testuj** przy prawej krawędzi, co spowoduje rozwinięcie karty testu. Wprowadź poniższą wartość w polu **Treść żądania**, a następnie kliknij przycisk **Uruchom**.

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

    ![Testowanie funkcji w witrynie Azure Portal](media/functions-openapi-definition/test-function.png)

    W treści odpowiedzi jest zwracana poniższa wartość.

    ```json
    {"message":"Yes","revenueOpportunity":"$7200","costToFix":"$1600"}
    ```

Funkcja określająca opłacalność naprawy awaryjnej jest już gotowa. Następnie możesz wygenerować definicji interfejsu OpenAPI dla aplikacji funkcji.

## <a name="generate-the-openapi-definition"></a>Generowanie definicji interfejsu OpenAPI

Teraz możesz przystąpić do generowania definicji interfejsu OpenAPI.

1. Wybierz aplikację funkcji, a następnie w **funkcje platformy**, wybierz **usługi API Management** i wybierz **Utwórz nową** w obszarze **usługi API Management**.

    ![Wybierz usługi API Management w funkcje platformy](media/functions-openapi-definition/select-all-settings-openapi.png)

1. Użyj ustawień usługi API Management określonych w tabeli znajdującej się poniżej obrazu.

    ![Utwórz nową usługę API Management](media/functions-openapi-definition/new-apim-service-openapi.png)

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa** | Nazwa unikatowa w skali globalnej | Nazwa jest generowana w oparciu o nazwę aplikacji funkcji. |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w którym tworzony jest nowy zasób. |  
    | **[Grupa zasobów](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Ten sam zasób aplikacji funkcji, które należy uzyskać dla Ciebie. |
    | **Lokalizacja** | Zachodnie stany USA | Wybierz lokalizację, w regionie zachodnie stany USA. |
    | **Nazwa organizacji** | Contoso | Nazwa organizacji używane w portalu dla deweloperów i powiadomień e-mail. |
    | **Adres e-mail administratora** | adres e-mail | Wiadomość e-mail, który otrzymał powiadomienia systemowe z usługi API Management. |
    | **Warstwa cenowa** | Użycie (wersja zapoznawcza) | Warstwa użycia jest w wersji zapoznawczej i nie jest dostępna we wszystkich regionach. Pełne szczegóły cen można znaleźć [usługi API Management, cennik](https://azure.microsoft.com/pricing/details/api-management/) |

1. Wybierz **Utwórz** do utworzenia wystąpienia usługi API Management, co może potrwać kilka minut.

1. Wybierz **Włącz usługę Application Insights** wysyłanie dzienników do tej samej lokalizacji co aplikacja funkcji, następnie zaakceptuj pozostałe wartości domyślne i wybierz pozycję **interfejsu API łącza**.

1. **Importu usługi Azure Functions** otwiera z **TurbineRepair** funkcja wyróżnione. Wybierz **wybierz** aby kontynuować.

    ![Importowanie usługi Azure Functions do usługi API Management](media/functions-openapi-definition/import-function-openapi.png)

1. W **tworzenie aplikacji funkcji** strony, zaakceptuj wartości domyślne i wybierz **Create**

    ![Tworzenie na podstawie aplikacji funkcji](media/functions-openapi-definition/create-function-openapi.png)

Interfejs API został utworzony dla tej funkcji.

## <a name="test-the-openapi-definition"></a>Testowanie definicji interfejsu OpenAPI

Zanim użyjesz definicji interfejsu API, należy sprawdzić, czy działa.

1. Na **testu** kartę funkcji, wybierz opcję **WPIS** operacji

1. Wprowadź wartości w polach **godzin** i **pojemności**

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Kliknij przycisk **wysyłania**, następnie zobacz odpowiedź HTTP.

    ![Interfejsu API test function](media/functions-openapi-definition/test-function-api-openapi.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o usłudze API Management](../api-management/api-management-key-concepts.md)
