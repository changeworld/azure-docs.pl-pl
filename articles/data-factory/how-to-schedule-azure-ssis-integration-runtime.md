---
title: Planowanie środowiska uruchomieniowego integracji Azure SSIS | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia harmonogramu uruchamiania i zatrzymywania ze środowiska uruchomieniowego integracji Azure SSIS przy użyciu usługi Automatyzacja Azure i fabryki danych.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 06/01/2018
ms.author: douglasl
ms.openlocfilehash: 8eeed91da3942d00bbab17a2dffc4b4e888a6f70
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34725112"
---
# <a name="how-to-schedule-starting-and-stopping-of-an-azure-ssis-integration-runtime"></a>Jak można zaplanować uruchamianie i zatrzymywanie środowiska uruchomieniowego integracji Azure SSIS 
W tym artykule opisano sposób tworzenia harmonogramu uruchamiania i zatrzymywania Azure SSIS integrację środowiska uruchomieniowego (IR) przy użyciu usługi Automatyzacja Azure i fabryki danych Azure. Uruchomiona środowiska uruchomieniowego integracji SSIS Azure (SQL Server Integration Services) (IR) ma opłat skojarzonych z nim. W związku z tym który chcesz uruchomić IR tylko wtedy, gdy jest to wymagane do uruchamiania pakietów SSIS na platformie Azure i zatrzymaj ją, gdy nie będzie potrzebny. Można użyć interfejsu użytkownika z fabryki danych lub Azure PowerShell do [ręcznie uruchomić lub zatrzymać IR SSIS Azure](manage-azure-ssis-integration-runtime.md)).

Na przykład można utworzyć działania sieci Web z elementów webhook do elementu runbook usługi Automatyzacja Azure w programie PowerShell i łańcucha działanie wykonanie pakietu usług SSIS między nimi. Działania w sieci Web można uruchomić i zatrzymać Twojej IR Azure SSIS, tylko w czasie przed i po uruchomieniu pakietu. Aby uzyskać więcej informacji na temat działania wykonanie pakietu usług SSIS zobacz [uruchamiania pakietów SSIS za pomocą działania SSIS w fabryce danych Azure](how-to-invoke-ssis-package-ssis-activity.md).

## <a name="overview-of-the-steps"></a>Omówienie kroków procesu

Poniżej przedstawiono ogólne kroki opisane w tym artykule:

1. **Tworzenie i testowanie elementu runbook usługi Automatyzacja Azure.** W tym kroku tworzenia elementu runbook programu PowerShell ze skryptem, który uruchomienia lub zatrzymania podczerwieni Azure SSIS. Następnie testu elementu runbook w scenariuszach zarówno uruchamianie i ZATRZYMYWANIE i upewnij się, że IR uruchomienia lub zatrzymania. 
2. **Utwórz dwa harmonogramy dla elementu runbook.** Pierwszy harmonogramu można skonfigurować elementu runbook z START jako wykonać operację. Drugi harmonogramu należy skonfigurować element runbook z ZATRZYMANA jako operacji. Dla obu harmonogramy należy określić okresach, w którym element runbook jest uruchomiony. Na przykład można zaplanować pierwsza z nich do uruchomienia na 8 AM codziennie i drugi na 23: 00 codziennie. Pierwszy element runbook działa, uruchamia podczerwieni Azure SSIS. Po uruchomieniu drugiego elementu runbook przestaje podczerwieni Azure SSIS. 
3. **Utwórz dwa elementów webhook dla elementu runbook**, jeden dla operacji START, a drugą dla operacji ZATRZYMANIA. Adresy URL tych elementów webhook można użyć podczas konfigurowania sieci web działania w potoku fabryki danych. 
4. **Utworzyć potok fabryki danych**. Potok, w którym można utworzyć składa się z trzech działań. Pierwszy **Web** działania wywołuje pierwszego elementu webhook uruchomić podczerwieni Azure SSIS. **Procedury składowanej** działanie uruchamia skrypt SQL, który uruchamia pakiet SSIS. Drugi **Web** działania zatrzymuje podczerwieni Azure SSIS. Aby uzyskać więcej informacji na temat wywoływania pakietów SSIS z potoku fabryki danych za pomocą działania procedury składowanej, zobacz [wywołania pakietów SSIS](how-to-invoke-ssis-package-stored-procedure-activity.md). Następnie można utworzyć wyzwalacza harmonogramu, można zaplanować potoku do uruchamiania w okresach, które określisz.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [pakietów SSIS wywołać przy użyciu działania procedury składowanej w wersji 1](v1/how-to-invoke-ssis-package-stored-procedure-activity.md).

 
## <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie zostało już udostępniane środowiska uruchomieniowego integracji Azure SSIS, zainicjujesz go przy zgodnie z instrukcjami wyświetlanymi w [samouczek](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-test-an-azure-automation-runbook"></a>Tworzenie i testowanie elementu runbook usługi Automatyzacja Azure
W tej sekcji należy wykonać następujące kroki: 

