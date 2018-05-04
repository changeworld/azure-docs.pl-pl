---
title: Zbieranie danych analizy dzienników z elementem runbook automatyzacji Azure | Dokumentacja firmy Microsoft
description: Samouczek krok po kroku, który przedstawiono tworzenie elementu runbook automatyzacji Azure w celu zbierania danych analizy dzienników do repozytorium OMS do analizy.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: operations-management-suite
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2017
ms.author: bwren
ms.openlocfilehash: 0784e2317fbc98561b486547654ca27bb30e76c3
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="collect-data-in-log-analytics-with-an-azure-automation-runbook"></a>Zbieranie danych w analizy dzienników z elementu runbook usługi Automatyzacja Azure
Można zbierać znaczną ilość danych analizy dzienników z różnych źródeł w tym [źródeł danych](../log-analytics/log-analytics-data-sources.md) na agentach, a także [dane zbierane z platformy Azure](../log-analytics/log-analytics-azure-storage.md).  Chociaż wymagających zbierania danych, która nie jest dostępny za pośrednictwem tych źródeł standardowe są scenariusze.  W takich przypadkach można użyć [interfejsu API modułów zbierających dane HTTP](../log-analytics/log-analytics-data-collector-api.md) można zapisać danych do analizy dzienników za pomocą dowolnego klienta interfejsu API REST.  Częstą metodą do wykonania tej kolekcji danych używa elementu runbook automatyzacji Azure.   

Ten samouczek przeprowadzi Cię przez proces tworzenia i Planowanie elementu runbook automatyzacji Azure można zapisać danych do analizy dzienników.


## <a name="prerequisites"></a>Wymagania wstępne
Ten scenariusz wymaga następujących zasobów, które są skonfigurowane w ramach subskrypcji platformy Azure.  Jednocześnie może być bezpłatne konto.

- [Obszar roboczy analizy dziennika](../log-analytics/log-analytics-get-started.md).
- [Konto usługi Automatyzacja Azure](../automation/automation-offering-get-started.md).

## <a name="overview-of-scenario"></a>Omówienie scenariusza
W tym samouczku przedstawiono tworzenie elementu runbook, który służy do zbierania informacji o automatyzacji zadań.  Elementy Runbook automatyzacji Azure są implementowane przy użyciu programu PowerShell, więc będzie rozpoczyna się od pisania i testowania skrypt w edytorze usługi Automatyzacja Azure.  Po upewnieniu się, że zbierasz wymagane informacje, będzie zapisu danych do analizy dzienników i sprawdź niestandardowego typu danych.  Na koniec utworzysz harmonogram uruchamiania elementu runbook w regularnych odstępach czasu.

> [!NOTE]
> Można skonfigurować do wysyłania informacji o zadania do analizy dzienników bez tego elementu runbook usługi Automatyzacja Azure.  Ten scenariusz jest głównie używana do obsługi tego samouczka i zaleca się wysłanie danych do obszaru roboczego testu.  


