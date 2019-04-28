---
title: Rozpoczynanie pracy z zestawem SDK usługi WebJobs - Azure
description: Wprowadzenie do zestawu SDK zadań Webjob dla przetwarzania w tle oparte na zdarzeniach. Dowiedz się, jak uzyskać dostęp do danych w usługach platformy Azure i usługi innych firm.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: jeconnoc
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: b824c99a015cfa2c1d1c75e2a1257eff482e8dd6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60833312"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Rozpoczynanie pracy z usługą Azure WebJobs SDK, przetwarzanie w tle oparte na zdarzeniach

W tym artykule pokazano, jak utworzyć projekt zestawu Azure WebJobs SDK, uruchomić je lokalnie i następnie wdrożysz go do programu Visual Studio 2017 [usługi Azure App Service](overview.md). Projekt, możesz utworzyć jest aplikację konsoli .NET Core, która korzysta z wersji 3.x zestawu WebJobs SDK. Jeśli interesuje Cię w wersji 2.x, która używa .NET Framework, zobacz [tworzenie i wdrażanie zadań Webjob za pomocą programu Visual Studio — usłudze Azure App Service](webjobs-dotnet-deploy-vs.md).

Aby dowiedzieć się więcej na temat pracy z zestawem SDK WebJobs, zobacz [jak używać zestawu Azure WebJobs SDK na potrzeby przetwarzania w tle oparte na zdarzeniach](webjobs-sdk-how-to.md).

## <a name="prerequisites"></a>Wymagania wstępne

* [Instalowanie programu Visual Studio 2017](/visualstudio/install/) z **programowanie na platformie Azure** obciążenia. Jeśli masz już program Visual Studio, ale nie ma tego obciążenia, dodać obciążenie, wybierając **Narzędzia > Pobierz narzędzia i funkcje**.

