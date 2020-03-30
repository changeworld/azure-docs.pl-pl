---
title: Monitorowanie usługi Azure Functions
description: Dowiedz się, jak używać usługi Azure Application Insights z usługą Azure Functions do monitorowania wykonywania funkcji.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 04/04/2019
ms.openlocfilehash: 08da17f1ef023676aa0c499cf4e7e1bb9687f1c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257857"
---
# <a name="monitor-azure-functions"></a>Monitorowanie usługi Azure Functions

[Usługa Azure Functions](functions-overview.md) oferuje wbudowaną integrację z [usługą Azure Application Insights](../azure-monitor/app/app-insights-overview.md) w celu monitorowania funkcji. W tym artykule pokazano, jak skonfigurować usługi Azure Functions do wysyłania plików dziennika generowanych przez system do usługi Application Insights.

Zaleca się korzystanie z usługi Application Insights, ponieważ zbiera dane dziennika, wydajności i błędów. Automatycznie wykrywa anomalie wydajności i zawiera zaawansowane narzędzia analityczne ułatwiające diagnozowanie problemów i zrozumienie, jak używane są funkcje. Usługa ta pomaga w ciągłym udoskonalaniu wydajności i użyteczności tworzonych rozwiązań. Można nawet użyć usługi Application Insights podczas tworzenia projektu aplikacji funkcji lokalnych. Aby uzyskać więcej informacji, zobacz [Co to jest usługa Application Insights?](../azure-monitor/app/app-insights-overview.md).

