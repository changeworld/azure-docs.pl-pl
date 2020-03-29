---
title: Debugowanie usługi Azure Functions w programie PowerShell lokalnie
description: Dowiedz się, jak tworzyć funkcje przy użyciu programu PowerShell.
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 133e89bd9187ae5e48fa208b407678760d31adfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163764"
---
# <a name="debug-powershell-azure-functions-locally"></a>Debugowanie usługi Azure Functions w programie PowerShell lokalnie

Usługa Azure Functions umożliwia tworzenie funkcji jako skryptów programu PowerShell.

Funkcje programu PowerShell można debugować lokalnie, tak jak w przypadku skryptów programu PowerShell przy użyciu następujących standardowych narzędzi programistycznych:

* [Visual Studio Code:](https://code.visualstudio.com/)Bezpłatny, lekki i otwarty edytor tekstu firmy Microsoft z rozszerzeniem programu PowerShell, które oferuje pełne środowisko programistyczne programu PowerShell.
* Konsola programu PowerShell: Debugowanie przy użyciu tych samych poleceń, które służy do debugowania innego procesu programu PowerShell.

[Narzędzia Podstawowe usługi Azure Functions core](functions-run-local.md) obsługuje lokalne debugowanie funkcji platformy Azure, w tym funkcje programu PowerShell.

## <a name="example-function-app"></a>Przykładowa aplikacja funkcji

Aplikacja funkcji używana w tym artykule ma jedną funkcję wyzwalaną HTTP i ma następujące pliki:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Ta aplikacja funkcji jest podobna do tej, którą otrzymujesz po zakończeniu [programu Szybki start programu PowerShell](functions-create-first-function-powershell.md).

Kod funkcji `run.ps1` w wygląda następująco:

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>Ustawianie punktu dołączania

Aby debugować dowolną funkcję programu PowerShell, funkcja musi zostać zatrzymana, aby debuger został dołączony. Polecenie `Wait-Debugger` cmdlet zatrzymuje wykonywanie i czeka na debugera.

Wszystko, co musisz zrobić, to `Wait-Debugger` dodać połączenie do `if` polecenia cmdlet tuż nad instrukcją, w następujący sposób:

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

Debugowanie rozpoczyna się `if` od instrukcji. 

Z `Wait-Debugger` w miejscu, można teraz debugować funkcje przy użyciu programu Visual Studio Code lub konsoli programu PowerShell.

## <a name="debug-in-visual-studio-code"></a>Debugowanie w programie Visual Studio Code

Aby debugować funkcje programu PowerShell w programie Visual Studio Code, musisz mieć zainstalowane następujące elementy:

* [Rozszerzenie programu PowerShell dla programu Visual Studio Code](/powershell/scripting/components/vscode/using-vscode)
* [Rozszerzenie usługi Azure Functions dla programu Visual Studio Code](functions-create-first-function-vs-code.md)
* [Program PowerShell Core 6.2 lub nowszy](/powershell/scripting/install/installing-powershell-core-on-windows)

Po zainstalowaniu tych zależności załaduj istniejący projekt programu PowerShell Functions lub [utwórz pierwszy projekt programu PowerShell Functions.](functions-create-first-function-powershell.md)

>[!NOTE]
> Jeśli projekt nie ma potrzebnych plików konfiguracyjnych, zostanie wyświetlony monit o ich dodanie.

### <a name="set-the-powershell-version"></a>Ustawianie wersji programu PowerShell

Program PowerShell Core instaluje się obok siebie za pomocą programu Windows PowerShell. Ustaw program PowerShell Core jako wersję programu PowerShell do użycia z rozszerzeniem programu PowerShell dla programu Visual Studio Code.

1. Naciśnij klawisz F1, aby wyświetlić `Session`paletę poleceń, a następnie wyszukaj .

1. Wybierz pozycję **PowerShell: Pokaż menu sesji**.

1. Jeśli **bieżącą sesją** nie jest **program PowerShell Core 6,** wybierz opcję **Przełącz na: PowerShell Core 6**.

Po otwarciu pliku programu PowerShell w prawym dolnym rogu okna zostanie wyświetlona wersja wyświetlana na zielono. Wybranie tego tekstu powoduje również wyświetlenie menu sesji. Aby dowiedzieć się więcej, zobacz [Wybieranie wersji programu PowerShell do użycia z rozszerzeniem](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension).

### <a name="start-the-function-app"></a>Uruchamianie aplikacji funkcji

Sprawdź, `Wait-Debugger` czy jest ustawiona w funkcji, w której chcesz dołączyć debuger.  Po `Wait-Debugger` dodaniu można debugować aplikację funkcji przy użyciu programu Visual Studio Code.

Wybierz okienko **debugowania,** a następnie **funkcję Dołącz do programu PowerShell**.

![Debuger](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

Można również nacisnąć klawisz F5, aby rozpocząć debugowanie.

Uruchom debugowanie operacji wykonuje następujące zadania:

* Uruchamia `func extensions install` się w terminalu, aby zainstalować wszystkie rozszerzenia usługi Azure Functions wymagane przez aplikację funkcji.
* Uruchamia `func host start` się w terminalu, aby uruchomić aplikację funkcji na hoście Functions.
* Dołącz debuger programu PowerShell do obszaru wykonawczego programu PowerShell w środowisku wykonywania Functions.

>[!NOTE]
> Należy upewnić się, PSWorkerInProcConcurrencyUpperBound jest ustawiona na 1, aby zapewnić poprawne środowisko debugowania w programie Visual Studio Code. Domyślnie włączone.

Po uruchomieniu aplikacji funkcji potrzebna jest oddzielna konsola programu PowerShell, aby wywołać funkcję wyzwalaną http.

W takim przypadku konsola programu PowerShell jest klientem. Jest `Invoke-RestMethod` używany do wyzwalania funkcji.

W konsoli programu PowerShell uruchom następujące polecenie:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Zauważysz, że odpowiedź nie jest natychmiast zwracana. To dlatego, `Wait-Debugger` że ma dołączone debugera i wykonanie programu PowerShell przeszedł w tryb przerwania tak szybko, jak to możliwe. Wynika to z [koncepcji BreakAll](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), która jest wyjaśniona później. Po naciśnięciu `continue` przycisku debuger teraz pęknie na linii zaraz po `Wait-Debugger`.

W tym momencie debuger jest dołączony i można wykonać wszystkie normalne operacje debugera. Aby uzyskać więcej informacji na temat korzystania z debugera w programie Visual Studio Code, zobacz [oficjalną dokumentację](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Po kontynuowaniu i pełnym wywołaniu skryptu zauważysz, że:

* Konsola programu PowerShell, która `Invoke-RestMethod` wykonała ten program, zwróciła wynik
* Zintegrowana konsola programu PowerShell w programie Visual Studio Code oczekuje na wykonanie skryptu

Później po wywołaniu tej samej funkcji debuger w rozszerzeniu programu PowerShell przerywa prawo po `Wait-Debugger`.

## <a name="debugging-in-a-powershell-console"></a>Debugowanie w konsoli programu PowerShell

>[!NOTE]
> W tej sekcji przyjęto założenie, że przeczytałeś dokumenty podstawowe `func host start` narzędzia azure [functions](functions-run-local.md) i wiesz, jak użyć polecenia do uruchomienia aplikacji funkcji.

Otwórz konsolę `cd` w katalogu aplikacji funkcyjnej i uruchom następujące polecenie:

```sh
func host start
```

Po uruchomieniu aplikacji funkcji `Wait-Debugger` i miejscu, można dołączyć do procesu. Potrzebujesz jeszcze dwóch konsol Programu PowerShell.

Jedna z konsol działa jako klient. Z tego wywołać, `Invoke-RestMethod` aby wyzwolić funkcję. Na przykład można uruchomić następujące polecenie:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Zauważysz, że nie zwraca odpowiedzi, co jest wynikiem `Wait-Debugger`. Obszar runshell programu PowerShell oczekuje teraz na dołączenie debugera. Załączmy to.

W innej konsoli programu PowerShell uruchom następujące polecenie:

```powershell
Get-PSHostProcessInfo
```

To polecenie cmdlet zwraca tabelę, która wygląda jak następujące dane wyjściowe:

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

Zanotuj `ProcessId` element w tabeli `ProcessName` z `dotnet`jako . Ten proces jest aplikacją funkcji.

Następnie uruchom następujący fragment kodu:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Po uruchomieniu debuger przerywa i pokazuje coś podobnego do następującego wyjścia:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

W tym momencie zostaniesz zatrzymany w punkcie przerwania w [debugerze programu PowerShell](/powershell/module/microsoft.powershell.core/about/about_debuggers). W tym miejscu można wykonać wszystkie operacje debugowania zwykle, krok nad, krok do, kontynuować, zamknąć i inne. Aby wyświetlić pełny zestaw poleceń debugowania dostępnych `h` `?` w konsoli, uruchom polecenia lub polecenia.

Można również ustawić punkty przerwania na `Set-PSBreakpoint` tym poziomie za pomocą polecenia cmdlet.

Po kontynuowaniu i pełnym wywołaniu skryptu zauważysz, że:

* Konsola programu PowerShell, w której wykonano, `Invoke-RestMethod` została zwrócona.
* Konsola programu PowerShell, w której wykonano wykonanie, `Debug-Runspace` oczekuje na wykonanie skryptu.

Można wywołać tę samą `Invoke-RestMethod` funkcję ponownie (przy użyciu na przykład) `Wait-Debugger` i debuger przerwy w prawo po poleceniu.

## <a name="considerations-for-debugging"></a>Zagadnienia dotyczące debugowania

Należy pamiętać o następujących problemach podczas debugowania kodu funkcji.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll`może spowodować, że debuger pęknie w nieoczekiwanym miejscu

Rozszerzenie programu PowerShell `Debug-Runspace`używa , co z kolei `BreakAll` opiera się na funkcji programu PowerShell. Ta funkcja informuje program PowerShell, aby zatrzymać przy pierwszym poleceniem, które jest wykonywane. To zachowanie daje możliwość ustawiania punktów przerwania w debugowanym obszarze runspace.

Środowisko uruchomieniowe usługi Azure Functions uruchamia kilka `run.ps1` poleceń przed faktycznym wywołaniem skryptu, więc `Microsoft.Azure.Functions.PowerShellWorker.psm1` `Microsoft.Azure.Functions.PowerShellWorker.psd1`możliwe, że debuger kończy się podziałem w obrębie lub .

Jeśli ta przerwa się `continue` `c` zdarzyć, uruchom lub polecenia, aby pominąć ten punkt przerwania. Następnie zatrzymać się w oczekiwanym punkcie przerwania.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat tworzenia funkcji przy użyciu programu PowerShell, zobacz [Przewodnik dla deweloperów programu Azure Functions PowerShell.](functions-reference-powershell.md)