* Konieczne jest posiadanie [konta platformy Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Aby opublikować projekt zestawu SDK zadań Webjob na platformie Azure.

## <a name="create-a-project"></a>Tworzenie projektu

1. W programie Visual Studio, wybierz **Plik > Nowy > Projekt**.

2. Wybierz **platformy .NET Core > konsoli aplikacji (.NET Core)**.

3. Nadaj projektowi nazwę *WebJobsSDKSample*, a następnie wybierz pozycję **OK**.

   ![Okno dialogowe Nowy projekt](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>Pakiety NuGet zadań Webjob

1. Zainstaluj najnowsze wersje stabilne 3.x następujące pakiety NuGet:

   * `Microsoft.Azure.WebJobs`
   * `Microsoft.Azure.WebJobs.Extensions`

     Oto **Konsola Menedżera pakietów** polecenia dla wersji 3.0.4:

     ```powershell
     Install-Package Microsoft.Azure.WebJobs -version 3.0.4
     Install-Package Microsoft.Azure.WebJobs.Extensions -version 3.0.1
     ```

## <a name="create-the-host"></a>Utwórz hosta

Host jest kontener środowiska uruchomieniowego dla funkcji, która nasłuchuje wyzwalaczy i wywołania funkcji. Poniższe kroki umożliwiają utworzenie hosta, który implementuje [ `IHost` ](/dotnet/api/microsoft.extensions.hosting.ihost), czyli ogólnego hostów w programie ASP.NET Core.

1. W *Program.cs*, Dodaj `using` instrukcji:

    ```cs
    using Microsoft.Extensions.Hosting;
    ```

1. Zastąp metodę `Main` poniższym kodem:

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

W programie ASP.NET Core konfiguracji hosta są ustawiane przez wywołanie metody w [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder) wystąpienia. Aby uzyskać więcej informacji, zobacz [Host ogólny .NET](/aspnet/core/fundamentals/host/generic-host). `ConfigureWebJobs` — Metoda rozszerzenia inicjuje hosta usługi WebJobs. W `ConfigureWebJobs`, zainicjować określone rozszerzenia zadań Webjob i ustawiać właściwości tych rozszerzeń.  

## <a name="enable-console-logging"></a>Włączanie rejestrowania w konsoli

W tej sekcji można skonfigurować rejestrowania konsoli, która używa [struktury rejestrowania platformy ASP.NET Core](/aspnet/core/fundamentals/logging).

1. Zainstaluj najnowszą wersję następujące pakiety NuGet:

   * `Microsoft.Extensions.Logging` -Struktury rejestrowania.
   * `Microsoft.Extensions.Logging.Console` — Konsola dostawcę, który wysyła dzienniki w konsoli.

   Poniżej przedstawiono **Konsola Menedżera pakietów** poleceń dla wersji 2.2.0:

   ```powershell
   Install-Package Microsoft.Extensions.Logging -version 2.2.0
   ```

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.2.0
   ```

1. W *Program.cs*, Dodaj `using` instrukcji:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. Wywołaj [ `ConfigureLogging` ](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) metody [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder). [ `AddConsole` ](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) Metoda dodaje rejestrowania konsoli do konfiguracji.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    `Main` Metoda wygląda teraz następująco:

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

    Ta aktualizacja zapewnia następujące funkcje:

    * Wyłącza [rejestrowania pulpit nawigacyjny](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Pulpit nawigacyjny jest starsza wersja narzędzi do monitorowania i rejestrowania pulpit nawigacyjny nie jest zalecane na potrzeby scenariuszy produkcyjnych o wysokiej przepływności.
    * Dodaje dostawcę konsoli z domyślną [filtrowanie](webjobs-sdk-how-to.md#log-filtering).

Teraz można dodać funkcji, który jest wyzwalany przez komunikaty przychodzące w [kolejki usługi Azure Storage](../azure-functions/functions-bindings-storage-queue.md).

## <a name="install-the-storage-binding-extension"></a>Instalowanie rozszerzenia powiązania usługi Storage

Począwszy od wersji 3.x, należy jawnie zainstalować rozszerzenie powiązania magazynu wymaganego przez zestaw SDK zadań Webjob. W poprzednich wersjach powiązania magazynu zostały uwzględnione w zestawie SDK.

1. Zainstaluj najnowszą stabilną wersję [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) pakietu NuGet w wersji 3.x. 

    Oto **Konsola Menedżera pakietów** polecenie, aby uzyskać wersję 3.0.3:

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.3
    ```

2. W `ConfigureWebJobs` metodę rozszerzenia, wywołanie `AddAzureStorage` metody [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder) wystąpienie do zainicjowania rozszerzenia magazynu. W tym momencie `ConfigureWebJobs` metoda wygląda następująco:

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>Tworzenie funkcji

1. Kliknij prawym przyciskiem myszy projekt, wybierz **Dodaj** > **nowy element...** , wybierz **klasy**, nadaj nazwę nowej C# plik klasy *Functions.cs*i wybierz **Dodaj**.

1. W Functions.cs Zastąp wygenerowany szablon z następującym kodem:

   ```cs
   using Microsoft.Azure.WebJobs;
   using Microsoft.Extensions.Logging;

   namespace WebJobsSDKSample
   {
       public class Functions
       {
           public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
           {
               logger.LogInformation(message);
           }
       }
   }
   ```

   `QueueTrigger` Atrybut informuje środowiska uruchomieniowego, aby wywołać tę funkcję, gdy nowa wiadomość jest zapisywany w kolejce usługi Azure Storage, o nazwie `queue`. Zawartość komunikatu w kolejce znajdują się na kod metody w `message` parametru. Treść metody jest to, gdzie przetwarzania danych wyzwalacza. W tym przykładzie kod rejestruje tylko wiadomości.

   `message` Parametru nie musi być ciągiem. Możesz również powiązać obiekt JSON z tablicą bajtów lub [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage) obiektu. [Zobacz użycie wyzwalacz kolejki](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Każdy typ powiązania (na przykład kolejek, obiektów blob lub tabel) ma inny zestaw typów parametrów, które można powiązać.

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Emulator usługi Azure Storage, który jest uruchamiany lokalnie, nie ma wszystkie funkcje, których potrzebuje, zestaw SDK zadań Webjob. Dlatego w tej sekcji Tworzenie konta magazynu na platformie Azure i skonfiguruj projekt, aby go użyć. Jeśli masz już konto usługi storage, przejdź do kroku 6.

1. Otwórz **Eksploratora serwera** w programie Visual studio i zaloguj się do platformy Azure. Kliknij prawym przyciskiem myszy **Azure** węzeł, a następnie wybierz **nawiązywanie połączenia z subskrypcją platformy Microsoft Azure**.

   ![Logowanie do platformy Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. W obszarze **Azure** w węźle **Eksploratora serwera**, kliknij prawym przyciskiem myszy **magazynu**, a następnie wybierz pozycję **konta magazynu, Utwórz**.

   ![Tworzenie menu konta magazynu](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. W **Utwórz konto magazynu** okna dialogowego wprowadź unikatową nazwę konta magazynu.

1. Wybierz taką samą **Region** że utworzono aplikację usługi App Service w lub bliski region.

1. Wybierz pozycję **Utwórz**.

   ![Tworzenie konta magazynu](./media/webjobs-sdk-get-started/create-storage-account.png)

1. W obszarze **magazynu** w węźle **Eksploratora serwera**, wybierz nowe konto magazynu. W **właściwości** okna, wybierz przycisk wielokropka (**...** ) w prawym rogu **parametry połączenia** pole wartości.

   ![Wielokropek ciąg połączenia](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. Skopiuj parametry połączenia, a następnie zapisz tę wartość w jakimś miejscu, można go było kopiować ponownie łatwo.

   ![Skopiuj parametry połączenia](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>Konfigurowanie magazynu, aby uruchomić lokalnie

Zestaw SDK zadań Webjob wyszukuje parametry połączenia magazynu w ustawieniach aplikacji na platformie Azure. Po uruchomieniu lokalnie szuka tę wartość w lokalnym pliku konfiguracji lub zmienne środowiskowe.

1. Kliknij prawym przyciskiem myszy projekt, wybierz **Dodaj** > **nowy element...** , wybierz **pliku konfiguracji JavaScript JSON**, nadaj nowemu plikowi *appsettings.json* pliku, a następnie wybierz **Dodaj**. 

1. W nowym pliku Dodaj `AzureWebJobsStorage` pola, jak w poniższym przykładzie:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Zastąp *{parametry połączenia magazynu}* przy użyciu parametrów połączenia, które wcześniej zostały skopiowane.

1. Wybierz *appsettings.json* pliku w Eksploratorze rozwiązań, a w **właściwości** oknie **Kopiuj do katalogu wyjściowego** do **Kopiuj Jeśli nowszy**.

Później należy dodać ustawienie tej samej aplikacji parametrów połączenia w swojej aplikacji w usłudze Azure App Service.

## <a name="test-locally"></a>Przetestować ją lokalnie

W tej sekcji Tworzenie i uruchamianie projektu lokalnie i wyzwolenia funkcji przez utworzenie komunikatu w kolejce.

1. Naciśnij klawisz **kombinację klawiszy Ctrl + F5** Aby uruchomić projekt.

   Konsoli Pokazuje, że środowisko uruchomieniowe znaleziono funkcję i czeka na wiadomości w kolejce do wyzwolenia go. Następujących danych wyjściowych jest generowany przez hosta v3.x:

   ```console
    info: Microsoft.Azure.WebJobs.Hosting.JobHostService[0]
          Starting JobHost
    info: Host.Startup[0]
          Found the following functions:
          WebJobsSDKSample.Functions.ProcessQueueMessage

    info: Host.Startup[0]
          Job host started
    Application started. Press Ctrl+C to shut down.
    Hosting environment: Development
    Content root path: C:\WebJobsSDKSample\WebJobsSDKSample\bin\Debug\netcoreapp2.1\
   ```

1. Zamknij okno konsoli.

1. W **Eksploratora serwera** w programie Visual Studio, rozwiń węzeł dla nowego konta magazynu, a następnie kliknij prawym przyciskiem myszy **kolejek**.

1. Wybierz **Utwórz kolejkę**.

1. Wprowadź *kolejki* jako nazwę kolejki, a następnie wybierz **OK**.

   ![Tworzenie kolejki](./media/webjobs-sdk-get-started/create-queue.png)

1. Kliknij prawym przyciskiem myszy węzeł nowej kolejki, a następnie wybierz **Wyświetl kolejkę**.

1. Wybierz **Dodaj komunikat** ikony.

   ![Tworzenie kolejki](./media/webjobs-sdk-get-started/create-queue-message.png)

1. W **Dodaj komunikat** okno dialogowe, wprowadź *Witaj, świecie!* jako **tekst komunikatu**, a następnie wybierz pozycję **OK**. Jest teraz wiadomość w kolejce.

   ![Tworzenie kolejki](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Uruchom ponownie projekt.

   Ponieważ użyto `QueueTrigger` atrybutu w `ProcessQueueMessage` funkcji WeJobs zestawu SDK środowiska uruchomieniowego nasłuchuje komunikatów w kolejce podczas jego uruchamiania. Znajdzie się nowego komunikatu w kolejce w kolejkę o nazwie *kolejki* i wywołuje funkcję.

   Ze względu na [sondowania wykładniczego wycofywania kolejki](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm), może upłynąć tak długo, jak 2 minuty środowiska uruchomieniowego Znajdź komunikat i wywołania funkcji. Można zmniejszyć czas oczekiwania, uruchamiając w [trybu opracowywania](webjobs-sdk-how-to.md#host-development-settings).

   Dane wyjściowe konsoli wygląda następująco:

   ```console
    info: Function.ProcessQueueMessage[0]
          Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=2c319369-d381-43f3-aedf-ff538a4209b8)
    info: Function.ProcessQueueMessage[0]
          Trigger Details: MessageId: b00a86dc-298d-4cd2-811f-98ec39545539, DequeueCount: 1, InsertionTime: 1/18/2019 3:28:51 AM +00:00
    info: Function.ProcessQueueMessage.User[0]
          Hello World!
    info: Function.ProcessQueueMessage[0]
          Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=2c319369-d381-43f3-aedf-ff538a4209b8)
   ```

1. Zamknij okno konsoli. 

1. Wróć do okna kolejki i ich odświeżanie. Komunikat został usunięty, ponieważ został on przetworzony przez funkcję uruchomionej na komputerze lokalnym. 

## <a name="add-application-insights-logging"></a>Dodawanie rejestrowania w usłudze Application Insights

Po uruchomieniu projektu na platformie Azure, nie można monitorować wykonywania funkcji, wyświetlając dane wyjściowe z konsoli. Rozwiązanie do monitorowania, firma Microsoft zaleca się [usługi Application Insights](../azure-monitor/app/app-insights-overview.md). Aby uzyskać więcej informacji, zobacz [monitora usługi Azure Functions](../azure-functions/functions-monitoring.md).

W tej sekcji należy wykonać następujące zadania, aby skonfigurować rejestrowanie usługi Application Insights, przed wdrożeniem na platformie Azure:

* Upewnij się, że masz aplikację usługi App Service i wystąpienie usługi Application Insights do pracy z.
* Konfigurowanie aplikacji usługi App Service korzystanie z wystąpienia usługi Application Insights i konto magazynu, który został utworzony wcześniej.
* Konfigurowanie projektu do logowania do usługi Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Tworzenie aplikacji usługi app Service i wystąpienie usługi Application Insights

1. Jeśli nie masz już aplikację usługi App Service, który będzie można użyć, [utworzyć](app-service-web-get-started-dotnet-framework.md). Podczas tworzenia aplikacji, można też utworzyć połączony zasób usługi Application Insights. Gdy to zrobisz, `APPINSIGHTS_INSTRUMENTATIONKEY` jest ustawiona dla Ciebie w swojej aplikacji.

1. Jeśli nie masz jeszcze zasobu usługi Application Insights, który będzie można użyć, [utworzyć](../azure-monitor/app/create-new-resource.md ). Ustaw **typ aplikacji** do **ogólne**, a następnie przejdź w kolejnych sekcjach **skopiuj klucz Instrumentacji**.

1. Jeśli masz już zasób usługi Application Insights, dla którego chcesz używać, [skopiuj klucz Instrumentacji](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Konfigurowanie ustawień aplikacji 

1. W **Eksploratora serwera** w programie Visual Studio rozwiń **usługi App Service** węźle **Azure**.

1. Rozwiń grupę zasobów, który znajduje się w aplikacji usługi app Service, a następnie kliknij prawym przyciskiem myszy aplikację usługi App Service.

1. Wybierz **wyświetlić ustawienia**.

1. W **parametry połączenia** Dodaj następujący wpis.

   |Name (Nazwa)  |Ciąg połączenia  |Typ bazy danych|
   |---------|---------|------|
   |AzureWebJobsStorage | {Parametry połączenia magazynu, które wcześniej zostały skopiowane}|Niestandardowy|

1. Jeśli **ustawienia aplikacji** okno nie ma instrumentacji usługi Application Insights klucza, należy dodać go, który został wcześniej skopiowany. (Klucz Instrumentacji może być już istnieje, w zależności od sposobu tworzenia aplikacji usługi app Service.)

   |Name (Nazwa)  |Wartość  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {klucz Instrumentacji} |

1. Zastąp *{klucz Instrumentacji}* z kluczem Instrumentacji z zasobem usługi Application Insights, którego używasz.

1. Wybierz pozycję **Zapisz**.

1. Dodaj połączenie usługi Application Insights do projektu tak, aby uruchomić ją lokalnie. W *appsettings.json* Dodaj `APPINSIGHTS_INSTRUMENTATIONKEY` pola, jak w poniższym przykładzie:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    Zastąp *{klucz Instrumentacji}* z kluczem Instrumentacji z zasobem usługi Application Insights, którego używasz.

1. Zapisz zmiany.

### <a name="add-application-insights-logging-provider"></a>Dodawanie dostawcy logowania do usługi Application Insights

Aby móc korzystać z [usługi Application Insights](../azure-monitor/app/app-insights-overview.md) rejestrowanie, zaktualizować swój kod rejestrowania, aby wykonać następujące czynności:

* Dodaj dostawcę rejestrowanie usługi Application Insights z domyślną [filtrowanie](webjobs-sdk-how-to.md#log-filtering); wszystkie informacje i dzienniki wyższego poziomu przechodzi do konsoli i usługi Application Insights po uruchomieniu lokalnie.
* Umieść `LoggerFactory` obiektu `using` bloku, aby upewnić się, że dane wyjściowe dziennika jest opróżniany, gdy kończy działanie hosta.

1. Instalowanie najnowszej wersji 3.x stabilny pakiet NuGet dla dostawcy logowania usługi Application Insights: `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`.

   Oto **Konsola Menedżera pakietów** polecenie, aby uzyskać wersję 3.0.2:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version 3.0.2
   ```

1. Otwórz *Program.cs* i Zastąp kod w `Main` metoda następującym kodem:

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.UseEnvironment(EnvironmentName.Development);
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                    b.AddAzureStorage();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();

                    // If the key exists in settings, use it to enable Application Insights.
                    string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                    if (!string.IsNullOrEmpty(instrumentationKey))
                    {
                        b.AddApplicationInsights(o => o.InstrumentationKey = instrumentationKey);
                    }
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

    Spowoduje to dodanie dostawcy usługi Application Insights do rejestrowania, za pomocą klucza, który dodano wcześniej w ustawieniach Twojej aplikacji.

## <a name="test-application-insights-logging"></a>Rejestrowanie usługi Application Insights testu

W tej sekcji uruchamiasz lokalnie ponownie Aby zweryfikować, że rejestrowanie danych posłuży do Application Insights do konsoli.

1. Użyj **Eksploratora serwera** w programie Visual Studio do utworzenia komunikatu w kolejce, tak samo jak [wcześniej](#trigger-the-function-in-azure), z wyjątkiem wprowadź *Hello usługi App Insights!* jako tekst komunikatu.

1. Uruchom projekt.

   Zestaw SDK zadań Webjob przetwarza komunikat w kolejce i wyświetlić dzienniki w oknie konsoli.

1. Zamknij okno konsoli.

1. Otwórz [witryny Azure portal](https://portal.azure.com/), a następnie przejdź do zasobu usługi Application Insights.

1. Wybierz **wyszukiwania**.

   ![Wybierz opcję wyszukiwanie](./media/webjobs-sdk-get-started/select-search.png)

1. Jeśli nie widzisz *Insights aplikacji Hello!* komunikat, wybierz opcję **Odśwież** okresowo przez kilka minut. (Dzienniki nie pojawiają się natychmiast z powodu czasu dla klienta usługi Application Insights w celu opróżnienia dzienniki są przetwarzane.)

   ![Dzienniki w usłudze Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Zamknij okno konsoli.

## <a name="deploy-as-a-webjob"></a>Wdrażanie na platformie Azure

Podczas wdrażania możesz utworzyć wystąpienie usługi aplikacji, w której chcesz uruchamiać swoje funkcje. Podczas publikowania aplikacji konsolowej .NET Core do usługi App Service na platformie Azure, jego automatycznie pobiera uruchamiać jako zadanie WebJob. Aby dowiedzieć się więcej o publikowaniu, zobacz [tworzenie i wdrażanie zadań Webjob za pomocą programu Visual Studio](webjobs-dotnet-deploy-vs.md).

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

## <a name="trigger-the-function-in-azure"></a>Wyzwalanie funkcji na platformie Azure

1. Upewnij się, że nie jest uruchomiony lokalnie (Zamknij w oknie konsoli, jeśli jest nadal otwarty). W przeciwnym razie lokalne wystąpienie może być pierwszym przetwarzać komunikatów kolejki utworzonej.

1. W **kolejki** strony w programie Visual Studio, tak jak poprzednio Dodawanie komunikatu do kolejki.

1. Odśwież **kolejki** strony i nowy komunikat zniknie, ponieważ został on przetworzony przez funkcję działających na platformie Azure.

   > [!TIP]
   > Podczas testowania na platformie Azure, użyj [trybu opracowywania](webjobs-sdk-how-to.md#host-development-settings) upewnij się, czy funkcji wyzwalacza kolejki jest wywoływana natychmiast i uniknąć opóźnień, ze względu na [sondowania wykładniczego wycofywania kolejki](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm).

### <a name="view-logs-in-application-insights"></a>Wyświetlanie dzienników w usłudze Application Insights

1. Otwórz [witryny Azure portal](https://portal.azure.com/), a następnie przejdź do zasobu usługi Application Insights.

1. Wybierz **wyszukiwania**.

1. Jeśli nie widzisz *Hello Azure!* komunikat, wybierz opcję **Odśwież** okresowo przez kilka minut.

   Zobacz dzienniki z funkcji w zadanie WebJob, w tym *Hello Azure!* tekst, który został wprowadzony w poprzedniej sekcji.

## <a name="add-an-input-binding"></a>Dodawanie powiązania danych wejściowych

Powiązania danych wejściowych uprościć kod, który odczytuje dane. W tym przykładzie komunikatu w kolejce będzie nazwa obiektu blob, a nazwa obiektu blob będzie używana w celu odnalezienia i odczytania obiektu blob w usłudze Azure Storage.

1. W *Functions.cs*, Zastąp `ProcessQueueMessage` metoda następującym kodem:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   W tym kodzie `queueTrigger` jest [powiązania wyrażeń](../azure-functions/functions-bindings-expressions-patterns.md), co oznacza, że jest rozpoznawana jako różne wartości w czasie wykonywania.  W czasie wykonywania w nim zawartość komunikatu w kolejce.

1. Dodaj `using`:

   ```cs
   using System.IO;
   ```

1. Utwórz kontener obiektów blob na koncie magazynu.

   a. W **Eksploratora serwera** w programie Visual Studio, rozwiń węzeł dla konta magazynu, kliknij prawym przyciskiem myszy **obiektów blob**, a następnie wybierz pozycję **Utwórz kontener obiektów Blob**.

   b. W **Utwórz kontener obiektów Blob** okno dialogowe, wprowadź *kontenera* nazwę kontenera, a następnie kliknij przycisk **OK**.

1. Przekaż *Program.cs* pliku do kontenera obiektów blob. (Ten plik jest używany tutaj jako przykładu; można przekazać dowolny plik tekstowy i utworzenia komunikatu w kolejce przy użyciu nazwy pliku.)

   a. W **Eksploratora serwera**, kliknij dwukrotnie węzeł dla utworzonego kontenera.

   b. W **kontenera** wybierz **przekazywanie** przycisku.

   ![Przycisk Przekaż obiekt blob](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Znajdź i zaznacz *Program.cs*, a następnie wybierz pozycję **OK**.

1. Utworzenia komunikatu w kolejce w kolejce został utworzony wcześniej, za pomocą *Program.cs* jako tekst komunikatu.

   ![Plik Program.cs komunikatu w kolejce](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Lokalne uruchamianie projektu.

   Komunikat w kolejce wyzwala funkcję, która następnie odczytuje obiekt blob i rejestruje jego długość. Dane wyjściowe konsoli wygląda następująco:

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```

## <a name="add-an-output-binding"></a>Dodawanie powiązania danych wyjściowych

Powiązania danych wyjściowych uprościć kod, który zapisuje dane. Ten przykład modyfikuje poprzedni, zapisując kopię obiektu blob zamiast rejestrowania jej rozmiaru. Powiązania magazynu obiektów blob znajdują się w pakiecie rozszerzenia Azure Storage, który wcześniej zainstalowanej.

1. Zastąp metodę `ProcessQueueMessage` poniższym kodem:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

1. Utwórz inny komunikat w kolejce z *Program.cs* jako tekst komunikatu.

1. Lokalne uruchamianie projektu.

   Komunikat w kolejce wyzwala funkcję, która następnie odczytuje obiekt blob, dzienniki jego długość i tworzy nowy obiekt blob. Dane wyjściowe konsoli jest taki sam, ale po przejdź do okna kontenera obiektów blob i wybraniu **Odśwież**, zostanie wyświetlony nowy obiekt blob o nazwie *Program.cs kopiowania.*

## <a name="republish-the-updates-to-azure"></a>Ponownie opublikować aktualizacje na platformie Azure

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**.

1. W **Publikuj** okna dialogowego, upewnij się, że bieżący profil jest zaznaczone, a następnie wybierz **Publikuj**. Wyniki publikowania są szczegółowo opisane w **dane wyjściowe** okna.
 
1. Sprawdź funkcji na platformie Azure, ponownie przekazać plik do kontenera obiektów blob i dodając komunikat do kolejki, która jest nazwą przekazanego pliku. Wyświetlony komunikat do usunięcia z kolejki i kopiowania pliku utworzonego w kontenerze obiektów blob. 

## <a name="next-steps"></a>Kolejne kroki

W tym artykule pokazano, jak tworzenie, uruchamianie i wdrażanie projektu 3.x zestawu SDK usługi WebJobs.

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat zestawu SDK usługi WebJobs](webjobs-sdk-how-to.md)