## <a name="1-install-data-collector-api-module"></a>1. Zainstaluj moduł interfejsu API modułów zbierających dane
Każdy [żądania z interfejsu API modułów zbierających dane HTTP](../log-analytics/log-analytics-data-collector-api.md#create-a-request) musi być prawidłowo sformatowany i Dołącz nagłówek autoryzacji.  Można to zrobić w elemencie runbook, ale zmniejsza ilość kodu wymagane przy użyciu modułu, który ułatwia ten proces.  Jest jeden moduł, którego można używać [modułu OMSIngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI) w galerii programu PowerShell.

Aby użyć [modułu](../automation/automation-integration-modules.md) w elemencie runbook, musi być zainstalowany na Twoim koncie automatyzacji.  Dowolnym elemencie runbook na tym samym koncie można następnie użyć funkcji w module.  Można zainstalować nowy moduł, wybierając **zasoby** > **modułów** > **dodać moduł** na Twoim koncie automatyzacji.  

Galerii programu PowerShell zapewnia jednak szybkie możliwość wdrożenia modułu bezpośrednio na Twoje konto usługi Automatyzacja, dzięki czemu można użyć tej opcji na potrzeby tego samouczka.  

![Moduł OMSIngestionAPI](media/operations-management-suite-runbook-datacollect/OMSIngestionAPI.png)

1. Przejdź do [galerii programu PowerShell](https://www.powershellgallery.com/).
2. Wyszukaj **OMSIngestionAPI**.
3. Polecenie **Wdróż automatyzacji Azure** przycisku.
4. Wybierz konto automatyzacji, a następnie kliknij przycisk **OK** do zainstalowania modułu.


## <a name="2-create-automation-variables"></a>2. Utwórz zmienne automatyzacji
[Zmienne automatyzacji](..\automation\automation-variables.md) przechowywania wartości, które mogą być używane przez wszystkie elementy runbook na Twoim koncie automatyzacji.  Tworzenia elementów runbook bardziej elastyczne, umożliwiając Zmień wartości tych bez konieczności edytowania rzeczywistego elementu runbook. Każde żądanie z interfejsu API modułów zbierających dane HTTP wymaga identyfikator i klucz obszaru roboczego OMS i zasoby zmiennej idealnie nadają się do przechowywania tych informacji.  

![Zmienne](media/operations-management-suite-runbook-datacollect/variables.png)

1. W portalu Azure przejdź do swojego konta automatyzacji.
2. Wybierz **zmienne** w obszarze **zasobów udostępnionych**.
2. Kliknij przycisk **dodać zmienną** i Utwórz dwie zmienne w poniższej tabeli.

| Właściwość | Wartość Identyfikatora obszaru roboczego | Wartość klucza obszaru roboczego |
|:--|:--|:--|
| Name (Nazwa) | WorkspaceId | WorkspaceKey |
| Typ | Ciąg | Ciąg |
| Wartość | Wklej identyfikator obszaru roboczego z obszaru roboczego analizy dzienników. | Wklej za pomocą podstawowej lub dodatkowej klucz obszaru roboczego analizy dzienników. |
| Zaszyfrowane | Nie | Yes |



## <a name="3-create-runbook"></a>3. Tworzenie elementu runbook

Automatyzacja Azure ma edytora w portalu, w którym można edytować i przetestuj element runbook.  Istnieje możliwość użycia Edytor skryptów do pracy z [PowerShell bezpośrednio](../automation/automation-edit-textual-runbook.md) lub [utworzyć graficzny element runbook](../automation/automation-graphical-authoring-intro.md).  W tym samouczku będzie działać z skrypt programu PowerShell. 

![Edytowanie elementu Runbook](media/operations-management-suite-runbook-datacollect/edit-runbook.png)

1. Przejdź do swojego konta automatyzacji.  
2. Kliknij przycisk **Runbook** > **Dodaj element runbook** > **Utwórz nowy element runbook**.
3. Wpisz nazwę elementu runbook **zbieranie automatyzacji zadania**.  Wybierz typ elementu runbook **PowerShell**.
4. Kliknij przycisk **Utwórz** do tworzenia elementu runbook i uruchomić edytora.
5. Skopiuj i wklej następujący kod do elementu runbook.  Zapoznaj się z komentarzami w skrypcie wyjaśnienie kodu.
    
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
        Connect-AzureRmAccount `
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
        $jobs = Get-AzureRmAutomationJob -ResourceGroupName $resourceGroupName -AutomationAccountName $automationAccountName -StartTime (Get-Date).AddHours(-1)
        
        if ($jobs -ne $null) {
            # Convert the job data to json
            $body = $jobs | ConvertTo-Json
        
            # Write the body to verbose output so we can inspect it if verbose logging is on for the runbook.
            Write-Verbose $body
        
            # Send the data to Log Analytics.
            Send-OMSAPIIngestionFile -customerId $customerId -sharedKey $sharedKey -body $body -logType $logType -TimeStampField CreationTime
        }


## <a name="4-test-runbook"></a>4. Testowego elementu runbook
Automatyzacja Azure zawiera środowisko do [przetestuj element runbook](../automation/automation-testing-runbook.md) przed opublikowaniem.  Można sprawdzić danych zbieranych przez element runbook i sprawdź, czy zapisuje do analizy dzienników zgodnie z oczekiwaniami przed opublikowaniem go do produkcji. 
 
![Testowego elementu runbook](media/operations-management-suite-runbook-datacollect/test-runbook.png)

6. Kliknij przycisk **zapisać** można zapisać elementu runbook.
1. Kliknij przycisk **okienku testu** można otworzyć elementu runbook w środowisku testowym.
3. Ponieważ element runbook ma parametry, zostanie wyświetlony monit o wprowadź wartości dla nich.  Wprowadź nazwę grupy zasobów i automatyzację konta, którego będziesz zbierać informacje o zadaniu.
4. Kliknij przycisk **Start** do uruchomienia elementu runbook.
3. Element runbook rozpocznie się ze stanem **w kolejce** przed trafia do **systemem**.  
3. Element runbook powinien być wyświetlany pełne dane wyjściowe z zadania zbierane w formacie json.  Jeśli nie są wyświetlane żadne zadania, następnie mogły wystąpić żadne zadania utworzone w ramach konta usługi Automatyzacja w ciągu ostatniej godziny.  Spróbuj uruchomić przy użyciu konta automatyzacji dowolnym elemencie runbook, a następnie wykonaj test ponownie.
4. Upewnij się, czy dane wyjściowe nie wyświetla wszelkie błędy w poleceniu post do analizy dzienników.  Należy dobrze komunikat podobny do następującego.

    ![Dane wyjściowe POST](media/operations-management-suite-runbook-datacollect/post-output.png)

## <a name="5-verify-records-in-log-analytics"></a>5. Weryfikuj rekordy w analizy dzienników
Po elementu runbook została ukończona w teście i upewnieniu się, że dane wyjściowe został pomyślnie odebrany, można sprawdzić, czy rekordy zostały utworzone przy użyciu [wyszukiwania dziennika w analizy dzienników](../log-analytics/log-analytics-log-searches.md).

![Wpisu w Dzienniku](media/operations-management-suite-runbook-datacollect/log-output.png)

1. W portalu Azure wybierz obszar roboczy analizy dzienników.
2. Polecenie **dziennika wyszukiwania**.
3. Wpisz następujące polecenie `Type=AutomationJob_CL` i kliknij przycisk wyszukiwania. Należy pamiętać, że typ rekordu zawiera _CL, który nie jest podany w skrypcie.  Ten sufiks jest automatycznie dołączane do typ dziennika, aby wskazać, że typ dziennik niestandardowy.
4. Powinny pojawić się jeden lub więcej rekordów zwrócił wskazujący, że element runbook działa zgodnie z oczekiwaniami.


## <a name="6-publish-the-runbook"></a>6. Publikowanie elementu runbook
Po upewnieniu się, że element runbook działa poprawnie, należy opublikować go, dlatego może być uruchomiony w środowisku produkcyjnym.  Można nadal edytować i przetestowania elementu runbook bez modyfikowania opublikowanej wersji.  

![Publikowanie elementu runbook](media/operations-management-suite-runbook-datacollect/publish-runbook.png)

1. Wróć do swojego konta automatyzacji.
2. Polecenie **Runbook** i wybierz **zbieranie automatyzacji zadania**.
3. Kliknij przycisk **Edytuj** , a następnie **publikowania**.
4. Kliknij przycisk **tak** po otrzymaniu monitu, aby sprawdzić, czy chcesz zastąpić poprzednio opublikowanej wersji.

## <a name="7-set-logging-options"></a>7. Ustaw opcje rejestrowania 
Dla testu, można było wyświetlić [pełne dane wyjściowe](../automation/automation-runbook-output-and-messages.md#message-streams) ponieważ wartość zmiennej $VerbosePreference w skrypcie.  W środowisku produkcyjnym należy ustawić właściwości rejestrowania dla elementu runbook, jeśli chcesz wyświetlić pełne dane wyjściowe.  Dla elementu runbook używane w tym samouczku spowoduje to wyświetlenie danych json są wysyłane do analizy dzienników.

![Rejestrowanie i śledzenie](media/operations-management-suite-runbook-datacollect/logging.png)

1. We właściwościach elementu runbook wybierz **rejestrowania i śledzenia** w obszarze **ustawienia elementu Runbook**.
2. Zmień ustawienia **rejestrowania rekordów pełnych** do **na**.
3. Kliknij pozycję **Zapisz**.

## <a name="8-schedule-runbook"></a>8. Planowanie elementu runbook
Najczęściej uruchomienia elementu runbook, który służy do zbierania danych monitorowania jest można zaplanować automatyczne uruchamianie.  Można to zrobić, tworząc [Harmonogram automatyzacji Azure](../automation/automation-schedules.md) i dołączenie go do elementu runbook.

![Planowanie elementu runbook](media/operations-management-suite-runbook-datacollect/schedule-runbook.png)

1. We właściwościach elementu runbook, zaznacz **harmonogramy** w obszarze **zasobów**.
2. Kliknij przycisk **Dodaj harmonogram** > **Połącz harmonogram z elementem runbook** > **Utwórz nowy harmonogram**.
5. Wpisz następujące wartości harmonogramu i kliknij przycisk **Utwórz**.

| Właściwość | Wartość |
|:--|:--|
| Name (Nazwa) | Co godzinę AutomationJobs |
| Początek | Wybierz, w przypadku co najmniej pięć minut późniejsza niż bieżąca godzina. |
| Cykl | Cyklicznie |
| Powtarzaj co | 1 godzina |
| Wygaśnięcie zestawu | Nie |

Po utworzeniu harmonogramu, należy określić wartości parametrów, które będzie używane przy każdym tego harmonogramu uruchamiania elementu runbook.

6. Kliknij przycisk **Skonfiguruj parametry i parametry uruchomieniowe**.
7. Podaj wartości dla Twojego **ResourceGroupName** i **AutomationAccountName**.
8. Kliknij przycisk **OK**. 

## <a name="9-verify-runbook-starts-on-schedule"></a>9. Sprawdź element runbook uruchamia zgodnie z harmonogramem
Zawsze, gdy element runbook jest uruchamiany, [tworzone jest zadanie](../automation/automation-runbook-execution.md) i wszelkie dane wyjściowe rejestrowane.  W rzeczywistości są tych samych zadań, które zbiera elementu runbook.  Możesz sprawdzić, czy element runbook uruchamiany zgodnie z oczekiwaniami po upływie czas rozpoczęcia harmonogramu, sprawdzając zadania dla elementu runbook.

![Zadania](media/operations-management-suite-runbook-datacollect/jobs.png)

1. We właściwościach elementu runbook, zaznacz **zadania** w obszarze **zasobów**.
2. Powinna zostać wyświetlona lista zadań dla każdej godziny uruchomienia elementu runbook.
3. Kliknij jeden z zadań, aby wyświetlić jego szczegóły.
4. Polecenie **wszystkie dzienniki** Sprawdź dzienniki i dane wyjściowe z elementu runbook.
5. Przewiń w dół, aby odnaleźć wpisu, podobnie jak na poniższym obrazie.<br>![Pełne](media/operations-management-suite-runbook-datacollect/verbose.png)
6. Kliknij ten wpis, aby wyświetlić dane szczegółowe json, który został wysłany do analizy dzienników.



## <a name="next-steps"></a>Kolejne kroki
- Użyj [Widok projektanta](../log-analytics/log-analytics-view-designer.md) utworzyć widok wyświetlanie danych zebranych w repozytorium analizy dzienników.
- Pakiet w elemencie runbook [rozwiązania do zarządzania](operations-management-suite-solutions-creating.md) do dystrybucji do klientów.
- Dowiedz się więcej o [analizy dzienników](https://docs.microsoft.com/azure/log-analytics/).
- Dowiedz się więcej o [usługi Automatyzacja Azure](https://docs.microsoft.com/azure/automation/).
- Dowiedz się więcej o [interfejsu API modułów zbierających dane HTTP](../log-analytics/log-analytics-data-collector-api.md).
