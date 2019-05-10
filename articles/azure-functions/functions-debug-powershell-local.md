---
title: Debugowanie programu PowerShell usługi Azure Functions lokalnie
description: Dowiedz się, jak tworzenie funkcji przy użyciu programu PowerShell.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha, glenga
ms.openlocfilehash: b699379448863c8df84fda0e059fc10846c09931
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230080"
---
# <a name="debug-powershell-azure-functions-locally"></a>Debugowanie programu PowerShell usługi Azure Functions lokalnie

Usługa Azure Functions umożliwia opracowywanie funkcji jako skrypty programu PowerShell.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Można debugować funkcje programu PowerShell lokalnie, tak jak wszystkie skrypty programu PowerShell przy użyciu następujących narzędzi tworzenia w standardzie:

* [Visual Studio Code](https://code.visualstudio.com/): Edytor tekstu bezpłatna, uproszczone i typu open source firmy Microsoft z rozszerzeniem programu PowerShell, które zapewnia pełne środowisko programistyczne środowiska PowerShell.
* Konsolę programu PowerShell: Debugowanie przy użyciu tych samych poleceń, które są używane do debugowania jakiś inny proces programu PowerShell.

[Podstawowe narzędzia usługi Azure Functions](functions-run-local.md) obsługuje lokalne debugowanie funkcji platformy Azure, w tym funkcje środowiska PowerShell.

## <a name="example-function-app"></a>Przykładowa aplikacja — funkcja

Aplikacja funkcji używane w tym artykule zawiera pojedynczą funkcję wyzwalaną przez protokół HTTP i ma następujące pliki:

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

Ta aplikacja funkcji jest podobny do tego, który pojawia się po wykonaniu [szybkiego startu programu PowerShell](functions-create-first-function-powershell.md).

Kod funkcji w `run.ps1` wygląda podobnie do następującego skryptu:

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

## <a name="set-the-attach-point"></a>Ustaw punkt dołączania

Aby debugować dowolnej funkcji programu PowerShell, funkcja wymaga zatrzymania można dołączyć debuger. `Wait-Debugger` Zatrzymuje wykonywanie polecenia cmdlet i czeka na debuger.

To wszystko, czego potrzebujesz, aby zrobić, dodaj wywołanie do `Wait-Debugger` polecenia cmdlet tuż nad `if` instrukcji, w następujący sposób:

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

Debugowanie rozpoczyna się od `if` instrukcji. 

Za pomocą `Wait-Debugger` w miejscu, można teraz debugować funkcje przy użyciu programu Visual Studio Code lub konsolę programu PowerShell.

## <a name="debug-in-visual-studio-code"></a>Debugowanie w programie Visual Studio Code

Aby debugować funkcje programu PowerShell w programie Visual Studio Code, konieczne jest posiadanie następujących rozszerzeń programu Visual Studio Code:

* [Program PowerShell](/powershell/scripting/components/vscode/using-vscode)
* [Azure Functions](functions-create-first-function-vs-code.md)

Po zainstalowaniu rozszerzenia programu PowerShell i usługi Azure Functions, Załaduj istniejący projekt aplikacji funkcji. Możesz również [Tworzenie projektu funkcji](functions-create-first-function-vs-code.md).

>[!NOTE]
> Projekt nie ma plików wymaganych konfiguracji, monit je dodać.

### <a name="start-the-function-app"></a>Uruchamiają aplikację — funkcja

Upewnij się, że `Wait-Debugger` jest ustawiona w funkcji, które chcesz dołączyć debuger.  Za pomocą `Wait-Debugger` dodane, można debugować aplikację funkcji przy użyciu programu Visual Studio Code.

Wybierz **debugowania** okienka i następnie **dołączyć do programu PowerShell funkcji**.

![Debuger](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

Można również nacisnąć klawisz F5, aby rozpocząć debugowanie.

Start debugging operacja wykonuje następujące zadania:

* Przebiegi `func extensions install` w terminalu, aby zainstalować wszystkie rozszerzenia usługi Azure Functions, wymagane przez aplikację funkcji.
* Przebiegi `func host start` w terminalu, aby uruchomić aplikację funkcji na hoście funkcji.
* Dołącz debuger środowiska PowerShell do obszaru działania programu PowerShell w ramach środowisko uruchomieniowe usługi Functions.

Uruchamianie aplikacji funkcji potrzebne konsolę programu PowerShell w oddzielnych, aby wywołać funkcję wyzwalaną przez protokół HTTP.

W tym przypadku konsoli programu PowerShell jest klient. `Invoke-RestMethod` Służy do wyzwalania funkcji.

W konsoli programu PowerShell uruchom następujące polecenie:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Można zauważyć, że odpowiedź nie jest natychmiast zwracane. To dlatego, że `Wait-Debugger` ma dołączony debuger i programu PowerShell wykonywania błąd w trybie przerwania, jak było to możliwe. Jest to z powodu [koncepcji BreakAll](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place), co jest opisane w dalszej części. Po naciśnięciu klawisza `continue` przycisku, debuger teraz przerywa w wierszu zaraz po `Wait-Debugger`.

W tym momencie jest dołączony debuger i normalnym debuger może wykonywać operacje. Aby uzyskać więcej informacji na temat używania debugera programu Visual Studio Code, zobacz [oficjalnej dokumentacji](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Po kontynuować i w pełni wywołanie skryptu, można będzie zauważyć, że:

* Konsoli programu PowerShell, na którym wykonano `Invoke-RestMethod` zwróciło wynik
* Zintegrowana konsola programu PowerShell, w programie Visual Studio Code oczekuje na skrypt do wykonania

Kolejnych godzin, po wywołaniu tej samej funkcji w debugerze programu PowerShell, rozszerzenie przerywa zaraz po `Wait-Debugger`.

## <a name="debugging-in-a-powershell-console"></a>Debugowanie w konsoli programu PowerShell

>[!NOTE]
> W tej sekcji założono, przeczytanie [docs podstawowych narzędzi usługi Azure Functions](functions-run-local.md) i wiesz, jak używać `func host start` polecenie, aby uruchomić aplikację funkcji.

Otwórz konsolę programu `cd` do katalogu aplikacji funkcji i uruchom następujące polecenie:

```sh
func host start
```

Za pomocą działanie aplikacji funkcji i `Wait-Debugger` w miejscu, można dołączyć do procesu. Potrzebujesz dwóch więcej konsoli programu PowerShell.

Jedną z konsol działa jako klient. Z tym należy wywołać `Invoke-RestMethod` do wyzwolenia funkcji. Można na przykład, uruchom następujące polecenie:

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

Można zauważyć, że nie zwraca odpowiedź, co wynika z `Wait-Debugger`. Obszarem działania programu PowerShell oczekuje teraz zostać dołączony debuger. Uzyskajmy dołączonego.

W innych konsoli programu PowerShell uruchom następujące polecenie:

```powershell
Get-PSHostProcessInfo
```

To polecenie cmdlet zwraca tabelę, która wygląda podobnie do następujących danych wyjściowych:

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

Zanotuj `ProcessId` dla elementu w tabeli z `ProcessName` jako `dotnet`. Ten proces jest aplikacja funkcji.

Następnie uruchom poniższy fragment kodu:

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

Po rozpoczęciu debuger przerywa i przedstawia podobny do następujących danych wyjściowych:

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

W tym momencie jest zatrzymany w punkcie przerwania w [debugera programu PowerShell](/powershell/module/microsoft.powershell.core/about/about_debuggers). W tym miejscu można wykonać wszystkie operacje debugowania zwykle, Przekrocz nad, wejdź do, kontynuować, zamknij i innym osobom. Aby wyświetlić pełny zestaw poleceń debugowania są dostępne w konsoli, uruchom `h` lub `?` poleceń.

Można również ustawić punkty przerwania na tym poziomie przy użyciu `Set-PSBreakpoint` polecenia cmdlet.

Po kontynuować i w pełni wywołanie skryptu, można będzie zauważyć, że:

* Konsola programu PowerShell, gdzie wykonywana `Invoke-RestMethod` teraz zwrócił wynik.
* Konsola programu PowerShell, gdzie wykonywana `Debug-Runspace` oczekuje na skrypt do wykonania.

Możesz wywołać tę samą funkcję ponownie (przy użyciu `Invoke-RestMethod` na przykład) i debuger przerywa w zaraz po `Wait-Debugger` polecenia.

## <a name="considerations-for-debugging"></a>Zagadnienia dotyczące debugowania

Należy pamiętać, następujące problemy podczas debugowania kodu usługi Functions.

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` może spowodować, że Twoje przerwanie debugowania w miejscu nieoczekiwany

Używa rozszerzenia PowerShell `Debug-Runspace`, który z kolei zależy od programu PowerShell `BreakAll` funkcji. Ta funkcja informuje program PowerShell w celu zatrzymania na pierwsze polecenie, który jest wykonywany. To zachowanie daje możliwość ustawiania punktów przerwania w debugowanym obszaru działania.

Środowisko uruchomieniowe usługi Azure Functions jest uruchamiany kilka poleceń przed faktycznie wywoływania usługi `run.ps1` skryptu, dzięki czemu jest możliwe, że debuger kończy się istotne w `Microsoft.Azure.Functions.PowerShellWorker.psm1` lub `Microsoft.Azure.Functions.PowerShellWorker.psd1`.

Powinno to rozbicie się zdarzyć, uruchom `continue` lub `c` polecenie, aby pominąć ten punkt przerwania. Następnie można zatrzymać w oczekiwanym punkcie przerwania.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat tworzenia funkcji przy użyciu programu PowerShell, zobacz [— przewodnik dewelopera usługi Azure Functions w programie PowerShell](functions-reference-powershell.md).
