---
title: Tworzenie aplikacji funkcji w systemie Linux w witrynie Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć pierwszą funkcję platformy Azure do wykonywania bezserwerowego przy użyciu witryny Azure Portal.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: glenga
ms.custom: ''
ms.openlocfilehash: cc99bc4345c388f22e72957590f3917a85e214e0
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126631"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Tworzenie aplikacji funkcji w systemie Linux w ramach planu usługi Azure App Service

Usługa Azure Functions umożliwia obsługę funkcji w systemie Linux w domyślnym kontenerze usługi Azure App Service. Tym artykule przedstawiono sposób użycia [witryny Azure portal](https://portal.azure.com) do tworzenia aplikacji hostowanych w systemie Linux — funkcja, która działa w [planu usługi App Service](functions-scale.md#app-service-plan). Możesz również [skorzystać z własnego kontenera niestandardowego](functions-create-function-linux-custom-image.md).

![Tworzenie aplikacji funkcji w witrynie Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com> przy użyciu danych konta Azure.

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Do obsługi wykonywania funkcji w systemie Linux potrzebna jest aplikacja funkcji. Aplikacja funkcji zapewnia środowisko do wykonywania kodu funkcji. Umożliwia ona grupowanie funkcji w ramach jednostki logicznej, co ułatwia wdrażanie i udostępnianie zasobów oraz zarządzanie nimi. W tym artykule umożliwiają utworzenie planu usługi App Service podczas tworzenia aplikacji funkcji.

1. Wybierz **Utwórz zasób** znajdujący się w lewym górnym rogu witryny Azure portal, następnie wybierz opcję **obliczenia** > **aplikacji funkcji**.

    ![Tworzenie aplikacji funkcji w witrynie Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-create-flow.png)

2. Użyj ustawień aplikacji funkcji podanych w tabeli pod obrazem.

    ![Definiowanie nowych ustawień aplikacji funkcji](./media/create-function-app-linux-app-service-plan/function-app-create-flow2.png)

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa aplikacji** | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nową aplikację funkcji. Prawidłowe znaki to `a-z`, `0-9` i `-`.  | 
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzona ta nowa aplikacja funkcji. | 
    | **[Grupa zasobów](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Nazwa nowej grupy zasobów, w której ma zostać utworzona aplikacja funkcji. |
    | **OS** | Linux | Aplikacja funkcji jest uruchamiana w systemie Linux. |
    | **Publikowanie** | Kod | Domyślny kontener systemu Linux dla Twojego **stosu środowiska uruchomieniowego** jest używany. To wszystko, czego potrzebujesz, aby zapewnić kodu projektu aplikacji funkcji. Innym rozwiązaniem jest publikowanie niestandardowego [obrazu platformy Docker](functions-create-function-linux-custom-image.md). |
    | **[Plan hostingu](functions-scale.md)** | Plan usługi App Service | Plan hostingu określający sposób przydzielania zasobów do aplikacji funkcji. Po uruchomieniu w ramach planu usługi App Service, możesz kontrolować [skalowanie aplikacji funkcji](functions-scale.md).  |
    | **Plan usługi App Service/lokalizacja** | Tworzenie planu | Wybierz **Utwórz nową** i podaj **planu usługi App Service** nazwy. Wybierz **lokalizacji** w [region](https://azure.microsoft.com/regions/) okolicy lub w pobliżu innych usług Twoje funkcje uzyskują dostęp. Wybierz odpowiednie  **[warstwa cenowa](https://azure.microsoft.com/pricing/details/app-service/linux/)**. <br/>Nie można uruchomić aplikacji funkcji z systemem Linux i Windows, w tym samym planie usługi App Service. |
    | **Stos środowiska uruchomieniowego** | Preferowany język | Wybierz środowisko uruchomieniowe, które obsługuje ulubiony język programowania funkcji. Wybierz **.NET** dla funkcji w językach C# i F#. [Obsługa języka Python](functions-reference-python.md) znajduje się w tej chwili w wersji zapoznawczej. |
    | **[Storage](../storage/common/storage-quickstart-create-account.md)** |  Nazwa unikatowa w skali globalnej |  Utwórz konto magazynu używane przez aplikację funkcji. Nazwy kont usługi Storage muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Możesz również użyć istniejącego konta, które musi spełniać [wymagania dotyczące konta magazynu](functions-scale.md#storage-account-requirements). |
    | **[Application Insights](functions-monitoring.md)** | Enabled (Włączony) | Usługa Application Insights jest domyślnie wyłączona. Firma Microsoft zaleca włączenie teraz Integracja usługi Application Insights i Wybieranie lokalizacji hostowania w pobliżu danej lokalizacji planu usługi App Service. Jeśli chcesz zrobić to później, zobacz [monitora usługi Azure Functions](functions-monitoring.md).  |

3. Wybierz pozycję **Utwórz**, aby zaaprowizować aplikację funkcji i wdrożyć ją.

4. Wybierz ikonę Powiadomienie w prawym górnym rogu portalu i poszukaj komunikatu **Wdrażanie zakończone pomyślnie**.

    ![Definiowanie nowych ustawień aplikacji funkcji](./media/create-function-app-linux-app-service-plan/function-app-create-notification.png)

5. Wybierz pozycję **Przejdź do zasobu**, aby wyświetlić nową aplikację funkcji.

> [!TIP]
> Jeśli masz problem ze znalezieniem aplikacji funkcji w portalu, spróbuj [dodać aplikacje funkcji do ulubionych w witrynie Azure Portal](functions-how-to-use-azure-function-app-settings.md#favorite).

Następnie należy utworzyć funkcję w nowej aplikacji funkcji. Mimo aplikacja funkcji jest dostępna, może upłynąć kilka minut, aby być w pełni zainicjowane.

## <a name="create-function"></a>Tworzenie funkcji wyzwalanej przez protokół HTTP

W tej sekcji przedstawiono sposób tworzenia funkcji w nowej aplikacji funkcji w portalu.

> [!NOTE]
> Środowisko programistyczne portalu może być przydatne do wypróbowania usługi Azure Functions. W większości przypadków należy wziąć pod uwagę opracowywania funkcji lokalnie i publikowania projektu do aplikacji funkcji przy użyciu [programu Visual Studio Code](functions-create-first-function-vs-code.md#create-an-azure-functions-project) lub [podstawowych narzędzi usługi Azure Functions](functions-run-local.md#create-a-local-functions-project).  

1. W nowej aplikacji funkcji, wybierz **Przegląd** kartę, a po jego załadowaniu całkowicie wybierz **+ nowa funkcja**.

    ![Utwórz nową funkcję na karcie Przegląd](./media/create-function-app-linux-app-service-plan/overview-create-function.png)

1. W **Szybki Start** kartę, wybrać **w portalu**i wybierz **Kontynuuj**.

    ![Wybierz własną platformę deweloperską funkcji.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-choose-portal.png)

1. Wybierz opcję **Element WebHook + interfejs API**, a następnie wybierz opcję **Utwórz**.

    ![Szybkie rozpoczynanie pracy z usługą Functions w witrynie Azure Portal.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-node-webhook.png)

Funkcja zostanie utworzona przy użyciu szablonu funkcji wyzwalanej przez protokół HTTP właściwego dla danego języka.

Możesz teraz uruchomić nową funkcję, wysyłając żądanie HTTP.

## <a name="test-the-function"></a>Testowanie funkcji

1. W nowej funkcji kliknij pozycję **</> Pobierz adres URL funkcji** w prawym górnym rogu, wybierz pozycję **domyślne (klawisz funkcji)**, a następnie kliknij pozycję **Kopiuj**. 

    ![Kopiowanie adresu URL funkcji z witryny Azure Portal](./media/create-function-app-linux-app-service-plan/function-app-develop-tab-testing.png)

2. Wklej adres URL funkcji do paska adresu przeglądarki. Dodaj wartość ciągu zapytania `&name=<yourname>` na końcu tego adresu URL, a następnie naciśnij klawisz `Enter` na klawiaturze, aby wykonać żądanie. W przeglądarce powinna zostać wyświetlona odpowiedź zwrócona przez funkcję.  

    Poniższy przykład przedstawia odpowiedź w przeglądarce:

    ![Odpowiedź funkcji wyświetlona w przeglądarce.](./media/create-function-app-linux-app-service-plan/function-app-browser-testing.png)

    Adres URL żądania zawiera klucz, który domyślnie jest wymagany do uzyskania dostępu do funkcji za pośrednictwem protokołu HTTP.

3. Gdy funkcja działa, informacje o śledzeniu są zapisywane w dziennikach. Aby wyświetlić dane wyjściowe śledzenia z poprzedniego wykonania, wróć do funkcji w portalu, a następnie kliknij strzałkę w dół w dolnej części ekranu, aby rozwinąć pozycję **Dzienniki**.

   ![Podgląd dziennika usługi Functions w witrynie Azure Portal.](./media/create-function-app-linux-app-service-plan/function-view-logs.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Kolejne kroki

Utworzono aplikację funkcji z prostą funkcją wyzwalaną przez protokół HTTP.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Aby uzyskać więcej informacji, zobacz [Powiązania protokołu HTTP w usłudze Azure Functions](functions-bindings-http-webhook.md).
