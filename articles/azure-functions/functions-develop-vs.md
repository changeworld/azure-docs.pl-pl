---
title: Opracowywanie funkcji usługi Azure Functions przy użyciu programu Visual Studio
description: Dowiedz się, jak tworzyć i testować usługi Azure Functions przy użyciu narzędzi Azure Functions Tools for Visual Studio 2019.
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: b3d683153a1e70f4c65dcc5e401f4ee702af8b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277104"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Opracowywanie funkcji usługi Azure Functions przy użyciu programu Visual Studio  

Visual Studio umożliwia tworzenie, testowanie i wdrażanie funkcji biblioteki klas języka C# na platformie Azure. Jeśli to środowisko jest twoje pierwsze z usługami Azure Functions, możesz dowiedzieć się więcej na [wprowadzenie do usługi Azure Functions.](functions-overview.md)

Visual Studio zapewnia następujące korzyści podczas opracowywania funkcji: 

* Edytuj, skompiluj i uruchom funkcje na lokalnym komputerze deweloperskim. 
* Opublikuj swój projekt usługi Azure Functions bezpośrednio na platformie Azure i utwórz zasoby platformy Azure w razie potrzeby. 
* Użyj atrybutów C#, aby zadeklarować powiązania funkcji bezpośrednio w kodzie języka C#.
* Tworzenie i wdrażanie wstępnie skompilowanych funkcji języka C#. Wstępnie spełnione funkcje zapewniają lepszą wydajność zimnego rozruchu niż funkcje oparte na skryptach języka C#. 
* Kod funkcji w języku C# mając jednocześnie wszystkie korzyści z rozwoju programu Visual Studio. 

Ten artykuł zawiera szczegółowe informacje na temat sposobu korzystania z programu Visual Studio do tworzenia funkcji biblioteki klas języka C# i publikowania ich na platformie Azure. Przed przeczytaniem tego artykułu należy ukończyć [przewodnik Szybki start funkcji programu Visual Studio](functions-create-your-first-function-visual-studio.md). 

O ile nie zaznaczono inaczej, procedury i przykłady pokazane są dla programu Visual Studio 2019. 

## <a name="prerequisites"></a>Wymagania wstępne

Narzędzia funkcji platformy Azure są zawarte w obciążeniu deweloperów platformy Azure programu Visual Studio, począwszy od programu Visual Studio 2017. Upewnij się, że uwzględnisz obciążenie **deweloperskie platformy Azure** w instalacji programu Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Inne zasoby, które są potrzebne, takie jak konto usługi Azure Storage, są tworzone w ramach subskrypcji podczas procesu publikowania.

