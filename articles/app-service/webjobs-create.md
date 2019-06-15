---
title: Uruchamianie zadań w tle za pomocą zadań Webjob — usłudze Azure App Service
description: Dowiedz się, jak uruchamiać zadania w tle w usłudze Azure App Service web apps, aplikacje interfejsu API lub aplikacje mobilne za pomocą zadań Webjob.
services: app-service
documentationcenter: ''
author: ggailey777
manager: jeconnoc
editor: jimbe
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: glenga;msangapu;david.ebbo;suwatch;pbatum;naren.soni;
ms.custom: seodec18
ms.openlocfilehash: 0f2053e978b7c890f4e175515ed54f69694950c6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60833584"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Uruchamianie zadań w tle za pomocą zadań Webjob w usłudze Azure App Service

## <a name="overview"></a>Omówienie
Zadania Webjob są funkcją [usługi Azure App Service](https://docs.microsoft.com/azure/app-service/) , pozwala na uruchamianie programu lub skryptu w tym samym kontekście jako aplikację sieci web, aplikacji mobilnej lub aplikacji interfejsu API. Brak bez dodatkowych kosztów korzystania z zadań WebJobs.

> [!IMPORTANT]
> Zadania Webjob nie jest jeszcze obsługiwana dla usługi App Service w systemie Linux.

W tym artykule pokazano, jak wdrażać zadania Webjob przy użyciu [witryny Azure portal](https://portal.azure.com) można przekazać plik wykonywalny lub skrypt. Aby dowiedzieć się, jak tworzyć i wdrażać zadania Webjob przy użyciu programu Visual Studio, zobacz [wdrożyć WebJobs za pomocą programu Visual Studio](webjobs-dotnet-deploy-vs.md).

Zestawu Azure WebJobs SDK może służyć za pomocą zadań Webjob, aby uprościć wiele zadań programistycznych. Aby uzyskać więcej informacji, zobacz [co to jest zestaw SDK zadań Webjob](https://github.com/Azure/azure-webjobs-sdk/wiki).

Usługa Azure Functions udostępnia inny sposób uruchamiania programów i skryptów. Aby uzyskać porównanie funkcji i zadań Webjob, zobacz [wybór między usługi Flow, Logic Apps, Functions i WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Typy zadań WebJob

W poniższej tabeli opisano różnice między *ciągłe* i *wyzwalane* zadań Webjob.


|Ciągłe  |Wyzwolone  |
|---------|---------|
| Rozpoczyna się natychmiast, po utworzeniu zadania WebJob. Aby zakończenia zadania, programu lub skryptu zwykle działa wewnątrz pętli nieskończonej. Jeśli zadanie zakończy się, można uruchomić go ponownie. | Zostanie uruchomiony tylko wtedy, gdy jest to wyzwalane ręcznie lub zgodnie z harmonogramem. |
| Uruchamiany we wszystkich wystąpieniach, w których działa aplikacja sieci web. Zadania WebJob możesz opcjonalnie ograniczyć do pojedynczego wystąpienia. |Uruchamiany na pojedyncze wystąpienie, które platforma Azure wybiera dla równoważenia obciążenia.|
| Obsługuje zdalne debugowanie. | Nie obsługuje debugowania zdalnego.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="acceptablefiles"></a>Obsługiwane typy plików do skryptów lub programów

Obsługiwane są następujące typy plików:

* .cmd, .bat, .exe (przy użyciu polecenia Windows)
* .ps1 (przy użyciu programu PowerShell)
* SH (przy użyciu powłoki Bash)
* PHP (za pomocą języka PHP)
* PY (przy użyciu języka Python)
* js (przy użyciu środowiska Node.js)
* JAR (przy użyciu języka Java)

## <a name="CreateContinuous"></a> Tworzenie ciągłego zadania WebJob

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. W [witryny Azure portal](https://portal.azure.com), przejdź do **usługi App Service** strona aplikacji internetowej App Service, aplikacji interfejsu API lub aplikacji mobilnej.

2. Wybierz **WebJobs**.

   ![Wybierz zadania Webjob](./media/web-sites-create-web-jobs/select-webjobs.png)

2. W **WebJobs** wybierz opcję **Dodaj**.

    ![Strona zadania WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Użyj **Dodaj zadanie WebJob** ustawienia określone w tabeli.

   ![Dodaj stronę zadania WebJob](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Ustawienie      | Wartość przykładowa   | Opis  |
   | ------------ | ----------------- | ------------ |
   | **Nazwa** | myContinuousWebJob | Nazwa, która jest unikatowa w obrębie aplikacji usługi App Service. Musi rozpoczynać się literą lub cyfrą i nie może zawierać znaków specjalnych innych niż "-" i "_". |
   | **Przekazywanie pliku** | ConsoleApp.zip | A *zip* pliku, który zawiera plik wykonywalny lub skrypt, a także wszelkie pliki pomocnicze, potrzebne do uruchomienia programu lub skryptu. Obsługiwane typy plików plik wykonywalny lub skrypt są wymienione w [obsługiwane typy plików](#acceptablefiles) sekcji. |
   | **Typ** | Ciągłe | [Typy zadań WebJob](#webjob-types) są opisane we wcześniejszej części tego artykułu. |
   | **Skalowanie** | Z wieloma instancjami | Dostępne tylko w przypadku ciągłych zadań Webjob. Określa, czy program lub skrypt jest uruchamiany na wszystkich wystąpień lub tylko jednego wystąpienia. Opcja uruchamiania wielu wystąpień nie ma zastosowania do bezpłatna i współdzielona [warstw cenowych](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). | 

4. Kliknij przycisk **OK**.

   Nowe zadanie WebJob jest wyświetlany na **WebJobs** strony.

   ![Lista zadań Webjob](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Aby zatrzymać lub uruchomić ponownie ciągłe zadanie WebJob, kliknij prawym przyciskiem myszy zadanie WebJob na liście, a następnie kliknij przycisk **zatrzymać** lub **Start**.

    ![Zatrzymaj ciągłe zadanie WebJob](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="CreateOnDemand"></a> Tworzenie ręcznie wyzwolone zadania WebJob

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. W [witryny Azure portal](https://portal.azure.com), przejdź do **usługi App Service** strona aplikacji internetowej App Service, aplikacji interfejsu API lub aplikacji mobilnej.

2. Wybierz **WebJobs**.

   ![Wybierz zadania Webjob](./media/web-sites-create-web-jobs/select-webjobs.png)

2. W **WebJobs** wybierz opcję **Dodaj**.

    ![Strona zadania WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Użyj **Dodaj zadanie WebJob** ustawienia określone w tabeli.

   ![Dodaj stronę zadania WebJob](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Ustawienie      | Wartość przykładowa   | Opis  |
   | ------------ | ----------------- | ------------ |
   | **Nazwa** | myTriggeredWebJob | Nazwa, która jest unikatowa w obrębie aplikacji usługi App Service. Musi rozpoczynać się literą lub cyfrą i nie może zawierać znaków specjalnych innych niż "-" i "_".|
   | **Przekazywanie pliku** | ConsoleApp.zip | A *zip* pliku, który zawiera plik wykonywalny lub skrypt, a także wszelkie pliki pomocnicze, potrzebne do uruchomienia programu lub skryptu. Obsługiwane typy plików plik wykonywalny lub skrypt są wymienione w [obsługiwane typy plików](#acceptablefiles) sekcji. |
   | **Typ** | Wyzwolone | [Typy zadań WebJob](#webjob-types) są opisane we wcześniejszej części tego artykułu. |
   | **Wyzwalacze** | Ręcznie | |

4. Kliknij przycisk **OK**.

   Nowe zadanie WebJob jest wyświetlany na **WebJobs** strony.

   ![Lista zadań Webjob](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. Aby uruchomić zadania WebJob, kliknij prawym przyciskiem myszy jego nazwę na liście, a następnie kliknij przycisk **Uruchom**.
   
    ![Uruchamianie zadania WebJob](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="CreateScheduledCRON"></a> Tworzenie zaplanowanego zadania WebJob

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. W [witryny Azure portal](https://portal.azure.com), przejdź do **usługi App Service** strona aplikacji internetowej App Service, aplikacji interfejsu API lub aplikacji mobilnej.

2. Wybierz **WebJobs**.

   ![Wybierz zadania Webjob](./media/web-sites-create-web-jobs/select-webjobs.png)

2. W **WebJobs** wybierz opcję **Dodaj**.

   ![Strona zadania WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Użyj **Dodaj zadanie WebJob** ustawienia określone w tabeli.

   ![Dodaj stronę zadania WebJob](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Ustawienie      | Wartość przykładowa   | Opis  |
   | ------------ | ----------------- | ------------ |
   | **Nazwa** | myScheduledWebJob | Nazwa, która jest unikatowa w obrębie aplikacji usługi App Service. Musi rozpoczynać się literą lub cyfrą i nie może zawierać znaków specjalnych innych niż "-" i "_". |
   | **Przekazywanie pliku** | ConsoleApp.zip | A *zip* pliku, który zawiera plik wykonywalny lub skrypt, a także wszelkie pliki pomocnicze, potrzebne do uruchomienia programu lub skryptu. Obsługiwane typy plików plik wykonywalny lub skrypt są wymienione w [obsługiwane typy plików](#acceptablefiles) sekcji. |
   | **Typ** | Wyzwolone | [Typy zadań WebJob](#webjob-types) są opisane we wcześniejszej części tego artykułu. |
   | **Wyzwalacze** | Zaplanowane | Podczas planowania niezawodną pracę, należy włączyć funkcję zawsze włączone. Zawsze włączone jest dostępna tylko w języku Basic, Standard i warstw cenowych Premium.|
   | **Wyrażenie CRON** | 0 0/20 * * * * | [Wyrażenia CRON](#cron-expressions) są opisane w poniższej sekcji. |

4. Kliknij przycisk **OK**.

   Nowe zadanie WebJob jest wyświetlany na **WebJobs** strony.

   ![Lista zadań Webjob](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="cron-expressions"></a>Wyrażenia CRON

Możesz wprowadzić [wyrażenie CRON](../azure-functions/functions-bindings-timer.md#cron-expressions) w portalu lub zawierać `settings.job` pliku w katalogu głównym zadania WebJob *zip* pliku, jak w poniższym przykładzie:

```json
{
    "schedule": "0 */15 * * * *"
}
```

Aby dowiedzieć się więcej, zobacz [planowania wyzwolone zadania WebJob](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).

## <a name="ViewJobHistory"></a> Wyświetlanie historii zadań

1. Wybierz zadania WebJob, aby zobaczyć historię, a następnie wybierz **dzienniki** przycisku.
   
   ![Przycisk dzienników](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. W **szczegóły zadania WebJob** wybierz czas, aby wyświetlić szczegóły dla jednego przebiegu.
   
   ![Szczegóły zadania WebJob](./media/web-sites-create-web-jobs/webjobdetails.png)

3. W **szczegóły przebiegu zadania WebJob** wybierz opcję **Przełącz dane wyjściowe** tekst zawartość dziennika.
   
    ![Zadanie Web job szczegóły przebiegu](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Aby wyświetlić tekst danych wyjściowych w osobnym oknie przeglądarki, wybierz **Pobierz**. Aby pobrać sam tekst, kliknij prawym przyciskiem myszy **Pobierz** i użyj opcji przeglądarki, aby zapisać zawartość pliku.
   
5. Wybierz **WebJobs** link łączy do stron nadrzędnych u góry strony aby przejść do listy zadań Webjob.

    ![Zadania WebJob linków do stron nadrzędnych](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Lista zadań Webjob w pulpit nawigacyjny historii](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="NextSteps"></a> Następne kroki

Zestawu Azure WebJobs SDK może służyć za pomocą zadań Webjob, aby uprościć wiele zadań programistycznych. Aby uzyskać więcej informacji, zobacz [co to jest zestaw SDK zadań Webjob](https://github.com/Azure/azure-webjobs-sdk/wiki).
