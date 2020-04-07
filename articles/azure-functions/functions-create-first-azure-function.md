---
title: Tworzenie pierwszej funkcji w witrynie Azure Portal
description: Dowiedz się, jak utworzyć pierwszą funkcję platformy Azure do wykonywania bezserwerowego przy użyciu witryny Azure Portal.
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: a96d2ede80b4c57e7b85048379a4bfb66cacfd52
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754845"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Tworzenie pierwszej funkcji w witrynie Azure Portal

Usługa Azure Functions umożliwia uruchamianie kodu w środowisku bezserwerowym bez konieczności tworzenia maszyny wirtualnej (VM) lub publikowania aplikacji sieci web. W tym artykule dowiesz się, jak używać usługi Azure Functions do tworzenia funkcji wyzwalanych HTTP "hello world" w witrynie Azure portal.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Jeśli jesteś deweloperem języka C#, należy rozważyć [utworzenie pierwszej funkcji w programie Visual Studio 2019,](functions-create-your-first-function-visual-studio.md) a nie w portalu. 

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do [witryny Azure portal](https://portal.azure.com) za pomocą konta platformy Azure.

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Do obsługi wykonywania funkcji potrzebna jest aplikacja funkcji. Aplikacja funkcji umożliwia grupowanie funkcji jako jednostki logicznej w celu łatwiejszego zarządzania, wdrażania, skalowania i udostępniania zasobów.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Następnie utwórz funkcję w nowej aplikacji funkcji.

## <a name="create-an-http-triggered-function"></a><a name="create-function"></a>Tworzenie funkcji wyzwalanej przez protokół HTTP

1. Rozwiń nową aplikację funkcyjną, wybierz **+** przycisk obok pozycji **Funkcje**, wybierz pozycję W **portalu**, a następnie wybierz pozycję **Kontynuuj**.

    ![Funkcje szybkiego startu przy wyborze platformy.](./media/functions-create-first-azure-function/function-app-quickstart-choose-portal.png)

1. Wybierz **pozycję WebHook + API**, a następnie wybierz pozycję **Utwórz**.

    ![Szybkie rozpoczynanie pracy z usługą Functions w witrynie Azure Portal.](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

   Funkcja zostanie utworzona przy użyciu szablonu funkcji wyzwalanej przez protokół HTTP właściwego dla danego języka.

Możesz teraz uruchomić nową funkcję, wysyłając żądanie HTTP.

## <a name="test-the-function"></a>Testowanie funkcji

1. W nowej funkcji wybierz **</> Pobierz adres URL funkcji** w prawym górnym rogu. 

1. W oknie dialogowym **Pobierz adres URL funkcji** wybierz pozycję **domyślny (klawisz funkcji)** z listy rozwijanej, a następnie wybierz pozycję **Kopiuj**. 

    ![Kopiowanie adresu URL funkcji z witryny Azure Portal](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Wklej adres URL funkcji do paska adresu przeglądarki. Dodaj wartość `&name=<your_name>` ciągu zapytania na końcu tego adresu URL i naciśnij klawisz Enter, aby uruchomić żądanie. 

    Poniższy przykład przedstawia odpowiedź w przeglądarce:

    ![Odpowiedź funkcji wyświetlona w przeglądarce.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    Adres URL żądania zawiera klucz, który domyślnie jest wymagany do uzyskania dostępu do funkcji za pośrednictwem protokołu HTTP.

1. Gdy funkcja działa, informacje o śledzeniu są zapisywane w dziennikach. Aby wyświetlić dane wyjściowe śledzenia z poprzedniego wykonania, wróć do funkcji w portalu i wybierz strzałkę u dołu ekranu, aby rozwinąć **dzienniki**.

   ![Podgląd dziennika usługi Functions w witrynie Azure Portal.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

