---
title: Łączenie funkcji z usługą Azure Storage przy użyciu programu Visual Studio
description: Dowiedz się, jak dodać powiązanie danych wyjściowych C# , aby połączyć funkcje biblioteki klas z kolejką usługi Azure Storage przy użyciu programu Visual Studio.
author: ggailey777
ms.author: glenga
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
manager: gwallace
ms.openlocfilehash: 6ef6f4ae86f0f732164603bd8b38dc1bbef95dd9
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600259"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio"></a>Łączenie funkcji z usługą Azure Storage przy użyciu programu Visual Studio

Azure Functions umożliwia łączenie funkcji z usługami platformy Azure i innymi zasobami bez konieczności pisania kodu integracji. Te wstępnie zdefiniowane połączenia są nazywane *powiązaniami*. Powiązania umożliwiają używanie usług platformy Azure i innych zasobów jako danych wejściowych i wyjściowych funkcji.

Wykonywanie funkcji jest uruchamiane przez pojedynczy *wyzwalacz*. Wyzwalacz jest specjalnym typem powiązania danych wejściowych. Chociaż funkcja ma tylko jeden wyzwalacz, może mieć wiele powiązań wejściowych i wyjściowych. Aby dowiedzieć się więcej, zobacz temat [Azure Functions wyzwalacze i koncepcje powiązań](functions-triggers-bindings.md).

W tym artykule pokazano, jak za pomocą programu Visual Studio połączyć funkcję utworzoną w [Poprzedni artykuł szybkiego startu] z usługą Azure Storage. Powiązanie danych wyjściowych dodawane do tej funkcji zapisuje dane z żądania HTTP do wiadomości w kolejce usługi Azure queue storage. 

Większość powiązań wymaga przechowywanych parametrów połączenia używanych przez funkcje do uzyskiwania dostępu do usługi powiązanej. Aby ułatwić sobie korzystanie z konta magazynu utworzonego za pomocą aplikacji funkcji programu. Połączenie z tym kontem jest już przechowywane w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.  

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego artykułu należy: 

 - Ukończ [część 1 przewodnika Szybki Start dla programu Visual Studio] [./Functions-Create-First-Function-vs-Code.MD]. 

- Logowanie się do subskrypcji platformy Azure z poziomu programu Visual Studio

## <a name="download-the-function-app-settings"></a>Pobierz ustawienia aplikacji funkcji

W [poprzednim artykule szybki start](functions-create-first-function-vs-code.md)utworzono aplikację funkcji na platformie Azure wraz z wymaganym kontem magazynu. Parametry połączenia dla tego konta są bezpiecznie przechowywane w ustawieniach aplikacji na platformie Azure. W tym artykule opisano pisanie komunikatów w kolejce magazynu w ramach tego samego konta. Aby nawiązać połączenie z kontem magazynu podczas lokalnego uruchamiania funkcji, musisz pobrać ustawienia aplikacji do pliku *Local. Settings. JSON* . 

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**. 

1. W obszarze **Akcje**wybierz pozycję **Edytuj ustawienia Azure App Service**. 

    ![Edytuj ustawienia aplikacji](media/functions-add-output-binding-storage-queue-vs/edit-app-settings.png)

1. W obszarze **AzureWebJobsStorage**skopiuj wartość ciągu **zdalnego** do **lokalnego**, a następnie wybierz przycisk **OK**. 

Powiązanie magazynu, które używa `AzureWebJobsStorage` ustawienia dla połączenia, może teraz połączyć się z magazynem kolejki podczas lokalnego uruchamiania.

## <a name="register-binding-extensions"></a>Rejestrowanie rozszerzeń do wiązania

Ze względu na to, że jest używane powiązanie danych wyjściowych magazynu kolejek, przed uruchomieniem projektu należy zainstalować rozszerzenie powiązania magazynu. Z wyjątkiem wyzwalaczy HTTP i Timer, powiązania są implementowane jako pakiety rozszerzeń. 