Ponieważ wymagane instrumentacji usługi Application Insights jest wbudowany w usługi Azure Functions, wszystko, czego potrzebujesz, to prawidłowy klucz instrumentacji, aby połączyć aplikację funkcji do zasobu usługi Application Insights. Klucz instrumentacji należy dodać do ustawień aplikacji, gdy zasób aplikacji funkcji jest tworzony na platformie Azure. Jeśli aplikacja funkcyjna nie ma jeszcze tego klucza, możesz [ustawić go ręcznie](#enable-application-insights-integration).  

## <a name="application-insights-pricing-and-limits"></a>Ceny i limity usługi Application Insights

Możesz wypróbować integrację usługi Application Insights z aplikacjami funkcji za darmo. Istnieje dzienny limit ilości danych, które mogą być przetwarzane za darmo. Możesz osiągnąć ten limit podczas testowania. Platforma Azure udostępnia powiadomienia w portalu i wiadomości e-mail, gdy zbliżasz się do dziennego limitu. Jeśli przegapisz te alerty i osiągniesz limit, nowe dzienniki nie będą wyświetlane w kwerendach usługi Application Insights. Należy pamiętać o limicie, aby uniknąć niepotrzebnego czasu rozwiązywania problemów. Aby uzyskać więcej informacji, zobacz [Zarządzanie cennikiem i wolumenem danych w usłudze Application Insights](../azure-monitor/app/pricing.md).

Pełna lista funkcji usługi Application Insights dostępnych dla aplikacji funkcji jest szczegółowo opisana w [usłudze Application Insights for Azure Functions obsługiwane funkcje.](../azure-monitor/app/azure-functions-supported-features.md)

## <a name="view-telemetry-in-monitor-tab"></a>Wyświetlanie danych telemetrycznych na karcie Monitor

Po [włączeniu integracji usługi Application Insights](#enable-application-insights-integration)można wyświetlać dane telemetryczne na karcie **Monitor.**

1. Na stronie aplikacji funkcji wybierz funkcję, która została uruchomiony co najmniej raz po skonfigurowaniu usługi Application Insights. Następnie wybierz kartę **Monitor.** Wybierz okresowe **odświeżanie,** aż pojawi się lista wywołań funkcji.

   ![Lista wywołań](media/functions-monitoring/monitor-tab-ai-invocations.png)

    > [!NOTE]
    > Może upłynąć do pięciu minut, aby lista pojawiła się, gdy klient telemetrii partii danych do transmisji do serwera. Opóźnienie nie dotyczy [strumienia metryk na żywo](../azure-monitor/app/live-stream.md). Ta usługa łączy się z hostem funkcji podczas ładowania strony, więc dzienniki są przesyłane strumieniowo bezpośrednio do strony.

1. Aby wyświetlić dzienniki dla wywołania określonej funkcji, wybierz łącze kolumny **Data (UTC)** dla tego wywołania. Dane wyjściowe rejestrowania dla tego wywołania pojawia się na nowej stronie.

   ![Szczegóły wywołania](media/functions-monitoring/invocation-details-ai.png)

1. Wybierz łącze **Uruchom w usłudze Application Insights,** aby wyświetlić źródło kwerendy pobierającej dane dziennika usługi Azure Monitor w dzienniku azure Jeśli jest to pierwszy raz przy użyciu usługi Azure Log Analytics w ramach subskrypcji, zostaniesz poproszony o włączenie.

1. Po wybraniu tego łącza i wybierz, aby włączyć log analitycznych. zostanie wyświetlona następująca kwerenda. Widać, że wyniki kwerendy są ograniczone do ostatnich`where timestamp > ago(30d)`30 dni ( ). Ponadto wyniki pokazują nie więcej niż 20`take 20`rzędów ( ). Natomiast lista szczegółów wywołania funkcji jest dla ostatnich 30 dni bez limitu.

   ![Lista wywołań usługi Application Insights Analytics](media/functions-monitoring/ai-analytics-invocation-list.png)

Aby uzyskać więcej informacji, zobacz [Kwerenda danych telemetrycznych](#query-telemetry-data) w dalszej części tego artykułu.

## <a name="view-telemetry-in-application-insights"></a>Wyświetlanie danych telemetrycznych w usłudze Application Insights

Aby otworzyć usługę Application Insights z aplikacji funkcji w witrynie Azure portal, przejdź do aplikacji funkcji **przegląd** strony. W obszarze **Skonfigurowane funkcje**wybierz pozycję **Application Insights**.

![Otwórz statystykę aplikacji na stronie Przegląd aplikacji funkcji](media/functions-monitoring/ai-link.png)

Aby uzyskać informacje dotyczące korzystania z usługi Application Insights, zobacz [dokumentację usługi Application Insights](https://docs.microsoft.com/azure/application-insights/). W tej sekcji przedstawiono kilka przykładów sposobu wyświetlania danych w usłudze Application Insights. Jeśli znasz już aplikację Application Insights, możesz przejść bezpośrednio do [sekcji dotyczących konfigurowania i dostosowywania danych telemetrycznych](#configure-categories-and-log-levels).

![Karta Przegląd usługi Application Insights](media/functions-monitoring/metrics-explorer.png)

Następujące obszary usługi Application Insights mogą być przydatne podczas oceny zachowania, wydajności i błędów w funkcjach:

| Zbadaj | Opis |
| ---- | ----------- |
| **[Błędy](../azure-monitor/app/asp-net-exceptions.md)** |  Tworzenie wykresów i alertów na podstawie błędów funkcji i wyjątków serwera. **Nazwa operacji** jest nazwą funkcji. Błędy w zależnościach nie są wyświetlane, chyba że zaimplementujesz niestandardowe dane telemetryczne dla zależności. |
| **[Wydajność](../azure-monitor/app/performance-counters.md)** | Analizuj problemy z wydajnością, wyświetlając wykorzystanie zasobów i przepływność na **wystąpienia roli w chmurze.** Te dane mogą być przydatne w przypadku debugowania scenariuszy, w których funkcje są bogging dół podstawowych zasobów. |
| **[Metryki](../azure-monitor/app/metrics-explorer.md)** | Tworzenie wykresów i alertów opartych na metrykach. Metryki obejmują liczbę wywołań funkcji, czas wykonywania i wskaźniki powodzenia. |
| **[Dane na żywo](../azure-monitor/app/live-stream.md)** | Wyświetlanie danych metryk utworzonych w czasie zbliżonym do rzeczywistego. |

## <a name="query-telemetry-data"></a>Zapytanie o dane telemetryczne

[Usługa Application Insights Analytics](../azure-monitor/app/analytics.md) zapewnia dostęp do wszystkich danych telemetrycznych w postaci tabel w bazie danych. Analytics udostępnia język zapytań do wyodrębniania, manipulowania i wizualizacji danych. 

Wybierz **dzienniki** do eksplorowania lub kwerendy dla zarejestrowanych zdarzeń.

![Przykład analizy](media/functions-monitoring/analytics-traces.png)

Oto przykład kwerendy, który pokazuje dystrybucję żądań na pracownika w ciągu ostatnich 30 minut.

<pre>
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
</pre>

Tabele, które są dostępne są wyświetlane na karcie **Schemat** po lewej stronie. Dane generowane przez wywołania funkcji można znaleźć w następujących tabelach:

| Tabela | Opis |
| ----- | ----------- |
| **Ślady** | Dzienniki utworzone przez środowisko wykonawcze i kod funkcji. |
| **żądania** | Jedno żądanie dla każdego wywołania funkcji. |
| **Wyjątki** | Wszelkie wyjątki zgłaszane przez środowisko wykonawcze. |
| **customMetrics ( customMetrics )** | Liczba udanych i nieudanych wywołań, wskaźnik sukcesu i czas trwania. |
| **customEvents** | Zdarzenia śledzone przez środowisko wykonawcze, na przykład: żądania HTTP, które wyzwalają funkcję. |
| **Performancecounters** | Informacje o wydajności serwerów, na których działają funkcje. |

Inne tabele są dla testów dostępności i danych telemetrycznych klienta i przeglądarki. Można zaimplementować niestandardowe dane telemetryczne, aby dodać do nich dane.

W każdej tabeli niektóre dane specyficzne dla `customDimensions` funkcji znajdują się w polu.  Na przykład następująca kwerenda pobiera wszystkie ślady, które mają poziom `Error`dziennika .

<pre>
traces 
| where customDimensions.LogLevel == "Error"
</pre>

Środowisko wykonawcze `customDimensions.LogLevel` zawiera `customDimensions.Category` pola i. Można podać dodatkowe pola w dziennikach, które piszesz w kodzie funkcji. Zobacz [rejestrowanie strukturalne](#structured-logging) w dalszej części tego artykułu.

## <a name="configure-categories-and-log-levels"></a>Konfigurowanie kategorii i poziomów dziennika

Usługi Application Insights można używać bez konfiguracji niestandardowej. Domyślna konfiguracja może spowodować duże ilości danych. Jeśli używasz subskrypcji platformy Azure programu Visual Studio, może trafić limit danych dla usługi Application Insights. W dalszej części tego artykułu dowiesz się, jak skonfigurować i dostosować dane wysyłane przez funkcje do usługi Application Insights. W przypadku aplikacji funkcyjnej rejestrowanie jest skonfigurowane w pliku [host.json.]

### <a name="categories"></a>Kategorie

Rejestrator funkcji platformy Azure zawiera *kategorię* dla każdego dziennika. Kategoria wskazuje, która część kodu środowiska wykonawczego lub kodu funkcji napisał dziennik. Na poniższym wykresie opisano główne kategorie dzienników, które tworzy środowisko wykonawcze. 

| Kategoria | Opis |
| ----- | ----- | 
| Host.Results (Kategoria Host.Results) | Te dzienniki są wyświetlane jako **żądania** w usłudze Application Insights. Wskazują one na powodzenie lub niepowodzenie funkcji. Wszystkie te dzienniki są `Information` zapisywane na poziomie. Jeśli filtrujesz `Warning` na poziomie lub powyżej, nie zobaczysz żadnych tych danych. |
| Host.Agregator | Te dzienniki zapewniają liczby i średnie wywołania funkcji w [konfigurowalnym](#configure-the-aggregator) okresie czasu. Domyślny okres to 30 sekund lub 1000 wyników, w zależności od tego, co nastąpi wcześniej. Dzienniki są dostępne w tabeli **customMetrics** w usłudze Application Insights. Przykładami są liczba przebiegów, wskaźnik sukcesu i czas trwania. Wszystkie te dzienniki są `Information` zapisywane na poziomie. Jeśli filtrujesz `Warning` na poziomie lub powyżej, nie zobaczysz żadnych tych danych. |

Wszystkie dzienniki dla kategorii innych niż te są dostępne w tabeli **śledzenia** w usłudze Application Insights.

Wszystkie dzienniki z kategoriami, które zaczynają `Host` się są zapisywane przez środowisko uruchomieniowe functions. **Funkcja uruchomiona** i **funkcja ukończone** `Host.Executor`dzienniki mają kategorię . W przypadku udanych przebiegów te dzienniki są `Information` na poziomie. Wyjątki są rejestrowane `Error` na poziomie. Środowisko wykonawcze `Warning` tworzy również dzienniki poziomu, na przykład: wiadomości kolejki wysyłane do kolejki zatruć.

Środowisko wykonawcze Functions tworzy dzienniki z kategorią, która zaczyna się od "Hosta". W wersji 1.x `function started` `function executed`, `function completed` i dzienniki mają `Host.Executor`kategorię . Począwszy od wersji 2.x, te `Function.<YOUR_FUNCTION_NAME>`dzienniki mają kategorię .

Jeśli piszesz dzienniki w kodzie funkcji, `Function.<YOUR_FUNCTION_NAME>.User` kategoria jest i może być dowolny poziom dziennika. W wersji 1.x środowiska wykonawczego Functions kategoria `Function`jest .

### <a name="log-levels"></a>Poziomy rejestrowania

Rejestrator usług Azure Functions zawiera również *poziom dziennika* z każdym dziennikiem. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) jest wyliczeniem, a kod liczby całkowitej wskazuje względne znaczenie:

|LogLevel    |Code|
|------------|---|
|Ślad       | 0 |
|Debugowanie       | 1 |
|Informacje | 2 |
|Ostrzeżenie     | 3 |
|Błąd       | 4 |
|Krytyczny    | 5 |
|Brak        | 6 |

Poziom `None` dziennika jest wyjaśniony w następnej sekcji. 

### <a name="log-configuration-in-hostjson"></a>Konfiguracja dziennika w pliku host.json

Plik [host.json] konfiguruje ilość rejestrowania aplikacji funkcji wysyłanych do usługi Application Insights. Dla każdej kategorii należy wskazać minimalny poziom dziennika do wysłania. Istnieją dwa przykłady: pierwszy przykład dotyczy [wersji 2.x i nowszych](functions-versions.md#version-2x) funkcji środowiska wykonawczego (z .NET Core), a drugi przykład jest dla środowiska wykonawczego w wersji 1.x.

### <a name="version-2x-and-higher"></a>Wersja 2.x lub nowsza

Wersja w wersji 2.x i nowsze wersje środowiska wykonawczego Funkcji korzystają z [hierarchii filtrów rejestrowania .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

### <a name="version-1x"></a>Wersja 1.x

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

W tym przykładzie konfiguruje następujące reguły:

* W przypadku dzienników `Host.Results` `Function`z kategorią lub , wyślij tylko `Error` poziom i powyżej do usługi Application Insights. Dzienniki `Warning` dla poziomu i poniżej są ignorowane.
* W przypadku dzienników `Host.Aggregator`z kategorią wyślij wszystkie dzienniki do usługi Application Insights. Poziom `Trace` dziennika jest taki sam, jak `Verbose`niektóre rejestratory wywołać , ale używać `Trace` w pliku [host.json.]
* Dla wszystkich innych dzienników, wyślij tylko `Information` poziom i powyżej do usługi Application Insights.

Wartość kategorii w [host.json] kontroluje rejestrowanie dla wszystkich kategorii, które zaczynają się od tej samej wartości. `Host`w [host.json] kontroluje `Host.General`rejestrowanie dla , `Host.Executor`, `Host.Results`i tak dalej.

Jeśli [host.json] zawiera wiele kategorii, które zaczynają się od tego samego ciągu, dłuższe są dopasowywać najpierw. Załóżmy, że chcesz `Host.Aggregator` wszystko z `Error` środowiska wykonawczego, `Host.Aggregator` z wyjątkiem `Information` dziennika na poziomie, ale chcesz zalogować się na poziomie:

### <a name="version-2x-and-later"></a>Wersja 2.x i nowsze wersje

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

### <a name="version-1x"></a>Wersja 1.x 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Aby pominąć wszystkie dzienniki dla kategorii, `None`można użyć poziomu dziennika . Żadne dzienniki nie są zapisywane z tej kategorii i nie ma poziomu dziennika powyżej niego.

## <a name="configure-the-aggregator"></a>Konfigurowanie agregatora

Jak wspomniano w poprzedniej sekcji, środowisko wykonawcze agreguje dane dotyczące wykonywania funkcji w okresie czasu. Domyślny okres to 30 sekund lub 1000 przebiegów, w zależności od tego, co nastąpi wcześniej. To ustawienie można skonfigurować w pliku [host.json.]  Oto przykład:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Konfigurowanie próbkowania

Usługa Application Insights ma funkcję [próbkowania,](../azure-monitor/app/sampling.md) która może chronić cię przed tworzeniem zbyt dużej ilości danych telemetrycznych w zakończonych wykonaniach w okresach obciążenia szczytowego. Gdy szybkość przychodzących wykonań przekracza określony próg, usługa Application Insights zaczyna losowo ignorować niektóre przychodzące wykonania. Domyślne ustawienie maksymalnej liczby wykonań na sekundę wynosi 20 (pięć w wersji 1.x). Próbkowanie można skonfigurować w [pliku host.json].  Oto przykład:

### <a name="version-2x-and-later"></a>Wersja 2.x i nowsze wersje

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20
      }
    }
  }
}
```

### <a name="version-1x"></a>Wersja 1.x 

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

> [!NOTE]
> [Próbkowanie](../azure-monitor/app/sampling.md) jest domyślnie włączone. Jeśli wydaje się, że brakuje danych, może być konieczne dostosowanie ustawień próbkowania, aby pasowały do określonego scenariusza monitorowania.

## <a name="write-logs-in-c-functions"></a>Write logs in C# functions (Zapisywanie dzienników w funkcjach języka C#)

Można napisać dzienniki w kodzie funkcji, które pojawiają się jako ślady w usłudze Application Insights.

### <a name="ilogger"></a>ILogger

Użyj parametru [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) w swoich `TraceWriter` funkcjach zamiast parametru. Dzienniki utworzone `TraceWriter` za pomocą usługi `ILogger` Przejdź do usługi Application Insights, ale umożliwia [rejestrowanie strukturalne.](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)

Za `ILogger` pomocą obiektu `Log<level>` wywołać [metody rozszerzenia na ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) do tworzenia dzienników. Poniższy kod `Information` zapisuje dzienniki z kategorią "Function.<YOUR_FUNCTION_NAME>. Użytkownik".

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Rejestrowanie strukturalne

Kolejność symboli zastępczych, a nie ich nazwy, określa, które parametry są używane w komunikacie dziennika. Załóżmy, że masz następujący kod:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Jeśli zachowasz ten sam ciąg wiadomości i odwrócisz kolejność parametrów, wynikowy tekst wiadomości będzie miał wartości w niewłaściwych miejscach.

Symbole zastępcze są obsługiwane w ten sposób, dzięki czemu można wykonać rejestrowanie strukturalne. Usługa Application Insights przechowuje pary nazwa-wartość parametru i ciąg komunikatu. W rezultacie argumenty wiadomości stają się polami, na których można wysyłać kwerendy.

Jeśli wywołanie metody rejestratora wygląda jak w poprzednim `customDimensions.prop__rowKey`przykładzie, można zbadać pole . `prop__` Prefiks jest dodawany, aby upewnić się, że nie ma żadnych kolizji między polami, które dodaje środowisko wykonawcze i pola dodawane przez kod funkcji.

Można również zbadać oryginalny ciąg wiadomości, odwołując się do pola `customDimensions.prop__{OriginalFormat}`.  

Oto przykładowa reprezentacja `customDimensions` danych JSON:

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="custom-metrics-logging"></a>Rejestrowanie niestandardowych metryk

W c# funkcje skryptu, można użyć metody `LogMetric` rozszerzenia do `ILogger` tworzenia metryk niestandardowych w usłudze Application Insights. Oto przykładowe wywołanie metody:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Ten kod jest alternatywą dla wywoływania `TrackMetric` przy użyciu interfejsu API usługi Application Insights dla platformy .NET.

## <a name="write-logs-in-javascript-functions"></a>Write logs in JavaScript functions (Zapisywanie dzienników w funkcjach języka JavaScript)

W node.js funkcje, użyj `context.log` do zapisu dzienników. Rejestrowanie strukturalne nie jest włączone.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Rejestrowanie niestandardowych metryk

Po uruchomieniu w [wersji 1.x](functions-versions.md#creating-1x-apps) środowiska wykonawczego functions, Node.js `context.log.metric` funkcje można użyć metody do tworzenia niestandardowych metryk w usłudze Application Insights. Ta metoda nie jest obecnie obsługiwana w wersji 2.x i nowszej. Oto przykładowe wywołanie metody:

```javascript
context.log.metric("TestMetric", 1234);
```

Ten kod jest alternatywą dla wywoływania `trackMetric` przy użyciu node.js SDK dla usługi Application Insights.

## <a name="log-custom-telemetry-in-c-functions"></a>Log custom telemetry in C# functions (Rejestrowanie telemetrii niestandardowej w funkcjach języka C#)

Istnieje wersja SDK usługi application insights specyficzna dla funkcji, której można używać do wysyłania niestandardowych danych telemetrycznych z funkcji do usługi Application Insights: [Microsoft.Azure.WebJobs.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights). Aby zainstalować ten pakiet, użyj następującego polecenia z wiersza polecenia:

# <a name="command"></a>[Polecenie](#tab/cmd)

```cmd
dotnet add package Microsoft.Azure.WebJobs.Logging.ApplicationInsights --version <VERSION>
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <VERSION>
```

---

W tym poleceniu zastąp `<VERSION>` wersją tego pakietu, która obsługuje zainstalowaną wersję programu [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/). 

Poniższe przykłady języka C# używają [niestandardowego interfejsu API telemetrii](../azure-monitor/app/api-custom-events-metrics.md). Przykład jest dla biblioteki klas .NET, ale kod usługi Application Insights jest taki sam dla skryptu Języka C#.

### <a name="version-2x-and-later"></a>Wersja 2.x i nowsze wersje

Wersja 2.x i nowsze wersje środowiska wykonawczego używają nowszych funkcji w usłudze Application Insights do automatycznego skorelowania danych telemetrycznych z bieżącą operacją. Nie ma potrzeby ręcznego ustawiania `ParentId`operacji `Name` `Id`ani pól.

```cs
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using System.Linq;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Track an Event
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
            this.telemetryClient.TrackMetric(metric);

            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

[GetMetric](../azure-monitor/app/api-custom-events-metrics.md#getmetric) jest obecnie zalecany interfejs API do tworzenia metryki.

### <a name="version-1x"></a>Wersja 1.x

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```

Nie dzwonić `TrackRequest` `StartOperation<RequestTelemetry>` lub ponieważ zobaczysz zduplikowane żądania wywołania funkcji.  Środowisko wykonawcze Funkcji automatycznie śledzi żądania.

Nie ustawiaj `telemetryClient.Context.Operation.Id`. To ustawienie globalne powoduje niepoprawną korelację, gdy wiele funkcji jest uruchomionych jednocześnie. Zamiast tego należy utworzyć nowe`DependencyTelemetry`wystąpienie telemetrii ( , `EventTelemetry`) i zmodyfikować jego `Context` właściwość. Następnie przekaż w wystąpieniu telemetrii`TrackDependency()` `TrackEvent()`do `TrackMetric()`odpowiedniej `Track` metody on `TelemetryClient` ( , , ). Ta metoda zapewnia, że dane telemetryczne ma poprawne szczegóły korelacji dla bieżącego wywołania funkcji.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Log custom telemetry in JavaScript functions (Rejestrowanie telemetrii niestandardowej w funkcjach języka JavaScript)

Oto przykładowy fragment kodu, który wysyła niestandardowe dane telemetryczne za pomocą [sdk Node.js aplikacji:](https://github.com/microsoft/applicationinsights-node.js)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    context.done();
};
```

Parametr `tagOverrides` ustawia `operation_Id` identyfikator wywołania funkcji. To ustawienie umożliwia skorelowanie wszystkich automatycznie generowanych i niestandardowych danych telemetrycznych dla danego wywołania funkcji.

## <a name="dependencies"></a>Zależności

Funkcje w wersji 2 automatycznie zbiera zależności dla żądań HTTP, ServiceBus, EventHub i SQL.

Można napisać kod niestandardowy, aby wyświetlić zależności. Przykładowy kod można znaleźć w [sekcji niestandardowych danych telemetrycznych języka C#.](#log-custom-telemetry-in-c-functions) Przykładowy kod powoduje *mapę aplikacji* w usłudze Application Insights, która wygląda następująco:

![Mapa aplikacji](./media/functions-monitoring/app-map.png)

## <a name="enable-application-insights-integration"></a>Enable Application Insights integration (Włączanie integracji z usługą Application Insights)

Aby aplikacja funkcji wysyłała dane do usługi Application Insights, musi znać klucz instrumentacji zasobu usługi Application Insights. Klucz musi znajdować się w ustawieniach aplikacji o nazwie **APPINSIGHTS_INSTRUMENTATIONKEY**.

Podczas tworzenia aplikacji funkcji [w witrynie Azure portal](functions-create-first-azure-function.md), z wiersza polecenia przy użyciu narzędzia Azure Functions Core [Tools](functions-create-first-azure-function-azure-cli.md)lub przy użyciu programu Visual [Studio Code](functions-create-first-function-vs-code.md), integracja usługi Application Insights jest domyślnie włączona. Zasób usługi Application Insights ma taką samą nazwę jak aplikacja funkcji i jest tworzony w tym samym regionie lub w najbliższym regionie.

### <a name="new-function-app-in-the-portal"></a>Nowa aplikacja funkcji w portalu

Aby przejrzeć tworzony zasób usługi Application Insights, zaznacz go, aby rozwinąć okno **Usługi Application Insights.** Możesz zmienić **nazwę nowego zasobu** lub wybrać inną **lokalizację** w lokalizacji [platformy Azure,](https://azure.microsoft.com/global-infrastructure/geographies/) w której chcesz przechowywać dane.

![Włączanie usługi Application Insights podczas tworzenia aplikacji funkcji](media/functions-monitoring/enable-ai-new-function-app.png)

Po wybraniu opcji **Utwórz**zasób usługi Application Insights `APPINSIGHTS_INSTRUMENTATIONKEY` jest tworzony za pomocą aplikacji funkcji, która ma ustawiony w ustawieniach aplikacji. Wszystko jest gotowe do pracy.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Dodawanie do istniejącej aplikacji funkcji 

Podczas tworzenia aplikacji funkcji przy użyciu [programu Visual Studio](functions-create-your-first-function-visual-studio.md)należy utworzyć zasób usługi Application Insights. Następnie można dodać klucz instrumentacji z tego zasobu jako ustawienie aplikacji w aplikacji funkcji.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Wczesne wersje funkcji używane wbudowane monitorowanie, które nie jest już zalecane. Podczas włączania integracji usługi Application Insights dla takiej aplikacji funkcji, należy również [wyłączyć wbudowane rejestrowanie](#disable-built-in-logging).  

## <a name="report-issues"></a>Zgłaszanie problemów

Aby zgłosić problem z integracją usługi Application Insights w usłudze Functions lub zgłosić sugestię lub żądanie, [utwórz problem w usłudze GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Dzienniki przesyłania strumieniowego

Podczas tworzenia aplikacji często chcesz zobaczyć, co jest zapisywane w dziennikach w czasie zbliżonym do rzeczywistego podczas uruchamiania na platformie Azure.

Istnieją dwa sposoby wyświetlania strumienia plików dziennika generowanych przez wykonanie funkcji.

* **Wbudowane przesyłanie strumieniowe dziennika:** platforma usługi App Service umożliwia wyświetlanie strumienia plików dziennika aplikacji. Jest to równoważne dane wyjściowe widoczne podczas debugowania funkcji podczas [rozwoju lokalnego](functions-develop-local.md) i podczas korzystania z karty **Test** w portalu. Wyświetlane są wszystkie informacje oparte na logowaniu. Aby uzyskać więcej informacji, zobacz [Dzienniki strumienia](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Ta metoda przesyłania strumieniowego obsługuje tylko jedno wystąpienie i nie można jej używać z aplikacją działającą w systemie Linux w planie zużycia.

* **Strumień metryk na żywo:** gdy aplikacja funkcji jest [połączona z usługą Application Insights,](#enable-application-insights-integration)można wyświetlać dane dziennika i inne metryki w czasie zbliżonym do rzeczywistego w witrynie Azure portal przy użyciu [strumienia metryk na żywo.](../azure-monitor/app/live-stream.md) Tej metody należy używać podczas monitorowania funkcji uruchomionych w wielu wystąpieniach lub w systemie Linux w planie zużycia. Ta metoda wykorzystuje [dane próbkowania](#configure-sampling).

Strumienie dziennika można wyświetlać zarówno w portalu, jak i w większości lokalnych środowisk programistów. 

### <a name="portal"></a>Portal

Można wyświetlić oba typy strumieni dziennika w portalu.

#### <a name="built-in-log-streaming"></a>Wbudowane przesyłanie strumieniowe dziennika

Aby wyświetlić dzienniki przesyłania strumieniowego w portalu, wybierz kartę **Funkcje platformy** w aplikacji funkcji. Następnie w obszarze **Monitorowanie**wybierz pozycję **Log streaming**.

![Włączanie dzienników przesyłania strumieniowego w portalu](./media/functions-monitoring/enable-streaming-logs-portal.png)

Spowoduje to połączenie aplikacji z usługą przesyłania strumieniowego dziennika, a dzienniki aplikacji są wyświetlane w oknie. Można przełączać się między **dziennikami aplikacji** a **dziennikami serwera sieci Web**.  

![Wyświetlanie dzienników przesyłania strumieniowego w portalu](./media/functions-monitoring/streaming-logs-window.png)

#### <a name="live-metrics-stream"></a>Transmisja strumieniowa metryk na żywo

Aby wyświetlić strumień metryk na żywo dla aplikacji, wybierz kartę **Przegląd** aplikacji funkcji. Po włączeniu usługi Application Insights w obszarze **Skonfigurowane funkcje**zostanie wyświetlone łącze **Usługi Application Insights** . To łącze spowoduje przejście do strony Statystyka aplikacji dla aplikacji.

W usłudze Application Insights wybierz pozycję **Live Metrics Stream**. [Przykładowe wpisy dziennika](#configure-sampling) są wyświetlane w obszarze **Przykładowe dane telemetryczne**.

![Wyświetlanie strumienia metryk na żywo w portalu](./media/functions-monitoring/live-metrics-stream.png) 

### <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="core-tools"></a>Podstawowe narzędzia

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Dzienniki przesyłania strumieniowego można włączyć przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Zaloguj się, wybierz subskrypcję i strumieniuj pliki dziennika, użyj następujących poleceń:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

Dzienniki przesyłania strumieniowego można włączyć przy użyciu [programu Azure PowerShell](/powershell/azure/overview). W przypadku programu PowerShell użyj następujących poleceń, aby dodać konto platformy Azure, wybrać subskrypcję i przesyłać strumieniowo pliki dziennika:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>Wyłączanie wbudowanego rejestrowania

Po włączeniu usługi Application Insights należy wyłączyć wbudowane rejestrowanie korzystające z usługi Azure Storage. Wbudowane rejestrowanie jest przydatne do testowania z lekkimi obciążeniami, ale nie jest przeznaczone do użycia w produkcji o dużym obciążeniu. Do monitorowania produkcji zaleca się usługa Application Insights. Jeśli wbudowane rejestrowanie jest używane w produkcji, rekord rejestrowania może być niekompletny z powodu ograniczania przepustowości w usłudze Azure Storage.

Aby wyłączyć wbudowane rejestrowanie, `AzureWebJobsDashboard` usuń ustawienie aplikacji. Aby uzyskać informacje dotyczące usuwania ustawień aplikacji w witrynie Azure portal, zobacz sekcję **Ustawienia aplikacji** w sekcji Jak [zarządzać aplikacją funkcji](functions-how-to-use-azure-function-app-settings.md#settings). Przed usunięciem ustawienia aplikacji upewnij się, że żadne istniejące funkcje w tej samej aplikacji funkcji używają ustawienia dla wyzwalaczy lub powiązań usługi Azure Storage.

## <a name="next-steps"></a>Następne kroki

Więcej informacji zawierają następujące zasoby:

* [Application Insights](/azure/application-insights/)
* [rejestrowanie rdzenia ASP.NET](/aspnet/core/fundamentals/logging/)

[host.json]: functions-host-json.md
