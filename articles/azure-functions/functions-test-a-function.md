---
title: Testowanie usługi Azure Functions
description: Tworzenie zautomatyzowanych testów dla C# funkcji w programie Visual Studio i funkcji języka JavaScript w programie VS Code
author: craigshoemaker
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: c60cd631e703f929eaae56138a2acd3687121924
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226570"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Strategie do testowania kodu w usłudze Azure Functions

W tym artykule przedstawiono sposób tworzenia zautomatyzowanych testów dla usługi Azure Functions. 

Testowanie całego kodu jest zalecane, jednak mogą uzyskać najlepsze wyniki, zakończeniem funkcji Logic Apps, a następnie tworząc testy poza funkcją. Abstrakcyjność logiki natychmiast ogranicza wiersze kodu funkcji i umożliwia korzystanie z funkcji wyłącznie odpowiedzialną za wywoływanie innych klas lub moduły. Jednak w tym artykule pokazano, jak tworzyć zautomatyzowane testy dla protokołu HTTP i funkcji wyzwalanej przez czasomierz.

Zawartość występującego jest podzielona na dwie różne sekcje przeznaczone do różnych języków i środowisk. Nauczysz się tworzyć testy w:

- [C#w programie Visual Studio z xUnit](#c-in-visual-studio)
- [Język JavaScript w VS Code za pomocą](#javascript-in-vs-code)

Przykładowe repozytorium jest dostępne w witrynie [GitHub](https://github.com/Azure-Samples/azure-functions-tests).

## <a name="c-in-visual-studio"></a>C#w programie Visual Studio
W poniższym przykładzie opisano sposób tworzenia aplikacji C# funkcji w programie Visual Studio oraz uruchamiania i testowania przy użyciu [xUnit](https://xunit.github.io).

![Testowanie usługi Azure Functions z C# w programie Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Konfigurowanie

Aby skonfigurować środowisko, Utwórz funkcję i testowanie aplikacji. Poniższe kroki ułatwiają tworzenie aplikacji i funkcje wymagane do obsługi testów:

1. [Utwórz nową aplikację funkcji](./functions-create-first-azure-function.md) i nadaj jej nazwę *Functions*
2. [Utwórz funkcję http z szablonu](./functions-create-first-azure-function.md) i nadaj jej nazwę *HttpTrigger*.
3. [Utwórz funkcję timer na podstawie szablonu](./functions-create-scheduled-function.md) i nadaj jej nazwę *TimerTrigger*.
4. [Utwórz aplikację testową xUnit](https://xunit.github.io/docs/getting-started-dotnet-core) w programie Visual Studio, klikając pozycję **plik > nowy > project C# > Visual > .NET Core > xUnit test Project** i nazwij go *Functions. test*. 
5. Użyj narzędzia NuGet, aby dodać odwołania z aplikacji testowej [Microsoft. AspNetCore. MVC](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/)
6. [Odwołuje się do aplikacji *Functions* ](https://docs.microsoft.com/visualstudio/ide/managing-references-in-a-project?view=vs-2017) z poziomu aplikacji *Functions. test* .

### <a name="create-test-classes"></a>Tworzenie klas testowych

Teraz, gdy aplikacje są tworzone, można utworzyć klasy używane do uruchamiania testów automatycznych.

Każda funkcja przyjmuje wystąpienie [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) do obsługi rejestrowania komunikatów. Niektóre testy nie komunikaty dziennika albo mieć nie kwestią w przypadku implementacji rejestrowania. Czym trzeba ocenić komunikaty zarejestrowane w celu ustalenia, czy test jest przekazanie innych testów.

Klasa `ListLogger` ma zaimplementować interfejs `ILogger` i przechowywać w wewnętrznej liście komunikatów do oceny podczas testu.

**Kliknij prawym przyciskiem myszy** aplikację *Functions. test* i wybierz polecenie **Dodaj > Class**, nadaj jej nazwę **NullScope.cs** i wprowadź następujący kod:

```csharp
using System;

namespace Functions.Tests
{
    public class NullScope : IDisposable
    {
        public static NullScope Instance { get; } = new NullScope();

        private NullScope() { }

        public void Dispose() { }
    }
}
```

Następnie **kliknij prawym przyciskiem myszy** aplikację *Functions. test* , a następnie wybierz pozycję **Dodaj > Class**, nadaj jej nazwę **ListLogger.cs** i wprowadź następujący kod:

```csharp
using Microsoft.Extensions.Logging;
using System;
using System.Collections.Generic;
using System.Text;

namespace Functions.Tests
{
    public class ListLogger : ILogger
    {
        public IList<string> Logs;

        public IDisposable BeginScope<TState>(TState state) => NullScope.Instance;

        public bool IsEnabled(LogLevel logLevel) => false;

        public ListLogger()
        {
            this.Logs = new List<string>();
        }

        public void Log<TState>(LogLevel logLevel, 
                                EventId eventId,
                                TState state,
                                Exception exception,
                                Func<TState, Exception, string> formatter)
        {
            string message = formatter(state, exception);
            this.Logs.Add(message);
        }
    }
}
```

Klasa `ListLogger` implementuje następujących członków zgodnie z umową `ILogger` Interface:

- **BeginScope**: zakresy dodają kontekst do rejestrowania. W takim przypadku test tylko wskazuje na wystąpienie statyczne w klasie `NullScope`, aby umożliwić testowi działanie.

- **IsEnabled**: podano wartość domyślną `false`.

- **Dziennik**: Ta metoda używa podanej funkcji `formatter`, aby sformatować komunikat, a następnie dodać tekst otrzymany do kolekcji `Logs`.

Kolekcja `Logs` jest wystąpieniem `List<string>` i jest inicjowana w konstruktorze.

Następnie **kliknij prawym przyciskiem myszy** aplikację *Functions. test* , a następnie wybierz pozycję **Dodaj > Class**, nadaj jej nazwę **LoggerTypes.cs** i wprowadź następujący kod:

```csharp
namespace Functions.Tests
{
    public enum LoggerTypes
    {
        Null,
        List
    }
}
```
To wyliczenie Określa typ rejestratora, używany przez testy. 

Następnie **kliknij prawym przyciskiem myszy** aplikację *Functions. test* , a następnie wybierz pozycję **Dodaj > Class**, nadaj jej nazwę **TestFactory.cs** i wprowadź następujący kod:

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Http.Internal;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.Abstractions;
using Microsoft.Extensions.Primitives;
using System.Collections.Generic;

namespace Functions.Tests
{
    public class TestFactory
    {
        public static IEnumerable<object[]> Data()
        {
            return new List<object[]>
            {
                new object[] { "name", "Bill" },
                new object[] { "name", "Paul" },
                new object[] { "name", "Steve" }

            };
        }

        private static Dictionary<string, StringValues> CreateDictionary(string key, string value)
        {
            var qs = new Dictionary<string, StringValues>
            {
                { key, value }
            };
            return qs;
        }

        public static DefaultHttpRequest CreateHttpRequest(string queryStringKey, string queryStringValue)
        {
            var request = new DefaultHttpRequest(new DefaultHttpContext())
            {
                Query = new QueryCollection(CreateDictionary(queryStringKey, queryStringValue))
            };
            return request;
        }

        public static ILogger CreateLogger(LoggerTypes type = LoggerTypes.Null)
        {
            ILogger logger;

            if (type == LoggerTypes.List)
            {
                logger = new ListLogger();
            }
            else
            {
                logger = NullLoggerFactory.Instance.CreateLogger("Null Logger");
            }

            return logger;
        }
    }
}
```
Klasa `TestFactory` implementuje następujących członków:

- **Dane**: Ta właściwość zwraca kolekcję [IEnumerable](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable) przykładowych danych. Pary klucz-wartość reprezentują wartości, które są przekazywane do ciągu zapytania.

- **IsDictionary**: Ta metoda akceptuje parę klucz/wartość jako argumenty i zwraca nowy `Dictionary` używany do tworzenia `QueryCollection` do reprezentowania wartości ciągu zapytania.

- Zdarzenie **: Ta**Metoda tworzy żądanie HTTP zainicjowane przy użyciu podanym parametrów ciągu zapytania.

- **Wyrejestrowania**: na podstawie typu rejestratora ta metoda zwraca klasę rejestratora używaną do testowania. `ListLogger` śledzi zarejestrowane komunikaty dostępne do oceny w testach.

Następnie **kliknij prawym przyciskiem myszy** aplikację *Functions. test* , a następnie wybierz pozycję **Dodaj > Class**, nadaj jej nazwę **FunctionsTests.cs** i wprowadź następujący kod:

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using Xunit;

namespace Functions.Tests
{
    public class FunctionsTests
    {
        private readonly ILogger logger = TestFactory.CreateLogger();

        [Fact]
        public async void Http_trigger_should_return_known_string()
        {
            var request = TestFactory.CreateHttpRequest("name", "Bill");
            var response = (OkObjectResult)await HttpTrigger.Run(request, logger);
            Assert.Equal("Hello, Bill", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await HttpTrigger.Run(request, logger);
            Assert.Equal($"Hello, {queryStringValue}", response.Value);
        }

        [Fact]
        public void Timer_should_log_message()
        {
            var logger = (ListLogger)TestFactory.CreateLogger(LoggerTypes.List);
            TimerTrigger.Run(null, logger);
            var msg = logger.Logs[0];
            Assert.Contains("C# Timer trigger function executed at", msg);
        }
    }
}
```
Elementy członkowskie zaimplementowane w tej klasie są:

- **Http_trigger_should_return_known_string**: ten test tworzy żądanie z wartościami ciągu zapytania `name=Bill` do funkcji http i sprawdza, czy zwracana jest oczekiwana odpowiedź.

- **Http_trigger_should_return_string_from_member_data**: ten test używa atrybutów xUnit, aby zapewnić dane przykładowe do funkcji http.

- **Timer_should_log_message**: ten test tworzy wystąpienie `ListLogger` i przekazuje je do funkcji timer. Po jego uruchomieniu funkcji dziennika jest sprawdzany, aby upewnić się, że oczekiwany komunikat jest obecny.

Jeśli chcesz uzyskać dostęp do ustawień aplikacji w testach, możesz użyć metody [System. Environment. GetEnvironmentVariable](./functions-dotnet-class-library.md#environment-variables).

### <a name="run-tests"></a>Uruchom testy

Aby uruchomić testy, przejdź do **Eksploratora testów** i kliknij przycisk **Uruchom wszystkie**.

![Testowanie usługi Azure Functions z C# w programie Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Debuguj testy

Aby debugować testy, ustaw punkt przerwania w teście, przejdź do **Eksploratora testów** i kliknij przycisk **Uruchom > Debuguj ostatnie uruchomienie**.

## <a name="javascript-in-vs-code"></a>Język JavaScript w programie VS Code

W poniższym przykładzie opisano sposób tworzenia aplikacji funkcji JavaScript w VS Code i uruchamiania oraz [testów za pomocą](https://jestjs.io). Ta procedura powoduje utworzenie Azure Functions za pomocą [rozszerzenia funkcji vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) .

![Testowanie usługi Azure Functions, za pomocą języka JavaScript w programie VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Konfigurowanie

Aby skonfigurować środowisko, zainicjuj nową aplikację Node. js w pustym folderze, uruchamiając `npm init`.

```bash
npm init -y
```
Następnie zainstaluj Jest, uruchamiając następujące polecenie:

```bash
npm i jest
```
Teraz Zaktualizuj plik _Package. JSON_ , aby zamienić istniejące polecenie testowe na następujące polecenie:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Tworzenie modułów testu
Za pomocą projektu zainicjowany można utworzyć moduły używane do uruchamiania testów automatycznych. Zacznij od utworzenia nowego folderu o nazwie *test* w celu przechowywania modułów obsługi.

W folderze *testowym* Dodaj nowy plik, nadaj mu nazwę **kontekst domyślny. js**, a następnie Dodaj następujący kod:

```javascript
module.exports = {
    log: jest.fn()
};
```
Ten moduł służy do makietowania funkcji *log* w celu reprezentowania domyślnego kontekstu wykonania.

Następnie Dodaj nowy plik, nadaj mu nazwę **defaultTimer. js**, a następnie Dodaj następujący kod:

```javascript
module.exports = {
    IsPastDue: false
};
```

Ten moduł implementuje Właściwość `IsPastDue`, aby było to wystąpienie fałszywego czasomierza. Konfiguracje czasomierza, takie jak wyrażenia NCRONTAB, nie są wymagane w tym miejscu, ponieważ zespół testów ma po prostu wywołanie funkcji bezpośrednio w celu przetestowania wyniku.

Następnie użyj rozszerzenia funkcji VS Code, aby [utworzyć nową funkcję http języka JavaScript](/azure/javascript/tutorial-vscode-serverless-node-01) i nadaj jej nazwę *HttpTrigger*. Po utworzeniu funkcji Dodaj nowy plik do tego samego folderu o nazwie **index. test. js**i Dodaj następujący kod:

```javascript
const httpFunction = require('./index');
const context = require('../testing/defaultContext')

test('Http trigger should return known text', async () => {

    const request = {
        query: { name: 'Bill' }
    };

    await httpFunction(context, request);

    expect(context.log.mock.calls.length).toBe(1);
    expect(context.res.body).toEqual('Hello Bill');
});
```
Funkcja protokołu HTTP z szablonu zwraca ciąg "Hello" jest połączona z nazwa podana w ciągu zapytania. Ten test tworzy fałszywe wystąpienie żądanie i przekazuje je do funkcji HTTP. Test sprawdza, czy metoda *log* jest wywoływana jednokrotnie, a zwracany tekst jest równy "Hello Bill".

Następnie użyj rozszerzenia funkcji VS Code, aby utworzyć nową funkcję czasomierza JavaScript i nadaj jej nazwę *TimerTrigger*. Po utworzeniu funkcji Dodaj nowy plik do tego samego folderu o nazwie **index. test. js**i Dodaj następujący kod:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```
Funkcja czasomierza z szablonu rejestruje wiadomość na końcu treści funkcji. Ten test zapewnia jednokrotne wywołanie funkcji *log* .

### <a name="run-tests"></a>Uruchom testy
Aby uruchomić testy, naciśnij **klawisze CTRL + ~** , aby otworzyć okno wiersza polecenia, a następnie uruchom `npm test`:

```bash
npm test
```

![Testowanie usługi Azure Functions, za pomocą języka JavaScript w programie VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Debuguj testy

Aby debugować testy, Dodaj następującą konfigurację do pliku *Launch. JSON* :

```json
{
  "type": "node",
  "request": "launch",
  "name": "Jest Tests",
  "disableOptimisticBPs": true,
  "program": "${workspaceRoot}/node_modules/jest/bin/jest.js",
  "args": [
      "-i"
  ],
  "internalConsoleOptions": "openOnSessionStart"
}
```

Następnie ustaw punkt przerwania w teście i naciśnij klawisz **F5**.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wyjaśniono sposób pisania testów automatycznych dla funkcji, Kontynuuj przy użyciu tych zasobów:
- [Ręcznie uruchom funkcję niewyzwalaną przez protokół HTTP](./functions-manually-run-non-http.md)
- [Obsługa błędów Azure Functions](./functions-bindings-error-pages.md)
- [Debugowanie lokalnego wyzwalacza siatki zdarzeń funkcji platformy Azure](./functions-debug-event-grid-trigger-local.md)
