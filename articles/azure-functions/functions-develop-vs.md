---
title: Tworzenie funkcji platformy Azure przy użyciu programu Visual Studio | Dokumentacja firmy Microsoft
description: Dowiedz się, jak opracowywać i testować funkcje platformy Azure przy użyciu narzędzi funkcji Azure dla programu Visual Studio 2017 r.
services: functions
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: glenga
ms.openlocfilehash: 93d5883071a012842106bdd946e4f09a0d7aa751
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260458"
---
# <a name="azure-functions-tools-for-visual-studio"></a>Środowisko Azure Functions Tools for Visual Studio  

Narzędzi funkcji platformy Azure dla programu Visual Studio 2017 to rozszerzenie dla programu Visual Studio, który pozwala tworzyć, testować i wdrażać funkcje C# na platformie Azure. To środowisko w przypadku pierwszego z usługi Azure Functions, możesz dowiedzieć się więcej, zobacz [wprowadzenie do usługi Azure Functions](functions-overview.md).

Narzędzia funkcji Azure zapewnia następujące korzyści: 

* Edytowania, tworzenia i uruchamiania funkcji na komputerze deweloperskim lokalnego. 
* Publikowanie projektu usługi Azure Functions bezpośrednio na platformie Azure. 
* Użycie zadań Webjob atrybutów w celu zadeklarowania powiązania funkcji bezpośrednio w kodzie C# zamiast utrzymywania osobnych function.json dla powiązania definicje.
* Tworzenie i wdrażanie wstępnie skompilowanym funkcje C#. Wstępnie skompilowane funkcje umożliwiać wydajności zimnego lepiej niż C# opartych na skryptach funkcji. 
* Kod funkcji w języku C# przy jednoczesnym zachowaniu wszystkie zalety tworzenia Visual Studio. 

W tym artykule przedstawiono sposób korzystania z narzędzi funkcji Azure dla programu Visual Studio 2017 do opracowywania funkcji w języku C#. Możesz również Dowiedz się, jak opublikować projekt na platformie Azure jako zestaw .NET.

> [!IMPORTANT]
> Nie można mieszać lokalne działania projektowe z portalu Programowanie w tej samej aplikacji funkcji. Podczas publikowania z lokalnej projektu aplikacji funkcji procesu wdrażania zastępuje wszystkie funkcje, które utworzonych w portalu.

## <a name="prerequisites"></a>Wymagania wstępne

