---
title: Uruchom zadania w tle z zadań Webjob w usłudze Azure App Service
description: Dowiedz się, jak użyć zadania Webjob do uruchomienia zadania w tle w aplikacji sieci web w usłudze Azure App Service, aplikacje interfejsu API lub aplikacji mobilnych.
services: app-service
documentationcenter: ''
author: tdykstra
manager: erikre
editor: jimbe
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2017
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: f41cc83bfb18146e46e7d8501318acd68ce9c421
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Uruchom zadania w tle z zadań Webjob w usłudze Azure App Service

## <a name="overview"></a>Przegląd
Zadania Webjob jest funkcją [usłudze Azure App Service](https://docs.microsoft.com/azure/app-service/) który umożliwia uruchamianie programu lub skryptu w tym samym kontekście jako aplikacji sieci web, aplikacji mobilnej lub aplikacji interfejsu API. Nie ma żadnych dodatkowych kosztów, aby użyć zadań Webjob.

W tym artykule pokazano, jak wdrażanie przy użyciu zadań Webjob [portalu Azure](https://portal.azure.com) można przekazać plik wykonywalny lub skrypt. Aby uzyskać informacje o sposobie tworzenia i wdrażania zadań Webjob za pomocą programu Visual Studio, zobacz [wdrażanie przy użyciu programu Visual Studio zadań Webjob](websites-dotnet-deploy-webjobs.md).

Zestaw SDK zadań Webjob Azure pozwala z zadań Webjob uprościć wiele zadań programistycznych. Aby uzyskać więcej informacji, zobacz [co to jest zestaw SDK zadań Webjob](https://github.com/Azure/azure-webjobs-sdk/wiki).

Środowisko Azure Functions zapewnia uruchamianie programów i skryptów w inny sposób. Porównanie między zadań Webjob i funkcji, zobacz [wybór między przepływu, Logic Apps, funkcje i zadań Webjob](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Typy zadań WebJob

W poniższej tabeli opisano różnice między *ciągłego* i *wyzwalane* zadań Webjob.


|Ciągłe  |Wyzwolone  |
|---------|---------|
| Uruchamiany natychmiast po utworzeniu zadania WebJob. Aby zakończenie zadania, programów lub skryptów zazwyczaj wykonuje pracę w pętli nieskończonej. Jeśli zadanie zakończone, można uruchomić go ponownie. | Zostanie uruchomiony tylko wtedy, gdy wyzwalana ręcznie lub zgodnie z harmonogramem. |
| Działa we wszystkich wystąpieniach, których używa aplikacja sieci web. Opcjonalnie można ograniczyć zadania WebJob do pojedynczego wystąpienia. |Równoważenie obciążenia działa w pojedynczym wystąpieniu wybierające dla platformy Azure.|
| Obsługuje zdalne debugowanie. | Nie obsługuje debugowania zdalnego.|

> [!NOTE]
> Aplikacja sieci web może upłynął limit czasu po upływie 20 minut braku aktywności. Tylko żądania do witryny scm (wdrożenie) lub stron aplikacji sieci web w portalu zresetowanie czasomierza. Żądania do rzeczywistej lokacji nie Resetuj czasomierz. Jeśli aplikacja będzie działać ciągłej lub Włącz zaplanowane zadania Webjob, **zawsze na** celu zapewnienia niezawodnego uruchamiania zadań Webjob. Ta funkcja jest dostępna tylko w przypadku podstawowa, standardowa i Premium [warstw cenowych](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="acceptablefiles"></a>Obsługiwane typy plików dla skryptów lub programów

Obsługiwane są następujące typy plików:

* cmd, bat, .exe (przy użyciu cmd systemu Windows)
* ps1 (przy użyciu programu PowerShell)
* SH (przy użyciu Bash)
* PHP (za pomocą języka PHP)
* .PY (przy użyciu języka Python)
* js (przy użyciu środowiska Node.js)
* JAR (przy użyciu języka Java)

## <a name="CreateContinuous"></a> Utwórz ciągłe zadanie WebJob

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. W [portalu Azure](https://portal.azure.com), przejdź do **usługi aplikacji** aplikacji sieci web usługi aplikacji, w aplikacji interfejsu API lub w aplikacji mobilnej.

2. Wybierz **Webjob**.

   ![Wybierz zadania Webjob](./media/web-sites-create-web-jobs/select-webjobs.png)

2. W **Webjob** wybierz pozycję **Dodaj**.

    ![Strona zadania WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Użyj **dodać zadania WebJob** ustawienia określone w tabeli.

   ![Dodaj stronę zadania WebJob](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Ustawienie      | Wartość przykładowa   | Opis  |
   | ------------ | ----------------- | ------------ |
   | **Nazwa** | myContinuousWebJob | Nazwa, która jest unikatowa w obrębie aplikacji usługi aplikacji. Musi rozpoczynać się literą lub cyfrą i nie może zawierać znaków specjalnych innych niż "-" i "_". |
   | **Przekazywanie pliku** | ConsoleApp.zip | A *zip* plik, który zawiera plik wykonywalny lub skrypt, a także wszelkie pliki pomocnicze potrzebne do uruchomienia tego programu lub skryptu. Obsługiwane typy plików plik wykonywalny lub skrypt są wymienione w [obsługiwanych typów plików](#acceptablefiles) sekcji. |
   | **Typ** | Ciągłe | [Typy zadań WebJob](#webjob-types) są opisane w tym artykule. |
   | **Skalowanie** | Wieloma instancjami | Dostępna tylko w przypadku ciągłe zadania Webjob. Określa, czy program lub skrypt jest uruchamiany na wszystkich wystąpień lub tylko jedno wystąpienie. Opcję uruchamiania na wielu wystąpień nie dotyczą wolne lub Shared [warstw cenowych](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). | 

4. Kliknij przycisk **OK**.

   Na pojawi się nowe zadania WebJob **Webjob** strony.

   ![Lista zadań Webjob](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Aby zatrzymać lub uruchomić ponownie ciągłe zadanie WebJob, kliknij prawym przyciskiem myszy zadanie WebJob na liście, a następnie kliknij przycisk **zatrzymać** lub **Start**.

    ![Zatrzymaj ciągłe zadanie WebJob](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="CreateOnDemand"></a> Utwórz ręcznie wyzwalanych zadania WebJob

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. W [portalu Azure](https://portal.azure.com), przejdź do **usługi aplikacji** aplikacji sieci web usługi aplikacji, w aplikacji interfejsu API lub w aplikacji mobilnej.

2. Wybierz **Webjob**.

   ![Wybierz zadania Webjob](./media/web-sites-create-web-jobs/select-webjobs.png)

2. W **Webjob** wybierz pozycję **Dodaj**.

    ![Strona zadania WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Użyj **dodać zadania WebJob** ustawienia określone w tabeli.

   ![Dodaj stronę zadania WebJob](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Ustawienie      | Wartość przykładowa   | Opis  |
   | ------------ | ----------------- | ------------ |
   | **Nazwa** | myTriggeredWebJob | Nazwa, która jest unikatowa w obrębie aplikacji usługi aplikacji. Musi rozpoczynać się literą lub cyfrą i nie może zawierać znaków specjalnych innych niż "-" i "_".|
   | **Przekazywanie pliku** | ConsoleApp.zip | A *zip* plik, który zawiera plik wykonywalny lub skrypt, a także wszelkie pliki pomocnicze potrzebne do uruchomienia tego programu lub skryptu. Obsługiwane typy plików plik wykonywalny lub skrypt są wymienione w [obsługiwanych typów plików](#acceptablefiles) sekcji. |
   | **Typ** | Wyzwolone | [Typy zadań WebJob](#webjob-types) są opisane w tym artykule. |
   | **Wyzwalacze** | Ręczne | |

4. Kliknij przycisk **OK**.

   Na pojawi się nowe zadania WebJob **Webjob** strony.

   ![Lista zadań Webjob](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. Aby uruchomić zadania WebJob, kliknij prawym przyciskiem myszy jego nazwę na liście, a następnie kliknij przycisk **Uruchom**.
   
    ![Uruchamianie zadania WebJob](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="CreateScheduledCRON"></a> Utwórz zaplanowane zadania WebJob

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. W [portalu Azure](https://portal.azure.com), przejdź do **usługi aplikacji** aplikacji sieci web usługi aplikacji, w aplikacji interfejsu API lub w aplikacji mobilnej.

2. Wybierz **Webjob**.

   ![Wybierz zadania Webjob](./media/web-sites-create-web-jobs/select-webjobs.png)

2. W **Webjob** wybierz pozycję **Dodaj**.

   ![Strona zadania WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Użyj **dodać zadania WebJob** ustawienia określone w tabeli.

   ![Dodaj stronę zadania WebJob](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Ustawienie      | Wartość przykładowa   | Opis  |
   | ------------ | ----------------- | ------------ |
   | **Nazwa** | myScheduledWebJob | Nazwa, która jest unikatowa w obrębie aplikacji usługi aplikacji. Musi rozpoczynać się literą lub cyfrą i nie może zawierać znaków specjalnych innych niż "-" i "_". |
   | **Przekazywanie pliku** | ConsoleApp.zip | A *zip* plik, który zawiera plik wykonywalny lub skrypt, a także wszelkie pliki pomocnicze potrzebne do uruchomienia tego programu lub skryptu. Obsługiwane typy plików plik wykonywalny lub skrypt są wymienione w [obsługiwanych typów plików](#acceptablefiles) sekcji. |
   | **Typ** | Wyzwolone | [Typy zadań WebJob](#webjob-types) są opisane w tym artykule. |
   | **Wyzwalacze** | Zaplanowane | Podczas planowania działała prawidłowo, należy włączyć funkcję zawsze włączone. Zawsze włączone jest dostępna tylko w podstawowa, standardowa i Premium warstw cenowych.|
   | **Wyrażenie usługi CRON** | 0 0/20 * * * * | [Wyrażenia CRON](#cron-expressions) są opisane w poniższej sekcji. |

4. Kliknij przycisk **OK**.

   Na pojawi się nowe zadania WebJob **Webjob** strony.

   ![Lista zadań Webjob](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="cron-expressions"></a>Wyrażenia usługi CRON

Możesz wprowadzić [wyrażenie CRON](../azure-functions/functions-bindings-timer.md#cron-expressions) w portalu lub obejmują `settings.job` pliku w katalogu głównym WebJob *.zip* plików, jak w poniższym przykładzie:

```json
{
    "schedule": "0 */15 * * * *"
}
``` 

> [!NOTE]
> Podczas wdrażania zadanie WebJob z programu Visual Studio, oznacz Twojej `settings.job` właściwości jako pliku **Kopiuj, jeśli nowszy**.

## <a name="ViewJobHistory"></a> Wyświetlanie historii zadań

1. Wybierz zadania WebJob, aby wyświetlić historię, a następnie wybierz **dzienniki** przycisku.
   
   ![Przycisk Dzienniki](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. W **szczegóły zadania WebJob** wybierz czas, aby wyświetlić szczegóły dla pojedynczego uruchomienia.
   
   ![Szczegóły zadania WebJob](./media/web-sites-create-web-jobs/webjobdetails.png)

3. W **szczegóły uruchomienia zadania WebJob** wybierz pozycję **dane wyjściowe Przełącz** tekst zawartość dziennika.
   
    ![Szczegóły uruchomienia zadania sieci Web](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Aby wyświetlić tekstu wyjściowego w osobnym oknie przeglądarki, wybierz **Pobierz**. Aby pobrać samego tekstu, kliknij prawym przyciskiem myszy **Pobierz** i użyj opcji przeglądarki, aby zapisać zawartości pliku.
   
5. Wybierz **Webjob** łącze stron nadrzędnych w górnej części strony, aby przejść do listy zadań Webjob.

    ![Zadanie WebJob nawigacją](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Lista zadań Webjob na pulpicie nawigacyjnym historii](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="NextSteps"></a> Następne kroki

Zestaw SDK zadań Webjob Azure pozwala z zadań Webjob uprościć wiele zadań programistycznych. Aby uzyskać więcej informacji, zobacz [co to jest zestaw SDK zadań Webjob](https://github.com/Azure/azure-webjobs-sdk/wiki).