1. Utwórz konto usługi Automatyzacja Azure.
2. Utworzenie elementu runbook programu PowerShell w ramach konta usługi Automatyzacja Azure. Skrypt programu PowerShell skojarzono element runbook uruchamia lub zatrzymuje IR SSIS Azure oparte na polecenie, które można określić parametru operacji. 
3. Testowanie elementu runbook w obu start i Zatrzymaj scenariuszami, aby potwierdzić, że działa. 

### <a name="create-an-azure-automation-account"></a>Tworzenie konta usługi Azure Automation
Jeśli nie masz konta usługi Automatyzacja Azure, utwórz je zgodnie z instrukcjami w tym kroku. Aby uzyskać szczegółowe instrukcje, zobacz [utworzyć konto usługi Automatyzacja Azure](../automation/automation-quickstart-create-account.md). W ramach tego kroku, możesz utworzyć **Uruchom jako platformy Azure** konta (podmiotu zabezpieczeń w usłudze Azure Active Directory service) i dodaj go do **współautora** roli subskrypcji platformy Azure. Upewnij się, że jest taki sam jak subskrypcji, która zawiera fabryki danych, która ma podczerwieni Azure SSIS. Automatyzacja Azure używa tego konta do uwierzytelniania w usłudze Azure Resource Manager i działają na Twoich zasobów. 

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
2. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).    
3. Wybierz **nowy** w menu po lewej stronie wybierz **monitorowanie i zarządzanie**i wybierz **automatyzacji**. 

    ![Nowy -> Monitorowanie i zarządzanie -> automatyzacji](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
2. W **Dodaj konto automatyzacji** okna, wykonaj następujące czynności: 

    1. Określ **nazwa** dla konta automatyzacji. 
    2. Wybierz **subskrypcji** mający fabryka danych z usługi Azure SSIS IR. 
    3. Dla **grupy zasobów**, wybierz pozycję **Utwórz nowy** Aby utworzyć nową grupę zasobów lub wybierz **Użyj istniejącego** wybierz istniejącą grupę zasobów. 
    4. Wybierz **lokalizacji** dla konta automatyzacji. 
    5. Upewnij się, że **utworzyć Uruchom jako konta** ustawiono **tak**. Nazwy głównej usługi jest tworzony w usłudze Azure Active Directory. Jest ona dodawana do **współautora** roli subskrypcji platformy Azure
    6. Wybierz **Przypnij do pulpitu nawigacyjnego** umożliwiającego wyświetlanie na pulpicie nawigacyjnym portalu. 
    7. Wybierz pozycję **Utwórz**. 

        ![Nowy -> Monitorowanie i zarządzanie -> automatyzacji](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
3. Zostanie wyświetlony **stan wdrożenia** na pulpicie nawigacyjnym i powiadomień. 
    
    ![Wdrażanie automatyzacji](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
4. Zostanie wyświetlona strona główna dla konta automatyzacji po utworzeniu pomyślnie. 

    ![Strona główna automatyzacji](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-data-factory-modules"></a>Zaimportuj moduły fabryki danych

1. Wybierz **modułów** w **UDOSTĘPNIONE zasoby** sekcji w menu po lewej stronie, a następnie sprawdź, czy masz **AzureRM.Profile** i **AzureRM.DataFactoryV2** na liście modułów.

    ![Sprawdź wymagane moduły](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Przejdź do galerii programu PowerShell dla [modułu AzureRM.DataFactoryV2](https://www.powershellgallery.com/packages/AzureRM.DataFactoryV2/), wybierz pozycję **Wdróż automatyzacji Azure**, wybierz konto automatyzacji, a następnie wybierz **OK**. Przejdź wstecz, aby wyświetlić **modułów** w **UDOSTĘPNIONE zasoby** sekcji w menu po lewej stronie, a następnie poczekaj na wyświetlenie **stan** z **AzureRM.DataFactoryV2** zmiana modułu **dostępne**.

    ![Sprawdź modułu fabryki danych](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Przejdź do galerii programu PowerShell dla [modułu AzureRM.Profile](https://www.powershellgallery.com/packages/AzureRM.profile/), kliknij **Wdróż automatyzacji Azure**, wybierz konto automatyzacji, a następnie wybierz **OK**. Przejdź wstecz, aby wyświetlić **modułów** w **UDOSTĘPNIONE zasoby** sekcji w menu po lewej stronie, a następnie poczekaj na wyświetlenie **stan** z **AzureRM.Profile**zmiana modułu **dostępne**.

    ![Sprawdź modułu profilu](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-a-powershell-runbook"></a>Tworzenie elementu runbook programu PowerShell
Poniższa procedura zawiera kroki tworzenia elementu runbook programu PowerShell. Skrypt skojarzony z elementem runbook albo rozpoczyna/zatrzymuje IR SSIS Azure oparte na określone dla polecenia **operacji** parametru. W tej sekcji podano wszystkich szczegółów tworzenia elementu runbook. Aby uzyskać więcej informacji, zobacz [utworzyć element runbook](../automation/automation-quickstart-create-runbook.md) artykułu.

1. Przełącz się do **Runbook** , a następnie wybierz **+ Dodaj element runbook** z paska narzędzi. 

    ![Dodawanie przycisku elementu runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
2. Wybierz **Utwórz nowy element runbook**i wykonaj następujące czynności: 

    1. Aby uzyskać **nazwa**, typ **StartStopAzureSsisRuntime**.
    2. Aby uzyskać **typ elementu Runbook**, wybierz pozycję **PowerShell**.
    3. Wybierz pozycję **Utwórz**.
    
        ![Dodawanie przycisku elementu runbook](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
3. Skopiuj/Wklej poniższy skrypt do okna Skrypt runbook. Zapisz, a następnie opublikować element runbook za pomocą **zapisać** i **publikowania** przycisków na pasku narzędzi. 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Connect-AzureRmAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

    ![Edytuj element runbook programu PowerShell](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
5. Testowanie elementu runbook, wybierając **Start** przycisk na pasku narzędzi. 

    ![Element runbook przycisk Start](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
6. W **Uruchom element Runbook** okna, wykonaj następujące czynności: 

    1. Aby uzyskać **Nazwa grupy zasobów**, wprowadź nazwę grupy zasobów z fabryki danych, która ma podczerwieni Azure SSIS. 
    2. Aby uzyskać **nazwa FABRYKI danych**, wprowadź nazwę fabryki danych, która ma podczerwieni Azure SSIS. 
    3. Aby uzyskać **AZURESSISNAME**, wprowadź nazwę podczerwieni Azure SSIS. 
    4. Aby uzyskać **operacji**, wprowadź **START**. 
    5. Kliknij przycisk **OK**.  

        ![Uruchom okno elementu runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
7. W oknie zadania wybierz **dane wyjściowe** kafelka. W oknie danych wyjściowych zadania, poczekaj, aż zostanie wyświetlony komunikat **### Ukończono ###** po **### uruchamianie ###**. Uruchamianie IR SSIS Azure wymaga około 20 minut. Zamknij **zadania** okno i wrócić do **Runbook** okna.

    ![Azure IR SSIS — uruchomiona](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
8.  Powtórz dwa poprzednie kroki, ale przy użyciu **ZATRZYMAĆ** jako wartość **operacji**. Ponownie uruchom element runbook, wybierając **Start** przycisk na pasku narzędzi. Określ nazwę grupy zasobów, nazwa fabryki danych i nazwę Azure SSIS IR. Aby uzyskać **operacji**, wprowadź **ZATRZYMAĆ**. 

    W oknie danych wyjściowych zadania, poczekaj, aż zostanie wyświetlony komunikat **### Ukończono ###** po **### zatrzymywanie ###**. Zatrzymywanie Azure SSIS IR nie przyjmuje tak długo, jak uruchamianie podczerwieni Azure SSIS. Zamknij **zadania** okno i wrócić do **Runbook** okna.

## <a name="create-schedules-for-the-runbook-to-startstop-the-azure-ssis-ir"></a>Tworzenie harmonogramów dla elementu runbook w celu uruchamiania/zatrzymywania Azure SSIS IR
W poprzedniej sekcji utworzyć element runbook usługi Automatyzacja Azure, który może uruchomić lub zatrzymać podczerwieni Azure SSIS. W tej sekcji utworzysz dwa harmonogramy dla elementu runbook. Podczas konfigurowania pierwszego harmonogramu, należy określić rozpoczęcia dla parametru operacji. Podobnie podczas konfigurowania harmonogramu drugiego, możesz określić ZATRZYMANIA dla tej operacji. Aby uzyskać szczegółowe instrukcje dotyczące tworzenia harmonogramów, zobacz [Utwórz harmonogram](../automation/automation-schedules.md#creating-a-schedule).

1. W **Runbook** wybierz **harmonogramy**i wybierz **+ Dodaj harmonogram** na pasku narzędzi. 

    ![Azure IR SSIS — uruchomiona](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
2. W **Runbook harmonogram** okna, wykonaj następujące czynności: 

    1. Wybierz **Połącz harmonogram z elementem runbook**. 
    2. Wybierz **Utwórz nowy harmonogram**.
    3. W **nowy harmonogram** wpisz **Start IR codziennie** dla **nazwa**. 
    4. W **rozpoczęcie sekcji**, raz, określ czas za kilka minut późniejsza niż bieżąca godzina. 
    5. Aby uzyskać **cyklu**, wybierz pozycję **cykliczny**. 
    6. W **Powtórz co** zaznacz **dzień**. 
    7. Wybierz pozycję **Utwórz**. 

        ![Harmonogram start Azure SSIS IR.](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
3. Przełącz się do **parametry i ustawienia uruchamiania** kartę. Określ nazwę grupy zasobów, nazwa fabryki danych i nazwę Azure SSIS IR. Aby uzyskać **operacji**, wprowadź **START**. Kliknij przycisk **OK**. Wybierz **OK** ponownie, aby można znaleźć w harmonogramie na **harmonogramy** strony elementu runbook. 

    ![Harmonogram uruchomieniem Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
4. Powtórz dwa poprzednie kroki, aby utworzyć harmonogram o nazwie **zatrzymać IR codziennie**. Teraz, określ czas przynajmniej 30 minut, po upływie czasu określony dla **Start IR codziennie** harmonogramu. Aby uzyskać **operacji**, określ **ZATRZYMAĆ**. 
5. W **Runbook** wybierz **zadania** w menu po lewej stronie. Powinny pojawić się zadań utworzonych przez harmonogramy od określonego czasu i ich Stany. Można zobaczyć szczegółowe informacje dotyczące zadań, takich jak dane wyjściowe podobne do co przejrzane podczas testowania elementu runbook. 

    ![Harmonogram uruchomieniem Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
6. Po zakończeniu testowania, wyłącz harmonogramy ich edycją i wybierając **nr** dla **włączone**. Wybierz **harmonogramy** w menu po lewej stronie wybierz **Start IR codziennie/Stop IR codziennie**i wybierz **nr** dla **włączone**. 

## <a name="create-webhooks-to-start-and-stop-the-azure-ssis-ir"></a>Tworzenie elementów webhook, aby uruchomić i zatrzymać Azure SSIS IR
Postępuj zgodnie z instrukcjami wyświetlanymi w [tworzenia elementu webhook](../automation/automation-webhooks.md#creating-a-webhook) do utworzenia dwóch elementów webhook dla elementu runbook. Dla pierwszego z nich Określ START jako operacji, a dla drugiego z nich, określ STOP jako operacji. Zapisz w adresach URL dla obu elementów webhook miejscu (na przykład plik tekstowy lub notes OneNote). Te adresy URL są używane podczas konfigurowania sieci Web działania w potoku fabryki danych. Poniższej ilustracji przedstawiono przykład tworzenia elementu webhook, który rozpoczyna się Azure SSIS IR:

1. W **Runbook** wybierz **elementów Webhook** z menu po lewej stronie, a następnie wybierz **+ Dodaj element Webhook** na pasku narzędzi. 

    ![Elementów Webhook -> Dodaj elementu Webhook](./media/how-to-schedule-azure-ssis-integration-runtime/add-web-hook-menu.png)
2. W **Dodawanie elementu Webhook** wybierz **tworzenia nowego elementu webhook**, i wykonaj następujące czynności: 

    1. Aby uzyskać **nazwa**, wprowadź **StartAzureSsisIR**. 
    2. Upewnij się, że **włączone** ustawiono **tak**. 
    3. Kopiuj **adres URL** i zapisz go w innym miejscu. Ten krok jest ważne. Użytkownik nie ma adresu URL później. 
    4. Kliknij przycisk **OK**. 

        ![Nowe okno elementu Webhook](./media/how-to-schedule-azure-ssis-integration-runtime/new-web-hook-window.png)
3. Przełącz się do **parametry i ustawienia uruchamiania** kartę. Określ nazwę grupy zasobów, nazwa fabryki danych i nazwę Azure SSIS IR. Aby uzyskać **operacji**, wprowadź **START**. Kliknij przycisk **OK**. Następnie kliknij pozycję **Utwórz**. 

    ![Element Webhook — parametry i ustawienia uruchamiania](./media/how-to-schedule-azure-ssis-integration-runtime/webhook-parameters.png)
4. Powtórz poprzednie trzy kroki, aby utworzyć inny element webhook o nazwie **StopAzureSsisIR**. Należy pamiętać skopiować adres URL. Określając parametry i ustawienia uruchamiania, wprowadź **ZATRZYMAĆ** dla **operacji**. 

Powinien mieć dwa adresy URL: jeden dla **StartAzureSsisIR** webhook i drugi dla **StopAzureSsisIR** elementu webhook. Możesz wysłać żądanie HTTP POST do tych adresów URL do uruchamiania/zatrzymywania IR. SSIS sieci Azure 

## <a name="create-and-schedule-a-data-factory-pipeline-that-startsstops-the-ir"></a>Tworzenie i planowanie potok fabryki danych, który rozpoczyna/zatrzymuje IR
W tej sekcji przedstawia sposób użycia aktywności sieci Web do wywołania elementów webhook, który został utworzony w poprzedniej sekcji.

Potok, w którym można utworzyć składa się z trzech działań. 

1. Pierwszy **Web** działania wywołuje pierwszego elementu webhook uruchomić podczerwieni Azure SSIS. 
2. **Wykonanie pakietu usług SSIS** działania lub **procedury składowanej** pakietów SSIS odbywa się działanie.
3. Drugi **Web** działania wywołuje element webhook, aby zatrzymać Azure SSIS IR. 

Po utworzeniu i przetestować potoku utworzyć wyzwalacza harmonogram i skojarzyć z potoku. Wyzwalacz harmonogram definiuje harmonogramu dla potoku. Załóżmy, można utworzyć wyzwalaczy, które jest zaplanowane do uruchomienia codziennie o 23: 00. Wyzwalacz uruchamia potoku godzinie 23: 00 każdego dnia. Potok uruchamia Azure SSIS IR, wykonuje pakietów SSIS i zatrzymywany podczerwieni Azure SSIS. 

### <a name="create-a-data-factory"></a>Tworzenie fabryki danych

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).    
2. Kliknij przycisk **Nowy** w lewym menu, kliknij pozycję **Dane + analiza**, a następnie kliknij pozycję **Data Factory**. 
   
   ![Nowy-> Fabryka danych](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. Na stronie **Nowa fabryka danych** wprowadź jako **nazwę** wartość **MyAzureSsisDataFactory**. 
      
     ![Strona Nowa fabryka danych](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Nazwa fabryki danych platformy Azure musi być **globalnie unikatowa**. Jeśli wystąpi poniższy błąd, zmień nazwę fabryki danych (np. twojanazwaMyAzureSsisDataFactory) i spróbuj utworzyć ją ponownie. Artykuł [Data Factory — Naming Rules (Usługa Data Factory — reguły nazewnictwa)](naming-rules.md) zawiera reguły nazewnictwa artefaktów usługi Data Factory.
  
       `Data factory name �MyAzureSsisDataFactory� is not available`
3. Wybierz **subskrypcję** Azure, w której chcesz utworzyć fabrykę danych. 
4. Dla opcji **Grupa zasobów** wykonaj jedną z następujących czynności:
     
      - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej. 
      - Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę grupy zasobów.   
         
      Informacje na temat grup zasobów znajdują się w artykule [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Używanie grup zasobów do zarządzania zasobami platformy Azure).  
4. Wybierz wartość **V2 (wersja zapoznawcza)** dla **wersji**.
5. Na liście **lokalizacja** wybierz lokalizację fabryki danych. Na liście są wyświetlane tylko lokalizacje obsługiwane na potrzeby tworzenia fabryk danych.
6. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.     
7. Kliknij przycisk **Utwórz**.
8. Na pulpicie nawigacyjnym jest widoczny następujący kafelek ze stanem: **Wdrażanie fabryki danych**. 

    ![kafelek Wdrażanie fabryki danych](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**, jak pokazano na poniższej ilustracji.
   
   ![Strona główna fabryki danych](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Kliknij pozycję **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić interfejs użytkownika usługi Data Factory.

### <a name="create-a-pipeline"></a>Tworzenie potoku

1. W **wprowadzenie** wybierz pozycję **tworzenie potoku**. 

   ![Strona Wprowadzenie](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
2. W **działania** przybornika, rozwiń węzeł **ogólne**, przeciągnij upuść **Web** działania na powierzchnię projektanta potoku. W **ogólne** karcie **właściwości** okna, Zmień nazwę działania do **StartIR**.

   ![Pierwsze działanie sieci Web — karta Ogólne](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-general-tab.png)
3. Przełącz się do **ustawienia** karcie **właściwości** okna, i wykonaj następujące czynności: 

    1. Aby uzyskać **adres URL**, wklej adres URL elementu webhook, która rozpoczyna się Azure SSIS IR. 
    2. Aby uzyskać **metody**, wybierz pozycję **POST**. 
    3. Aby uzyskać **treści**, wprowadź `{"message":"hello world"}`. 
   
        ![Pierwsze działanie sieci Web — karta ustawień](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-settnigs-tab.png)

4. Przeciągnij i upuść działanie wykonanie pakietu usług SSIS lub działania procedury składowanej z **ogólne** sekcji **działania** przybornika. Ustaw nazwę działania do **RunSSISPackage**. 

5. W przypadku wybrania działania wykonanie pakietu usług SSIS, postępuj zgodnie z instrukcjami [uruchamiania pakietów SSIS za pomocą działania SSIS w fabryce danych Azure](how-to-invoke-ssis-package-ssis-activity.md) aby zakończyć tworzenie działania.  Upewnij się, że określono wystarczającą liczbę ponownych prób, które są często oczekiwania na dostępność IR Azure SSIS, ponieważ potrzebny do 30 minut, aby rozpocząć. 

    ![Ustawienia ponawiania](media/how-to-schedule-azure-ssis-integration-runtime/retry-settings.png)

6. W przypadku wybrania działania procedury składowanej, postępuj zgodnie z instrukcjami [wywołania pakietów SSIS za pomocą działania procedury składowanej w fabryce danych Azure](how-to-invoke-ssis-package-stored-procedure-activity.md) aby zakończyć tworzenie działania. Upewnij się, że Wstaw skrypt języka Transact-SQL, który oczekuje na dostępność IR Azure SSIS, ponieważ trwa do 30 minut, aby uruchomić.
    ```sql
    DECLARE @return_value int, @exe_id bigint, @err_msg nvarchar(150)

    -- Wait until Azure-SSIS IR is started
    WHILE NOT EXISTS (SELECT * FROM [SSISDB].[catalog].[worker_agents] WHERE IsEnabled = 1 AND LastOnlineTime > DATEADD(MINUTE, -10, SYSDATETIMEOFFSET()))
    BEGIN
        WAITFOR DELAY '00:00:01';
    END

    EXEC @return_value = [SSISDB].[catalog].[create_execution] @folder_name=N'YourFolder',
        @project_name=N'YourProject', @package_name=N'YourPackage',
        @use32bitruntime=0, @runincluster=1, @useanyworker=1,
        @execution_id=@exe_id OUTPUT 

    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1

    EXEC [SSISDB].[catalog].[start_execution] @execution_id = @exe_id, @retry_count = 0

    -- Raise an error for unsuccessful package execution, check package execution status = created (1)/running (2)/canceled (3)/
    -- failed (4)/pending (5)/ended unexpectedly (6)/succeeded (7)/stopping (8)/completed (9) 
    IF (SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id = @exe_id) <> 7 
    BEGIN
        SET @err_msg=N'Your package execution did not succeed for execution ID: '+ CAST(@execution_id as nvarchar(20))
        RAISERROR(@err_msg, 15, 1)
    END
    ```

7. Połącz **Web** działanie **wykonanie pakietu usług SSIS** lub **procedury składowanej** działania. 

    ![Połącz działań w sieci Web i procedury składowanej](./media/how-to-schedule-azure-ssis-integration-runtime/connect-web-sproc.png)

8. Przeciągnij i upuść innego **Web** działania z prawej strony **wykonanie pakietu usług SSIS** działania lub **procedury składowanej** działania. Ustaw nazwę działania do **StopIR**. 
9. Przełącz się do **ustawienia** karcie **właściwości** okna, i wykonaj następujące czynności: 

    1. Aby uzyskać **adres URL**, wklej adres URL elementu webhook, która kończy się podczerwieni Azure SSIS. 
    2. Aby uzyskać **metody**, wybierz pozycję **POST**. 
    3. Aby uzyskać **treści**, wprowadź `{"message":"hello world"}`.  
10. Połącz **wykonanie pakietu usług SSIS** działania lub **procedury składowanej** działania do ostatniego **Web** działania.

    ![Pełna potoku](./media/how-to-schedule-azure-ssis-integration-runtime/full-pipeline.png)
11. Sprawdź poprawność ustawień potoku, klikając **weryfikacji** na pasku narzędzi. Zamknij **raport weryfikacji potoku** klikając **>>** przycisku. 

    ![Weryfikowanie potoku](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-the-pipeline"></a>Testowe uruchamianie potoku

1. Wybierz **testu** na pasku narzędzi dla potoku. Zobacz dane wyjściowe w **dane wyjściowe** okna w dolnym okienku. 

    ![Uruchomienie testu](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
2. W **Runbook** strony konta usługi Automatyzacja Azure, możesz sprawdzić, czy zadania zostały wykonane do uruchamiania i zatrzymywania podczerwieni Azure SSIS. 

    ![Zadania elementu Runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbook-jobs.png)
3. Uruchom program SQL Server Management Studio. W **Połącz z serwerem** okna, wykonaj następujące czynności: 

    1. Aby uzyskać **nazwy serwera**, określ  **&lt;bazy danych Azure SQL&gt;. database.windows.net**.
    2. Wybierz **Opcje >>**.
    3. Aby uzyskać **Połącz z bazą danych**, wybierz pozycję **SSISDB**.
    4. Wybierz przycisk **Połącz**. 
    5. Rozwiń węzeł **Integracja usług katalogów** -> **SSISDB** -> folderu -> **projekty** -> Your SSIS projektu -> **pakietów** . 
    6. Kliknij prawym przyciskiem myszy pakiet SSIS, a następnie wybierz **raporty** -> **raportów standardowych** -> **wszystkie wykonaniami**. 
    7. Sprawdź, czy uruchomiono pakietów SSIS. 

        ![Sprawdź uruchamiania pakietów SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/verfiy-ssis-package-run.png)

### <a name="schedule-the-pipeline"></a>Harmonogram potoku 
Teraz, gdy proces działa jako oczekiwano, można utworzyć wyzwalacza do uruchomienia tego potoku w określonych okresach. Aby uzyskać więcej informacji o sposobie kojarzenia wyzwalacz harmonogramu z potoku, zobacz [wyzwolenia potoku zgodnie z harmonogramem](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

1. Na pasku narzędzi dla potoku, wybierz **wyzwalacza**i wybierz **nowy i edytować**. 

    ![Wyzwalacz -> Nowy i edytowanie](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)
2. W **dodać wyzwalaczy** wybierz **+ nowy**.

    ![Dodaj nowy wyzwalaczy-](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)
3. W **nowego wyzwalacza**, wykonaj następujące czynności: 

    1. Aby uzyskać **nazwa**, określ nazwę wyzwalacza. W poniższym przykładzie **codziennie** to nazwa wyzwalacza. 
    2. Aby uzyskać **typu**, wybierz pozycję **harmonogram**. 
    3. Aby uzyskać **Data rozpoczęcia**, wybierz datę i godzinę. 
    4. Aby uzyskać **cyklu**, określ okresach wyzwalacza. W poniższym przykładzie jego codziennych — jeden raz. 
    5. Dla **zakończenia**, można określić datę i godzinę, klikając **na daty** opcji. 
    6. Wybierz **aktywowany**. Wyzwalacz jest aktywowany bezpośrednio po publikowanie rozwiązania z fabryką danych. 
    7. Wybierz opcję **Dalej**.

        ![Wyzwalacz -> Nowy i edytowanie](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
4. W **Parametry uruchamiania wyzwalacza** strony, przejrzyj ostrzeżenie o, a następnie wybierz **Zakończ**. 
5. Publikowanie rozwiązania z fabryką danych, wybierając **publikowania wszystkich** w okienku po lewej stronie. 

    ![Publikuj wszystko](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)
6. Aby monitorować wyzwalacz uruchamia i działa w potoku, należy użyć **Monitor** karcie po lewej stronie. Aby uzyskać szczegółowe instrukcje, zobacz [monitorować potoku](quickstart-create-data-factory-portal.md#monitor-the-pipeline).

    ![Uruchomienia potoków](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)
7. Aby wyświetlić uruchomień działania związane z potokiem Uruchom, wybierz pierwszy link (**odbywa się działanie widoku**) w **akcje** kolumny. Zobacz uruchomień działania trzy skojarzone z każdego działania w potoku (najpierw sieci Web, działania procedury składowanej, a drugi działaniem sieci Web). Aby powrócić do wyświetlenia uruchamia potoku, wybierz **potoki** łącze u góry.

    ![Uruchomienia działania](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)
8. Wyzwalacz uruchamia można również wyświetlić, wybierając **wyzwolenia działa** z listy rozwijanej obok pola jest **uruchamia potoku** u góry. 

    ![Uruchomienia wyzwalacza](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

## <a name="next-steps"></a>Kolejne kroki
Zobacz następującym wpisie w blogu:
-   [Modernizacji i rozszerzanie przepływy pracy ETL/ELT z działaniami SSIS w potokach ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)

Zobacz następujące artykuły wchodzące w skład usług SSIS: 

- [Deploy, run, and monitor an SSIS package on Azure (Wdrażanie, uruchamianie i monitorowanie pakietu usług SSIS na platformie Azure)](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Connect to SSIS catalog on Azure (Łączenie z wykazem usług SSIS na platformie Azure)](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Schedule package execution on Azure (Planowanie wykonywania pakietów na platformie Azure)](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Connect to on-premises data sources with Windows authentication (Łączenie z lokalnymi źródłami danych przy użyciu uwierzytelniania systemu Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
