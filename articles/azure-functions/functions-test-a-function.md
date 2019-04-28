---
title: Testowanie usługi Azure Functions
description: Tworzenie zautomatyzowanych testów dla C# funkcji w programie Visual Studio i funkcji języka JavaScript w programie VS Code
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure funkcji, funkcje, przetwarzanie zdarzeń, elementy webhook, obliczanie dynamiczne, architektura bezserwerowa testowania
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: e0009e1c6380e02e2e0e24bf86e6dab435b6c022
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61021136"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Strategie do testowania kodu w usłudze Azure Functions

W tym artykule przedstawiono sposób tworzenia zautomatyzowanych testów dla usługi Azure Functions. 

Testowanie całego kodu jest zalecane, jednak mogą uzyskać najlepsze wyniki, zakończeniem funkcji Logic Apps, a następnie tworząc testy poza funkcją. Abstrakcyjność logiki natychmiast ogranicza wiersze kodu funkcji i umożliwia korzystanie z funkcji wyłącznie odpowiedzialną za wywoływanie innych klas lub moduły. Jednak w tym artykule pokazano, jak tworzyć zautomatyzowane testy dla protokołu HTTP i funkcji wyzwalanej przez czasomierz.

Zawartość występującego jest podzielona na dwie różne sekcje przeznaczone do różnych języków i środowisk. Nauczysz się tworzyć testy w:

- [C#w programie Visual Studio w narzędziu xUnit](#c-in-visual-studio)
- [Język JavaScript w programie VS Code z Jest](#javascript-in-vs-code)

Przykładowe repozytorium jest dostępna w [GitHub](https://github.com/Azure-Samples/azure-functions-tests).

## <a name="c-in-visual-studio"></a>C#w programie Visual Studio
Poniższy przykład zawiera opis sposobu tworzenia C# aplikacji w programie Visual Studio funkcji i Uruchamianie testów za pomocą [xUnit](https://xunit.github.io).

![Testowanie usługi Azure Functions z C# w programie Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Konfigurowanie

Aby skonfigurować środowisko, Utwórz funkcję i testowanie aplikacji. Poniższe kroki ułatwiają tworzenie aplikacji i funkcje wymagane do obsługi testów:

1. [Utwórz nową aplikację funkcji](./functions-create-first-azure-function.md) i nadaj mu nazwę *funkcji*
2. [Tworzenie funkcji przez protokół HTTP na podstawie szablonu](./functions-create-first-azure-function.md) i nadaj mu nazwę *HttpTrigger*.
3. [Tworzenie funkcji czasomierzem z szablonu](./functions-create-scheduled-function.md) i nadaj mu nazwę *TimerTrigger*.
4. [Tworzenie aplikacji testu xUnit](https://xunit.github.io/docs/getting-started-dotnet-core) w programie Visual Studio, klikając **Plik > Nowy > Projekt > Visual C# > platformy .NET Core > Projekt testu xUnit** i nadaj mu nazwę *Functions.Test*. 
5. Użyć Nuget, aby dodać odwołań z aplikacja testowa [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/)
6. [Odwołanie *funkcje* aplikacji](https://docs.microsoft.com/visualstudio/ide/managing-references-in-a-project?view=vs-2017) z *Functions.Test* aplikacji.

### <a name="create-test-classes"></a>Tworzenie klas testowych

Teraz, gdy aplikacje są tworzone, można utworzyć klasy używane do uruchamiania testów automatycznych.

Każda funkcja przyjmuje wystąpienie klasy [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) do obsługi rejestrowania komunikatów. Niektóre testy nie komunikaty dziennika albo mieć nie kwestią w przypadku implementacji rejestrowania. Czym trzeba ocenić komunikaty zarejestrowane w celu ustalenia, czy test jest przekazanie innych testów.

`ListLogger` Klasa jest przeznaczona do implementowania `ILogger` interfejsu i przytrzymaj wewnętrzną listę komunikatów w wersji ewaluacyjnej podczas testu.

**Kliknij prawym przyciskiem myszy** na *Functions.Test* aplikacji i wybierz **Dodaj > klasa**, nadaj jej nazwę **NullScope.cs** i wprowadź następujący kod:

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

Następnie **kliknij prawym przyciskiem myszy** na *Functions.Test* aplikacji i wybierz **Dodaj > klasa**, nadaj jej nazwę **ListLogger.cs** i wprowadź Poniższy kod:

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

`ListLogger` Klasa implementuje następujących elementów członkowskich, ponieważ zawarła umowę `ILogger` interfejsu:

- **BeginScope**: Zakresy dodać kontekst do Twojej rejestracji. W tym przypadku testu po prostu wskazuje na wystąpienie statyczne na `NullScope` klasy w celu umożliwienia testu do funkcji.

- **IsEnabled**: Wartość domyślna `false` podano.

- **Dziennik**: Ta metoda wykorzystuje podane `formatter` funkcji wiadomość w formacie, a następnie dodaje tekst wynikowy `Logs` kolekcji.

`Logs` Kolekcji jest wystąpieniem `List<string>` i jest inicjowana w konstruktorze.

Następnie **kliknij prawym przyciskiem myszy** na *Functions.Test* aplikacji i wybierz **Dodaj > klasa**, nadaj jej nazwę **LoggerTypes.cs** i wprowadź Poniższy kod:

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

Następnie **kliknij prawym przyciskiem myszy** na *Functions.Test* aplikacji i wybierz **Dodaj > klasa**, nadaj jej nazwę **TestFactory.cs** i wprowadź Poniższy kod:

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
`TestFactory` Klasa implementuje następujące elementy członkowskie:

- **Dane**: Ta właściwość zwraca [IEnumerable](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable) kolekcję przykładowych danych. Pary klucz-wartość reprezentują wartości, które są przekazywane do ciągu zapytania.

- **Createdictionary —**: Ta metoda przyjmuje pary klucz/wartość jako argumenty i zwraca nowy `Dictionary` użyty do utworzenia `QueryCollection` do reprezentowania wartości ciągu zapytania.

- **CreateHttpRequest**: Ta metoda tworzy zainicjować za pomocą parametrów ciągu zapytania danego żądania HTTP.

- **CreateLogger**: Na podstawie rejestratora typu, Metoda ta zwraca klasę rejestratora, używany do testowania. `ListLogger` Śledzi informacje o zarejestrowanych komunikatów dostępnych do oceny w testach.

Następnie **kliknij prawym przyciskiem myszy** na *Functions.Test* aplikacji i wybierz **Dodaj > klasa**, nadaj jej nazwę **FunctionsTests.cs** i wprowadź Poniższy kod:

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

- **Http_trigger_should_return_known_string**: Ten test tworzy żądanie z zapytaniem wartości ciągu `name=Bill` funkcji przez protokół HTTP i sprawdza, czy zwracana jest oczekiwana odpowiedź.

- **Http_trigger_should_return_string_from_member_data**: Ten test używa atrybutów xUnit, aby zapewnić przykładowych danych, aby funkcja protokołu HTTP.

- **Timer_should_log_message**: Ten test tworzy wystąpienie `ListLogger` i przekazuje je do funkcji czasomierza. Po jego uruchomieniu funkcji dziennika jest sprawdzany, aby upewnić się, że oczekiwany komunikat jest obecny.

Jeśli chcesz uzyskać dostęp ustawienia aplikacji w testach, możesz użyć [o nazwie System.Environment.GetEnvironmentVariable](./functions-dotnet-class-library.md#environment-variables).

### <a name="run-tests"></a>Uruchom testy

Aby uruchomić testy, przejdź do **Eksplorator testów** i kliknij przycisk **uruchomić wszystkie**.

![Testowanie usługi Azure Functions z C# w programie Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Debuguj testy

Debuguj testy, ustaw punkt przerwania dla testu, przejdź do **Eksploratora testów** i kliknij przycisk **Uruchom > debugowania Uruchom ostatniego**.

## <a name="javascript-in-vs-code"></a>Język JavaScript w programie VS Code

W poniższym przykładzie opisano, jak utworzyć aplikację funkcji języka JavaScript w programie VS Code i uruchomić i testów za pomocą [Jest](https://jestjs.io). Ta procedura wykorzystuje [rozszerzenie programu VS Code funkcje](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) tworzenia funkcji platformy Azure.

![Testowanie usługi Azure Functions, za pomocą języka JavaScript w programie VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Konfigurowanie

Konfigurowanie środowiska, należy zainicjować przez uruchomienie nowej aplikacji Node.js w pustym folderze `npm init`.

```bash
npm init -y
```
Następnie zainstaluj Jest, uruchamiając następujące polecenie:

```bash
npm i jest
```
Teraz zaktualizować _package.json_ zastąpić istniejące polecenia testowania za pomocą następującego polecenia:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Tworzenie modułów testu
Za pomocą projektu zainicjowany można utworzyć moduły używane do uruchamiania testów automatycznych. Rozpocznij od utworzenia nowy folder o nazwie *testowania* do przechowywania modułów pomocy technicznej.

W *testowania* folder Dodaj nowy plik i nadaj mu nazwę **defaultContext.js**i Dodaj następujący kod:

```javascript
module.exports = {
    log: jest.fn()
};
```
Ten moduł mocks *dziennika* funkcji do reprezentowania domyślny kontekst wykonywania.

Następnie dodaj nowy plik, nadaj jej nazwę **defaultTimer.js**i Dodaj następujący kod:

```javascript
module.exports = {
    IsPastDue: false
};
```
Ten moduł stanowi wdrożenie `IsPastDue` właściwości do autonomicznych jest jako wystąpienie fałszywych czasomierza.

Następnie należy użyć rozszerzenia programu VS Code funkcje [Tworzenie nowej funkcji języka JavaScript HTTP](https://code.visualstudio.com/tutorials/functions-extension/getting-started) i nadaj mu nazwę *HttpTrigger*. Po utworzeniu funkcji należy dodać nowy plik w tym samym folderze o nazwie **index.test.js**i Dodaj następujący kod:

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
Funkcja protokołu HTTP z szablonu zwraca ciąg "Hello" jest połączona z nazwa podana w ciągu zapytania. Ten test tworzy fałszywe wystąpienie żądanie i przekazuje je do funkcji HTTP. Test sprawdza, czy *dziennika* metoda jest wywoływana jeden raz i zwracanym tekście jest równe "Hello Bill".

Następnie użyj rozszerzenia programu VS Code funkcji, aby utworzyć nową funkcję JavaScript Czasomierz i nadaj mu *TimerTrigger*. Po utworzeniu funkcji należy dodać nowy plik w tym samym folderze o nazwie **index.test.js**i Dodaj następujący kod:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```
Funkcja czasomierza z szablonu rejestruje wiadomość na końcu treści funkcji. Ten test zapewnia *dziennika* funkcja jest wywoływana jeden raz.

### <a name="run-tests"></a>Uruchom testy
Aby uruchomić testy, naciśnij klawisz **CTRL + ~** Otwórz okno polecenia i uruchom `npm test`:

```bash
npm test
```

![Testowanie usługi Azure Functions, za pomocą języka JavaScript w programie VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Debuguj testy

Aby debugować testy, dodaj następującą konfigurację do Twojej *launch.json* pliku:

```json
{
  "type": "node",
  "request": "launch",
  "name": "Jest Tests",
  "program": "${workspaceRoot}\\node_modules\\jest\\bin\\jest.js",
  "args": [
      "-i"
  ],
  "internalConsoleOptions": "openOnSessionStart"
}
```

Następnie ustaw punkt przerwania w testów i naciśnij klawisz **F5**.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wyjaśniono sposób pisania testów automatycznych dla funkcji, Kontynuuj przy użyciu tych zasobów:
- [Ręcznie uruchomić bez funkcji wyzwalanej przez HTTP](./functions-manually-run-non-http.md)
- [Obsługa błędów w usłudze Azure Functions](./functions-bindings-error-pages.md)
- [Debugowanie funkcji platformy Azure zdarzeń siatki wyzwalacza lokalnego](./functions-debug-event-grid-trigger-local.md)
