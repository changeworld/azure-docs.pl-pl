---
title: Tworzenie funkcji przy użyciu platformy Azure dla studentów | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć funkcję platformy Azure, z w ramach subskrypcji Azure for for Students Starter
Customer intent: As a student, I want to be able to create a HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
services: functions
documentationcenter: na
author: alexkarcher-msft
manager: ggailey777
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 02/22/2019
ms.author: alkarche
ms.openlocfilehash: 860fedb13e84054e8ba264116be4e452445b7e9b
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143102"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Tworzenie funkcji przy użyciu platformy Azure dla studentów

W tym samouczku zostanie utworzona funkcja HTTP hello world w subskrypcji Azure for Students Starter. Omówimy również dostępne w usłudze Azure Functions w tego typu subskrypcji.

Microsoft *Azure for Students Starter* ułatwia rozpoczęcie pracy z produktów platformy Azure do tworzenia w chmurze bez ponoszenia nie kosztów. [Dowiedz się więcej na temat tej oferty, w tym miejscu.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Usługa Azure Functions umożliwia wykonywanie kodu w środowisku [bezserwerowym](https://azure.microsoft.com/solutions/serverless/) bez konieczności uprzedniego tworzenia maszyny wirtualnej lub publikowania aplikacji internetowej. [Dowiedz się więcej o funkcji w tym miejscu.](./functions-overview.md)

## <a name="create-a-function"></a>Tworzenie funkcji

 W tym temacie Dowiedz się, jak za pomocą funkcji, aby utworzyć funkcję wyzwalaną przez protokół HTTP "hello world" w witrynie Azure portal.

![Tworzenie aplikacji funkcji w witrynie Azure Portal](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

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
   | **[Plan usługi App Service/lokalizacja](./functions-scale.md)** | Nowa | Planie hostingu kontrolować, jakie regionie jest wdrażana aplikacja funkcji i z dużą gęstością zasobów. Wiele aplikacji funkcyjnych, wdrożone na tym samym planie będą współdzielić ten sam pojedyncze wystąpienie bezpłatne. To ograniczenie plan for Students Starter. Opcje hostingu pełne są [wyjaśniono tutaj.](./functions-scale.md)|
    | **Stos środowiska uruchomieniowego** | Preferowany język | Wybierz środowisko uruchomieniowe, które obsługuje ulubiony język programowania funkcji. Wybierz **.NET** dla funkcji w językach C# i F#. |
    |**[Application Insights](./functions-monitoring.md)**| Enabled (Włączony) | Usługa Application Insights umożliwia przechowywanie i analizowanie dzienników aplikacji funkcji. Jest włączona domyślnie po wybraniu lokalizację, która obsługuje usługę Application Insights. Usługi Application Insights można włączyć dla dowolnej funkcji, ręcznie, wybierając pobliskim regionie, aby wdrożyć usługę Application Insights. Bez usługi Application Insights tylko będzie mogła wyświetlać dzienniki przesyłania strumieniowego na żywo.

3. Wybierz **plan usługi App Service/lokalizacja** powyżej, aby wybrać inną lokalizację

4. Wybierz **Utwórz nową** i następnie nadaj unikatową nazwę planu usługi.

5. Wybierz lokalizację najbliżej Ciebie. [Zobacz pełną mapę regionów świadczenia usługi Azure w tym miejscu.](https://azure.microsoft.com/global-infrastructure/regions/) 

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

1. W nowej funkcji kliknij pozycję **</> Pobierz adres URL funkcji** w prawym górnym rogu, wybierz pozycję **domyślne (klawisz funkcji)**, a następnie kliknij pozycję **Kopiuj**. 

    ![Kopiowanie adresu URL funkcji z witryny Azure Portal](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

2. Wklej adres URL funkcji do paska adresu przeglądarki. Dodaj wartość ciągu zapytania `&name=<yourname>` na końcu tego adresu URL, a następnie naciśnij klawisz `Enter` na klawiaturze, aby wykonać żądanie. W przeglądarce powinna zostać wyświetlona odpowiedź zwrócona przez funkcję.  

    Poniższy przykład przedstawia odpowiedź w przeglądarce:

    ![Odpowiedź funkcji wyświetlona w przeglądarce.](./media/functions-create-student-starter/function-app-browser-testing.png)

    Adres URL żądania zawiera klucz, który domyślnie jest wymagany do uzyskania dostępu do funkcji za pośrednictwem protokołu HTTP.

3. Gdy funkcja działa, informacje o śledzeniu są zapisywane w dziennikach. Aby wyświetlić dane wyjściowe śledzenia z poprzedniego wykonania, wróć do funkcji w portalu, a następnie kliknij strzałkę w dół w dolnej części ekranu, aby rozwinąć pozycję **Dzienniki**.

   ![Podgląd dziennika usługi Functions w witrynie Azure Portal.](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Obsługiwane funkcje na platformie Azure for Students Starter

Na platformie Azure for Students Starter masz dostęp do większości funkcji środowiska uruchomieniowego usługi Azure Functions, z kilkoma ograniczeniami klucza wymienionych poniżej:

* Wyzwalacz HTTP jest obsługiwane tylko typ wyzwalacza.
    * Wszystkie dane wejściowe i wszystkie dane wyjściowe są obsługiwane przez wiązania! [Zobacz pełną listę.](functions-triggers-bindings.md)
* Obsługiwane języki: 
    * C#(.NET core 2)
    * JavaScript (Node.js, 8 i 10)
    * F#(.NET core 2)
    * [Zobacz języków obsługiwanych w planach wyższych, w tym miejscu](supported-languages.md)
* Windows jest jedynym obsługiwanym systemie operacyjnym.
* Skala jest ograniczony do [jedno wystąpienie warstwy bezpłatne](https://azure.microsoft.com/pricing/details/app-service/windows/) uruchomiona po 60 minutach każdego dnia. Możesz serverlessly skalowane wraz z zakresu od 0 do 1 wystąpienia automatycznie zostanie odebrany ruch HTTP, ale żadne dodatkowe.
* Tylko [środowisko uruchomieniowe 2.x](functions-versions.md) jest obsługiwana.
* Wszystkie narzędzia dla deweloperów jest obsługiwane do edycji i publikowania funkcji. W tym programu VS Code, program Visual Studio, wiersza polecenia platformy Azure i witryny Azure portal. Jeśli chcesz użyć coś innego niż portalu, musisz najpierw utworzyć aplikację w portalu, a następnie wybierz tę aplikację jako cel wdrożenia w preferowanym narzędziem.

## <a name="next-steps"></a>Kolejne kroki

Utworzono aplikację funkcji z prostą funkcją wyzwalaną przez protokół HTTP! Teraz możesz eksplorować, lokalnego narzędzia, więcej języków, monitorowania i integracji.

 * [Tworzenie pierwszej funkcji przy użyciu programu Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Tworzenie pierwszej funkcji przy użyciu programu Visual Studio Code](./functions-create-first-function-vs-code.md)
 * [Przewodnik dla deweloperów w usłudze Azure Functions JavaScript](./functions-reference-node.md)
 * [Łączenie z bazą danych SQL Azure za pomocą usługi Azure Functions](./functions-scenario-database-table-cleanup.md)
 * [Dowiedz się więcej o powiązań usługi Azure Functions HTTP](./functions-bindings-http-webhook.md).
 * [Monitorowanie usługi Azure Functions](./functions-monitoring.md)
