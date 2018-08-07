---
title: Wprowadzenie do zestawu Azure WebJobs SDK
description: Wprowadzenie do zestawu SDK zadań Webjob dla przetwarzania w tle oparte na zdarzeniach. Dowiedz się, jak uzyskać dostęp do danych w usługach platformy Azure i usługi innych firm.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: glenga
ms.openlocfilehash: 72f7090c285e629149519920ac82f0fe962abc48
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39577309"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Rozpoczynanie pracy z usługą Azure WebJobs SDK, przetwarzanie w tle oparte na zdarzeniach

W tym artykule przedstawiono sposób tworzenia projektu zestawu Azure WebJobs SDK, uruchomić je lokalnie i wdróż je w usłudze Azure App Service.

Instrukcje są przeznaczone dla [programu Visual Studio 2017](https://www.visualstudio.com/vs/), ale te same zadania może się odbywać z innych narzędzi, takich jak [programu Visual Studio Code](https://code.visualstudio.com/).

## <a name="what-is-the-azure-webjobs-sdk"></a>Co to jest zestaw SDK usługi Azure WebJobs

Zestaw SDK zadań Webjob platformy Azure to platforma, która upraszcza proces pisania kodu przetwarzania tła, który uzyskuje dostęp do danych w usługach platformy Azure. Zestaw SDK zawiera składni deklaratywnej służącą do zdarzenia, które powinny wywoływać funkcję, takie jak nowy komunikat do kolejki. Podobne składni deklaratywnej steruje odczytywania i zapisywania danych w przypadku, gdy funkcja została wyzwolona. Ten system wyzwalaczy i powiązań zajmuje się większość niskiego poziomu zadań kodowania, związane z uzyskiwaniem dostępu do platformy Azure i usługi innych firm.

### <a name="functions-triggers-and-bindings"></a>Funkcje, wyzwalaczami i powiązaniami

Projekt zestawu SDK usługi WebJobs definiuje jeden lub więcej *funkcji*. Funkcja jest metodą, która ma atrybut wyzwalacza w jego podpis metody. Wyzwalacze Określanie warunków dla wywołanie funkcji i powiązania Określ akcję do odczytu i zapisu. Na przykład atrybutu wyzwalacza w następującej funkcji informuje środowiska uruchomieniowego, aby wywołać funkcję w każdym przypadku, gdy w kolejce zostanie wyświetlony komunikat `items` kolejki. `Blob` Atrybut informuje środowiska uruchomieniowego do użycia komunikatu w kolejce w celu odczytania obiektu blob w *workitems* kontenera. Zawartość komunikatu w kolejce &mdash; podawany `queueTrigger` parametru &mdash; to nazwa obiektu blob.

```cs
public static void Run(
    [QueueTrigger("items")] string myQueueItem,
    [Blob("workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

### <a name="versions-2x-and-3x"></a>Wersji 2.x i 3.x

Instrukcje informują, jak utworzyć projekt 2.x wersji zestawu SDK usługi WebJobs. Najnowsza wersja zestawu WebJobs SDK to 3.x, ale jest obecnie w wersji zapoznawczej i w tym artykule nie ma jeszcze instrukcje dotyczące tej wersji. Główne zmiany wprowadzone w wersji 3.x polega na użyciu platformy .NET Core, a nie .NET Framework.

### <a name="azure-functions"></a>Azure Functions

[Usługa Azure Functions](../azure-functions/functions-overview.md) opiera się na zestaw SDK zadań Webjob, i jest opcją w przypadku nie trzeba korzystać bezpośrednio do zestawu WebJobs SDK. Usługa Azure Functions korzysta 1.x zestawu WebJobs SDK 2.x. Aby uzyskać więcej informacji, zobacz [Porównanie usług Azure Functions i zestawem SDK usługi WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, masz [konta platformy Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) i doświadczenia z [aplikacji w usłudze Azure App Service](app-service-web-overview.md). Wykonanie czynności opisanych w tym artykule:

* [Instalowanie programu Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/) z **programowanie na platformie Azure** obciążenia. Jeśli masz już program Visual Studio, ale nie ma tego obciążenia, dodać obciążenie, wybierając **Narzędzia > Pobierz narzędzia i funkcje**.
* [Utwórz aplikację usługi App Service](app-service-web-get-started-dotnet-framework.md). Jeśli już masz, którą można wdrożyć zadanie WebJob było, można użyć, zamiast tworzyć nowy.

## <a name="create-a-project"></a>Tworzenie projektu

1. W programie Visual Studio, wybierz **Plik > Nowy projekt**.

1. Wybierz **Windows Classic Desktop > Aplikacja konsoli (.NET Framework)**.

1. Nadaj projektowi nazwę *WebJobsSDKSample*, a następnie wybierz pozycję **OK**.

   ![Okno dialogowe Nowy projekt](./media/webjobs-sdk-get-started/new-project.png)

## <a name="add-webjobs-nuget-package"></a>Dodaj pakiet NuGet zadań Webjob

1. Instalowanie najnowszej wersji 2.x stabilny pakiet NuGet `Microsoft.Azure.WebJobs`.
 
   Oto **Konsola Menedżera pakietów** polecenie, aby uzyskać wersję 2.2.0:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs -version 2.2.0
   ``` 

## <a name="create-the-jobhost"></a>Utwórz JobHost

`JobHost` Obiekt jest kontener środowiska uruchomieniowego dla funkcji: nasłuchuje wyzwalaczy i wywołania funkcji. 

1. W *Program.cs*, Dodaj `using` instrukcji:

   ```cs
   using Microsoft.Azure.WebJobs;
   ```

1. Zastąp metodę `Main` poniższym kodem:

   ```cs
   static void Main()
   {
       var config = new JobHostConfiguration();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

## <a name="enable-console-logging"></a>Włączanie rejestrowania w konsoli

Dostępnych jest kilka opcji rejestrowania w projekcie zestawu SDK usługi WebJobs. Firma Microsoft zaleca się jeden [struktury rejestrowania, który został opracowany dla platformy ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging). Ta struktura zapewnia lepszą wydajność i zapewnia większą elastyczność w nośnikach magazynowania i filtrowania. 

W tej sekcji służy do konfigurowania rejestrowania konsoli, która korzysta z nowej struktury.

1. Zainstaluj najnowszą wersję następujące pakiety NuGet:

   * `Microsoft.Extensions.Logging` -Struktury rejestrowania.
   * `Microsoft.Extensions.Logging.Console` Konsola *dostawcy*. Dostawca wysyła dzienniki do określonego miejsca docelowego, w tym przypadku do konsoli. 
 
   Poniżej przedstawiono **Konsola Menedżera pakietów** polecenia dla wersji 2.0.1:

   ```powershell
   Install-Package Microsoft.Extensions.Logging -version 2.0.1
   ``` 

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
   ``` 

1. W *Program.cs*, Dodaj `using` instrukcji:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. W `Main` metody, Dodaj kod, aby zaktualizować `JobHostConfiguration` przed utworzeniem `JobHost`:
 
   ```
   config.DashboardConnectionString = "";
   var loggerFactory = new LoggerFactory();
   config.LoggerFactory = loggerFactory
       .AddConsole();
   ```

   Ten kod wprowadza następujące zmiany:

   * Wyłącza [rejestrowania pulpit nawigacyjny](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Pulpit nawigacyjny jest starsza wersja narzędzi do monitorowania i rejestrowania pulpit nawigacyjny nie jest zalecane na potrzeby scenariuszy produkcyjnych o wysokiej przepływności.
   * Dodaje dostawcę konsoli z domyślną [filtrowanie](webjobs-sdk-how-to.md#log-filtering). 

   `Main` Metoda wygląda teraz następująco:

   ```
   var config = new JobHostConfiguration();
   config.DashboardConnectionString = "";
   var loggerFactory = new LoggerFactory();
   config.LoggerFactory = loggerFactory
       .AddConsole();
   var host = new JobHost(config);
   host.RunAndBlock();
   ```
   
## <a name="create-a-function"></a>Tworzenie funkcji

1. Tworzenie *Functions.cs* w folderze projektu i Zastąp kod szablonu przy użyciu tego kodu:

   ```cs
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Host;
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

Emulator usługi Azure Storage, który jest uruchamiany lokalnie, nie ma wszystkie funkcje, których potrzebuje, zestaw SDK zadań Webjob. Dlatego w tej sekcji Tworzenie konta magazynu na platformie Azure i skonfiguruj projekt, aby go użyć.

1. Otwórz **Eksploratora serwera** i zaloguj się do platformy Azure. Kliknij prawym przyciskiem myszy **Azure** węzeł, a następnie wybierz **nawiązywanie połączenia z subskrypcją platformy Microsoft Azure**.

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

## <a name="configure-storage-for-running-locally"></a>Skonfiguruj magazyn dla uruchomionej na komputerze lokalnym

Zestaw SDK zadań Webjob wyszukuje parametry połączenia magazynu w kolekcji ustawień aplikacji. Po uruchomieniu lokalnie szuka tej wartości w *App.config* pliku zmienne lub zmienne środowiskowe.

1. Dodaj następujący kod XML do *App.config* pliku natychmiast po otwarciu `<configuration>` tagu.

   ```xml
   <connectionStrings>
     <add name="AzureWebJobsStorage" connectionString="{storage connection string}" />
   </connectionStrings>
   ```

1. Zastąp *{parametry połączenia magazynu}* przy użyciu parametrów połączenia, które wcześniej zostały skopiowane.

   Później użyjesz parametry połączenia ponownie, gdy Konfigurowanie aplikacji usługi app Service na platformie Azure.

## <a name="test-locally"></a>Przetestować ją lokalnie

W tej sekcji Tworzenie i uruchamianie projektu lokalnie i wyzwolenia funkcji przez utworzenie komunikatu w kolejce.

1. Naciśnij klawisze Ctrl + F5, aby uruchomić projekt.

   Konsoli Pokazuje, że środowisko uruchomieniowe znaleziono funkcję i czeka na wiadomości w kolejce do wyzwolenia go.

   ```console
   Found the following functions:
   WebJobsSDKSample.Functions.ProcessQueueMessage
   info: Host.Startup[0]
         Found the following functions:
         WebJobsSDKSample.Functions.ProcessQueueMessage
   Job host started
   info: Host.Startup[0]
         Job host started
   ```

   Może zostać wyświetlony komunikat ostrzegawczy z informacją `ServicePointManager` ustawienie. Do testowania który czynności z tego projektu, możesz zignorować ostrzeżenie. Aby uzyskać więcej informacji na temat ostrzeżenia, zobacz [jak używać zestawu SDK usługi WebJobs](webjobs-sdk-how-to.md#jobhost-servicepointmanager-settings).

1. Zamknij okno konsoli.

1. W **Eksploratora serwera**, rozwiń węzeł dla nowego konta magazynu, a następnie kliknij prawym przyciskiem myszy **kolejek**. 

1. Wybierz **Utwórz kolejkę**. 

1. Wprowadź *kolejki* jako nazwę kolejki, a następnie wybierz **OK**.

   ![Tworzenie kolejki](./media/webjobs-sdk-get-started/create-queue.png)

1. Kliknij prawym przyciskiem myszy węzeł nowej kolejki, a następnie wybierz **Wyświetl kolejkę**.

1. Wybierz **Dodaj komunikat** ikony.

   ![Tworzenie kolejki](./media/webjobs-sdk-get-started/create-queue-message.png)

1. W **Dodaj komunikat** okno dialogowe, wprowadź *Witaj, świecie!* jako **tekst komunikatu**, a następnie wybierz pozycję **OK**.

   ![Tworzenie kolejki](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Uruchom ponownie projekt.

   Ponieważ użyto `QueueTrigger` atrybutu w `ProcessQueueMessage` funkcji WeJobs zestawu SDK środowiska uruchomieniowego nasłuchuje komunikatów w kolejce podczas jego uruchamiania. Znajdzie się nowego komunikatu w kolejce w kolejkę o nazwie *kolejki* i wywołuje funkcję.

   Ze względu na [sondowania wykładniczego wycofywania kolejki](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm), może upłynąć tak długo, jak 2 minuty środowiska uruchomieniowego Znajdź komunikat i wywołania funkcji. Można zmniejszyć czas oczekiwania, uruchamiając w [trybu opracowywania](webjobs-sdk-how-to.md#jobhost-development-settings).

  Dane wyjściowe konsoli wygląda następująco:

   ```console
   Found the following functions:
   WebJobsSDKSample.Functions.ProcessQueueMessage
   info: Host.Startup[0]
         Found the following functions:
         WebJobsSDKSample.Functions.ProcessQueueMessage
   Job host started
   info: Host.Startup[0]
         Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   info: Function[0]
         Hello World!
   info: Host.Results[0]
         Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   ```

1. Zamknij okno konsoli.

## <a name="add-application-insights-logging"></a>Dodawanie rejestrowania w usłudze Application Insights

Po uruchomieniu projektu na platformie Azure, nie można monitorować wykonywania funkcji, wyświetlając dane wyjściowe z konsoli. Rozwiązanie do monitorowania, firma Microsoft zaleca się [usługi Application Insights](../application-insights/app-insights-overview.md). Aby uzyskać więcej informacji, zobacz [monitora usługi Azure Functions](../azure-functions/functions-monitoring.md).

W tej sekcji należy wykonać następujące zadania, aby skonfigurować rejestrowanie usługi Application Insights, przed wdrożeniem na platformie Azure:

* Upewnij się, że masz aplikację usługi App Service i wystąpienie usługi Application Insights do pracy z.
* Konfigurowanie aplikacji usługi App Service korzystanie z wystąpienia usługi Application Insights i konto magazynu, który został utworzony wcześniej.
* Konfigurowanie projektu do logowania do usługi Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Tworzenie aplikacji usługi app Service i wystąpienie usługi Application Insights

1. Jeśli nie masz już aplikację usługi App Service, który będzie można użyć, [utworzyć](app-service-web-get-started-dotnet-framework.md).

1. Jeśli nie masz jeszcze zasobu usługi Application Insights, który będzie można użyć, [utworzyć](../application-insights/app-insights-create-new-resource.md). Ustaw **typ aplikacji** do **ogólne**, a następnie przejdź w kolejnych sekcjach **skopiuj klucz Instrumentacji**.

1. Jeśli masz już zasób usługi Application Insights, dla którego chcesz używać, [skopiuj klucz Instrumentacji](../application-insights/app-insights-create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Konfigurowanie ustawień aplikacji 

1. W **Eksploratora serwera**, rozwiń węzeł **usługi App Service** węźle **Azure**.

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

1. Dodaj następujący kod XML do *App.config* pliku, bezpośrednio po pobraniu parametrów połączenia.

   ```xml
   <appSettings>
     <add key="APPINSIGHTS_INSTRUMENTATIONKEY" value="{instrumentation key}" />
   </appSettings>
   ```

1. Zastąp *{klucz Instrumentacji}* z kluczem Instrumentacji z zasobem usługi Application Insights, którego używasz.

   Dodanie tych danych do *App.config* pliku umożliwia testowanie połączenia usługi Application Insights, gdy lokalne uruchamianie projektu. 

1. Zapisz zmiany.

### <a name="add-application-insights-logging-provider"></a>Dodawanie dostawcy logowania do usługi Application Insights

1. Instalowanie najnowszej wersji 2.x stabilny pakiet NuGet dla dostawcy logowania usługi Application Insights: `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`.

   Oto **Konsola Menedżera pakietów** polecenie, aby uzyskać wersję 2.2.0:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
   ``` 

1. Zainstaluj najnowszą wersję 4.x stabilny pakiet NuGet dla programu .NET configuration manager: `System.Configuration.ConfigurationManager`.

   Oto **Konsola Menedżera pakietów** polecenia w wersji 4.4.1:

   ```powershell
   Install-Package System.Configuration.ConfigurationManager -version 4.4.1
   ``` 

1. Otwórz *Program.cs* i Dodaj `using` poufności informacji dla programu configuration manager:

   ```csharp
   using System.Configuration;
   ```

1. Zastąp kod w `Main` metoda następującym kodem:

   ```csharp
   using (var loggerFactory = new LoggerFactory())
   {
       var config = new JobHostConfiguration();
       var instrumentationKey =
           ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];
       config.DashboardConnectionString = "";
       config.LoggerFactory = loggerFactory
           .AddApplicationInsights(instrumentationKey, null)
           .AddConsole();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

   Ten kod wprowadza następujące zmiany:

   * Dodaje dostawcę rejestrowanie usługi Application Insights z domyślną [filtrowanie](webjobs-sdk-how-to.md#log-filtering); wszystkie informacje i wyższy poziom dzienniki będą teraz przechodzić do konsoli i usługi Application Insights po uruchomieniu lokalnie. 
   * Umieszcza `LoggerFactory` obiektu `using` bloku, aby upewnić się, że dane wyjściowe dziennika jest opróżniany, gdy kończy działanie hosta. 

## <a name="test-application-insights-logging"></a>Rejestrowanie usługi Application Insights testu

W tej sekcji uruchamiasz lokalnie ponownie, aby sprawdzić, czy dane rejestrowania posłuży do Application Insights do konsoli.

1. Użyj **Eksploratora serwera** do utworzenia komunikatu w kolejce, tak samo jak [wcześniej](#trigger-the-function), z wyjątkiem wprowadź *Hello usługi App Insights!* jako tekst komunikatu.

1. Uruchom projekt.

   Zestaw SDK zadań Webjob przetwarza komunikat w kolejce i wyświetlić dzienniki w oknie konsoli.

1. Zamknij okno konsoli.

1. Otwórz [witryny Azure portal](https://portal.azure.com/), a następnie przejdź do zasobu usługi Application Insights.

1. Wybierz **wyszukiwania**.

   ![Wybierz opcję wyszukiwanie](./media/webjobs-sdk-get-started/select-search.png)

1. Jeśli nie widzisz *Insights aplikacji Hello!* komunikat, wybierz opcję **Odśwież** okresowo przez kilka minut. (Dzienniki nie pojawiają się natychmiast z powodu czasu dla klienta usługi Application Insights w celu opróżnienia dzienniki są przetwarzane.)

   ![Dzienniki w usłudze Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Zamknij okno konsoli.

## <a name="deploy-as-a-webjob"></a>Wdróż jako zadanie WebJob

W tej sekcji możesz wdrożyć projekt jako zadanie WebJob. Wdrażania aplikacji usługi App Service, [utworzony wcześniej](#create-app-service-app-and-application-insights-instance). Aby przetestować kod, po uruchomieniu na platformie Azure, będzie wyzwalacza wywołania funkcji przez utworzenie komunikatu w kolejce.

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt, a następnie wybierz **Publikuj jako zadanie Azure WebJob**.

1. W **Dodaj zadanie WebJob systemu Azure** okno dialogowe, wybierz opcję **OK**.

   ![Dodaj zadanie WebJob platformy Azure](./media/webjobs-sdk-get-started/add-azure-webjob.png)

   Program Visual Studio automatycznie instaluje pakiet NuGet publikowania zadań WebJob.

1. W **profilu** kroku **Publikuj** kreatora wybierz **Microsoft Azure App Service**.

   ![Okno dialogowe publikowanie](./media/webjobs-sdk-get-started/publish-dialog.png)

1. W **usługi App Service** okno dialogowe, wybierz opcję **dana grupa zasobów > aplikacji usługi app Service**, a następnie wybierz pozycję **OK**.

   ![Okno dialogowe usługi App Service](./media/webjobs-sdk-get-started/app-service-dialog.png)

1. W **połączenia** kroku kreatora wybierz pozycję **Publikuj**.

## <a name="trigger-the-function-in-azure"></a>Wyzwalanie funkcji na platformie Azure

1. Upewnij się, że nie jest uruchomiony lokalnie (Zamknij w oknie konsoli, jeśli jest nadal otwarty). W przeciwnym razie lokalne wystąpienie może być pierwszym przetwarzać komunikatów kolejki utworzonej.

1. Użyj **Eksploratora serwera** do utworzenia komunikatu w kolejce, tak samo jak [wcześniej](#trigger-the-function), z wyjątkiem wprowadź *Hello Azure!*.

1. Odśwież **kolejki** strony w programie Visual Studio i nowy komunikat zniknął, ponieważ funkcja działa w usłudze Azure App Service przetworzyć go.

   > [!TIP]
   > Podczas testowania na platformie Azure, użyj [trybu opracowywania](webjobs-sdk-how-to.md#jobhost-development-settings) upewnij się, czy funkcji wyzwalacza kolejki jest wywoływana natychmiast i uniknąć opóźnień, ze względu na [sondowania wykładniczego wycofywania kolejki](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm).

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

   W tym kodzie `queueTrigger` jest [powiązania wyrażeń](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns), co oznacza, że jest rozpoznawana jako różne wartości w czasie wykonywania.  W czasie wykonywania w nim zawartość komunikatu w kolejce.

1. Dodaj `using`:

   ```cs
   using System.IO;
   ```

1. Utwórz kontener obiektów blob na koncie magazynu.

   a. W **Eksploratora serwera**, rozwiń węzeł dla konta magazynu, kliknij prawym przyciskiem myszy **obiektów blob**, a następnie wybierz pozycję **Utwórz kontener obiektów Blob**.

   b. W **Utwórz kontener obiektów Blob** okno dialogowe, wprowadź *kontenera* nazwę kontenera, a następnie kliknij przycisk **OK**.

1. Przekaż *Program.cs* pliku do kontenera obiektów blob. (Ten plik jest używany tutaj jako przykładu; można przekazać dowolny plik tekstowy i utworzenia komunikatu w kolejce przy użyciu nazwy pliku.)

   a. W **Eksploratora serwera**, kliknij dwukrotnie węzeł dla właśnie utworzonego kontenera.

   b. W **kontenera** wybierz **przekazywanie** przycisku.

   ![Przycisk Przekaż obiekt blob](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Znajdź i zaznacz *Program.cs*, a następnie wybierz pozycję **OK**.

1. Utworzenia komunikatu w kolejce w kolejce został utworzony wcześniej, za pomocą *Program.cs* jako tekst komunikatu.

   ![Plik Program.cs komunikatu w kolejce](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Uruchom projekt.

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

Powiązania danych wyjściowych uprościć kod, który zapisuje dane. Ten przykład modyfikuje poprzedni, zapisując kopię obiektu blob zamiast rejestrowania jej rozmiaru.

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

1. Uruchom projekt.

   Komunikat w kolejce wyzwala funkcję, która następnie odczytuje obiekt blob, dzienniki jego długość i tworzy nowy obiekt blob. Dane wyjściowe konsoli jest taki sam, ale po przejdź do okna kontenera obiektów blob i wybraniu **Odśwież**, zostanie wyświetlony nowy obiekt blob o nazwie *Program.cs kopiowania.*

## <a name="next-steps"></a>Kolejne kroki

Ten przewodnik pokazuje, jak tworzenie, uruchamianie i wdrażanie projektu zestawu SDK usługi WebJobs.

Aby wyświetlić wszystko, co prowadzi do projektu zestawu SDK usługi WebJobs, instrukcje miały podczas tworzenia projektu od podstaw. Jednak po utworzeniu swój następny projekt, należy wziąć pod uwagę przy użyciu **zadanie Azure WebJob** szablonu w **chmury** kategorii. Ten szablon tworzy projekt z pakietów NuGet i przykładowy kod już skonfigurowane. Należy zauważyć, że przykładowy kod może zostać zmieniony na nowej struktury rejestrowania.

Aby uzyskać więcej informacji, zobacz [jak używać zestawu SDK usługi WebJobs](webjobs-sdk-how-to.md).