1. Z **narzędzia** menu, wybierz opcję **Menedżera pakietów NuGet** > **Konsola Menedżera pakietów**. 

1. W konsoli programu uruchom następujące polecenie [instalacji pakietu](/nuget/tools/ps-ref-install-package) , aby zainstalować rozszerzenia magazynu:

    ```Command
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.6
    ````

Teraz można dodać powiązanie danych wyjściowych magazynu do projektu.

## <a name="add-an-output-binding"></a>Dodawanie powiązania danych wyjściowych

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Dodawanie kodu korzystającego z powiązania danych wyjściowych

Po zdefiniowaniu powiązania można użyć `name` powiązania, aby uzyskać do niego dostęp jako atrybut w sygnaturze funkcji. Za pomocą powiązania danych wyjściowych nie trzeba używać kodu zestawu SDK usługi Azure Storage do uwierzytelniania, uzyskiwania odwołania do kolejki lub zapisywania danych. Te zadania są wykonywane za pomocą środowiska uruchomieniowego usługi Functions i powiązania danych wyjściowych kolejki.

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Nowa kolejka o `outqueue` nazwie zostanie utworzona na koncie magazynu przez środowisko uruchomieniowe funkcji, gdy zostanie użyte powiązanie danych wyjściowych. Użyjesz Eksploratora chmury do sprawdzenia, czy kolejka została utworzona wraz z nową wiadomością.

## <a name="examine-the-output-queue"></a>Sprawdzanie kolejki wyjściowej

1. W programie Visual Studio z menu **Widok** wybierz opcję **Eksplorator chmury**.

1. W **Eksploratorze chmury**rozwiń swoje konta subskrypcji i **magazynu**platformy Azure, a następnie rozwiń konto magazynu używane przez funkcję. Jeśli nie pamiętasz nazwy konta magazynu, sprawdź `AzureWebJobsStorage` ustawienie parametrów połączenia w pliku *Local. Settings. JSON* .  

1. Rozwiń węzeł **kolejki** , a następnie kliknij dwukrotnie kolejkę o nazwie dequeueing, aby wyświetlić zawartość kolejki w programie Visual Studio. 

   Kolejka zawiera komunikat utworzony za pomocą powiązania danych wyjściowych kolejki po uruchomieniu funkcji wyzwalanej przez protokół HTTP. Jeśli funkcja została wywołana przy użyciu domyślnego elementu `name` o wartości *Azure*, komunikat w kolejce to *Nazwa przekazana do funkcji: Azure*.

    ![Komunikat w kolejce wyświetlany w Eksplorator usługi Azure Storage](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Uruchom ponownie funkcję, Wyślij kolejne żądanie, a w kolejce zobaczysz nowy komunikat.  

Teraz można ponownie opublikować zaktualizowaną aplikację funkcji na platformie Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Wdróż ponownie i sprawdź zaktualizowaną aplikację

1. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Publikuj**, a następnie wybierz polecenie **Publikuj** , aby ponownie opublikować projekt na platformie Azure.

1. Po zakończeniu wdrażania możesz użyć przeglądarki, aby przetestować ponownie wdrożoną funkcję. Tak jak wcześniej, dołącz ciąg `&name=<yourname>` zapytania do adresu URL.

1. Ponownie [Wyświetl komunikat w kolejce magazynu](#examine-the-output-queue) , aby sprawdzić, czy powiązanie danych wyjściowych ponownie generuje nowy komunikat w kolejce.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Została zaktualizowana funkcja wyzwalana przez protokół HTTP w celu zapisania danych w kolejce magazynu. Aby dowiedzieć się więcej na temat opracowywania funkcji, zobacz [programowanie Azure Functions przy użyciu programu Visual Studio](functions-develop-vs.md).

Następnie należy włączyć monitorowanie Application Insights dla aplikacji funkcji:

> [!div class="nextstepaction"]
> [Włącz integrację Application Insights](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/
[Poprzedni artykuł szybkiego startu]: functions-create-your-first-function-visual-studio.md
