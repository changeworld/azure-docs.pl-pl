---
title: Tworzenie funkcji platformy Azure przy użyciu programu Visual Studio | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzyć i testować usługi Azure Functions przy użyciu narzędzi usługi Azure Functions dla programu Visual Studio 2017.
services: functions
documentationcenter: .net
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: glenga
ms.openlocfilehash: 33ec96b3708bc89f3fbd415f892e0810fc468876
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58889808"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Tworzenie funkcji platformy Azure przy użyciu programu Visual Studio  

Narzędzia usługi Azure Functions dla programu Visual Studio 2017 to rozszerzenie dla programu Visual Studio, która umożliwia tworzenie, testowanie i wdrażanie funkcji języka C# na platformie Azure. To środowisko w przypadku pierwszej za pomocą usługi Azure Functions, można dowiedzieć się więcej o [wprowadzenie do usługi Azure Functions](functions-overview.md).

Narzędzia usługi Azure Functions zapewnia następujące korzyści: 

* Edytowanie, tworzenie i uruchamianie functions na lokalnym komputerze deweloperskim. 
* Publikowanie projektu usługi Azure Functions bezpośrednio na platformie Azure. 
* Używanie atrybutów zadania Webjob do deklarowania powiązań funkcji bezpośrednio w kodzie języka C#, zamiast zajmować się utrzymywaniem oddzielnych function.json dla powiązania definicje.
* Twórz i wdrażaj wstępnie skompilowanych funkcji języka C#. Wstępnie skompilowane funkcje zapewniają lepsze zimnego startu wydajność niż funkcji języka C# opartych na skryptach. 
* Kod funkcji w języku C#, mając wszystkie korzyści wynikające z programowania Visual Studio. 

Ten artykuł zawiera szczegółowe informacje o sposobie używania narzędzi usługi Azure Functions dla programu Visual Studio 2017 do tworzenia C# funkcje i opublikuj je na platformie Azure. Przed przeczytaniem tego artykułu, należy wykonać [szybkiego startu usługi Functions dla programu Visual Studio](functions-create-your-first-function-visual-studio.md). 

> [!IMPORTANT]
> Nie można mieszać lokalnego opracowywania aplikacji za pomocą portalu w tej samej aplikacji funkcji. Podczas publikowania z poziomu lokalnego projektu aplikacji funkcji w procesie wdrażania zastępuje wszystkie funkcje, które opracowane w portalu.

## <a name="prerequisites"></a>Wymagania wstępne

