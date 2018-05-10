---
title: Zainstaluj rozszerzenie funkcji trwałe i przykłady - Azure
description: Dowiedz się, jak zainstalować rozszerzenie funkcji trwałe dla usługi Azure Functions do rozwoju portalu lub tworzenia Visual Studio.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 4dd4bbb9c382b772f8f60b259844e7e471ec73e3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="install-the-durable-functions-extension-and-samples-azure-functions"></a>Zainstaluj rozszerzenie funkcji trwałe i przykłady (usługi Azure Functions)

[Trwałe funkcji](durable-functions-overview.md) rozszerzenie dla usługi Azure Functions znajduje się w pakiecie NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). W tym artykule przedstawiono sposób instalowania pakietu i zestaw przykładów dla następujących środowisk deweloperskich:

* Visual Studio 2017 r (zalecane dla C#) 
* Kod programu Visual Studio (zalecane dla języka JavaScript)
* Azure Portal

## <a name="visual-studio-2017"></a>Visual Studio 2017

Program Visual Studio udostępnia obecnie najlepsze środowisko dla opracowywania aplikacji korzystających z funkcji trwałe.  Funkcji mogą być uruchamiane lokalnie, a także mogą być publikowane na platformie Azure. Pusty projekt lub zestaw funkcji przykładowe można uruchomić.

### <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [najnowszej wersji programu Visual Studio](https://www.visualstudio.com/downloads/) (wersja 15.3 lub nowsza). Obejmują **Azure programowanie** obciążenia w opcjach instalacji.

### <a name="start-with-sample-functions"></a>Uruchom funkcje próbki 

1. Pobierz [pliku .zip Przykładowa aplikacja dla programu Visual Studio](https://azure.github.io/azure-functions-durable-extension/files/VSDFSampleApp.zip). Nie musisz dodać odwołanie NuGet, ponieważ już ma przykładowy projekt.
2. Zainstaluj i uruchom [emulatora magazynu Azure](https://docs.microsoft.com/azure/storage/storage-use-emulator) w wersji 5.2 lub nowszej. Alternatywnie możesz zaktualizować *local.appsettings.json* pliku z rzeczywistych parametrów połączenia usługi Azure Storage.
3. Otwórz projekt w programie Visual Studio 2017 r. 
4. Aby uzyskać instrukcje na temat uruchamiania próbki, rozpoczynać się od [funkcji łańcucha — Witaj próbki sekwencji](durable-functions-sequence.md). Próbki można uruchomić lokalnie lub opublikowane na platformie Azure.

### <a name="start-with-an-empty-project"></a>Rozpoczynać pusty projekt
 
Postępuj zgodnie z instrukcjami sam jak w przypadku począwszy próbki, ale należy wykonać poniższe kroki zamiast pobierania *.zip* pliku:

1. Utwórz projekt aplikacji funkcji.
2. Wyszukaj następujący NuGet pakietu odwołania przy użyciu *Zarządzaj pakietami NuGet* i dodaj go do projektu: Microsoft.Azure.WebJobs.Extensions.DurableTask v1.4.0 (Sprawdź *Uwzględnij wersję wstępną* do Wyszukaj ten pakiet)
   
## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code zapewnia środowisko rozwoju lokalnego obejmujących wszystkich głównych platform - systemu Windows, system macOS i Linux.  Funkcje mogą być uruchamiane lokalnie i zostaną opublikowane na platformie Azure. Pusty projekt lub zestaw funkcji przykładowe można uruchomić.

### <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [najnowszej wersji programu Visual Studio Code](https://code.visualstudio.com/Download) 

* Postępuj zgodnie z instrukcjami w obszarze "Zainstaluj Azure funkcje podstawowe narzędzia" na [kodu oraz testów usługi Azure Functions lokalnie](https://docs.microsoft.com/azure/azure-functions/functions-run-local)

    >[!IMPORTANT]
    > Jeśli masz już narzędzi Cross Platform w programie Azure funkcji, aktualizację do najnowszej dostępnej wersji.

    >[!IMPORTANT]
    >Trwałe funkcji w języku JavaScript wymaga wersji 2.x podstawowe narzędzia funkcji platformy Azure.

*  Jeśli na komputerze z systemem Windows, zainstalować i uruchomić [emulatora magazynu Azure](https://docs.microsoft.com/azure/storage/storage-use-emulator) w wersji 5.2 lub nowszej. Alternatywnie możesz zaktualizować *local.appsettings.json* pliku z rzeczywistą połączenia usługi Azure Storage. 


### <a name="start-with-sample-functions"></a>Uruchom funkcje próbki

#### <a name="c"></a>C#

1. Klonowanie [repozytorium trwałe funkcji](https://github.com/Azure/azure-functions-durable-extension.git).
2. Przejdź na tym komputerze do [folderu przykładów skryptu C#](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx). 
3. Zainstaluj rozszerzenie trwałe funkcji platformy Azure, uruchamiając następujące polecenia oknie monitu / terminala:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.4.0
    ```
4. Zainstaluj rozszerzenie usługi Twilio funkcji platformy Azure, uruchamiając następujące polecenia oknie monitu / terminala:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.Twilio -v 3.0.0-beta5
    ```
5. Uruchom Emulator usługi Azure Storage lub aktualizacji *local.appsettings.json* pliku z rzeczywistych parametrów połączenia usługi Azure Storage.
6. Otwórz projekt w programie Visual Studio Code. 
7. Aby uzyskać instrukcje na temat uruchamiania próbki, rozpoczynać się od [funkcji łańcucha — Witaj próbki sekwencji](durable-functions-sequence.md). Próbki można uruchomić lokalnie lub opublikowane na platformie Azure.
8. Uruchom projekt, uruchamiając w poleceniu monitu / terminali następujące polecenie:
    ```bash
    func host start
    ```

#### <a name="javascript-functions-v2-only"></a>JavaScript (tylko funkcje v2)

1. Klonowanie [repozytorium trwałe funkcji](https://github.com/Azure/azure-functions-durable-extension.git).
2. Przejdź na tym komputerze do [folderu przykładów JavaScript](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/javascript). 
3. Zainstaluj rozszerzenie trwałe funkcji platformy Azure, uruchamiając następujące polecenia oknie monitu / terminala:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.4.0
    ```
4. Przywracanie pakietów npm, uruchamiając następujące polecenia oknie monitu / terminala:
    
    ```bash
    npm install
    ``` 
5. Aktualizacja *local.appsettings.json* pliku z rzeczywistych parametrów połączenia usługi Azure Storage.
6. Otwórz projekt w programie Visual Studio Code. 
7. Aby uzyskać instrukcje na temat uruchamiania próbki, rozpoczynać się od [funkcji łańcucha — Witaj próbki sekwencji](durable-functions-sequence.md). Próbki można uruchomić lokalnie lub opublikowane na platformie Azure.
8. Uruchom projekt, uruchamiając w poleceniu monitu / terminali następujące polecenie:
    ```bash
    func host start
    ```

### <a name="start-with-an-empty-project"></a>Rozpoczynać pusty projekt
 
1. W poleceniu monitu / terminalu przejdź do folderu, który będzie hostem aplikacji funkcji.
2. Zainstaluj rozszerzenie trwałe funkcji platformy Azure, uruchamiając następujące polecenia oknie monitu / terminala:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.4.0
    ```
3. Utwórz projekt aplikacji funkcji, uruchamiając następujące polecenie:

    ```bash
    func init
    ``` 
4. Uruchom Emulator usługi Azure Storage lub aktualizacji *local.appsettings.json* pliku z rzeczywistych parametrów połączenia usługi Azure Storage.
5. Następnie utwórz nową funkcję, uruchamiając następujące polecenie i wykonaj kroki kreatora:

    ```bash
    func new
    ```
    >[!IMPORTANT]
    > Obecnie szablon trwałe funkcja nie jest dostępny, ale można uruchomić z jednym z obsługiwanych opcji, a następnie zmodyfikować kod. Użyj odwołania przykłady dotyczące [klienta aranżacji](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/HttpStart), [wyzwalacza aranżacji](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence), i [działania wyzwalacza](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence).

6. Otwórz folder projektu w programie Visual Studio Code i kontynuuj przez zmodyfikowanie kod szablonu. 
7. Uruchom projekt, uruchamiając w poleceniu monitu / terminali następujące polecenie:
    ```bash
    func host start
    ```

## <a name="azure-portal"></a>Azure Portal

Jeśli wolisz, można użyć portalu Azure dla rozwoju trwałe funkcji.

   > [!NOTE]
   > Trwałe funkcji w języku JavaScript nie są jeszcze dostępne w portalu.

### <a name="create-an-orchestrator-function"></a>Tworzenie funkcji programu orchestrator

1. Utwórz nową aplikację funkcji w [functions.azure.com](https://functions.azure.com/signin).

2. Konfigurowanie aplikacji funkcji [Użyj wersji 2.0 runtime](set-runtime-version.md).

   Rozszerzenie funkcji trwałe działa zarówno środowiska uruchomieniowego 1.X i 2.0 runtime, ale szablony portalu Azure są dostępne tylko, jeśli celem 2.0 runtime.

3. Utwórz nową funkcję, wybierając **"Tworzenie funkcji niestandardowej".** .

4. Zmień **języka** do **C#**, **scenariusza** do **trwałe funkcji** i wybierz **trwałe Starter Http funkcji -C#** szablonu.

5. W obszarze **nie zainstalowano rozszerzenia**, kliknij przycisk **zainstalować** do pobrania rozszerzenie z NuGet.org. 

6. Po zakończeniu instalacji należy kontynuować tworzenie funkcji klienta aranżacji — **"HttpStart"** utworzonego przez wybranie **trwałe funkcje Http Starter - C#** szablonu.

7. Teraz Utwórz funkcję aranżacji **"HelloSequence"** z **trwałe funkcje programu Orchestrator - C#** szablonu.

8. I zostanie wywołana funkcja ostatniego **"Hello"** z **trwałe działania funkcji - C#** szablonu.

9. Przejdź do **"HttpStart"** funkcji i skopiuj adres URL.

10. Umożliwia Postman lub programu cURL wywołanie funkcji trwałe. Przed testowaniem, Zastąp w adresie URL **{functionName}** z nazwą funkcji orchestrator - **HelloSequence**.  Nie są wymagane żadne dane, po prostu użyj zlecenie POST. 

    ```bash
    curl -X POST https://{your function app name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

11. Następnie wywołaj metodę **"statusQueryGetUri"** punktu końcowego i zostanie wyświetlony bieżący stan trwały funkcji

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

12. Kontynuować wywoływania **"statusQueryGetUri"** punktu końcowego, aż stan zmieni się na **"Ukończone"** 

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

Gratulacje! Swoją pierwszą funkcję trwałego jest uruchomiona w portalu Azure!

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Uruchamianie funkcji łańcucha próbki](durable-functions-sequence.md)
