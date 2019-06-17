---
title: Zbieranie danych usługi Log Analytics przy użyciu elementu runbook w usłudze Azure Automation | Dokumentacja firmy Microsoft
description: Samouczek krok po kroku, który przeprowadzi tworzenia elementu runbook w usłudze Azure Automation w celu zbierania danych do repozytorium na potrzeby analizy przez usługę Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/27/2017
ms.author: bwren
ms.openlocfilehash: 67378a5911e5bd83888342aa3773f7f5ed4ccf29
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60454199"
---
# <a name="collect-data-in-log-analytics-with-an-azure-automation-runbook"></a>Zbieranie danych w usłudze Log Analytics przy użyciu elementu runbook usługi Azure Automation

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Znacznej ilości danych w usłudze Log Analytics może zbierać z różnych źródeł, w tym [źródeł danych](../../azure-monitor/platform/agent-data-sources.md) na agentach, a także [dane zbierane z platformy Azure](../../azure-monitor/platform/collect-azure-metrics-logs.md). Istnieją scenariusze, chociaż wymagających zbierania danych, która nie jest dostępny za pośrednictwem tych standardowych źródeł. W takich przypadkach można użyć [interfejsu API modułu zbierającego dane HTTP](../../azure-monitor/platform/data-collector-api.md) można zapisać danych do usługi Log Analytics za pomocą dowolnego klienta interfejsu API REST. Typowe metodę w celu zbierania danych używa elementu runbook w usłudze Azure Automation.

Ten samouczek przedstawia proces tworzenia i Planowanie elementu runbook w usłudze Azure Automation w celu zapisania danych do usługi Log Analytics.

## <a name="prerequisites"></a>Wymagania wstępne
Ten scenariusz wymaga następujących zasobów, które są skonfigurowane w ramach subskrypcji platformy Azure. Jednocześnie może być utworzenie bezpłatnego konta.

