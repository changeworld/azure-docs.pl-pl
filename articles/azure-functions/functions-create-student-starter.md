---
title: Create a Function using Azure for Students Starter
description: Learn how to create an Azure Function from within an Azure for Student Starter subscription
Customer intent: As a student, I want to be able to create a HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
author: alexkarcher-msft
ms.topic: quickstart
ms.date: 02/22/2019
ms.author: alkarche
ms.openlocfilehash: b86ea2367a17baa8c57b8d0c0331c0cd3cfbb481
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227098"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Create a Function using Azure for Students Starter

In this tutorial, we will create a hello world HTTP function in an Azure for Students Starter subscription. We'll also walk through what's available in Azure Functions in this subscription type.

Microsoft *Azure for Students Starter* gets you started with the Azure products you need to develop in the cloud at no cost to you. [Learn more about this offer here.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Usługa Azure Functions umożliwia wykonywanie kodu w środowisku [bezserwerowym](https://azure.microsoft.com/solutions/serverless/) bez konieczności uprzedniego tworzenia maszyny wirtualnej lub publikowania aplikacji internetowej. [Learn more about Functions here.](./functions-overview.md)

## <a name="create-a-function"></a>Create a Function

 In this topic, learn how to use Functions to create an HTTP triggered "hello world" function in the Azure portal.

![Tworzenie aplikacji funkcji w witrynie Azure Portal](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com> przy użyciu danych konta Azure.

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Do obsługi wykonywania funkcji potrzebna jest aplikacja funkcji. Aplikacja funkcji umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia wdrażanie i udostępnianie zasobów oraz zarządzanie nimi. 

1. Wybierz przycisk **Nowy** znajdujący się w lewym górnym rogu witryny Azure Portal, a następnie wybierz pozycje **Obliczenia** > **Aplikacja funkcji**.

    ![Tworzenie aplikacji funkcji w witrynie Azure Portal](./media/functions-create-student-starter/function-app-create-flow.png)

2. Użyj ustawień aplikacji funkcji podanych w tabeli pod obrazem.

    <img src="./media/functions-create-student-starter/Function-create-start.png" width="315">

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa aplikacji** | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nową aplikację funkcji. Prawidłowe znaki to `a-z`, `0-9` i `-`.  | 
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzona ta nowa aplikacja funkcji. | 
    | **[Grupa zasobów](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Nazwa nowej grupy zasobów, w której ma zostać utworzona aplikacja funkcji. |
   | **[App Service Plan/Location](./functions-scale.md)** | Nowość | The hosting plan that controls what region your function app is deployed to and the density of your resources. Multiple Function Apps deployed to the same plan will all share the same single free instance. This is a restriction of the Student Starter plan. The full hosting options are [explained here.](./functions-scale.md)|
    | **Stos środowiska uruchomieniowego** | Preferowany język | Wybierz środowisko uruchomieniowe, które obsługuje ulubiony język programowania funkcji. Wybierz **.NET** dla funkcji w językach C# i F#. |
    |**[Application Insights](./functions-monitoring.md)**| Enabled (Włączony) | Application Insights is used to store and analyze your function app's logs. It is enabled by default if you choose a location that supports Application Insights. Application Insights can be enabled for any function by manually choosing a nearby region to deploy Application Insights. Without Application Insights, you will only be able to view live streaming logs.

3. Select **App Service plan/Location** above to choose a different location

4. Select **Create new** and then give your plan a unique name.

5. Select the location closest to you. [See a full map of Azure regions here.](https://azure.microsoft.com/global-infrastructure/regions/) 

    <img src="./media/functions-create-student-starter/Create-ASP.png" width="800">

6. Wybierz pozycję **Utwórz**, aby zaaprowizować aplikację funkcji i wdrożyć ją.

    <img src="./media/functions-create-student-starter/Function-create-end.png" width="315">

7. Wybierz ikonę Powiadomienie w prawym górnym rogu portalu i poszukaj komunikatu **Wdrażanie zakończone pomyślnie**.

    ![Definiowanie nowych ustawień aplikacji funkcji](./media/functions-create-student-starter/function-app-create-notification.png)

8. Wybierz pozycję **Przejdź do zasobu**, aby wyświetlić nową aplikację funkcji.

Następnie należy utworzyć funkcję w nowej aplikacji funkcji.

## <a name="create-function"></a>Tworzenie funkcji wyzwalanej przez protokół HTTP

1. Rozwiń nową aplikację funkcji, a następnie wybierz przycisk **+** obok opcji **Functions**, wybierz opcję **W portalu** i wybierz opcję **Kontynuuj**.

    ![Szybkie rozpoczynanie pracy z usługą Functions — wybieranie platformy.](./media/functions-create-student-starter/function-app-quickstart-choose-portal.png)

1. Wybierz opcję **Element WebHook + interfejs API**, a następnie wybierz opcję **Utwórz**.

    ![Szybkie rozpoczynanie pracy z usługą Functions w witrynie Azure Portal.](./media/functions-create-student-starter/function-app-quickstart-node-webhook.png)

Funkcja zostanie utworzona przy użyciu szablonu funkcji wyzwalanej przez protokół HTTP właściwego dla danego języka.

Możesz teraz uruchomić nową funkcję, wysyłając żądanie HTTP.

## <a name="test-the-function"></a>Testowanie funkcji

1. W nowej funkcji kliknij pozycję **</> Pobierz adres URL funkcji** w prawym górnym rogu, wybierz pozycję **domyślne (klawisz funkcji)** , a następnie kliknij pozycję **Kopiuj**. 

    ![Kopiowanie adresu URL funkcji z witryny Azure Portal](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

2. Wklej adres URL funkcji do paska adresu przeglądarki. Dodaj wartość ciągu zapytania `&name=<yourname>` na końcu tego adresu URL, a następnie naciśnij klawisz `Enter` na klawiaturze, aby wykonać żądanie. W przeglądarce powinna zostać wyświetlona odpowiedź zwrócona przez funkcję.  

    Poniższy przykład przedstawia odpowiedź w przeglądarce:

    ![Odpowiedź funkcji wyświetlona w przeglądarce.](./media/functions-create-student-starter/function-app-browser-testing.png)

    Adres URL żądania zawiera klucz, który domyślnie jest wymagany do uzyskania dostępu do funkcji za pośrednictwem protokołu HTTP.

3. Gdy funkcja działa, informacje o śledzeniu są zapisywane w dziennikach. Aby wyświetlić dane wyjściowe śledzenia z poprzedniego wykonania, wróć do funkcji w portalu, a następnie kliknij strzałkę w dół w dolnej części ekranu, aby rozwinąć pozycję **Dzienniki**.

   ![Podgląd dziennika usługi Functions w witrynie Azure Portal.](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Supported Features in Azure for Students Starter

In Azure for Student Starter you have access to most of the features of the Azure Functions runtime, with several key limitations listed below:

* The HTTP trigger is the only trigger type supported.
    * All input and all output bindings are supported! [See the full list here.](functions-triggers-bindings.md)
* Languages Supported: 
    * C# (.NET Core 2)
    * Javascript (Node.js 8 & 10)
    * F# (.NET Core 2)
    * [See languages supported in higher plans here](supported-languages.md)
* Windows is the only supported operating system.
* Scale is restricted to [one free tier instance](https://azure.microsoft.com/pricing/details/app-service/windows/) running for up to 60 minutes each day. You will serverlessly scale from 0 to 1 instance automatically as HTTP traffic is received, but no further.
* Only [the 2.x runtime](functions-versions.md) is supported.
* All developer tooling is supported for editing and publishing functions. This includes VS Code, Visual Studio, the Azure CLI, and the Azure portal. If you'd like to use anything other than the portal, you will need to first create an app in the portal, and then choose that app as a deployment target in your preferred tool.

## <a name="next-steps"></a>Następne kroki

You have created a function app with a simple HTTP triggered function! Now you can explore local tooling, more languages, monitoring, and integrations.

 * [Tworzenie pierwszej funkcji przy użyciu programu Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Create your first function using Visual Studio Code](./functions-create-first-function-vs-code.md)
 * [Azure Functions JavaScript developer guide](./functions-reference-node.md)
 * [Use Azure Functions to connect to an Azure SQL Database](./functions-scenario-database-table-cleanup.md)
 * [Learn more about Azure Functions HTTP bindings](./functions-bindings-http-webhook.md).
 * [Monitor your Azure Functions](./functions-monitoring.md)
