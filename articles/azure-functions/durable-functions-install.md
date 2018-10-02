---
title: Instalowanie rozszerzenia funkcji trwałych i przykłady dla platformy Azure —
description: Dowiedz się, jak zainstalować rozszerzenia funkcji trwałych dla usługi Azure Functions dla dla portalu lub programowanie aplikacji programu Visual Studio.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: azfuncdf
ms.openlocfilehash: 2eb838bcb9d3f64d0bbf4657c516adb50d103223
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585309"
---
# <a name="install-the-durable-functions-extension-and-samples-azure-functions"></a>Instalowanie rozszerzenia funkcji trwałych i przykłady (usługi Azure Functions)

[Funkcje trwałe](durable-functions-overview.md) rozszerzenia dla usługi Azure Functions jest podawany jako pakiet NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). W tym artykule przedstawiono sposób instalowania pakietu i uzyskać zestaw przykładów dla następujących środowisk programowania:

* Program Visual Studio 2017 (zalecane dla języka C#) 
* Program Visual Studio Code (zalecane dla języka JavaScript)
* Azure Portal

## <a name="visual-studio-2017"></a>Visual Studio 2017

Visual Studio zawiera obecnie najlepsze środowisko do tworzenia aplikacji, które używają funkcje trwałe.  Funkcje mogą być uruchamiane lokalnie, a także mogą być publikowane na platformie Azure. Można uruchomić z pusty projekt lub zestaw funkcji próbki.

### <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [najnowszą wersję programu Visual Studio](https://www.visualstudio.com/downloads/) (w wersji 15.6 lub nowszej). Obejmują **programowanie na platformie Azure** obciążenie w opcjach Instalatora.

### <a name="start-with-sample-functions"></a>Start z usługą functions próbki 

1. Pobierz [plik zip Przykładowa aplikacja dla programu Visual Studio](https://azure.github.io/azure-functions-durable-extension/files/VSDFSampleApp.zip). Nie musisz dodać odwołanie NuGet, ponieważ przykładowy projekt zawiera już go.
2. Zainstaluj i uruchom [emulatora usługi Azure Storage](https://docs.microsoft.com/azure/storage/storage-use-emulator) wersji 5.6 lub nowszy. Alternatywnie, możesz zaktualizować *local.settings.json* plik z rzeczywistych parametrów połączenia usługi Azure Storage.
3. Otwórz projekt w programie Visual Studio 2017. 
4. Aby uzyskać instrukcje dotyczące sposobu uruchamiania przykładu, rozpoczynać się [funkcji łańcucha — Witaj przykładowy Sekwencja](durable-functions-sequence.md). Przykład można uruchomić lokalnie lub opublikowane na platformie Azure.

### <a name="start-with-an-empty-project"></a>Rozpoczynać pusty projekt
 
Postępuj zgodnie z instrukcjami ten sam, jak w przypadku począwszy od przykładu, ale należy wykonać poniższe kroki, zamiast pobierać *zip* pliku:

1. Utwórz projekt aplikacji funkcji.
2. Wyszukaj następujący NuGet pakietu odwołania przy użyciu *Zarządzaj pakietami NuGet* i dodaj go do projektu: Microsoft.Azure.WebJobs.Extensions.DurableTask v1.6.0
   
## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code zapewnia środowisko programowania lokalnego obejmujące wszystkich największych platform: Windows, macOS i Linux.  Funkcje mogą być uruchamiane lokalnie i zostaną opublikowane na platformie Azure. Można uruchomić z pusty projekt lub zestaw funkcji próbki.

### <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [najnowszą wersję programu Visual Studio Code](https://code.visualstudio.com/Download) 

* Postępuj zgodnie z instrukcjami w sekcji "Instalowanie narzędzi usługi Azure Functions Core" w [kodu i testowanie usługi Azure Functions lokalnie](https://docs.microsoft.com/azure/azure-functions/functions-run-local)

    >[!IMPORTANT]
    > Jeśli masz już narzędzia Cross Platform usługi Azure Functions, należy je zaktualizować do najnowszej dostępnej wersji.

    >[!IMPORTANT]
    >Trwałe funkcje w języku JavaScript wymaga wersji 2.x podstawowych narzędzi usługi Azure Functions.

*  Jeśli na komputerze Windows, zainstaluj i uruchom [emulatora usługi Azure Storage](https://docs.microsoft.com/azure/storage/storage-use-emulator) wersji 5.6 lub nowszy. Alternatywnie, możesz zaktualizować *local.settings.json* plików za pomocą rzeczywistych połączenia usługi Azure Storage. 


### <a name="start-with-sample-functions"></a>Start z usługą functions próbki

#### <a name="c"></a>C#

1. Klonuj [repozytorium funkcje trwałe](https://github.com/Azure/azure-functions-durable-extension.git).
2. Przejdź na Twojej maszynie do [C# script samples folderu](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx). 
3. Zainstaluj rozszerzenie niezawodne funkcje platformy Azure, uruchamiając następujące polecenia oknie monitu / terminalu:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.6.0
    ```
4. Instalowanie rozszerzenia usługi Twilio funkcji platformy Azure, uruchamiając następujące polecenia oknie monitu / terminalu:

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.Twilio -v 3.0.0-beta8
    ```
5. Uruchamianie emulatora usługi Azure Storage lub aktualizacji *local.settings.json* plików za pomocą rzeczywistych parametrów połączenia usługi Azure Storage.
6. Otwórz projekt w programie Visual Studio Code. 
7. Aby uzyskać instrukcje dotyczące sposobu uruchamiania przykładu, rozpoczynać się [funkcji łańcucha — Witaj przykładowy Sekwencja](durable-functions-sequence.md). Przykład można uruchomić lokalnie lub opublikowane na platformie Azure.
8. Uruchom projekt, uruchamiając w poleceniu monitu / terminalu następujące polecenie:
    ```bash
    func host start
    ```

#### <a name="javascript-functions-v2-only"></a>JavaScript (tylko funkcje v2)

1. Klonuj [repozytorium funkcje trwałe](https://github.com/Azure/azure-functions-durable-extension.git).
2. Przejdź na Twojej maszynie do [folderze samples JavaScript](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/javascript). 
3. Zainstaluj rozszerzenie niezawodne funkcje platformy Azure, uruchamiając następujące polecenia oknie monitu / terminalu

    ```
    func extensions install
    ```
    > [!NOTE] 
    > Wymaga to [zestawu .NET Core SDK](https://www.microsoft.com/net/download) do zainstalowania na komputerze
4. Przywróć pakiety npm, uruchamiając następujące polecenia oknie monitu / terminalu:
    
    ```
    npm install
    ``` 
5. Aktualizacja *local.settings.json* plik z parametrami połączenia z konta usługi Azure storage dla `AzureWebJobsStorage`.  To konto magazynu będzie służyć do stanu trwałego funkcji.
6. Otwórz projekt w edytorze, np. programu Visual Studio Code. 
7. Aby uzyskać instrukcje dotyczące sposobu uruchamiania przykładu, rozpoczynać się [funkcji łańcucha — Witaj przykładowy Sekwencja](durable-functions-sequence.md). Przykład można uruchomić lokalnie lub opublikowane na platformie Azure.
8. Uruchom projekt, uruchamiając w poleceniu monitu / terminalu następujące polecenie:
    ```
    func start
    ```

### <a name="start-with-an-empty-project"></a>Rozpoczynać pusty projekt
 
1. W poleceniu monitu / terminalu przejdź do folderu, który będzie hostować swoją aplikację funkcji.
3. Utwórz projekt aplikacji funkcji, uruchamiając następujące polecenie:

    ```
    func init
    ``` 
4. Uruchamianie emulatora usługi Azure Storage (tylko Windows) lub zaktualizować *local.settings.json* plików za pomocą rzeczywistych parametrów połączenia usługi Azure Storage dla `AzureWebJobsStorage`.
5. Następnie utwórz nową funkcję, uruchamiając następujące polecenie i wykonaj kroki kreatora:

    ```
    func new
    ```
    >[!IMPORTANT]
    > Obecnie szablon funkcji trwałe jest niedostępny, ale można zacząć od jednego z obsługiwanych opcji, a następnie zmodyfikować kod. Przykłady dla odwołania na użytek [klient Orkiestracji](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/HttpStart), [wyzwalacza aranżacji](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence), i [działania wyzwalacza](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence).
2. Zainstaluj rozszerzenie niezawodne funkcje platformy Azure, uruchamiając następujące polecenia oknie monitu / terminalu w katalogu aplikacji funkcji:

    ```
    func extensions install
    ```

6. Otwórz folder projektu w programie Visual Studio Code i kontynuować, modyfikując kod szablonu. 
7. Uruchom projekt, uruchamiając w poleceniu monitu / terminalu następujące polecenie:
    ```
    func start
    ```

## <a name="azure-portal"></a>Azure Portal

Jeśli wolisz, możesz użyć [witryny Azure portal](https://portal.azure.com) opracowywania funkcje trwałe.

   > [!NOTE]
   > Trwałe funkcje w języku JavaScript nie są jeszcze dostępne w portalu.

### <a name="create-an-orchestrator-function"></a>Tworzenie funkcji programu orchestrator

1. Utwórz nową aplikację funkcji w portalu, jak pokazano w [funkcje z artykułem Szybki Start](functions-create-first-azure-function.md#create-a-function-app).

2. Konfigurowanie aplikacji funkcji w celu [korzystać z wersji 2.0 środowisko uruchomieniowe](set-runtime-version.md).

   Rozszerzenia funkcji trwałych działa zarówno na środowisko uruchomieniowe 1.X i 2.0 środowisko uruchomieniowe, ale szablonów portalu systemu Azure są dostępne tylko podczas określania wartości docelowej 2.0 środowisko uruchomieniowe.

3. Utwórz nową funkcję, wybierając **"Utwórz własną funkcję niestandardową".** .

4. Zmiana **języka** do **C#**, **scenariusza** do **funkcje trwałe** i wybierz **niezawodne funkcje Http Starter -C#** szablonu.

5. W obszarze **rozszerzenia niezainstalowane**, kliknij przycisk **zainstalować** można pobrać rozszerzenia z witryny NuGet.org. 

6. Po zakończeniu instalacji kontynuuj tworzenie funkcję klienta aranżacji — **"HttpStart"** utworzonego przez wybranie **niezawodne funkcje — szablon Http Starter — C#** szablonu.

7. Teraz Utwórz funkcję aranżacji **"HelloSequence"** z **Durable Functions — Orkiestrator — C#** szablonu.

8. I zostanie wywołana funkcja last **"Hello"** z **Durable Functions — działanie — C#** szablonu.

9. Przejdź do **"HttpStart"** funkcji i skopiuj jej adres URL.

10. Użyj narzędzia Postman lub programu cURL, aby wywołać funkcję trwałe. Przed testowaniem, w powyższej ścieżce zastąpić adres URL **{functionName}** przy użyciu nazwy funkcji programu orchestrator - **HelloSequence**.  Żadne dane nie są wymagane, wystarczy użyć zlecenie POST. 

    ```bash
    curl -X POST https://{your function app name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

11. Następnie wywołaj **"statusQueryGetUri"** punktu końcowego i możesz wyświetlić bieżący stan trwały funkcji

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

12. Kontynuuj wywoływania **"statusQueryGetUri"** punktu końcowego, aż stan zmieni się na **"Ukończone"** 

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

Gratulacje! Pierwszą funkcję trwałe jest uruchomiona w witrynie Azure Portal!

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Uruchom funkcję łańcucha próbki](durable-functions-sequence.md)