Narzędzia usługi Azure Functions są objęte obciążenie programistyczne platformy Azure z [programu Visual Studio 2017 w wersji 15.5](https://www.visualstudio.com/vs/), lub nowszej wersji. Upewnij się, że dołączasz **programowanie na platformie Azure** obciążenie w instalacji programu Visual Studio 2017:

![Instalowanie programu Visual Studio 2017 z obciążeniem Programowanie na platformie Azure](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Upewnij się, że programu Visual Studio jest aktualny i że używasz [najbardziej aktualną wersję](#check-your-tools-version) narzędzi usługi Azure Functions.

### <a name="other-requirements"></a>Inne wymagania

Aby utworzyć i wdrożyć funkcje, również należy:

* Aktywna subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [bezpłatne konta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) są dostępne.

* Konto usługi Azure Storage. Aby utworzyć konto magazynu, zobacz temat [Tworzenie konta magazynu](../storage/common/storage-quickstart-create-account.md).

### <a name="check-your-tools-version"></a>Sprawdź swoją wersję narzędzia

1. Z **narzędzia** menu, wybierz **rozszerzenia i aktualizacje**. Rozwiń **zainstalowane** > **narzędzia** i wybierz polecenie **usługi Azure Functions i narzędzia Web Job**.

    ![Sprawdź wersję narzędzia funkcji](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

2. Należy pamiętać, jest zainstalowana **wersji**. Możesz porównać tej wersji do najnowszej wersji na liście [w informacjach o wersji](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md). 

3. W przypadku starszych wersji aktualizacji z narzędzi w programie Visual Studio, jak pokazano w poniższej sekcji.

### <a name="update-your-tools"></a>Aktualizowanie narzędzi

1. W **rozszerzenia i aktualizacje** okna dialogowego, rozwiń węzeł **aktualizacje** > **Visual Studio Marketplace**, wybierz **usługi Azure Functions i narzędzia Web Job**  i wybierz **aktualizacji**.

    ![Wersja narzędzi funkcji aktualizacji](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

2. Po pobraniu aktualizacji narzędzi, zamknij program Visual Studio, aby wywoływany narzędzia aktualizacji za pomocą Instalatora VSIX.

3. W oknie Instalatora wybierz **OK** można uruchomić i następnie **Modyfikuj** Aby zaktualizować narzędzia. 

4. Po zakończeniu aktualizacji wybierz **Zamknij** i uruchom ponownie program Visual Studio.

## <a name="create-an-azure-functions-project"></a>Tworzenie projektu usługi Azure Functions

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Szablon projektu tworzy projekt C#, instaluje `Microsoft.NET.Sdk.Functions` pakietu NuGet i ustawia platformę docelową. Funkcje 1.x obiektów docelowych programu .NET Framework i funkcji cele 2.x .NET Standard. Nowy projekt ma następujące pliki:

* **host.json**: Umożliwia konfigurowanie hostów funkcji. Te ustawienia mają zastosowanie zarówno, gdy uruchomiona lokalnie i na platformie Azure. Aby uzyskać więcej informacji, zobacz [dokumentacja pliku host.JSON](functions-host-json.md).

* **local.settings.json**: Przechowuje ustawienia używane podczas uruchamiania funkcji lokalnie. Te ustawienia nie są używane przez platformę Azure, są one używane przez [podstawowych narzędzi usługi Azure Functions](functions-run-local.md). Użyj tego pliku, aby określić ustawienia aplikacji dla zmiennych wymaganych funkcji. Dodaj nowy element do **wartości** tablicy dla każdego połączenia wymagane przez powiązania funkcji w projekcie. Aby uzyskać więcej informacji, zobacz [pliku ustawień lokalnych](functions-run-local.md#local-settings-file) w artykule podstawowych narzędzi usługi Azure Functions.

    >[!IMPORTANT]
    >Pliku local.settings.json może zawierać klucze tajne, musi on spod kontroli źródła z projektu. **Kopiuj do katalogu wyjściowego** ustawienie dla tego pliku powinna zawsze być **Kopiuj Jeśli nowszy**. 

Aby uzyskać więcej informacji, zobacz [projekt biblioteki klas funkcji](functions-dotnet-class-library.md#functions-class-library-project).

## <a name="configure-the-project-for-local-development"></a>Konfigurowanie projektu dla rozwoju lokalnych

Środowisko uruchomieniowe usługi Functions używa konta usługi Azure Storage wewnętrznie. W przypadku wyzwolenia wszystkich typów innych niż HTTP i elementy webhook, należy skonfigurować **Values.AzureWebJobsStorage** klucza prawidłowe parametry połączenia konta usługi Azure Storage. Aplikacja funkcji można również użyć [emulatora usługi Azure storage](../storage/common/storage-use-emulator.md) dla **AzureWebJobsStorage** połączenia ustawienie to znaczy wymagany przez projekt. Aby korzystać z emulatora, ustaw wartość **AzureWebJobsStorage** do `UseDevelopmentStorage=true`. Należy zmienić to ustawienie do połączenia rzeczywisty magazyn, przed przystąpieniem do wdrożenia.

Aby ustawić parametry połączenia konta magazynu:

1. W programie Visual Studio, otwórz **programu Cloud Explorer**, rozwiń węzeł **konta magazynu** > **konta magazynu**, a następnie wybierz **właściwości**i skopiuj **podstawowe parametry połączenia** wartość.

2. W swoim projekcie Otwórz pliku local.settings.json, a następnie ustaw wartość **AzureWebJobsStorage** skopiowany klucz do parametrów połączenia.

3. Powtórz poprzedni krok, aby dodać unikatowe klucze do **wartości** tablicy dla innych połączeń wymaganych funkcji.

## <a name="add-a-function-to-your-project"></a>Dodawanie funkcji do projektu

W przypadku funkcji wstępnie skompilowanym powiązania używane przez funkcję są definiowane przez zastosowanie atrybutów w kodzie. Jeśli narzędzia usługi Azure Functions umożliwia tworzenie funkcji na podstawie dostarczonych szablonów, te atrybuty są stosowane dla Ciebie. 

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy węzeł projektu i wybierz polecenie **Dodaj** > **Nowy element**. Wybierz **funkcji platformy Azure**, wpisz **nazwa** klasy, a następnie kliknij przycisk **Dodaj**.

2. Wybierz wyzwalacz, ustaw właściwości powiązania, a następnie kliknij przycisk **Utwórz**. Poniższy przykład pokazuje ustawienia podczas tworzenia usługi Queue storage funkcja wyzwalana przez. 

    ![Tworzenie funkcji wyzwalanej przez kolejkę](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    W tym przykładzie wyzwalacza użyto parametrów połączenia przy użyciu klucza o nazwie **QueueStorage**. To ustawienie parametrów połączenia musi być zdefiniowany w [pliku local.settings.json](functions-run-local.md#local-settings-file).

3. Sprawdź nowo dodane klasy. Zobacz statycznego **Uruchom** metody, która jest związana z **FunctionName** atrybutu. Ten atrybut wskazuje, że metoda jest punkt wejścia dla funkcji.

    Na przykład następujące klasy C# reprezentuje podstawowej funkcji wyzwalanej przez magazyn kolejki:

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
            public static void Run([QueueTrigger("myqueue-items", Connection = "QueueStorage")]string myQueueItem, ILogger log)
            {
                log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    }
    ```
    Atrybut specyficzny dla powiązania są stosowane do każdego parametru powiązania przekazana do metody punktu wejścia. Ten atrybut ma informacje o powiązaniu jako parametry. W poprzednim przykładzie pierwszy parametr ma **QueueTrigger** zastosowany, wskazując funkcji wyzwalanej przez kolejkę. Nazwa kolejki i nazwa ustawienia parametrów połączenia, które są przekazywane jako parametry do **QueueTrigger** atrybutu. Aby uzyskać więcej informacji, zobacz [powiązania magazynu kolejek platformy Azure dla usługi Azure Functions](functions-bindings-storage-queue.md#trigger---c-example).
    
Powyższej procedury można użyć, aby dodać więcej funkcji do projektu aplikacji funkcji. Każda funkcja w projekcie mogą mieć różne wyzwalacza, ale funkcja musi mieć dokładnie jeden wyzwalacz. Aby uzyskać więcej informacji, zobacz [pojęcia powiązania i Wyzwalacze usługi Azure Functions](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Dodawanie powiązania

Podobnie jak w przypadku wyzwalaczy, powiązania danych wejściowych i wyjściowych są dodawane do funkcji jako atrybutów powiązania. Dodawaj powiązania z funkcji w następujący sposób:

1. Upewnij się, że masz [skonfigurowano projekt do tworzenia aplikacji lokalnej](#configure-the-project-for-local-development).

2. Dodaj odpowiedni pakiet rozszerzenia NuGet dla określonego powiązania. Aby uzyskać więcej informacji, zobacz [rozwoju lokalnych C# za pomocą programu Visual Studio](./functions-bindings-register.md#local-csharp) w artykule wyzwalaczy i powiązań. Wymagań pakietu NuGet specyficznych dla powiązania znajdują się w artykule odwołania dla wiązania. Na przykład znaleźć pakietów dotyczące wyzwalacza usługi Event Hubs w [artykule informacyjnym na temat powiązania usługi Event Hubs](functions-bindings-event-hubs.md).

3. W przypadku ustawienia aplikacji, które powiązania musi dodać je do **wartości** kolekcji w [pliku ustawień lokalnych](functions-run-local.md#local-settings-file). Te wartości są używane po uruchomieniu funkcji lokalnie. Po uruchomieniu funkcji w aplikacji funkcji na platformie Azure, [ustawień aplikacji funkcji](#function-app-settings) są używane.

4. Dodaj atrybut odpowiednie powiązanie w podpisie metody. W poniższym przykładzie komunikatu w kolejce wywołuje funkcję i powiązania danych wyjściowych utworzenie nowego komunikatu w kolejce o ten sam tekst w innej kolejki.

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
   Połączenie do usługi Queue storage jest uzyskiwana z `AzureWebJobsStorage` ustawienie. Aby uzyskać więcej informacji zobacz artykuł odwołania dla określonego powiązania. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Testowanie funkcji

Podstawowe narzędzia usługi Azure Functions umożliwiają uruchamianie projektu usługi Azure Functions na lokalnym komputerze deweloperskim. Monit o zainstalowanie tych narzędzi pojawia się przy pierwszym uruchomieniu funkcji w programie Visual Studio.

Aby przetestować funkcję, naciśnij klawisz F5. Po wyświetleniu monitu zaakceptuj żądanie programu Visual Studio dotyczące pobrania i zainstalowania podstawowych narzędzi usługi Azure Functions (CLI). Może także być konieczne włączenie wyjątku zapory, aby umożliwić narzędziom obsługę żądań HTTP.

Z projektem, uruchamianie można przetestować kod, jak testowałbyś wdrożoną funkcję. Aby uzyskać więcej informacji, zobacz [strategii testowania kodu w usłudze Azure Functions](functions-test-a-function.md). Podczas pracy w trybie debugowania, punkty przerwania są osiągane w programie Visual Studio, zgodnie z oczekiwaniami. 

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->

Aby dowiedzieć się więcej na temat korzystania z podstawowych narzędzi usługi Azure Functions, zobacz [kodu i testowanie usługi Azure functions lokalnie](functions-run-local.md).

## <a name="publish-to-azure"></a>Publikowanie na platformie Azure

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Ustawienia aplikacji funkcji

Wszystkie ustawienia, które zostały dodane w local.settings.json należy również dodać do aplikacji funkcji na platformie Azure. Te ustawienia nie są ładowane automatycznie podczas publikowania projektu.

Najprostszym sposobem przekazywania wymaganych ustawień do aplikacji funkcji na platformie Azure jest użycie **Zarządzanie ustawieniami aplikacji...**  łącze, które jest wyświetlane, gdy opublikujesz projekt.

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

Spowoduje to wyświetlenie **ustawienia aplikacji** okno dialogowe dla aplikacji funkcji, w którym można dodawać nowych ustawień aplikacji lub zmodyfikuj istniejące.

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**Lokalne** reprezentuje wartość ustawienia w pliku local.settings.json i **zdalnego** jest bieżące ustawienie w aplikacji funkcji na platformie Azure.  Wybierz **Dodaj ustawienie** Aby utworzyć nowe ustawienie aplikacji. Użyj **wstawienia wartości na podstawie danych lokalnych** link, aby skopiować wartość ustawienia do **zdalnego** pola. Oczekujące zmiany są zapisywane w pliku ustawień lokalnych i aplikacji funkcji po wybraniu **OK**.

Można również zarządzać ustawienia aplikacji w jednym z tych sposobów:

* [Za pomocą witryny Azure portal](functions-how-to-use-azure-function-app-settings.md#settings).
* [Za pomocą `--publish-local-settings` opcja publikowania w podstawowych narzędzi usługi Azure Functions](functions-run-local.md#publish).
* [Przy użyciu wiersza polecenia platformy Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

## <a name="monitoring-functions"></a>Funkcje monitorowania

Jest to zalecany sposób Monitoruj wykonywanie funkcji na platformie Azure dzięki integracji z usługą Azure Application Insights. Po utworzeniu aplikacji funkcji w witrynie Azure portal, ta Integracja jest wykonywane domyślnie. Jednak po utworzeniu aplikacji funkcji podczas publikowania w programie Visual Studio nie jest wykonywane integracji w Twojej aplikacji funkcji na platformie Azure. Zamiast tego należy pobrać wbudowanych rejestrowania, która nie jest zalecane.

Aby włączyć usługę Application Insights dla aplikacji funkcji na platformie Azure:

1. Utwórz wystąpienie usługi Application Insights w [witryny Azure portal](https://portal.azure.com) i skopiuj klucz instrumentacji. Aby dowiedzieć się więcej, zobacz temat [ręcznie połączyć zasobu usługi App Insights](functions-monitoring.md#manually-connect-an-app-insights-resource).  

1. Dodaj aplikację, ustawienie o nazwie `APPINSIGHTS_INSTRUMENTATIONKEY` do ustawień aplikacji funkcji na platformie Azure, zgodnie z opisem w [ustawień aplikacji funkcji](#function-app-settings). To ustawienie aplikacji zawiera klucz instrumentacji, który został utworzony w poprzednim kroku.

1. Usuń `AzureWebJobsDashboard` ustawienia aplikacji na podstawie aplikacji funkcji na platformie Azure, która wyłącza wbudowane funkcje rejestrowania.  

Aby dowiedzieć się więcej, zobacz [monitora usługi Azure Functions](functions-monitoring.md).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat podstawowych narzędzi usługi Azure Functions, zobacz [kodu i testowanie usługi Azure functions lokalnie](functions-run-local.md).

Aby dowiedzieć się więcej o projektowaniu funkcji jako bibliotek klasy .NET, zobacz [dokumentacja dla deweloperów usługi Azure Functions C#](functions-dotnet-class-library.md). Ten artykuł zawiera również linki przykłady dotyczące używania atrybutów do deklarowania różne rodzaje powiązań, obsługiwane przez usługi Azure Functions.    
