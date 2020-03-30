---
title: Testowanie usługi Azure Functions
description: Tworzenie zautomatyzowanych testów dla funkcji języka C# w programie Visual Studio i funkcji JavaScript w programie VS Code
author: craigshoemaker
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: a37fd886e1bc70226b2e54750540dfcb79ee5973
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768881"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Strategie testowania kodu w usłudze Azure Functions

W tym artykule pokazano, jak utworzyć zautomatyzowane testy dla usługi Azure Functions. 

Testowanie całego kodu jest zalecane, jednak można uzyskać najlepsze wyniki przez zawijania logiki funkcji i tworzenia testów poza function. Logika abstrakcji od ogranicza function's wiersze kodu i pozwala Function być wyłącznie odpowiedzialny za wywoływanie innych klas lub modułów. W tym artykule pokazano jednak, jak utworzyć zautomatyzowane testy dla funkcji http i czasomierza wyzwalane.

Następująca zawartość jest podzielona na dwie różne sekcje przeznaczone do kierowania różnych języków i środowisk. Możesz nauczyć się budować testy w:

- [C# w programie Visual Studio z xUnit](#c-in-visual-studio)
- [JavaScript w programie VS Code z programem Jest](#javascript-in-vs-code)

Przykładowe repozytorium jest dostępne w [usłudze GitHub.](https://github.com/Azure-Samples/azure-functions-tests)

## <a name="c-in-visual-studio"></a>Język C# w programie Visual Studio
W poniższym przykładzie opisano sposób tworzenia aplikacji funkcji języka C# w programie Visual Studio i uruchamiania i testów za pomocą [jednostki xUnit.](https://xunit.github.io)

![Testowanie funkcji platformy Azure za pomocą języka C# w programie Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Konfiguracja

Aby skonfigurować środowisko, utwórz aplikację funkcji i testuj. Poniższe kroki ułatwiztwochowinie aplikacji i funkcji wymaganych do obsługi testów:

1. [Tworzenie nowej aplikacji Functions](./functions-create-first-azure-function.md) i nadawanie jej nazwy *Funkcje*
2. [Utwórz funkcję HTTP na podstawie szablonu](./functions-create-first-azure-function.md) i nazwij ją *HttpTrigger*.
3. [Utwórz funkcję czasomierza na podstawie szablonu](./functions-create-scheduled-function.md) i nazwij ją *TimerTrigger*.
4. [Utwórz aplikację testu xUnit](https://xunit.github.io/docs/getting-started-dotnet-core) w programie Visual Studio, klikając **polecenie File > New > Project > Visual C# > .NET Core > xUnit Test Project** i nazwij go *Functions.Test*. 
5. Użyj NuGet, aby dodać odwołanie z aplikacji testowej do [pliku Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/)
6. [Odwołaj się do aplikacji *Functions* ](https://docs.microsoft.com/visualstudio/ide/managing-references-in-a-project?view=vs-2017) z aplikacji *Functions.Test.*

### <a name="create-test-classes"></a>Tworzenie klas testowych

Teraz, gdy aplikacje są tworzone, można utworzyć klasy używane do uruchamiania testów automatycznych.

Każda funkcja przyjmuje [wystąpienie ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) do obsługi rejestrowania wiadomości. Niektóre testy albo nie rejestrują komunikatów lub nie dotyczą sposobu implementowania rejestrowania. Inne testy należy ocenić wiadomości rejestrowane, aby ustalić, czy test jest przekazywanie.

Klasa `ListLogger` implementuje `ILogger` interfejs i przechowuje wewnętrzną listę komunikatów do oceny podczas testu.

**Kliknij prawym przyciskiem myszy** aplikację *Functions.Test* i wybierz **pozycję Dodaj klasę >**, nazwij ją **NullScope.cs** i wprowadź następujący kod:

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

Następnie **kliknij prawym przyciskiem myszy** aplikację *Functions.Test* i wybierz **pozycję Dodaj klasę >**, nazwij ją **ListLogger.cs** i wprowadź następujący kod:

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

Klasa `ListLogger` implementuje następujące elementy członkowskie zgodnie `ILogger` z zakontraktowanym przez interfejs:

- **BeginScope:** Zakresy dodać kontekst do rejestrowania. W takim przypadku test tylko wskazuje na statyczne wystąpienie w `NullScope` klasie, aby umożliwić działanie testu.

- **IsEnabled**: Podana `false` jest wartość domyślna.

- **Dziennik:** Ta metoda używa `formatter` podanej funkcji do formatowania wiadomości, `Logs` a następnie dodaje wynikowy tekst do kolekcji.

Kolekcja `Logs` jest wystąpieniem `List<string>` i jest inicjowany w konstruktorze.

Następnie **kliknij prawym przyciskiem myszy** aplikację *Functions.Test* i wybierz **pozycję Dodaj klasę >**, nazwij ją **LoggerTypes.cs** i wprowadź następujący kod:

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
To wyliczenie określa typ rejestratora używanego przez testy. 

Następnie **kliknij prawym przyciskiem myszy** aplikację *Functions.Test* i wybierz **pozycję Dodaj klasę >**, nazwij ją **TestFactory.cs** i wprowadź następujący kod:

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
Klasa `TestFactory` implementuje następujące elementy członkowskie:

- **Dane:** Ta właściwość zwraca [iEnumerable](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable) kolekcji przykładowych danych. Pary wartości klucza reprezentują wartości, które są przekazywane do ciągu zapytania.

- **CreateDictionary**: Ta metoda akceptuje parę klucz/wartość jako `Dictionary` argumenty `QueryCollection` i zwraca nowy używany do tworzenia do reprezentowania wartości ciągu kwerendy.

- **UtwórzHttpRequest:** Ta metoda tworzy żądanie HTTP zainicjowane z podanymi parametrami ciągu zapytania.

- **CreateLogger**: Na podstawie typu rejestratora ta metoda zwraca klasę rejestratora używaną do testowania. Śledzi `ListLogger` rejestrowane komunikaty dostępne do oceny w testach.

Następnie **kliknij prawym przyciskiem myszy** aplikację *Functions.Test* i wybierz **pozycję Dodaj klasę >**, nazwij ją **FunctionsTests.cs** i wprowadź następujący kod:

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
Członkami zaimplementowanymi w tej klasie są:

- **Http_trigger_should_return_known_string:** Ten test tworzy żądanie z wartościami `name=Bill` ciągu zapytania do funkcji HTTP i sprawdza, czy zwracana jest oczekiwana odpowiedź.

- **Http_trigger_should_return_string_from_member_data:** Ten test używa atrybutów xUnit do dostarczania przykładowych danych do funkcji HTTP.

- **Timer_should_log_message:** Ten test tworzy wystąpienie `ListLogger` i przekazuje je do funkcji czasomierza. Po uruchomieniu funkcji, a następnie dziennik jest sprawdzany, aby upewnić się, że oczekiwany komunikat jest obecny.

Jeśli chcesz uzyskać dostęp do ustawień aplikacji w testach, możesz użyć [System.Environment.GetEnvironmentVariable](./functions-dotnet-class-library.md#environment-variables).

### <a name="run-tests"></a>Uruchom testy

Aby uruchomić testy, przejdź do **Eksploratora testów** i kliknij przycisk **Uruchom wszystkie**.

![Testowanie funkcji platformy Azure za pomocą języka C# w programie Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Testy debugowania

Aby debugować testy, ustaw punkt przerwania w teście, przejdź do **Eksploratora testów** i kliknij przycisk **Uruchom > debugowania ostatniego uruchomienia**.

## <a name="javascript-in-vs-code"></a>JavaScript w kodzie VS

W poniższym przykładzie opisano sposób tworzenia aplikacji Funkcji JavaScript w programie VS Code i uruchamiania i testów z [programem Jest](https://jestjs.io). Ta procedura używa [rozszerzenia funkcji kodu VS](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) do tworzenia usług Azure Functions.

![Testowanie funkcji platformy Azure za pomocą języka JavaScript w programie VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Konfiguracja

Aby skonfigurować środowisko, zaiwastuj nową aplikację Node.js w pustym folderze, uruchamiając go. `npm init`

```bash
npm init -y
```
Następnie zainstaluj program Jest, uruchamiając następujące polecenie:

```bash
npm i jest
```
Teraz zaktualizuj _plik package.json,_ aby zastąpić istniejące polecenie testu następującym poleceniem:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Tworzenie modułów testowych
Po zainicjowaniu projektu można utworzyć moduły używane do uruchamiania testów automatycznych. Rozpocznij od utworzenia nowego folderu o nazwie *testowanie* do przechowywania modułów pomocy technicznej.

W folderze *testowym* dodaj nowy plik, **nazwij go defaultContext.js**i dodaj następujący kod:

```javascript
module.exports = {
    log: jest.fn()
};
```
Ten moduł kpi z funkcji *dziennika* do reprezentowania domyślnego kontekstu wykonywania.

Następnie dodaj nowy plik, **nazwij go defaultTimer.js**i dodaj następujący kod:

```javascript
module.exports = {
    IsPastDue: false
};
```

Ten moduł implementuje `IsPastDue` właściwość stanąć jest jako fałszywe wystąpienie czasomierza. Konfiguracje czasomierza, takie jak wyrażenia NCRONTAB, nie są tutaj wymagane, ponieważ uprząż testowa po prostu wywołuje funkcję bezpośrednio w celu przetestowania wyniku.

Następnie użyj rozszerzenia VS Code Functions, aby [utworzyć nową funkcję HTTP JavaScript](/azure/javascript/tutorial-vscode-serverless-node-01) i nazwać ją *HttpTrigger*. Po utworzeniu funkcji dodaj nowy plik w tym samym folderze o nazwie **index.test.js**i dodaj następujący kod:

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
Funkcja HTTP z szablonu zwraca ciąg "Hello" połączony z nazwą podana w ciągu zapytania. Ten test tworzy fałszywe wystąpienie żądania i przekazuje je do funkcji HTTP. Test sprawdza, czy metoda *dziennika* jest wywoływana raz, a zwrócony tekst jest równy "Hello Bill".

Następnie użyj rozszerzenia VS Code Functions, aby utworzyć nową funkcję timera JavaScript i nazwać ją *TimerTrigger*. Po utworzeniu funkcji dodaj nowy plik w tym samym folderze o nazwie **index.test.js**i dodaj następujący kod:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```
Funkcja czasomierza z szablonu rejestruje komunikat na końcu treści funkcji. Ten test zapewnia, że funkcja *dziennika* jest wywoływana raz.

### <a name="run-tests"></a>Uruchom testy
Aby uruchomić testy, naciśnij **klawisze CTRL + ~,** aby otworzyć okno polecenia, i uruchom `npm test`:

```bash
npm test
```

![Testowanie funkcji platformy Azure za pomocą języka JavaScript w programie VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Testy debugowania

Aby debugować testy, dodaj następującą konfigurację do pliku *launch.json:*

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

Następnie ustaw punkt przerwania w teście i naciśnij **klawisz F5**.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak pisać automatyczne testy dla swoich funkcji, kontynuuj z tymi zasobami:
- [Ręczne uruchamianie funkcji niewyzwalanej przez protokół HTTP](./functions-manually-run-non-http.md)
- [Obsługa błędów usług Azure Functions](./functions-bindings-error-pages.md)
- [Debugowanie lokalnego wyzwalacza siatki zdarzeń funkcji platformy Azure](./functions-debug-event-grid-trigger-local.md)
