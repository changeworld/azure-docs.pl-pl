---
title: Łączenie funkcji z usługą Azure Storage przy użyciu programu Visual Studio
description: Dowiedz się, jak dodać powiązanie danych wyjściowych, aby połączyć funkcje biblioteki klas języka C# z kolejką usługi Azure Storage przy użyciu programu Visual Studio.
ms.date: 07/22/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 171479a0f60741b545a171315e99cc5e4e8bc843
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74849211"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio"></a>Łączenie funkcji z usługą Azure Storage przy użyciu programu Visual Studio

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

W tym artykule pokazano, jak używać programu Visual Studio do łączenia funkcji utworzonej w [poprzednim artykule szybki start] z usługą Azure Storage. Powiązanie danych wyjściowych, które można dodać do tej funkcji zapisuje dane z żądania HTTP do wiadomości w kolejce magazynu kolejki platformy Azure. 

Większość powiązań wymaga przechowywanego ciągu połączenia, którego funkcja używa do uzyskiwania dostępu do usługi powiązanej. Aby ułatwić, należy użyć konta magazynu utworzonego za pomocą aplikacji funkcji. Połączenie z tym kontem jest już `AzureWebJobsStorage`przechowywane w ustawieniach aplikacji o nazwie .  

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego artykułu należy: 

 - Ukończ [część 1 przewodnika Szybki start programu Visual Studio](./functions-create-first-function-vs-code.md). 

- Zaloguj się do subskrypcji platformy Azure w programie Visual Studio.

## <a name="download-the-function-app-settings"></a>Pobierz ustawienia aplikacji funkcji

W [poprzednim artykule szybki start](functions-create-first-function-vs-code.md)utworzono aplikację funkcji na platformie Azure wraz z wymaganym kontem magazynu. Parametry połączenia dla tego konta są bezpiecznie przechowywane w ustawieniach aplikacji na platformie Azure. W tym artykule piszesz wiadomości do kolejki magazynu na tym samym koncie. Aby połączyć się z kontem magazynu podczas lokalnego uruchamiania funkcji, należy pobrać ustawienia aplikacji do pliku *local.settings.json.* 

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**. 

1. W obszarze **Akcje**wybierz pozycję **Edytuj ustawienia usługi Azure App Service**. 

    ![Edytowanie ustawień aplikacji](media/functions-add-output-binding-storage-queue-vs/edit-app-settings.png)

1. W obszarze **AzureWebJobsStorage**skopiuj wartość ciągu **zdalnego** do **lokalnego,** a następnie wybierz przycisk **OK**. 

Powiązanie magazynu, który `AzureWebJobsStorage` używa ustawienia dla połączenia, można teraz połączyć się z magazynu kolejki podczas uruchamiania lokalnie.

## <a name="register-binding-extensions"></a>Rejestrowanie rozszerzeń do wiązania

Ponieważ używasz powiązania danych wyjściowych magazynu kolejki, przed uruchomieniem projektu należy zainstalować rozszerzenie powiązania magazynu. Z wyjątkiem wyzwalaczy HTTP i timer, powiązania są implementowane jako pakiety rozszerzenia. 

1. Z menu **Narzędzia** wybierz polecenie > **Konsola Menedżera pakietów** **Programu NuGet Package**Manager . 

1. Aby zainstalować rozszerzenia magazynu, w konsoli uruchom następujące polecenie [Install-Package:](/nuget/tools/ps-ref-install-package)

    ```Command
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.6
    ````

Teraz można dodać powiązanie danych wyjściowych magazynu do projektu.

## <a name="add-an-output-binding"></a>Dodawanie powiązania danych wyjściowych

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Dodawanie kodu korzystającego z powiązania danych wyjściowych

Po zdefiniowaniu powiązania można użyć `name` powiązania, aby uzyskać do niego dostęp jako atrybut w podpisie funkcji. Przy użyciu powiązania danych wyjściowych, nie trzeba używać kodu SDK usługi Azure Storage do uwierzytelniania, pobierania odwołania do kolejki lub zapisywania danych. Powiązania środowiska wykonawczego i danych wyjściowych kolejki funkcji wykonaj te zadania za Ciebie.

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Nowa kolejka `outqueue` o nazwie jest tworzona na koncie magazynu przez środowisko uruchomieniowe functions, gdy po raz pierwszy używane jest powiązanie danych wyjściowych. Użyjesz Cloud Explorer, aby sprawdzić, czy kolejka została utworzona wraz z nową wiadomością.

## <a name="examine-the-output-queue"></a>Sprawdzanie kolejki wyjściowej

1. W programie Visual Studio z menu **Widok** wybierz polecenie **Cloud Explorer**.

1. W **Eksploratorze chmury**rozwiń swoją subskrypcję platformy Azure i **konta magazynu,** a następnie rozwiń konto magazynu używane przez funkcję. Jeśli nie pamiętasz nazwy konta magazynu, `AzureWebJobsStorage` sprawdź ustawienie ciągu połączenia w pliku *local.settings.json.*  

1. Rozwiń węzeł **Kolejki,** a następnie kliknij dwukrotnie kolejkę o nazwie **outue,** aby wyświetlić zawartość kolejki w programie Visual Studio. 

   Kolejka zawiera komunikat utworzony za pomocą powiązania danych wyjściowych kolejki po uruchomieniu funkcji wyzwalanej przez protokół HTTP. Jeśli funkcja została wywołana przy użyciu domyślnego elementu `name` o wartości *Azure*, komunikat w kolejce to *Nazwa przekazana do funkcji: Azure*.

    ![Komunikat kolejki wyświetlany w Eksploratorze usługi Azure Storage](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. Uruchom tę funkcję ponownie, wyślij kolejne żądanie, a w kolejce pojawi się nowa wiadomość.  

Teraz nadszedł czas, aby ponownie opublikować zaktualizowaną aplikację funkcji na platformie Azure.

## <a name="redeploy-and-verify-the-updated-app"></a>Ponowne wdrożenie i zweryfikowanie zaktualizowanej aplikacji

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Publikuj**, a następnie wybierz pozycję **Publikuj,** aby ponownie opublikować projekt na platformie Azure.

1. Po zakończeniu wdrażania można ponownie użyć przeglądarki do przetestowania ponownie wdrożeniu funkcji. Tak jak poprzednio, `&name=<yourname>` dołącz ciąg zapytania do adresu URL.

1. Ponownie [wyświetl komunikat w kolejce magazynu,](#examine-the-output-queue) aby sprawdzić, czy powiązanie danych wyjściowych ponownie generuje nową wiadomość w kolejce.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Zaktualizowano funkcję wyzwalaną http, aby zapisywać dane w kolejce magazynu. Aby dowiedzieć się więcej na temat tworzenia funkcji, zobacz [Tworzenie funkcji platformy Azure przy użyciu programu Visual Studio.](functions-develop-vs.md)

Następnie należy włączyć monitorowanie usługi Application Insights dla aplikacji funkcji:

> [!div class="nextstepaction"]
> [Enable Application Insights integration (Włączanie integracji z usługą Application Insights)](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/
[Poprzedni artykuł szybki start]: functions-create-your-first-function-visual-studio.md