Narzędzi funkcji platformy Azure jest uwzględniona w obciążenie Azure programowanie [programu Visual Studio 2017 wersji 15,5 cala](https://www.visualstudio.com/vs/), lub jego nowsza wersja. Upewnij się, że uwzględniasz **Azure programowanie** obciążenia w instalacji programu Visual Studio 2017:

![Instalowanie programu Visual Studio 2017 z obciążeniem Programowanie na platformie Azure](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Upewnij się, że Visual Studio jest aktualny i że używasz [najnowszej wersji](#check-your-tools-version) narzędzi usługi Azure Functions.

### <a name="other-requirements"></a>Inne wymagania

Tworzenie i wdrażanie funkcji, należy również:

* Aktywna subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [wolnego konta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) są dostępne.

* Konto usługi Azure Storage. Aby utworzyć konto magazynu, zobacz [Utwórz konto magazynu](../storage/common/storage-create-storage-account.md#create-a-storage-account).

### <a name="check-your-tools-version"></a>Sprawdź wersję narzędzia

1. Z **narzędzia** menu, wybierz **rozszerzenia i aktualizacje**. Rozwiń węzeł **zainstalowana** > **narzędzia** i wybierz polecenie **narzędzia zadania sieci Web i usługi Azure Functions**.

    ![Sprawdź wersję narzędzia funkcji](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

2. Należy pamiętać, jest zainstalowana **wersji**. Możesz porównać tę wersję za pomocą najnowszej wersji wymienionych [w informacjach o wersji](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md). 

3. W przypadku starszych wersji aktualizacji z narzędzi programu Visual Studio, jak pokazano w poniższej sekcji.

### <a name="update-your-tools"></a>Zaktualizuj narzędziami

1. W **rozszerzenia i aktualizacje** okna dialogowego, rozwiń węzeł **aktualizacje** > **Visual Studio Marketplace**, wybierz **usługi Azure Functions i narzędzia zadania sieci Web**  i wybierz **aktualizacji**.

    ![Zaktualizuj wersję narzędzia funkcji](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

2. Po pobraniu narzędzia aktualizacji Zamknij program Visual Studio, aby wywoływany narzędzia aktualizacji za pomocą Instalatora VSIX.

3. W Instalatorze wybrać **OK** można uruchomić, a następnie **Modyfikuj** aktualizacji narzędzi. 

4. Po ukończeniu aktualizacji, wybierz **Zamknij** i uruchom ponownie program Visual Studio.

## <a name="create-an-azure-functions-project"></a>Tworzenie projektu usługi Azure Functions

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Szablon projektu spowoduje utworzenie projektu C#, instaluje `Microsoft.NET.Sdk.Functions` pakietu NuGet i ustawia platformy docelowej. Funkcje 1.x obiektów docelowych programu .NET Framework, a funkcje cele 2.x .NET Standard. Nowy projekt zawiera następujące pliki:

* **Host.JSON**: umożliwia skonfigurowanie hostów funkcji. Te ustawienia dotyczą zarówno podczas uruchamiania lokalnie i na platformie Azure. Aby uzyskać więcej informacji, zobacz [odwołania host.json](functions-host-json.md).

* **Local.Settings.JSON**: przechowuje ustawienia używane podczas uruchamiania lokalnego funkcji. Te ustawienia nie są używane przez platformę Azure, są one używane przez [Azure funkcje podstawowe narzędzia](functions-run-local.md). Użyj tego pliku, aby określić ustawienia aplikacji dla zmiennych wymagany przez funkcje. Dodaj nowy element do **wartości** tablicy dla każdego połączenia wymagany przez funkcje powiązania w projekcie. Aby uzyskać więcej informacji, zobacz [pliku ustawień lokalnych](functions-run-local.md#local-settings-file) w artykule Azure funkcje podstawowe narzędzia.

Aby uzyskać więcej informacji, zobacz [projektu biblioteki klas funkcji](functions-dotnet-class-library.md#functions-class-library-project).

## <a name="configure-the-project-for-local-development"></a>Konfigurowanie projektu dla rozwoju lokalnych

Środowisko uruchomieniowe Functions używa konta usługi Azure Storage wewnętrznie. W przypadku wyzwolenia wszystkich typów innych niż HTTP i elementów webhook, należy skonfigurować **Values.AzureWebJobsStorage** klucza do prawidłowego ciągu połączenia konta magazynu Azure. Funkcja aplikacji można również użyć [emulatora magazynu Azure](../storage/common/storage-use-emulator.md) dla **AzureWebJobsStorage** połączenia ustawienie wymagane przez projekt. Aby użyć emulatora, ustaw wartość **AzureWebJobsStorage** do `UseDevelopmentStorage=true`. Należy zmienić to ustawienie na połączenia z magazynem rzeczywiste przed przystąpieniem do wdrożenia.

Aby ustawić parametry połączenia konta magazynu:

1. W programie Visual Studio Otwórz **Eksplorator chmury**, rozwiń węzeł **konta magazynu** > **Twoje konto magazynu**, a następnie wybierz pozycję **właściwości**i skopiuj **parametry połączenia podstawowej** wartość.

2. W projekcie, otwórz plik local.settings.json i ustaw wartość **AzureWebJobsStorage** skopiować klucza w parametrach połączenia.

3. Powtórz poprzedni krok, aby dodać unikatowy kluczy do **wartości** tablicy dla innych połączeń wymagany przez funkcje.

## <a name="create-a-function"></a>Tworzenie funkcji

W przypadku funkcji wstępnie skompilowanym powiązania używane przez funkcję są definiowane przez stosowanie atrybutów w kodzie. Korzystając z narzędzi funkcji Azure można utworzyć funkcji z podanych szablonów, te atrybuty są stosowane dla Ciebie. 

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy węzeł projektu i wybierz polecenie **Dodaj** > **Nowy element**. Wybierz **funkcji platformy Azure**, wpisz **nazwa** klasy, a następnie kliknij przycisk **Dodaj**.

2. Wybierz wyzwalacz, ustaw właściwości powiązania, a następnie kliknij przycisk **Utwórz**. W poniższym przykładzie przedstawiono ustawienia wyzwolenia tworzenia magazynu kolejek funkcji. 

    ![Utwórz funkcję kolejki wyzwalane](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    W tym przykładzie wyzwalacza używane parametry połączenia za pomocą klucza o nazwie **QueueStorage**. To ustawienie ciągu połączenia musi być zdefiniowana w [pliku local.settings.json](functions-run-local.md#local-settings-file).

3. Sprawdź, czy nowo dodanego klasy. Zobacz statycznego **Uruchom** — metoda, która ma atrybut **FunctionName** atrybutu. Ten atrybut wskazuje, że metoda jest punkt wejścia dla funkcji.

    Na przykład następujące klasy C# reprezentuje podstawowych funkcji magazynu wyzwalane kolejki:

    ````csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;

    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]
            public static void Run([QueueTrigger("myqueue-items", Connection = "QueueStorage")]string myQueueItem, TraceWriter log)
            {
                log.Info($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    }
    ````
    Atrybut specyficzne dla powiązania jest stosowany do każdego parametru wiązania dostarczony do metody punktu wejścia. Ten atrybut ma informacje o powiązaniu jako parametry. W poprzednim przykładzie pierwszy parametr ma **QueueTrigger** atrybut, wskazujący funkcję kolejki wyzwolone. Nazwa kolejki i nazwa ustawienie parametrów połączenia są przekazywane jako parametry **QueueTrigger** atrybutu.
    
Powyższej procedury służy do dodania do projektu aplikacji funkcja więcej funkcji. Każdej funkcji w projekcie może mieć różne wyzwalacza, ale funkcja musi mieć dokładnie jeden wyzwalacz. Aby uzyskać więcej informacji, zobacz [usługi Azure Functions wyzwalaczy i powiązań pojęcia](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Dodaj powiązania

Podobnie jak w przypadku wyzwalaczy, powiązania wejściowe i wyjściowe są dodawane do funkcji jako powiązania atrybutów. Dodaj powiązania funkcji w następujący sposób:

1. Upewnij się, że masz [skonfigurować projekt dla rozwoju lokalnych](#configure-the-project-for-local-development).

2. Dodaj odpowiedni pakiet rozszerzenie NuGet dla określonego powiązania. Aby uzyskać więcej informacji, zobacz [rozwoju lokalnych C# za pomocą programu Visual Studio](functions-triggers-bindings.md#local-csharp) w artykule wyzwalaczy i powiązań. Wymagania pakietu NuGet specyficzne dla powiązania znajdują się w artykule dla wiązania. Na przykład znaleźć wymagań dotyczących pakietu dla wyzwalacza centra zdarzeń w [artykule informacyjnym powiązania usługi Event Hubs](functions-bindings-event-hubs.md).

3. Jeśli istnieją ustawienia aplikacji, które wymaga powiązania, dodaj je do **wartości** kolekcji w [pliku ustawień lokalnych](functions-run-local.md#local-settings-file). Te wartości są używane, gdy funkcja działa lokalnie. Po uruchomieniu funkcji w aplikacji funkcji na platformie Azure, [funkcji ustawienia aplikacji](#function-app-settings) są używane.

4. Dodaj atrybut powiązanie odpowiednie w podpisie metody. W poniższym przykładzie komunikatu w kolejce wyzwala funkcji i powiązania wyjściowego tworzy nowy komunikat kolejki z tego samego tekstu w innej kolejki.

    ```csharp
    public static class SimpleExampleWithOutput
    {
        [FunctionName("CopyQueueMessage")]
        public static void Run(
            [QueueTrigger("myqueue-items-source", Connection = "AzureWebJobsStorage")] string myQueueItem, 
            [Queue("myqueue-items-destination", Connection = "AzureWebJobsStorage")] out string myQueueItemCopy,
            TraceWriter log)
        {
            log.Info($"CopyQueueMessage function processed: {myQueueItem}");
            myQueueItemCopy = myQueueItem;
        }
    }
    ```
Połączenie z magazynem kolejek są uzyskiwane z `AzureWebJobsStorage` ustawienie. Aby uzyskać więcej informacji zobacz artykuł odwołania dla określonego powiązania. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Testowanie funkcji

Podstawowe narzędzia usługi Azure Functions umożliwiają uruchamianie projektu usługi Azure Functions na lokalnym komputerze deweloperskim. Monit o zainstalowanie tych narzędzi pojawia się przy pierwszym uruchomieniu funkcji w programie Visual Studio.

Aby przetestować funkcję, naciśnij klawisz F5. Po wyświetleniu monitu zaakceptuj żądanie programu Visual Studio dotyczące pobrania i zainstalowania podstawowych narzędzi usługi Azure Functions (CLI). Może także być konieczne włączenie wyjątku zapory, aby umożliwić narzędziom obsługę żądań HTTP.

W projekcie jest uruchomiona można przetestować kodu jako może przetestować wdrożonej funkcji. Aby uzyskać więcej informacji, zobacz [strategii do testowania kodu w usługi Azure Functions](functions-test-a-function.md). Podczas uruchamiania w trybie debugowania, punkty przerwania są osiągane w programie Visual Studio, zgodnie z oczekiwaniami. 

Na przykład jak przetestować funkcję kolejki wyzwalane zobacz [samouczek Szybki Start — funkcja kolejki wyzwalane](functions-create-storage-queue-triggered-function.md#test-the-function).  

Aby dowiedzieć się więcej o korzystaniu z narzędzi podstawowych funkcji Azure, zobacz [kodu i przetestuj usługę Azure functions lokalnie](functions-run-local.md).

## <a name="publish-to-azure"></a>Publikowanie na platformie Azure

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Ustawienia aplikacji funkcji

Wszystkie ustawienia, które zostały dodane w local.settings.json można również dodać do aplikacji funkcji na platformie Azure. Te ustawienia nie są przekazywane automatycznie podczas publikowania projektu.

Najprostszym sposobem przekazać do funkcji aplikacji na platformie Azure wymagane ustawienia jest użycie **Zarządzanie ustawieniami aplikacji...**  link wyświetlany po pomyślnym opublikować projekt. 

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

Spowoduje to wyświetlenie **ustawienia aplikacji** okno dialogowe dla aplikacji funkcja, której można dodawać nowe ustawienia aplikacji lub modyfikować istniejące.

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

Można również zarządzać ustawienia aplikacji w jednym z tych inne sposoby:

* [Przy użyciu portalu Azure](functions-how-to-use-azure-function-app-settings.md#settings).
* [Przy użyciu `--publish-local-settings` opcja publikowania w Azure funkcje podstawowe narzędzia](functions-run-local.md#publish).
* [Przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set). 

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat narzędzi funkcji Azure, zobacz sekcję Często zadawane pytania [2017 Visual Studio Tools dla usługi Azure Functions](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/) wpis w blogu.

Aby dowiedzieć się więcej o podstawowych narzędzi funkcji platformy Azure, zobacz [kodu i przetestuj usługę Azure functions lokalnie](functions-run-local.md).

Aby dowiedzieć się więcej o wdrażaniu funkcji jako biblioteki klas .NET, zobacz [dokumentacja dla deweloperów Azure funkcje C#](functions-dotnet-class-library.md). Ten artykuł zawiera również linki do przykłady tego, jak używać atrybutów, aby zadeklarować różnych typów powiązania obsługiwane przez usługi Azure Functions.    
