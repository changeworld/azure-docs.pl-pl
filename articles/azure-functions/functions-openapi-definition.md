---
title: Udostępnianie funkcji za pomocą funkcji OpenAPI przy użyciu usługi Azure API Management
description: Utwórz definicję interfejsu OpenAPI, która umożliwia innym aplikacjom i usługom wywołanie Twojej funkcji na platformie Azure.
ms.topic: tutorial
ms.date: 05/08/2019
ms.reviewer: sunayv
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 9465209467c83f7de075d16e724459c307d55bd3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77210212"
---
# <a name="create-an-openapi-definition-for-a-serverless-api-using-azure-api-management"></a>Tworzenie definicji OpenAPI dla interfejsu API bez użycia serwera przy użyciu usługi Azure API Management

INTERFEJSY API REST są często opisywane przy użyciu definicji OpenAPI. Ta definicja zawiera informacje o operacjach dostępnych w interfejsie API i wymaganej strukturze danych żądań i odpowiedzi dla interfejsu API.

W ramach tego samouczka utworzysz funkcję, która pozwoli określić, czy awaryjna naprawa turbiny wiatrowej jest opłacalna. Następnie należy utworzyć definicję OpenAPI dla aplikacji funkcji przy użyciu [usługi Azure API Management,](../api-management/api-management-key-concepts.md) dzięki czemu funkcja może być wywoływana z innych aplikacji i usług.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie funkcji na platformie Azure
> * Generowanie definicji OpenAPI przy użyciu usługi Azure API Management
> * Testowanie definicji przez wywołanie funkcji
> * Pobierz definicję OpenAPI

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Do obsługi wykonywania funkcji potrzebna jest aplikacja funkcji. Aplikacja funkcji umożliwia grupowanie funkcji jako jednostki logicznej w celu łatwiejszego zarządzania, wdrażania, skalowania i udostępniania zasobów.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-the-function"></a>Tworzenie funkcji

W tym samouczku użyto funkcji wyzwalanej http, która przyjmuje dwa parametry:

* Szacowany czas na naprawę turbiny w godzinach.
* Pojemność turbiny, w kilowatach. 