> [!NOTE]
> W programie Visual Studio 2017 obciążenie deweloperskie platformy Azure instaluje narzędzia azure functions jako oddzielne rozszerzenie. Podczas aktualizowania programu Visual Studio 2017 upewnij się również, że używasz [najnowszej wersji](#check-your-tools-version) narzędzi usługi Azure Functions. W poniższych sekcjach pokazano, jak sprawdzić i (w razie potrzeby) zaktualizować rozszerzenie narzędzia Azure Functions Tools w programie Visual Studio 2017. 
>
> Pomiń tę sekcję podczas korzystania z programu Visual Studio 2019.

### <a name="check-your-tools-version-in-visual-studio-2017"></a><a name="check-your-tools-version"></a>Sprawdzanie wersji narzędzi w programie Visual Studio 2017

1. Z menu **Narzędzia** wybierz polecenie **Rozszerzenia i aktualizacje**. Rozwiń węzeł **Zainstalowane** > **narzędzia** i wybierz pozycję Azure Functions and Web **Jobs Tools**.

    ![Sprawdzanie wersji narzędzi funkcji](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. Zanotuj **zainstalowaną wersję**. Możesz porównać tę wersję z najnowszą wersją [wymienioną w informacjach o wersji](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md). 

1. Jeśli wersja jest starsza, zaktualizuj narzędzia w programie Visual Studio, jak pokazano w poniższej sekcji.

### <a name="update-your-tools-in-visual-studio-2017"></a>Aktualizowanie narzędzi w programie Visual Studio 2017

1. W oknie dialogowym **Rozszerzenia i aktualizacje** rozwiń pozycję **Aktualizuje** > **witrynę Visual Studio Marketplace**, wybierz pozycję Azure Functions and Web Jobs **Tools** i select **Update**.

    ![Aktualizowanie wersji narzędzi funkcji](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. Po pobraniu aktualizacji narzędzi zamknij program Visual Studio, aby wyzwolić aktualizację narzędzi przy użyciu instalatora VSIX.

1. W instalatorze wybierz przycisk **OK,** aby rozpocząć, a następnie **zmodyfikuj,** aby zaktualizować narzędzia. 

1. Po zakończeniu aktualizacji wybierz pozycję **Zamknij** i uruchom ponownie program Visual Studio.

> [!NOTE]  
> W programie Visual Studio 2019 i nowszych rozszerzenie narzędzi usługi Azure Functions jest aktualizowane w ramach programu Visual Studio.  

## <a name="create-an-azure-functions-project"></a>Tworzenie projektu usługi Azure Functions

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Szablon projektu tworzy projekt języka C#, instaluje pakiet `Microsoft.NET.Sdk.Functions` NuGet i ustawia platformę docelową. Nowy projekt ma następujące pliki:

* **host.json**: Umożliwia skonfigurowanie hosta funkcji. Te ustawienia mają zastosowanie zarówno podczas uruchamiania lokalnie, jak i na platformie Azure. Aby uzyskać więcej informacji, zobacz [host.json reference](functions-host-json.md).

* **local.settings.json**: Zachowuje ustawienia używane podczas uruchamiania funkcji lokalnie. Te ustawienia nie są używane podczas uruchamiania na platformie Azure. Aby uzyskać więcej informacji, zobacz [Plik ustawień lokalnych](#local-settings-file).

    >[!IMPORTANT]
    >Ponieważ plik local.settings.json może zawierać wpisy tajne, należy wykluczyć go z kontroli źródła projektu. Ustawienie **Kopiuj do katalogu wyjściowego** dla tego pliku powinno być zawsze **kopiuj, jeśli jest nowsze**. 

Aby uzyskać więcej informacji, zobacz [Projekt biblioteki klas funkcji](functions-dotnet-class-library.md#functions-class-library-project).

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Ustawienia w local.settings.json nie są przekazywane automatycznie podczas publikowania projektu. Aby upewnić się, że te ustawienia istnieją również w aplikacji funkcji na platformie Azure, należy przekazać je po opublikowaniu projektu. Aby dowiedzieć się więcej, zobacz [Ustawienia aplikacji Funkcji](#function-app-settings).

Wartości w **ConnectionStrings** nigdy nie są publikowane.

Wartości ustawień aplikacji funkcji można również odczytać w kodzie jako zmienne środowiskowe. Aby uzyskać więcej informacji, zobacz [Zmienne środowiskowe](functions-dotnet-class-library.md#environment-variables).

## <a name="configure-the-project-for-local-development"></a>Konfigurowanie projektu do rozwoju lokalnego

Środowisko wykonawcze Functions używa konta usługi Azure Storage wewnętrznie. Dla wszystkich typów wyzwalaczy innych niż HTTP i webhooks należy ustawić **values.AzureWebJobsStorage** klucz prawidłowego ciągu połączenia konta usługi Azure Storage. Aplikacja funkcji można również użyć [emulatora magazynu platformy Azure](../storage/common/storage-use-emulator.md) dla ustawienia połączenia **AzureWebJobsStorage,** które jest wymagane przez projekt. Aby użyć emulatora, ustaw wartość **usługi AzureWebJobsStorage** na `UseDevelopmentStorage=true`. Zmień to ustawienie na rzeczywisty ciąg połączenia konta magazynu przed wdrożeniem.

Aby ustawić parametry połączenia konta magazynu:

1. W programie Visual Studio otwórz **Cloud Explorer**, rozwiń pozycję **Konto** > magazynu**Twoje konto magazynu**, a następnie na karcie Właściwości skopiuj wartość **podstawowego ciągu połączenia.** **Properties**

2. W projekcie otwórz plik local.settings.json i ustaw wartość klucza **AzureWebJobsStorage** na skopiowany ciąg połączenia.

3. Powtórz poprzedni krok, aby dodać unikatowe klucze do **tablicy Wartości** dla innych połączeń wymaganych przez funkcje. 

## <a name="add-a-function-to-your-project"></a>Dodawanie funkcji do projektu

W c# funkcje biblioteki klasy powiązania używane przez funkcję są definiowane przez zastosowanie atrybutów w kodzie. Podczas tworzenia wyzwalaczy funkcji z podanych szablonów atrybuty wyzwalacza są stosowane dla Ciebie. 

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy węzeł projektu i wybierz polecenie **Dodaj** > **Nowy element**. Wybierz **pozycję Funkcja platformy Azure**, wpisz **nazwę** klasy i kliknij przycisk **Dodaj**.

2. Wybierz wyzwalacz, ustaw właściwości powiązania i kliknij przycisk **Utwórz**. W poniższym przykładzie przedstawiono ustawienia podczas tworzenia funkcji wyzwalanego magazynu kolejki. 

    ![Tworzenie funkcji wyzwalania kolejki](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    W tym przykładzie wyzwalania użyto ciągu połączenia z kluczem o nazwie **QueueStorage**. To ustawienie ciągu połączenia musi być zdefiniowane w [pliku local.settings.json](functions-run-local.md#local-settings-file).

3. Sprawdź nowo dodanej klasy. Zostanie wyświetloną statyczną metodę **Run,** która jest przypisywana z atrybutem **FunctionName.** Ten atrybut wskazuje, że metoda jest punktem wejścia dla funkcji.

    Na przykład następująca klasa Języka C# reprezentuje podstawową funkcję wyzwalania magazynu kolejki:

    ```csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.Extensions.Logging;

    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]
            public static void Run([QueueTrigger("myqueue-items", 
                Connection = "QueueStorage")]string myQueueItem, ILogger log)
            {
                log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    }
    ```

    Atrybut specyficzne dla powiązania jest stosowany do każdego parametru wiązania dostarczonego do metody punktu wejścia. Atrybut przyjmuje informacje wiązania jako parametry. W poprzednim przykładzie pierwszy parametr ma zastosowany atrybut **QueueTrigger,** wskazując funkcję wyzwalaną kolejką. Nazwa kolejki i nazwa ustawienia ciągu połączenia są przekazywane jako parametry do atrybutu **QueueTrigger.** Aby uzyskać więcej informacji, zobacz [Powiązania magazynu kolejki platformy Azure dla usług Azure Functions](functions-bindings-storage-queue-trigger.md).

Powyższa procedura służy do dodawania większej liczby funkcji do projektu aplikacji funkcji. Każda funkcja w projekcie może mieć inny wyzwalacz, ale funkcja musi mieć dokładnie jeden wyzwalacz. Aby uzyskać więcej informacji, zobacz [Usługi Azure Functions wyzwalania i pojęć powiązań](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Dodawanie powiązań

Podobnie jak w odniesieniu do wyzwalaczy, powiązania danych wejściowych i wyjściowych są dodawane do funkcji jako atrybuty powiązania. Dodaj powiązania do funkcji w następujący sposób:

1. Upewnij się, że [projekt został skonfigurowany do rozwoju lokalnego](#configure-the-project-for-local-development).

2. Dodaj odpowiedni pakiet rozszerzenia NuGet dla określonego powiązania. Aby uzyskać więcej informacji, zobacz [lokalne tworzenie języka C# przy użyciu programu Visual Studio](./functions-bindings-register.md#local-csharp) w wyzwalacza i powiązania artykułu. Wymagania pakietu NuGet specyficzne dla powiązania znajdują się w artykule referencyjnym dla powiązania. Na przykład znajdź wymagania dotyczące pakietu dla wyzwalacza Usługi zdarzeń w [artykule odwołania do powiązania Usługi zdarzeń](functions-bindings-event-hubs.md).

3. Jeśli istnieją ustawienia aplikacji, których wymaga powiązanie, dodaj je do kolekcji **Wartości** w [pliku ustawień lokalnych](functions-run-local.md#local-settings-file). Wartości te są używane, gdy funkcja działa lokalnie. Gdy funkcja jest uruchamiana w aplikacji funkcji na platformie Azure, używane są [ustawienia aplikacji funkcji.](#function-app-settings)

4. Dodaj odpowiedni atrybut powiązania do podpisu metody. W poniższym przykładzie komunikat kolejki wyzwala funkcję, a powiązanie danych wyjściowych tworzy nową wiadomość kolejki z tym samym tekstem w innej kolejce.

    ```csharp
    public static class SimpleExampleWithOutput
    {
        [FunctionName("CopyQueueMessage")]
        public static void Run(
            [QueueTrigger("myqueue-items-source", Connection = "AzureWebJobsStorage")] string myQueueItem, 
            [Queue("myqueue-items-destination", Connection = "AzureWebJobsStorage")] out string myQueueItemCopy,
            ILogger log)
        {
            log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
            myQueueItemCopy = myQueueItem;
        }
    }
    ```
   Połączenie z magazynem kolejki `AzureWebJobsStorage` jest uzyskiwane z tego ustawienia. Aby uzyskać więcej informacji, zobacz artykuł referencyjny dla określonego powiązania. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Testowanie funkcji

Podstawowe narzędzia usługi Azure Functions umożliwiają uruchamianie projektu usługi Azure Functions na lokalnym komputerze deweloperskim. Monit o zainstalowanie tych narzędzi pojawia się przy pierwszym uruchomieniu funkcji w programie Visual Studio.

Aby przetestować funkcję, naciśnij klawisz F5. Po wyświetleniu monitu zaakceptuj żądanie programu Visual Studio dotyczące pobrania i zainstalowania podstawowych narzędzi usługi Azure Functions (CLI). Może także być konieczne włączenie wyjątku zapory, aby umożliwić narzędziom obsługę żądań HTTP.

Po uruchomieniu projektu można przetestować kod, jak przetestować wdrożoną funkcję. Aby uzyskać więcej informacji, zobacz [Strategie testowania kodu w usłudze Azure Functions.](functions-test-a-function.md) Podczas uruchamiania w trybie debugowania punkty przerwania są trafione w programie Visual Studio zgodnie z oczekiwaniami. 

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->

Aby dowiedzieć się więcej na temat korzystania z podstawowych narzędzi azure functions, zobacz [Kodowanie i testowanie funkcji platformy Azure lokalnie.](functions-run-local.md)

## <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Podczas publikowania z programu Visual Studio używana jest jedna z dwóch metod wdrażania:

* [Wdrażanie w sieci Web:](functions-deployment-technologies.md#web-deploy-msdeploy)pakiety i wdraża aplikacje systemu Windows na dowolnym serwerze usług IIS.
* [Zip Deploy z włączoną funkcją Uruchamianie od pakietu:](functions-deployment-technologies.md#zip-deploy)zalecane dla wdrożeń usługi Azure Functions.

Skorzystaj z poniższych kroków, aby opublikować projekt w aplikacji funkcji na platformie Azure.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Ustawienia aplikacji funkcji

Wszystkie ustawienia dodane w local.settings.json muszą również zostać dodane do aplikacji funkcji na platformie Azure. Te ustawienia nie są przekazywane automatycznie podczas publikowania projektu.

Najprostszym sposobem przekazania wymaganych ustawień do aplikacji funkcji na platformie Azure jest użycie łącza **Zarządzaj ustawieniami aplikacji...** wyświetlanym po pomyślnym opublikowaniu projektu.

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

Spowoduje to **wyświetlenie** okna dialogowego Ustawienia aplikacji dla aplikacji funkcyjnej, w którym można dodawać nowe ustawienia aplikacji lub modyfikować istniejące.

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**Local** reprezentuje wartość ustawienia w pliku local.settings.json, a **Remote** jest bieżącym ustawieniem w aplikacji funkcji na platformie Azure.  Wybierz **pozycję Dodaj ustawienie,** aby utworzyć nowe ustawienie aplikacji. Użyj **łącza Wstaw z lokalnego** łącza, aby skopiować wartość ustawienia do pola **Zdalne.** Oczekujące zmiany są zapisywane w pliku ustawień lokalnych i aplikacji funkcji po **wybraniu przycisku OK**.

> [!NOTE]
> Domyślnie plik local.settings.json nie jest sprawdzany w formancie źródła. Oznacza to, że po sklonowanie lokalnego projektu funkcji z kontroli źródła, projekt nie ma pliku local.settings.json. W takim przypadku należy ręcznie utworzyć plik local.settings.json w katalogu głównym projektu, aby okno dialogowe **Ustawienia aplikacji** działało zgodnie z oczekiwaniami. 

Ustawienia aplikacji można również zarządzać w jeden z następujących sposobów:

* [Korzystanie z witryny Azure portal](functions-how-to-use-azure-function-app-settings.md#settings).
* [Korzystanie `--publish-local-settings` z opcji publikowania w narzędziach podstawowe usług Azure Functions](functions-run-local.md#publish).
* [Korzystanie z interfejsu wiersza polecenia platformy Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

## <a name="monitoring-functions"></a>Funkcje monitorowania

Zalecanym sposobem monitorowania wykonywania funkcji jest integrowanie aplikacji funkcji z usługą Azure Application Insights. Podczas tworzenia aplikacji funkcji w witrynie Azure portal, ta integracja jest wykonywana domyślnie. Jednak podczas tworzenia aplikacji funkcji podczas publikowania programu Visual Studio integracja w aplikacji funkcji na platformie Azure nie jest wykonywana.

Aby włączyć usługę Application Insights dla aplikacji funkcji:

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Aby dowiedzieć się więcej, zobacz [Monitorowanie funkcji platformy Azure](functions-monitoring.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o podstawowych narzędziach usługi Azure Functions, zobacz [Kodowanie i testowanie funkcji platformy Azure lokalnie.](functions-run-local.md)

Aby dowiedzieć się więcej na temat tworzenia funkcji jako bibliotek klas .NET, zobacz [odwołanie dewelopera usługi Azure Functions C#.](functions-dotnet-class-library.md) W tym artykule zawiera również łącza do przykładów, jak używać atrybutów do deklarowania różnych typów powiązań obsługiwanych przez usługi Azure Functions.    