- [Zaloguj się obszar roboczy usługi Analytics](../../azure-monitor/learn/quick-create-workspace.md).
- [Konto usługi Azure automation](../..//automation/automation-quickstart-create-account.md).

## <a name="overview-of-scenario"></a>Omówienie scenariusza
W tym samouczku przedstawiono tworzenie elementu runbook, który służy do zbierania informacji na temat zadań usługi Automation. Elementy Runbook w usłudze Azure Automation są implementowane przy użyciu programu PowerShell, dzięki czemu będzie zacząć przy pisaniu i testowaniu skrypt w edytorze usługi Azure Automation. Po upewnieniu się, że Trwa zbieranie danych wymaganych informacji, będzie zapisać te dane do usługi Log Analytics i sprawdź niestandardowego typu danych. Na koniec utworzymy harmonogram w celu uruchomienia elementu runbook w regularnych odstępach czasu.

> [!NOTE]
> Można skonfigurować w usłudze Azure Automation, aby wysłać informacje o zadaniu do usługi Log Analytics bez tego elementu runbook. W tym scenariuszu jest używany głównie do obsługi tego samouczka i zaleca się wysłanie danych do obszaru roboczego testu.

## <a name="1-install-data-collector-api-module"></a>1. Instalowanie modułu interfejsu API modułu zbierającego dane
Każdy [żądania z interfejsu API modułu zbierającego dane HTTP](../../azure-monitor/platform/data-collector-api.md#create-a-request) muszą być odpowiednio sformatowane i dołączyć nagłówek autoryzacji. Można to zrobić w elemencie runbook, ale może zmniejszyć ilość kodu wymaganą przy użyciu modułu, która upraszcza ten proces. Jest jeden moduł, którego można używać [modułu OMSIngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI) w galerii programu PowerShell.

Aby użyć [modułu](../../automation/automation-integration-modules.md) w elemencie runbook, musi być zainstalowany na Twoim koncie usługi Automation.  Każdego elementu runbook na tym samym koncie następnie można użyć funkcji w module. Nowy moduł można zainstalować, wybierając **zasoby** > **modułów** > **Dodaj moduł** na koncie usługi Automation.

Galeria programu PowerShell zapewnia jednak możliwość szybkiego wdrażania modułu bezpośrednio do konta usługi automation, aby można było używać tej opcji na potrzeby tego samouczka.

![Moduł OMSIngestionAPI](media/runbook-datacollect/OMSIngestionAPI.png)

1. Przejdź do [galerii programu PowerShell](https://www.powershellgallery.com/).
2. Wyszukaj **OMSIngestionAPI**.
3. Kliknij pozycję **wdrażanie w usłudze Azure Automation** przycisku.
4. Wybierz konto usługi automation, a następnie kliknij przycisk **OK** do zainstalowania modułu.

## <a name="2-create-automation-variables"></a>2. Tworzenie zmiennych automatyzacji
[Zmienne automatyzacji](../../automation/automation-variables.md) przechowywać wartości, które mogą być używane przez wszystkie elementy runbook na Twoim koncie usługi Automation. Dokonają elementów runbook bardziej elastyczne, umożliwiając zmiany tych wartości bez konieczności edytowania rzeczywistego elementu runbook. Każde żądanie z interfejsu API modułu zbierającego dane HTTP wymaga Identyfikatora i klucza obszaru roboczego usługi Log Analytics i zasoby zmiennych są idealnym rozwiązaniem do przechowywania tych informacji.

![Zmienne](media/runbook-datacollect/variables.png)

1. W witrynie Azure portal przejdź do swojego konta usługi Automation.
2. Wybierz **zmienne** w obszarze **udostępnione zasoby**.
2. Kliknij przycisk **Dodaj zmienną** i Utwórz dwie zmienne w poniższej tabeli.

| Właściwość | Wartość Identyfikatora obszaru roboczego | Wartość klucza obszaru roboczego |
|:--|:--|:--|
| Name (Nazwa) | WorkspaceId | WorkspaceKey |
| Typ | String | String |
| Wartość | Wklej identyfikator obszaru roboczego z obszaru roboczego usługi Log Analytics. | Wklej się przy użyciu podstawowy lub pomocniczy klucz obszaru roboczego usługi Log Analytics. |
| Zaszyfrowane | Nie | Yes |

## <a name="3-create-runbook"></a>3. Tworzenie elementu runbook

Usługa Azure Automation obejmuje redaktorem w portalu, w którym można edytować i przetestować element runbook. Istnieje możliwość używania edytora skryptów do pracy z [programu PowerShell bezpośrednio](../../automation/automation-edit-textual-runbook.md) lub [tworzenie graficznego elementu runbook](../../automation/automation-graphical-authoring-intro.md). W tym samouczku będą działać przy użyciu skryptu programu PowerShell.

![Edytowanie elementu Runbook](media/runbook-datacollect/edit-runbook.png)

1. Przejdź do konta usługi Automation.
2. Kliknij przycisk **elementów Runbook** > **Dodaj element runbook** > **Utwórz nowy element runbook**.
3. Nazwa elementu runbook wpisz **zadań w przypadku usługi Automation zbieranie**. Wybierz typ elementu runbook **PowerShell**.
4. Kliknij przycisk **Utwórz** do tworzenia elementu runbook i uruchomić edytora.
5. Skopiuj i wklej następujący kod do elementu runbook. Zobacz komentarze w skrypcie, aby uzyskać informacje na temat kodu.
    ```
    # Get information required for the automation account from parameter values when the runbook is started.
    Param
    (
        [Parameter(Mandatory = $True)]
        [string]$resourceGroupName,
        [Parameter(Mandatory = $True)]
        [string]$automationAccountName
    )
    
    # Authenticate to the Automation account using the Azure connection created when the Automation account was created.
    # Code copied from the runbook AzureAutomationTutorial.
    $connectionName = "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName
    Connect-AzAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    
    # Set the $VerbosePreference variable so that we get verbose output in test environment.
    $VerbosePreference = "Continue"
    
    # Get information required for Log Analytics workspace from Automation variables.
    $customerId = Get-AutomationVariable -Name 'WorkspaceID'
    $sharedKey = Get-AutomationVariable -Name 'WorkspaceKey'
    
    # Set the name of the record type.
    $logType = "AutomationJob"
    
    # Get the jobs from the past hour.
    $jobs = Get-AzAutomationJob -ResourceGroupName $resourceGroupName -AutomationAccountName $automationAccountName -StartTime (Get-Date).AddHours(-1)
    
    if ($jobs -ne $null) {
        # Convert the job data to json
        $body = $jobs | ConvertTo-Json
    
        # Write the body to verbose output so we can inspect it if verbose logging is on for the runbook.
        Write-Verbose $body
    
        # Send the data to Log Analytics.
        Send-OMSAPIIngestionFile -customerId $customerId -sharedKey $sharedKey -body $body -logType $logType -TimeStampField CreationTime
    }
    ```

## <a name="4-test-runbook"></a>4. Test elementu runbook
Usługa Azure Automation obejmuje środowisko do [przetestować element runbook](../../automation/automation-testing-runbook.md) przed jego opublikowaniem. Można sprawdzić dane zebrane przez element runbook i sprawdź, czy zapisuje do usługi Log Analytics zgodnie z oczekiwaniami przed jej opublikowaniem w środowisku produkcyjnym.

![Test elementu runbook](media/runbook-datacollect/test-runbook.png)

1. Kliknij przycisk **Zapisz** można zapisać elementu runbook.
1. Kliknij przycisk **okienko testowania** można otworzyć elementu runbook w środowisku testowym.
1. Ponieważ element runbook ma parametry, zostanie wyświetlony monit wpisz wartości dla nich. Wprowadź nazwę grupy zasobów i konta usługi automation, do którego zamierzasz zbierać informacje o zadaniu z.
1. Kliknij przycisk **Start** do uruchamiania elementu runbook.
1. Element runbook rozpocznie się ze stanem **kolejce** zanim przejdzie do **systemem**.
1. Element runbook powinien być wyświetlany pełne dane wyjściowe z zadania, zbierane w formacie json. Jeśli nie są wyświetlane żadne zadania, następnie mógł wystąpić żadnych zadań utworzonych w ramach konta usługi automation w ciągu ostatniej godziny. Spróbuj uruchomić każdego elementu runbook w ramach konta usługi automation, a następnie ponownie wykonaj test.
1. Upewnij się, że dane wyjściowe nie wyświetlane ewentualne błędy w poleceniu post do usługi Log Analytics. Należy mieć komunikat podobny do następującego.

    ![Wpis w danych wyjściowych](media/runbook-datacollect/post-output.png)

## <a name="5-verify-records-in-log-analytics"></a>5. Weryfikowanie rekordów w usłudze Log Analytics
Gdy element runbook została ukończona w teście i upewnieniu się, że pomyślnie Odebrano dane wyjściowe, można sprawdzić, czy rekordy zostały utworzone przy użyciu [przeszukiwania dzienników w usłudze Log Analytics](../../azure-monitor/log-query/log-query-overview.md).

![Dane wyjściowe dziennika](media/runbook-datacollect/log-output.png)

1. W witrynie Azure portal wybierz obszar roboczy usługi Log Analytics.
2. Kliknij pozycję **wyszukiwania w dzienniku**.
3. Wpisz następujące polecenie `Type=AutomationJob_CL` i kliknij przycisk Wyszukaj. Należy pamiętać, że typ rekordu zawiera _CL, który nie jest podany w skrypcie. Ten sufiks jest automatycznie dołączany do typu dziennika, aby wskazać, że jest to typ dziennika niestandardowego.
4. Powinien zostać wyświetlony co najmniej jeden rekord, zwrócone, wskazujący, że element runbook działa zgodnie z oczekiwaniami.

## <a name="6-publish-the-runbook"></a>6. Publikowanie elementu runbook
Po upewnieniu się, że element runbook działa poprawnie, należy ją opublikować, aby można było uruchomić w środowisku produkcyjnym. Można nadal edytować i testowanie elementu runbook bez modyfikowania opublikowanej wersji.

![Publikowanie elementu runbook](media/runbook-datacollect/publish-runbook.png)

1. Wróć do konta usługi automation.
2. Kliknij pozycję **elementów Runbook** i wybierz **zadań w przypadku usługi Automation zbieranie**.
3. Kliknij przycisk **Edytuj** i następnie **publikowania**.
4. Kliknij przycisk **tak** po wyświetleniu monitu o potwierdzenie nadpisanie poprzednio opublikowanej wersji.

## <a name="7-set-logging-options"></a>7. Ustaw opcje rejestrowania
Dla testu, można było wyświetlić [pełne dane wyjściowe](../../automation/automation-runbook-output-and-messages.md#message-streams) ponieważ wartość zmiennej $VerbosePreference w skrypcie. W środowisku produkcyjnym należy ustawić właściwości rejestrowania dla elementu runbook, jeśli chcesz wyświetlić pełne dane wyjściowe. Dla elementu runbook użytego w tym samouczku spowoduje to wyświetlenie danych json, są wysyłane do usługi Log Analytics.

![Rejestrowanie i śledzenie](media/runbook-datacollect/logging.png)

1. We właściwościach elementu runbook, zaznacz **rejestrowanie i śledzenie** w obszarze **ustawienia elementu Runbook**.
2. Zmień ustawienia **rejestrowania rekordów pełnych** do **na**.
3. Kliknij pozycję **Zapisz**.

## <a name="8-schedule-runbook"></a>8. Planowanie elementu runbook
Najczęstszym sposobem uruchamiania elementu runbook, który służy do zbierania danych monitorowania jest zaplanowane do automatycznego uruchamiania. Można to zrobić, tworząc [harmonogramu w usłudze Azure Automation](../../automation/automation-schedules.md) i dołączania ich do elementu runbook.

![Planowanie elementu runbook](media/runbook-datacollect/schedule-runbook.png)

1. We właściwościach elementu runbook, zaznacz **harmonogramy** w obszarze **zasobów**.
2. Kliknij przycisk **Dodaj harmonogram** > **powiązania harmonogramu z elementem runbook** > **Utwórz nowy harmonogram**.
5. Wpisz następujące wartości dla harmonogramu, a następnie kliknij przycisk **Utwórz**.

| Właściwość | Wartość |
|:--|:--|
| Name (Nazwa) | Co godzinę AutomationJobs |
| Rozpoczyna się | Wybierz, w dowolnym momencie co najmniej 5 minut późniejsza niż bieżąca godzina. |
| Cykl | Cykliczna |
| Powtarzaj co | 1 godzina |
| Ustaw czas wygaśnięcia | Nie |

Po utworzeniu harmonogramu, musisz podać wartości parametrów, które będą używane przy każdym tego harmonogramu uruchamiania elementu runbook.

1. Kliknij przycisk **skonfigurować parametry i parametrów uruchomieniowych**.
1. Podaj wartości dla swojej **ResourceGroupName** i **AutomationAccountName**.
1. Kliknij przycisk **OK**.

## <a name="9-verify-runbook-starts-on-schedule"></a>9. Sprawdź element runbook uruchamia zgodnie z harmonogramem
Za każdym razem, gdy element runbook jest uruchamiany, [tworzone jest zadanie](../../automation/automation-runbook-execution.md) i wszelkie dane wyjściowe rejestrowane. W rzeczywistości są one tych samych zadań, które zbiera elementu runbook. Aby sprawdzić, czy element runbook uruchamiany zgodnie z oczekiwaniami, sprawdzając zadania elementu runbook, po upływie czasu rozpoczęcia dla harmonogramu.

![Zadania](media/runbook-datacollect/jobs.png)

1. We właściwościach elementu runbook, zaznacz **zadania** w obszarze **zasobów**.
2. Powinieneś zobaczyć listę zadań każdorazowo, gdy element runbook został uruchomiony.
3. Kliknij jedno z zadań, aby wyświetlić jego szczegóły.
4. Kliknij pozycję **wszystkie dzienniki** do wyświetlania dzienników i danych wyjściowych z elementu runbook.
5. Przewiń w dół, aby odnaleźć wpisu, podobnie jak na poniższej ilustracji.<br>![Pełny](media/runbook-datacollect/verbose.png)
6. Kliknij ten wpis, aby wyświetlić dane szczegółowe json, który został wysłany do usługi Log Analytics.

## <a name="next-steps"></a>Kolejne kroki
- Użyj [Projektant widoków](../../azure-monitor/platform/view-designer.md) utworzyć widok, wyświetlanie danych zebranych w repozytorium usługi Log Analytics.
- Pakiet w elemencie runbook [rozwiązania do zarządzania](../../azure-monitor/insights/solutions-creating.md) do dystrybucji do klientów.
- Dowiedz się więcej o [usługi Log Analytics](https://docs.microsoft.com/azure/log-analytics/).
- Dowiedz się więcej o [usługi Azure Automation](https://docs.microsoft.com/azure/automation/).
- Dowiedz się więcej o [interfejsu API modułu zbierającego dane HTTP](../../azure-monitor/platform/data-collector-api.md).