Następnie funkcja oblicza koszt naprawy oraz przychody, które mogą zostać uzyskane dzięki turbinie w ciągu 24 godzin. Aby utworzyć funkcję wyzwalane HTTP w [witrynie Azure portal:](https://portal.azure.com)

1. Rozwiń aplikację funkcji **+** i wybierz przycisk obok **pozycji Funkcje**. Wybierz **pozycję W portalu** > **Kontynuuj**.

1. Wybierz **więcej szablonów...**, a następnie wybierz pozycję **Zakończ i wyświetl szablony**

1. Wybierz wyzwalacz `TurbineRepair` HTTP, wpisz dla `Function` funkcji **Nazwa**, wybierz dla **[poziomu uwierzytelniania](functions-bindings-http-webhook-trigger.md#http-auth)**, a następnie wybierz pozycję **Utwórz**.  

    ![Tworzenie funkcji HTTP dla OpenAPI](media/functions-openapi-definition/select-http-trigger-openapi.png)

1. Zastąp zawartość pliku skryptu run.csx C# następującym kodem, a następnie wybierz pozycję **Zapisz:**

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

1. Aby przetestować tę funkcję, kliknij przycisk **Testuj** po prawej stronie, aby rozwinąć kartę testu. Wprowadź następującą wartość **treści Żądanie,** a następnie kliknij przycisk **Uruchom**.

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

Funkcja określająca opłacalność naprawy awaryjnej jest już gotowa. Następnie wygenerujesz definicję OpenAPI dla aplikacji funkcji.

## <a name="generate-the-openapi-definition"></a>Generowanie definicji interfejsu OpenAPI

Teraz możesz przystąpić do generowania definicji interfejsu OpenAPI.

1. Wybierz aplikację funkcji, a następnie w **obszarze Funkcje platformy**wybierz pozycję **Zarządzanie interfejsami API** i wybierz pozycję **Utwórz nowe w** obszarze Zarządzanie **interfejsami API**.

    ![Wybieranie funkcji zarządzania interfejsami API w funkcjach platformy](media/functions-openapi-definition/select-all-settings-openapi.png)

1. Użyj ustawień zarządzania interfejsami API, jak określono w tabeli poniżej obrazu.

    ![Tworzenie nowej usługi zarządzania interfejsami API](media/functions-openapi-definition/new-apim-service-openapi.png)

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa** | Nazwa unikatowa w skali globalnej | Nazwa jest generowana na podstawie nazwy aplikacji funkcji. |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której tworzony jest ten nowy zasób. |  
    | **[Grupa zasobów](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Ten sam zasób co aplikacja funkcji, który powinien zostać ustawiony dla Ciebie. |
    | **Lokalizacja** | Zachodnie stany USA | Wybierz lokalizację w zachodnie stany USA. |
    | **Nazwa organizacji** | Contoso | Nazwa organizacji używana w portalu dewelopera i dla powiadomień e-mail. |
    | **Adres e-mail administratora** | twój e-mail | Wiadomości e-mail, które otrzymały powiadomienia systemowe z usługi API Management. |
    | **Warstwa cenowa** | Zużycie (wersja zapoznawcza) | Warstwa zużycia jest w wersji zapoznawczej i nie jest dostępna we wszystkich regionach. Szczegółowe informacje o cenach można znaleźć na [stronie cennika usługi API Management](https://azure.microsoft.com/pricing/details/api-management/) |

1. Wybierz pozycję **Utwórz**, aby utworzyć wystąpienie usługi API Management, co może potrwać kilka minut.

1. Wybierz **pozycję Włącz usługę Application Insights,** aby wysyłać dzienniki w tym samym miejscu co aplikacja funkcyjna, a następnie zaakceptować pozostałe wartości domyślne i wybrać pozycję Interfejs API **łącza**.

1. Otwarcie **usługi Importuj usługi Azure z** wyróżnioną funkcją **TurbineRepair.** Wybierz pozycję **Wybierz**, aby kontynuować.

    ![Importowanie funkcji platformy Azure do usługi API Management](media/functions-openapi-definition/import-function-openapi.png)

1. Na stronie **Utwórz z aplikacji funkcji** zaakceptuj wartości domyślne i wybierz pozycję **Utwórz**

    ![Tworzenie z aplikacji funkcji](media/functions-openapi-definition/create-function-openapi.png)

Interfejs API jest teraz tworzony dla funkcji.

## <a name="test-the-api"></a>Testowanie interfejsu API

Przed użyciem definicji OpenAPI należy sprawdzić, czy interfejs API działa.

1. Na karcie **Test** funkcji wybierz pozycję **POST.**

1. Wprowadź wartości **godzin** i **pojemności**

    ```json
    {
    "hours": "6",
    "capacity": "2500"
    }
    ```

1. Kliknij **pozycję Wyślij**, a następnie wyświetl odpowiedź HTTP.

    ![Api funkcji testowej](media/functions-openapi-definition/test-function-api-openapi.png)

## <a name="download-the-openapi-definition"></a>Pobierz definicję OpenAPI

Jeśli interfejs API działa zgodnie z oczekiwaniami, można pobrać definicję OpenAPI.

1. Wybierz **pozycję Pobierz definicję OpenAPI** u góry strony.
   
   ![Pobieranie definicji interfejsu OpenAPI](media/functions-openapi-definition/download-definition.png)

2. Otwórz pobrany plik JSON i przejrzyj definicję.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Następne kroki

Do wygenerowania definicji funkcji za pomocą funkcji użyto integracji zarządzania interfejsami API. Teraz można edytować definicję w usłudze API Management w portalu. Możesz również [dowiedzieć się więcej o zarządzaniu interfejsami API](../api-management/api-management-key-concepts.md).

> [!div class="nextstepaction"]
> [Edytowanie definicji OpenAPI w usłudze Api Management](../api-management/edit-api.md)
