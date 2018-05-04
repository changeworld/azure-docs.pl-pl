---
title: Wprowadzenie do zestawu SDK zadań Webjob - Azure
description: Wprowadzenie do zestawu SDK zadań Webjob dla przetwarzania w tle sterowanego zdarzeniami. Dowiedz się, jak uzyskać dostęp do danych w usługach Azure i usług innych firm.
services: app-service\web, storage
documentationcenter: .net
author: tdykstra
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: tdykstra
ms.openlocfilehash: 324b578a7736719eb953cab4eca4e9a3e9bfa35d
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
---
# <a name="get-started-with-the-webjobs-sdk"></a>Wprowadzenie do zestawu SDK zadań Webjob

W tym artykule pokazano, jak utworzyć projekt zestawu SDK zadań Webjob, uruchomić je lokalnie i wdróż je na platformie Azure.

Instrukcje dotyczą [programu Visual Studio 2017](https://www.visualstudio.com/vs/), ale tych samych zadań można wykonywać przy użyciu innych narzędzi, takich jak [Visual Studio Code](https://code.visualstudio.com/).

## <a name="what-is-the-webjobs-sdk"></a>Co to jest zestaw SDK zadań Webjob

Zestaw SDK zadań Webjob Azure to platforma, która upraszcza proces pisania kodu przetwarzania tła, który uzyskuje dostęp do danych z usług Azure. Zestaw SDK funkcji składni deklaratywnej służącą do zdarzeń, które powinny wywoływać funkcji, takich jak nowy komunikat dodane do kolejki. Podobne składni deklaratywnej steruje odczytywania i zapisywania danych w przypadku, gdy funkcja zostało wyzwolone. Większość niskiego poziomu zadania kodowania związane z dostępem do usług innych firm i Azure zapewnia obsługę tego systemu, wyzwalaczy i powiązań.

### <a name="functions-triggers-and-bindings"></a>Funkcje, wyzwalaczy i powiązań

Zestaw SDK zadań Webjob projekt definiuje co najmniej jeden *funkcji*. Funkcja to metoda, która ma atrybut wyzwalacza w jego podpis metody. Wyzwalacze określają warunki, wywoływania funkcji i powiązania Określ, co do odczytu i zapisu. Na przykład atrybut wyzwalacza w następującej funkcji informuje środowiska uruchomieniowego w wywołaniu funkcji zawsze, gdy zostanie wyświetlony komunikat kolejki w `items` kolejki. `Blob` Atrybut informuje środowiska uruchomieniowego do użycia komunikatu w kolejce w celu odczytania obiektu blob w *pozycje robocze* kontenera. Zawartość komunikatu w kolejce &mdash; w `queueTrigger` parametru &mdash; to nazwa obiektu blob.

```cs
public static void Run(
    [QueueTrigger("items")] string myQueueItem,
    [Blob("workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

### <a name="versions-2x-and-3x"></a>Wersje 2.x i 3.x

Instrukcje informują, jak utworzyć projekt 2.x wersji zestawu SDK zadań Webjob, z uwagi na temat co różni 3.x (w wersji zapoznawczej). Główne zmiany wprowadzone przez 3.x jest korzystanie z platformy .NET Core zamiast .NET Framework.

### <a name="azure-functions"></a>Azure Functions

[Środowisko Azure Functions](../azure-functions/functions-overview.md) opiera się na zestaw SDK zadań Webjob i jest opcją w przypadku nie trzeba korzystać bezpośrednio z zestawu SDK zadań Webjob. Azure używa 1.x funkcje zestawu SDK WebJobs 2.x. Aby uzyskać więcej informacji, zobacz [porównanie usługi Azure Functions i zestaw SDK zadań Webjob](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, masz [konta platformy Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) i doświadczenia z [aplikacji w usłudze Azure App Service](app-service-web-overview.md). Aby wykonać kroki opisane w tym artykule:

* [Zainstaluj program Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/) z **Azure programowanie** obciążenia. Jeśli już masz program Visual Studio, ale nie ma tego obciążenia, obciążenie dodawać przez zaznaczenie **Narzędzia > Pobierz narzędzia i funkcje**.
* [Utwórz aplikację usługi aplikacji](app-service-web-get-started-dotnet-framework.md). Jeśli masz już konto, które można wdrożyć zadania WebJob do, można użyć który zamiast tworzenia nowej.

## <a name="create-a-project"></a>Tworzenie projektu

1. W programie Visual Studio, wybierz **Plik > Nowy projekt**.

2. Wybierz **klasycznego pulpitu systemu Windows > konsoli aplikacji (.NET Framework)**.

   Aby utworzyć projekt 3.x, wybierz **.NET Core > aplikacji konsoli (.NET Core)**.

3. Nazwij projekt *WebJobsSDKSample*, a następnie wybierz **OK**.

   ![Okno dialogowe nowego projektu](./media/webjobs-sdk-get-started/new-project.png)

## <a name="add-webjobs-nuget-package"></a>Dodaj pakiet NuGet zadań Webjob

1. Zainstaluj najnowszą wersję stabilna 2.x pakietu NuGet `Microsoft.Azure.WebJobs`. (Dla zestawu SDK zadań Webjob 3.x, należy wybrać najnowszej wersji 3.x.)
 
   Oto **Konsola Menedżera pakietów** polecenia dla wersji 2.2.0:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs -version 2.2.0
   ``` 

## <a name="create-the-jobhost"></a>Utwórz JobHost

`JobHost` Obiekt jest kontenerem środowiska uruchomieniowego dla funkcji: nasłuchuje wyzwalaczy i wywołania funkcji. 

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

## <a name="enable-console-logging"></a>Włącz rejestrowanie konsoli

Dostępnych jest kilka opcji rejestrowania w projekcie zestaw SDK zadań Webjob. Zalecamy to jeden [struktury rejestrowania, który został opracowany dla platformy ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging). Ta struktura zapewnia lepszą wydajność i większą elastyczność nośnikach magazynowania i filtrowania. 

W tej sekcji służy do konfigurowania rejestrowania konsoli, która używa nowej struktury.

1. Zainstaluj najnowszą wersję następujących pakietów NuGet:

   * `Microsoft.Extensions.Logging` -Struktury rejestrowania.
   * `Microsoft.Extensions.Logging.Console` -Konsola *dostawcy*. Dostawca wysyła dzienników do określonego miejsca docelowego, w tym przypadku do konsoli. 
 
   Poniżej przedstawiono **Konsola Menedżera pakietów** polecenia dla wersji 2.0.1:

   ```powershell
   Install-Package Microsoft.Extensions.Logging -version 2.0.1
   ``` 

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
   ``` 

2. W *Program.cs*, Dodaj `using` instrukcji:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

3. W `Main` metody, Dodaj kod, aby zaktualizować `JobHostConfiguration` przed utworzeniem `JobHost`:
 
   ```
   config.DashboardConnectionString = "";
   var loggerFactory = new LoggerFactory();
   config.LoggerFactory = loggerFactory
       .AddConsole();
   ```

   Ten kod wprowadza następujące zmiany:

   * Wyłącza [rejestrowania pulpitu nawigacyjnego](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Pulpit nawigacyjny jest starsza wersja narzędzi do monitorowania i rejestrowania pulpit nawigacyjny nie jest zalecane w scenariuszach wysokiej przepustowości w środowisku produkcyjnym.
   * Dodaje dostawcę konsoli z domyślnymi [filtrowania](webjobs-sdk-how-to.md#log-filtering). 

   `Main` Metody teraz wygląda następująco:

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

1. Utwórz *Functions.cs* w folderze projektu i Zastąp kod szablonu ten kod:

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

   `QueueTrigger` Atrybut informuje środowiska uruchomieniowego do wywołania tej funkcji, gdy nowy komunikat zostanie zapisany w kolejce usługi Azure Storage o nazwie `queue`. Zawartość komunikatu w kolejce są przekazywane do kodu metody `message` parametru. Treść metody jest, gdzie przetwarzania danych wyzwalacza. W tym przykładzie kodu rejestruje tylko wiadomości.

   `message` Parametru nie musi być ciągiem. Można także powiązać obiekt JSON, tablica bajtów lub [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage) obiektu. [Zobacz Użycie wyzwalacza kolejki](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Każdy typ powiązania (na przykład kolejek, obiektów blob lub tabel) ma inny zestaw typów parametrów, które można powiązać.

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Emulator usługi Azure Storage działa lokalnie nie ma wszystkich funkcji, które wymaga zestawu SDK zadań Webjob. Dlatego w tej sekcji Tworzenie konta magazynu na platformie Azure i konfigurowanie projektu z niego korzystać.

1. Otwórz **Eksploratora serwera** i logowanie do platformy Azure. Kliknij prawym przyciskiem myszy **Azure** węzeł, a następnie wybierz **nawiązywanie połączenia z subskrypcją platformy Microsoft Azure**.

   ![Logowanie do platformy Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. W obszarze **Azure** w węźle **Eksploratora serwera**, kliknij prawym przyciskiem myszy **magazynu**, a następnie wybierz **konta magazynu Utwórz**.

   ![Tworzenie menu konta magazynu](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

2. W **Utwórz konto magazynu** okna dialogowego wprowadź unikatową nazwę konta magazynu.

3. Wybierz taki sam **Region** czy utworzono aplikację usługi aplikacji w lub zamknij regionu.

1. Wybierz pozycję **Utwórz**.

   ![Tworzenie konta magazynu](./media/webjobs-sdk-get-started/create-storage-account.png)

1. W obszarze **magazynu** w węźle **Eksploratora serwera**, wybierz nowe konto magazynu. W **właściwości** okna, wybierz wielokropek (**...** ) w prawym rogu **ciąg połączenia** wartość pola.

   ![Wielokropek ciąg połączenia](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

2. Skopiuj parametry połączenia i Zapisz tę wartość w innym, że możesz skopiować go ponownie łatwo.

   ![Skopiuj parametry połączenia](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-for-running-locally"></a>Konfigurowanie magazynu dla uruchomionej na komputerze lokalnym

Zestaw SDK zadań Webjob szuka parametry połączenia magazynu w kolekcji ustawień aplikacji. Po lokalnym uruchomieniu szuka tej wartości w *App.config* zmienne plików lub środowiska.

1. Dodaj następujący kod XML *App.config* pliku natychmiast po otwarciu `<configuration>` tagu.

   ```xml
   <connectionStrings>
     <add name="AzureWebJobsStorage" connectionString="{storage connection string}" />
   </connectionStrings>
   ```

2. Zastąp *{parametry połączenia magazynu}* parametrami połączenia, które wcześniej zostały skopiowane.

   Później użyjesz parametry połączenia ponownie, podczas konfigurowania aplikacji usługi App Service na platformie Azure.

## <a name="test-locally"></a>Test lokalnie

W tej sekcji Tworzenie i uruchamianie projektu lokalnie i wywołać funkcję przez utworzenie komunikatu w kolejce.

1. Naciśnij klawisze Ctrl + F5, aby uruchomić projekt.

   Konsoli Pokazuje, że środowisko wykonawcze znaleziono funkcji i oczekuje na wiadomości w kolejce do wyzwolenia go.

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

   Zobaczysz komunikat ostrzegawczy z informacją `ServicePointManager` ustawienie. Do testowania, który będzie realizacji z tego projektu, można zignorować to ostrzeżenie. Aby uzyskać więcej informacji na temat tego ostrzeżenia, zobacz [sposobu korzystania z zestawu SDK WebJobs](webjobs-sdk-how-to.md#jobhost-servicepointmanager-settings).

2. Zamknij okno konsoli.

1. W **Eksploratora serwera**, rozwiń węzeł dla nowego konta magazynu, a następnie kliknij prawym przyciskiem myszy **kolejek**. 

2. Wybierz **Utwórz kolejkę**. 

3. Wprowadź *kolejki* jako nazwę kolejki, a następnie wybierz **OK**.

   ![Tworzenie kolejki](./media/webjobs-sdk-get-started/create-queue.png)

4. Kliknij prawym przyciskiem myszy węzeł nowej kolejki, a następnie wybierz **widoku kolejki**.

5. Wybierz **dodać komunikat** ikony.

   ![Tworzenie kolejki](./media/webjobs-sdk-get-started/create-queue-message.png)

6. W **dodać komunikat** okna dialogowego, wprowadź *Hello World!* jako **tekst wiadomości**, a następnie wybierz **OK**.

   ![Tworzenie kolejki](./media/webjobs-sdk-get-started/hello-world-text.png)

7. Uruchom ponownie projekt.

   Ponieważ użyto `QueueTrigger` atrybutu w `ProcessQueueMessage` funkcji środowiska uruchomieniowego WeJobs SDK nasłuchuje wiadomości w kolejce podczas uruchamiania. Znajdzie nowej kolejki wiadomości w kolejce o nazwie *kolejki* i wywołuje funkcję.

   Ze względu na [sondowania wykładniczego wycofywania kolejki](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm), może trwać tyle samo co 2 minuty, aż środowiska uruchomieniowego można znaleźć komunikatu i wywołanie funkcji. Czas oczekiwania, można zmniejszyć, uruchamiając [tryb programowania](webjobs-sdk-how-to.md#jobhost-development-settings).

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

8. Zamknij okno konsoli.

## <a name="add-application-insights-logging"></a>Dodaj rejestrowania usługi Application Insights

Po uruchomieniu projektu na platformie Azure, wykonanie funkcji nie można monitorować, wyświetlając dane wyjściowe konsoli. Rozwiązanie monitorowania, zaleca się [usługi Application Insights](../application-insights/app-insights-overview.md). Procedury dotyczące wyświetlania danych telemetrycznych są podobne do [monitorowanie usługi Azure Functions](../azure-functions/functions-monitoring.md#view-telemetry-data).

W tej sekcji możesz wykonać następujące zadania, aby skonfigurować rejestrowanie usługi Application Insights przed wdrożeniem na platformie Azure:

* Upewnij się, że masz aplikacji usługi app Service i wystąpienie usługi Application Insights do pracy z.
* Konfigurowanie aplikacji usługi aplikacji — korzystanie z wystąpienia usługi Application Insights i konto magazynu, który został utworzony wcześniej.
* Konfigurowanie projektu do logowania do usługi Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Tworzenie aplikacji usługi app Service i wystąpienie usługi Application Insights

1. Jeśli nie masz już aplikację usługi aplikacji, która będzie można użyć, [utworzyć](app-service-web-get-started-dotnet-framework.md).

2. Jeśli nie masz jeszcze zasobu usługi Application Insights, który będzie można użyć, [utworzyć](../application-insights/app-insights-create-new-resource.md). Ustaw **typu aplikacji** do **ogólne**i pomijania w kolejnych sekcjach **skopiuj klucz Instrumentacji**.

3. Jeśli masz już zasobu usługi Application Insights, którego chcesz używać, [skopiuj klucz Instrumentacji](../application-insights/app-insights-create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Konfigurowanie ustawień aplikacji 

1. W **Eksploratora serwera**, rozwiń węzeł **usługi aplikacji** węźle **Azure**.

1. Rozwiń grupę zasobów, zawierającą aplikację usługi aplikacji, a następnie kliknij prawym przyciskiem myszy aplikację usługi aplikacji.

3. Wybierz **wyświetlić ustawienia**.

4. W **parametry połączenia** Dodaj następujący wpis.

   |Name (Nazwa)  |Parametry połączenia  |Typ bazy danych|
   |---------|---------|------|
   |AzureWebJobsStorage | {Magazynu parametry połączenia, które wcześniej zostały skopiowane}|Niestandardowy|
   
6. Jeśli **ustawienia aplikacji** okno nie ma instrumentacji usługi Application Insights kluczy, dodaj je, które wcześniej zostały skopiowane. (Klucza Instrumentacji mogą już być, w zależności od sposobu tworzenia aplikacji usługi App Service).

   |Name (Nazwa)  |Wartość  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {klucz Instrumentacji} |

2. Zastąp *{klucza Instrumentacji}* z klucza Instrumentacji z zasobu usługi Application Insights, którego używasz.

2. Wybierz pozycję **Zapisz**.

1. Dodaj następujący kod XML *App.config* pliku, bezpośrednio po pobraniu parametrów połączenia.

   ```xml
   <appSettings>
     <add key="APPINSIGHTS_INSTRUMENTATIONKEY" value="{instrumentation key}" />
   </appSettings>
   ```

2. Zastąp *{klucza Instrumentacji}* z klucza Instrumentacji z zasobu usługi Application Insights, którego używasz.

   Dodanie tych danych na *App.config* pliku umożliwia testowanie połączenia usługi Application Insights, gdy lokalne uruchamianie projektu. 

3. Zapisz zmiany.

### <a name="add-application-insights-logging-provider"></a>Dodaj dostawcę rejestrowania usługi Application Insights

1. Zainstaluj najnowszą wersję stabilna 2.x pakietu NuGet dla dostawcy rejestrowania usługi Application Insights: `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`. (Dla zestawu SDK zadań Webjob 3.x, wybierz najnowszą wersję pakietu 3.x.)

   Oto **Konsola Menedżera pakietów** polecenia dla wersji 2.2.0:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
   ``` 

1. Zainstaluj najnowszą wersję stabilna 4.x pakietu NuGet dla programu configuration manager .NET: `System.Configuration.ConfigurationManager`.

   Oto **Konsola Menedżera pakietów** polecenia dla wersji 4.4.1:

   ```powershell
   Install-Package System.Configuration.ConfigurationManager -version 4.4.1
   ``` 

2. Otwórz *Program.cs* i Dodaj `using` instrukcji dla programu configuration manager:

   ```csharp
   using System.Configuration;
   ```

2. Zastąp kod w `Main` metodę z następującym kodem:

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

   * Dodaje dostawcę rejestrowania usługi Application Insights z domyślnymi [filtrowania](webjobs-sdk-how-to.md#log-filtering); wszystkie informacje i wyższym poziomie dzienniki teraz będzie przejdź do konsoli i usługi Application Insights, gdy jest uruchomione lokalnie. 
   * Umieszcza `LoggerFactory` obiektu w `using` bloku, aby upewnić się, że wpisu w dzienniku jest opróżniany, gdy host jest kończona. 

## <a name="test-application-insights-logging"></a>Test rejestrowania usługi Application Insights

W tej sekcji możesz uruchomić lokalnie ponownie można zweryfikować, że rejestrowanie danych to teraz będzie Application Insights do konsoli.

1. Użyj **Eksploratora serwera** można utworzyć komunikatu w kolejce, tak samo jak [wcześniejszych](#trigger-the-function), z wyjątkiem wprowadź *Insights aplikacji Hello!* jako tekst komunikatu.

1. Uruchom projekt.

   Zestaw SDK zadań Webjob przetwarza komunikat z kolejki i znajduje się w dziennikach, w oknie konsoli.

1. Zamknij okno konsoli.

1. Otwórz [portalu Azure](https://portal.azure.com/), a następnie przejdź do zasobu usługi Application Insights.

2. Wybierz **wyszukiwania**.

   ![Wybierz wyszukiwanie](./media/webjobs-sdk-get-started/select-search.png)

1. Jeśli nie widzisz *Insights aplikacji Hello!* komunikat, wybierz opcję **Odśwież** okresowo na kilka minut. (Dzienniki nie pojawiają się natychmiast z powodu czasu dla klienta usługi Application Insights opróżnić dziennik przetwarza.)

   ![Dzienniki w usłudze Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

3. Zamknij okno konsoli.

## <a name="deploy-as-a-webjob"></a>Wdróż jako zadanie WebJob

W tej sekcji możesz wdrożyć projekt jako zadanie WebJob. Wdrażania aplikacji usługi aplikacji — które [utworzony wcześniej](#create-app-service-app-and-application-insights-instance). Do testowania kodu, gdy działa na platformie Azure, będzie wyzwolenie wywołania funkcji, tworząc komunikatu w kolejce.

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt, a następnie wybierz **Publikuj jako zadanie WebJob Azure**.

1. W **dodać zadania WebJob Azure** okno dialogowe, wybierz opcję **OK**.

   ![Dodaj zadanie WebJob platformy Azure](./media/webjobs-sdk-get-started/add-azure-webjob.png)

   Visual Studio automatycznie instaluje pakietu NuGet publikowania zadań WebJob.

1. W **profilu** kroku **publikowania** kreatora wybierz **Microsoft Azure App Service**.

   ![Okno dialogowe publikowania](./media/webjobs-sdk-get-started/publish-dialog.png)

1. W **usługi aplikacji** okno dialogowe, wybierz opcję **grupie zasobów > aplikację usługi aplikacji**, a następnie wybierz **OK**.

   ![Usługi aplikacji — okno dialogowe](./media/webjobs-sdk-get-started/app-service-dialog.png)

1. W **połączenia** kroku kreatora wybierz pozycję **publikowania**.

## <a name="trigger-the-function-in-azure"></a>Wyzwalacz funkcji na platformie Azure

1. Upewnij się, że nie jest uruchomiona lokalnie (Zamknij okno konsoli, jeśli jest nadal otwarty). W przeciwnym razie lokalne wystąpienie może być pierwszą osobą, która przetworzyć wszystkie wiadomości w kolejce, którą utworzysz.

1. Użyj **Eksploratora serwera** można utworzyć komunikatu w kolejce, tak samo jak [wcześniejszych](#trigger-the-function), z wyjątkiem wprowadź *Hello Azure!*.

7. Odśwież **kolejki** strony w Visual Studio i nowy komunikat zniknął, ponieważ funkcja uruchomionych w usłudze Azure App Service przetworzyć go.

   > [!TIP]
   > Podczas testowania na platformie Azure, użyj [tryb programowania](webjobs-sdk-how-to.md#jobhost-development-settings) upewnij się, że funkcja wyzwalacza kolejki jest wywoływana od razu i uniknąć opóźnienia z powodu [sondowania wykładniczego wycofywania kolejki](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm).

### <a name="view-logs-in-application-insights"></a>Wyświetl dzienniki w usłudze Application Insights

1. Otwórz [portalu Azure](https://portal.azure.com/), a następnie przejdź do zasobu usługi Application Insights.

2. Wybierz **wyszukiwania**.

1. Jeśli nie widzisz *Hello Azure!* komunikat, wybierz opcję **Odśwież** okresowo na kilka minut.

   Zobacz dzienniki z funkcji uruchomionych w zadanie WebJob, w tym *Hello Azure!* tekst, który został wprowadzony w poprzedniej sekcji.

## <a name="add-an-input-binding"></a>Dodaj powiązania wejściowego

Powiązania wejściowe uprościć kod odczytuje dane. Na przykład komunikatu w kolejce będzie nazwa obiektu blob, a nazwa obiektu blob używanego do znajdowania i odczytywania obiektów blob w usłudze Azure Storage.

1. W *Functions.cs*, Zastąp `ProcessQueueMessage` metodę z następującym kodem:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   W tym kodzie `queueTrigger` jest [powiązanie wyrażenie](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns), co oznacza, że jest rozpoznawany jako inną wartość w czasie wykonywania.  W czasie wykonywania ma zawartość komunikatu w kolejce.

2. Dodaj `using`:

   ```cs
   using System.IO;
   ```

3. Tworzenie kontenera obiektów blob na koncie magazynu.

   a. W **Eksploratora serwera**, rozwiń węzeł dla konta magazynu, kliknij prawym przyciskiem myszy **obiekty BLOB**, a następnie wybierz **Tworzenie kontenera obiektów Blob**.

   b. W **Tworzenie kontenera obiektów Blob** okna dialogowego, wprowadź *kontenera* jako nazwa kontenera, a następnie kliknij przycisk **OK**.

4. Przekaż *Program.cs* pliku do kontenera obiektów blob. (Ten plik jest używany w tym miejscu jako przykład; można przekazać dowolny plik tekstowy i Tworzenie komunikatu w kolejce z nazwą pliku.)

   a. W **Eksploratora serwera**, kliknij dwukrotnie węzeł kontenera właśnie utworzony.

   b. W **kontenera** wybierz **przekazać** przycisku.

   ![Przycisk Prześlij obiektów blob](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Znajdź i zaznacz pozycję *Program.cs*, a następnie wybierz **OK**.

5. Tworzenie kolejki wiadomości w kolejce został utworzony wcześniej, z *Program.cs* jako tekst wiadomości.

   ![Komunikat z kolejki Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

6. Uruchom projekt.

   Komunikat z kolejki wyzwala funkcję, która następnie odczytuje obiektu blob i rejestruje jego długość. Dane wyjściowe konsoli wygląda następująco:

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

Powiązania danych wyjściowych uprościć kod, który zapisuje dane. W tym przykładzie modyfikuje poprzedniego przez zapisywanie kopii obiektu blob zamiast rejestrowania jego rozmiaru.

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

5. Utwórz innego komunikatu w kolejce z *Program.cs* jako tekst wiadomości.

6. Uruchom projekt.

   Komunikat z kolejki wyzwala funkcję, która następnie odczytuje obiektu blob, dzienniki jego długość i tworzy nowy obiekt blob. Dane wyjściowe konsoli jest taki sam, ale kiedy przejdź do okna kontenera obiektów blob i wybierz **Odśwież**, zobacz nowego obiektu blob o nazwie *Program.cs kopiowania.*

## <a name="next-steps"></a>Kolejne kroki

Ten podręcznik pokazuje sposób tworzenia, uruchamiania i Wdróż projekt zestawu SDK zadań Webjob.

Aby pokazać wszystko, co prowadzi do projektu zestawu SDK zadań Webjob, zgodnie z instrukcjami miał tworzenia projektu od początku. Jednak po utworzeniu swój kolejny projekt, należy rozważyć użycie **zadań WebJob Azure** szablonu w **chmury** kategorii. Ten szablon tworzy projekt z pakietami NuGet i przykładowy kod już skonfigurowane. Należy zauważyć, że przykładowy kod może zostać zmienione w celu obsługi nowej struktury rejestrowania.

Aby uzyskać więcej informacji, zobacz [sposobu korzystania z zestawu SDK WebJobs](webjobs-sdk-how-to.md).
