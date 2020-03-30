---
title: Tworzenie funkcji przy użyciu platformy Azure for Students Starter
description: Dowiedz się, jak utworzyć funkcję platformy Azure z poziomu subskrypcji platformy Azure dla studentów starter
Customer intent: As a student, I want to be able to create an HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
author: alexkarcher-msft
ms.topic: quickstart
ms.date: 02/22/2019
ms.author: alkarche
ms.openlocfilehash: 189a0bc80ab2894b2888b496b3beb45249d859f6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75921131"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Tworzenie funkcji przy użyciu platformy Azure for Students Starter

W tym samouczku utworzymy funkcję HTTP "hello world" w subskrypcji platformy Azure for Students Starter. Przejdziemy również do tego, co jest dostępne w usłudze Azure Functions w tym typie subskrypcji.

Microsoft *Azure for Students Starter* rozpoczyna pracę z produktami platformy Azure, które musisz tworzyć w chmurze bez żadnych kosztów. [Dowiedz się więcej o tej ofercie tutaj.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Usługa Azure Functions umożliwia wykonywanie kodu w środowisku [bezserwerowym](https://azure.microsoft.com/solutions/serverless/) bez konieczności tworzenia maszyny Wirtualnej lub publikowania aplikacji sieci web. [Dowiedz się więcej o funkcjach tutaj.](./functions-overview.md)

## <a name="create-a-function"></a>Tworzenie funkcji

 W tym temacie dowiesz się, jak utworzyć funkcję wyzwalaną przez http "hello world" w witrynie Azure portal.

![Tworzenie aplikacji funkcji w witrynie Azure Portal](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com> przy użyciu danych konta Azure.

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Do obsługi wykonywania funkcji potrzebna jest aplikacja funkcji. Aplikacja funkcji umożliwia grupowanie funkcji jako jednostki logicznej w celu łatwiejszego zarządzania, wdrażania, skalowania i udostępniania zasobów.

1. Wybierz przycisk **Utwórz zasób** znaleziony w lewym górnym rogu witryny Azure portal. Następnie wybierz pozycję Aplikacja funkcji **obliczeniowych** > **Function App**.

    ![Tworzenie aplikacji funkcji w witrynie Azure Portal](./media/functions-create-student-starter/function-app-create-flow.png)

2. Użyj ustawień aplikacji funkcji podanych w tabeli pod obrazem.

    <img src="./media/functions-create-student-starter/Function-create-start.png" width="315">

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa aplikacji** | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nową aplikację funkcji. Prawidłowe znaki to `a-z`, `0-9` i `-`.  | 
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzona ta nowa aplikacja funkcji. | 
    | **[Grupa zasobów](../azure-resource-manager/management/overview.md)** |  myResourceGroup | Nazwa nowej grupy zasobów, w której ma zostać utworzona aplikacja funkcji. |
   | **[Plan/lokalizacja usługi aplikacji](./functions-scale.md)** | Nowa | Plan hostingu, który kontroluje region, w jakim jest wdrażana aplikacja funkcji i gęstość zasobów. Wiele aplikacji funkcji wdrożonych w tym samym planie będzie współużytkować to samo pojedyncze wolne wystąpienie. Jest to ograniczenie planu Student Starter. Pełne opcje hostingu są [wyjaśnione tutaj.](./functions-scale.md)|
    | **Stos środowiska uruchomieniowego** | Preferowany język | Wybierz środowisko uruchomieniowe, które obsługuje ulubiony język programowania funkcji. Wybierz **.NET** dla funkcji w językach C# i F#. |
    |**[Wgląd w aplikacje](./functions-monitoring.md)**| Enabled (Włączony) | Usługa Application Insights służy do przechowywania i analizowania dzienników aplikacji funkcji. Jest ona domyślnie włączona, jeśli wybierzesz lokalizację, która obsługuje usługę Application Insights. Usługa Application Insights można włączyć dla dowolnej funkcji, ręcznie wybierając pobliski region do wdrożenia usługi Application Insights. Bez usługi Application Insights będzie można wyświetlać tylko dzienniki przesyłania strumieniowego na żywo.

3. Wybierz **plan/lokalizację usługi aplikacji** powyżej, aby wybrać inną lokalizację

4. Wybierz **pozycję Utwórz nowy,** a następnie nadaj planowi unikatową nazwę.

5. Wybierz lokalizację najbliższą. [Zobacz pełną mapę regionów platformy Azure tutaj.](https://azure.microsoft.com/global-infrastructure/regions/) 

    <img src="./media/functions-create-student-starter/Create-ASP.png" width="800">

6. Wybierz pozycję **Utwórz**, aby zaaprowizować aplikację funkcji i wdrożyć ją.

    <img src="./media/functions-create-student-starter/Function-create-end.png" width="315">

7. Wybierz ikonę Powiadomienie w prawym górnym rogu portalu i poszukaj komunikatu **Wdrażanie zakończone pomyślnie**.

    ![Definiowanie nowych ustawień aplikacji funkcji](./media/functions-create-student-starter/function-app-create-notification.png)

8. Wybierz pozycję **Przejdź do zasobu**, aby wyświetlić nową aplikację funkcji.

Następnie należy utworzyć funkcję w nowej aplikacji funkcji.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>Tworzenie funkcji wyzwalanej przez protokół HTTP

1. Rozwiń nową aplikację funkcyjną, a następnie wybierz **+** przycisk obok pozycji **Funkcje**, wybierz pozycję W **portalu**i wybierz pozycję **Kontynuuj**.

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

## <a name="supported-features-in-azure-for-students-starter"></a>Obsługiwane funkcje na platformie Azure dla studentów Starter

Na platformie Azure for Students Starter masz dostęp do większości funkcji środowiska uruchomieniowego usługi Azure Functions, z kilkoma kluczowymi ograniczeniami wymienionymi poniżej:

* Wyzwalacz HTTP jest jedynym obsługiwanym typem wyzwalacza.
    * Wszystkie powiązania wejściowe i wyjściowe są obsługiwane! [Zobacz pełną listę tutaj.](functions-triggers-bindings.md)
* Obsługiwane języki: 
    * C# (.NET Core 2)
    * JavaScript (Node.js 8 & 10)
    * F# (.NET Core 2)
    * [Zobacz języki obsługiwane w wyższych planach tutaj](supported-languages.md)
* Windows jest jedynym obsługiwanym systemem operacyjnym.
* Skala jest ograniczona do [jednego wystąpienia warstwy bezpłatnej](https://azure.microsoft.com/pricing/details/app-service/windows/) uruchomionej przez maksymalnie 60 minut każdego dnia. Będziesz bezserwerowo skalować od 0 do 1 wystąpienia automatycznie po odebraniu ruchu HTTP, ale nie dalej.
* Obsługiwane są tylko [wersje 2.x i nowsze](functions-versions.md) środowiska wykonawczego funkcji.
* Wszystkie narzędzia dla deweloperów są obsługiwane do edycji i publikowania funkcji. Obejmuje to vs kod, Visual Studio, interfejs wiersza polecenia platformy Azure i witryny Azure portal. Jeśli chcesz używać czegoś innego niż portal, musisz najpierw utworzyć aplikację w portalu, a następnie wybrać tę aplikację jako miejsce docelowe wdrożenia w preferowanym narzędziu.

## <a name="next-steps"></a>Następne kroki

Utworzono aplikację funkcyjną z prostą funkcją wyzwalaną HTTP! Teraz możesz eksplorować lokalne narzędzia, więcej języków, monitorowanie i integracje.

 * [Tworzenie pierwszej funkcji przy użyciu programu Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Tworzenie pierwszej funkcji przy użyciu programu Visual Studio Code](./functions-create-first-function-vs-code.md)
 * [Przewodnik dla deweloperów języka JavaScript w usłudze Azure Functions](./functions-reference-node.md)
 * [Łączenie się z bazą danych SQL usługi Azure za pomocą funkcji azure](./functions-scenario-database-table-cleanup.md)
 * [Dowiedz się więcej o powiązaniach HTTP usług Azure Functions](./functions-bindings-http-webhook.md).
 * [Monitorowanie funkcji platformy Azure](./functions-monitoring.md)
