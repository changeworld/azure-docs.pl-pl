---
title: Tworzenie i wdrażanie zadań Webjob za pomocą programu Visual Studio – Azure
description: Dowiedz się, jak tworzenie i wdrażanie usługi Azure WebJobs w usłudze Azure App Service przy użyciu programu Visual Studio.
services: app-service
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 02/18/2019
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: ede7e2fe3a2ab4c0dfd4efaea5ec789924968194
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60832411"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Tworzenie i wdrażanie zadań Webjob za pomocą programu Visual Studio — usłudze Azure App Service

W tym artykule wyjaśniono, jak używać programu Visual Studio, aby wdrożyć projekt aplikacji konsoli w aplikacji sieci web w [usługi App Service](overview.md) jako [zadanie Azure WebJob](https://go.microsoft.com/fwlink/?LinkId=390226). Aby uzyskać informacje o tym, jak wdrażać zadania Webjob przy użyciu [witryny Azure portal](https://portal.azure.com), zobacz [zadania w tle uruchom za pomocą zadań Webjob](webjobs-create.md).

Wiele zadań Webjob mogą publikować w pojedynczej aplikacji sieci web. Upewnij się, że każdy zadania WebJob w aplikacji sieci web ma unikatową nazwę.

Wersji 3.x programu [zestawu Azure WebJobs SDK](webjobs-sdk-how-to.md) umożliwia tworzenie zadań Webjob, który uruchamiany jako aplikacje platformy .NET Core, podczas gdy w wersji 2.x obsługuje programu .NET Framework. Sposób wdrażania projektu zadań Webjob jest różnych projektów .NET Core i .NET Framework z nich.

## <a name="webjobs-as-net-core-console-apps"></a>Zadania Webjob jako aplikacji konsoli .NET Core

Podczas używania wersji 3.x zadań webjob, można tworzyć i publikować zadania Webjob jako aplikacji konsoli .NET Core. Aby uzyskać instrukcje krok po kroku tworzenia i publikowania aplikacji konsoli .NET Core na platformie Azure jako zadanie WebJob, zobacz [Rozpoczynanie pracy z usługą Azure WebJobs SDK, przetwarzanie w tle oparte na zdarzeniach](webjobs-sdk-get-started.md).

> [!NOTE]
> Zadania Webjob programu .NET core nie mogą być połączone z projektami sieci web. Jeśli zajdzie potrzeba wdrożenia zadania WebJob z aplikacją sieci web, należy [Tworzenie zadania WebJob jako aplikacji konsoli .NET Framework](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Wdrażanie w usłudze Azure App Service

Publikowanie zadania WebJob .NET Core w usłudze App Service w programie Visual Studio używa tego samego narzędzia jako publikowania aplikacji platformy ASP.NET Core.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>Typy zadań WebJob

Domyślnie zadanie WebJob publikowane .NET Core, projekt konsoli działa tylko po ich wyzwoleniu lub na żądanie. Możesz także zaktualizować projekt, aby [uruchamiane zgodnie z harmonogramem](#scheduled-execution) lub w sposób ciągły.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Zaplanowane wykonanie

Po opublikowaniu aplikacji konsoli .NET Core na platformie Azure, nowy *settings.job* plik zostanie dodany do projektu. Użyj tego pliku, aby ustawić harmonogram wykonywania zadania WebJob. Aby uzyskać więcej informacji, zobacz [planowania wyzwolone zadania WebJob](#scheduling-a-triggered-webjob).

#### <a name="continuous-execution"></a>Ciągłego wykonywania

Aby zmienić zadania WebJob do uruchamiania stale, po włączeniu funkcji Always On na platformie Azure, można użyć programu Visual Studio.

1. Jeśli użytkownik jeszcze tego nie zrobiono, [publikowania projektu na platformie Azure](#deploy-to-azure-app-service).

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj**.

1. W **Publikuj** kartę, wybrać **ustawienia**. 

1. W **ustawienia profilu** okno dialogowe, wybierz **ciągłe** dla **typ zadania WebJob**i wybierz polecenie **Zapisz**.

    ![Publikowanie okno dialogowe Ustawienia dla zadania WebJob](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Wybierz **Publikuj** ponownie opublikować zadania WebJob ze zaktualizowanymi ustawieniami.

## <a name="webjobs-as-net-framework-console-apps"></a>Zadania Webjob jako aplikacji konsoli .NET Framework  

Gdy program Visual Studio wdroży projekt zadania Webjob .NET Framework konsoli aplikacji z obsługą, wykonuje dwie czynności:

* Kopiuje pliki środowiska uruchomieniowego do odpowiedniego folderu w aplikacji sieci web (*App_Data/zadania/continuous* dla ciągłych zadań Webjob i *App_Data/zadania/triggered* dla usługi WebJobs, zaplanowane lub na żądanie).
* Konfiguruje [usługi Azure Scheduler](https://docs.microsoft.com/azure/scheduler/) zadania dla zadań Webjob, które są planowane do uruchomienia w określonym czasie. (To nie jest potrzebna do ciągłych zadań Webjob.)

Włączone usługi WebJobs projekt ma następujące elementy, które dodano do niego:

* [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) pakietu NuGet.
* A [zadania webjob — publikowanie settings.json](#publishsettings) pliku zawierającego ustawienia wdrożenia i harmonogramu. 

![Diagram przedstawiający, co to jest dodawany do aplikacji konsoli, aby umożliwić wdrożenie jako zadanie WebJob](./media/webjobs-dotnet-deploy-vs/convert.png)

Można dodać te elementy do istniejącego projektu aplikacji konsoli lub Utwórz nowy projekt aplikacji konsoli z możliwością zadań Webjob przy użyciu szablonu. 

Możesz wdrożyć projekt jako zadanie WebJob przez siebie lub link do projektu sieci web tak, aby automatycznie wdraża zawsze wtedy, gdy wdrażanie projektu sieci web. Aby połączyć projektów, program Visual Studio zawiera nazwę projektu zadań Webjob, włączone w [webjobs list.json](#webjobslist) pliku w projekcie sieci web.

![Diagram przedstawiający projektu zadania WebJob łączenie do projektu sieci web](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Wymagania wstępne

Jeśli używasz programu Visual Studio 2015, zainstaluj [zestawu Azure SDK dla platformy .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Jeśli używasz programu Visual Studio 2017, zainstaluj [obciążenie programistyczne platformy Azure](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---select-workloads).

### <a id="convert"></a> Włącz wdrożenie zadania Webjob do istniejącego projektu aplikacji konsoli

Dostępne są dwie opcje:

* [Włącz automatyczne wdrażanie projektu sieci web](#convertlink).

  Skonfiguruj istniejący projekt aplikacji konsoli, aby go automatycznie wdraża jako zadanie WebJob podczas wdrażania projektu sieci web. Użyj tej opcji, gdy użytkownik chce uruchomić zadania WebJob w tej samej aplikacji sieci web, w którym są uruchomione aplikacji sieci web powiązanych.

* [Włącz wdrożenie bez projektu sieci web](#convertnolink).

  Skonfiguruj istniejący projekt aplikacji konsoli do wdrażania jako zadanie WebJob przez siebie, za pomocą żadnego linku do projektu sieci web. Użyj tej opcji, jeśli chcesz uruchamiać zadanie WebJob w aplikacji sieci web przez siebie, z żadnej aplikacji sieci web, uruchamiany w aplikacji sieci web. Można to zrobić, aby można było skalować swoje zasoby zadań WebJob, niezależnie od zasobów aplikacji sieci web.

#### <a id="convertlink"></a> Włącz automatyczne wdrażanie zadań Webjob za pomocą projektu sieci web

1. Kliknij prawym przyciskiem myszy projekt sieci web w **Eksploratora rozwiązań**, a następnie kliknij przycisk **Dodaj** > **istniejący projekt jako zadanie Azure WebJob**.
   
    ![Istniejący projekt jako zadanie WebJob platformy Azure](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    [Dodaj zadanie WebJob systemu Azure](#configure) pojawi się okno dialogowe.
2. W **Nazwa projektu** listy rozwijanej, wybierz projekt aplikacji konsoli, można dodać jako zadanie WebJob.
   
    ![Wybieranie projektu w oknie dialogowym Dodaj zadanie WebJob systemu Azure](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Wykonaj [Dodaj zadanie WebJob systemu Azure](#configure) okna dialogowego, a następnie kliknij **OK**. 

#### <a id="convertnolink"></a> Włącz wdrożenie usługi WebJobs bez projektu sieci web
1. Kliknij prawym przyciskiem myszy projekt aplikacji konsoli w **Eksploratora rozwiązań**, a następnie kliknij przycisk **Publikuj jako zadanie Azure WebJob...** . 
   
    ![Publikuj jako zadanie WebJob platformy Azure](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    [Dodaj zadanie WebJob systemu Azure](#configure) pojawi się okno dialogowe z projektu wybranego w **Nazwa projektu** pole.
2. Wykonaj [Dodaj zadanie WebJob systemu Azure](#configure) okno dialogowe, a następnie kliknij przycisk **OK**.
   
   **Publikowanie w sieci Web** pojawi się Kreator.  Jeśli nie chcesz opublikować natychmiast, zamknąć kreatora. Ustawienia, które zostały wprowadzone są zapisywane dla, gdy użytkownik chce [wdrażanie projektu](#deploy).

### <a id="create"></a>Utwórz nowy projekt z obsługą usługi WebJobs
Aby utworzyć nowy projekt z obsługą usługi WebJobs, można użyć szablonu projektu aplikacji konsoli i włączyć wdrażanie zadań Webjob, jak wyjaśniono w [poprzedniej sekcji](#convert). Alternatywnie można użyć szablonu nowy projekt zadania Webjob:

* [Użyj szablonu nowego projektu zadań Webjob dla niezależnych zadań WebJob](#createnolink)
  
    Tworzenie projektu i skonfigurowania go do wdrożenia przez siebie jako zadanie WebJob przy użyciu żadnego linku do projektu sieci web. Użyj tej opcji, jeśli chcesz uruchamiać zadanie WebJob w aplikacji sieci web przez siebie, z żadnej aplikacji sieci web, uruchamiany w aplikacji sieci web. Można to zrobić, aby można było skalować swoje zasoby zadań WebJob, niezależnie od zasobów aplikacji sieci web.
* [Użyj szablonu nowego projektu zadań Webjob dla zadania WebJob połączonej do projektu sieci web](#createlink)
  
    Utwórz projekt, który jest skonfigurowany do wdrożenia automatycznie jako zadanie WebJob podczas wdrażania projektu sieci web w taki sposób, w tym samym rozwiązaniu. Użyj tej opcji, gdy użytkownik chce uruchomić zadania WebJob w tej samej aplikacji sieci web, w którym są uruchomione aplikacji sieci web powiązanych.

> [!NOTE]
> Szablon nowego projektu zadania Webjob automatycznie instaluje pakiety NuGet i zawiera kod w *Program.cs* dla [zestawu SDK usługi WebJobs](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Jeśli nie chcesz używać zestawu SDK usługi WebJobs, usuń lub zmień `host.RunAndBlock` instrukcji w *Program.cs*.
> 
> 

#### <a id="createnolink"></a> Użyj szablonu nowego projektu zadań Webjob dla niezależnych zadań WebJob
1. Kliknij przycisk **pliku** > **nowy projekt**, a następnie w polu **nowy projekt** kliknij okno dialogowe **chmury**  >   **Zadanie WebJob platformy Azure (.NET Framework)**.
   
    ![Okno dialogowe nowego projektu przedstawiający szablon zadania WebJob](./media/webjobs-dotnet-deploy-vs/np.png)
2. Wykonaj instrukcje przedstawione wcześniej do [aplikację konsoli projektu niezależnie od projektu zadania Webjob](#convertnolink).

#### <a id="createlink"></a> Użyj szablonu nowego projektu zadań Webjob dla zadania WebJob połączonej do projektu sieci web
1. Kliknij prawym przyciskiem myszy projekt sieci web w **Eksploratora rozwiązań**, a następnie kliknij przycisk **Dodaj** > **nowy projekt zadania WebJob Azure**.
   
    ![Nowy wpis menu projektu zadania WebJob platformy Azure](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    [Dodaj zadanie WebJob systemu Azure](#configure) pojawi się okno dialogowe.
2. Wykonaj [Dodaj zadanie WebJob systemu Azure](#configure) okno dialogowe, a następnie kliknij przycisk **OK**.

### <a id="configure"></a>Okno dialogowe Dodaj zadanie WebJob systemu Azure
**Dodaj zadanie WebJob systemu Azure** okno dialogowe pozwala wprowadzić nazwę zadania WebJob, i uruchom tryb zadania WebJob. 

![Dodaj okno dialogowe zadań WebJob platformy Azure](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Pola w tym oknie dialogowym odpowiadają polom na **Dodaj zadanie WebJob** okna dialogowego w witrynie Azure Portal. Aby uzyskać więcej informacji, zobacz [zadania w tle uruchom za pomocą zadań Webjob](webjobs-create.md).

> [!NOTE]
> * Aby uzyskać informacji o wdrażaniu wiersza polecenia, zobacz [włączenie wiersza polecenia lub ciągłej dostawy Azure WebJobs](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Jeśli wdrażać zadania WebJob, a następnie zdecydować, aby zmienić typ zadania WebJob i ponownie wdróż, musisz usunąć *webjobs — publikowanie settings.json* pliku. To spowoduje, że program Visual Studio pokazuj opcje publikowania, dzięki czemu można zmienić typ zadania WebJob.
> * Jeśli wdrażać zadania WebJob i późniejszej zmiany w trybie uruchamiania z ciągłego — ciągły lub odwrotnie, Visual Studio tworzy nowe zadanie WebJob na platformie Azure, podczas ponownego wdrażania. Jeśli zmienisz inne ustawienia harmonogramu, ale pozostaw tryb uruchamiania, taka sama lub przełączać się między zaplanowane i na żądanie, Visual Studio aktualizuje istniejące zadanie zamiast Utwórz nową.
> 
> 

### <a id="publishsettings"></a>webjob-publish-settings.json
Po skonfigurowaniu aplikacji konsoli dla zadań Webjob, wdrażania, program Visual Studio instaluje [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet pakietu i planowania informacje zawarte w magazynach *zadania webjob — publikowanie settings.json*  pliku w projekcie *właściwości* folderu projektu zadania Webjob. Oto przykład tego pliku:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

Ten plik można edytować bezpośrednio, a program Visual Studio oferuje funkcję IntelliSense. Schemat pliku jest przechowywany w [ https://schemastore.org ](https://schemastore.org/schemas/json/webjob-publish-settings.json) i można je tam.  

### <a id="webjobslist"></a>webjobs-list.json
Gdy łączysz projektu zadań Webjob, włączone do projektu sieci web programu Visual Studio zapisuje Nazwa projektu zadania Webjob w *webjobs list.json* pliku w projekcie sieci web *właściwości* folderu. Lista może zawierać wiele projektów zadań Webjob, jak pokazano w poniższym przykładzie:

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

Ten plik można edytować bezpośrednio, a program Visual Studio oferuje funkcję IntelliSense. Schemat pliku jest przechowywany w [ https://schemastore.org ](https://schemastore.org/schemas/json/webjobs-list.json) i można je tam.

### <a id="deploy"></a>Wdrażanie projektu zadania Webjob
Projektu zadania Webjob, powiązane z projektem sieci web automatycznie wdraża się z projektem sieci web. Uzyskać informacje dotyczące wdrażania projektu sieci web, zobacz **prowadzi instrukcje** > **wdrażanie aplikacji** w nawigacji po lewej stronie.

Aby wdrożyć projekt zadania Webjob przez siebie, kliknij prawym przyciskiem myszy projekt w **Eksploratora rozwiązań** i kliknij przycisk **Publikuj jako zadanie Azure WebJob...** . 

![Publikuj jako zadanie WebJob platformy Azure](./media/webjobs-dotnet-deploy-vs/paw.png)

Dla niezależnych zadań WebJob, taka sama **publikowanie w sieci Web** kreatora, który jest używany dla projektów sieci web jest wyświetlana, ale mniej ustawień można zmienić.

## <a name="scheduling-a-triggered-webjob"></a>Planowanie wyzwolone zadania WebJob

Zadania Webjob używa *settings.job* plik, aby określić, gdy jest uruchomione zadanie WebJob. Użyj tego pliku, aby ustawić harmonogram wykonywania zadania WebJob. W poniższym przykładzie uruchamiane co godzinę od 9: 00 do 17:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Ten plik musi znajdować się w katalogu głównym folderu zadań Webjob, wzdłuż strona skryptów Twoje zadania WebJob, takich jak `wwwroot\app_data\jobs\triggered\{job name}` lub `wwwroot\app_data\jobs\continuous\{job name}`. Podczas wdrażania zadanie WebJob z poziomu programu Visual Studio, oznacz swoje `settings.job` właściwości jako pliku **Kopiuj Jeśli nowszy**. 

Gdy użytkownik [Tworzenie zadania WebJob w witrynie Azure portal](webjobs-create.md), zostanie utworzony plik settings.job.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>Wyrażenia CRON

Zadania Webjob używa tego samego wyrażenia CRON planowania jako wyzwalacz czasomierza w usłudze Azure Functions. Aby dowiedzieć się więcej na temat obsługi usługi CRON, zobacz [artykule informacyjnym na temat wyzwalaczy czasomierza](../azure-functions/functions-bindings-timer.md#cron-expressions).

### <a name="settingjob-reference"></a>Odwołanie Setting.job

Następujące ustawienia są obsługiwane przez usługi WebJobs:

| **Ustawienie** | **Typ**  | **Opis** |
| ----------- | --------- | --------------- |
| `is_in_place` | Wszyscy | Umożliwia zadanie zostanie uruchomione w miejscu bez najpierw kopiowane do folderu tymczasowego. Aby dowiedzieć się więcej, zobacz [katalogu roboczego zadań Webjob](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Ciągłe | Zadania Webjob można uruchamiać tylko w pojedynczym wystąpieniu, w przypadku skalowania do wewnątrz. Aby dowiedzieć się więcej, zobacz [ciągłe zadanie ustawiono jako pojedyncze](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Wyzwolone | Uruchamianie zadania WebJob, zgodnie z harmonogramem na podstawie CRON. Aby dowiedzieć się więcej, zobacz [artykule informacyjnym na temat wyzwalaczy czasomierza](../azure-functions/functions-bindings-timer.md#cron-expressions). |
| `stopping_wait_time`| Wszyscy | Zapewnia kontrolę nad zachowaniem zamknięcia. Aby dowiedzieć się więcej, zobacz [łagodne zamykanie](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat zestawu SDK usługi WebJobs](webjobs-sdk-how-to.md)
