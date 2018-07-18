---
title: Jak zaplanować środowiska Azure SSIS integration runtime | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia harmonogramu uruchamiania i zatrzymywania środowiska Azure SSIS integration Runtime za pomocą usługi Data Factory i Azure Automation.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/16/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f83715d2a382db271686210d9df285c255c09216
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113998"
---
# <a name="how-to-start-and-stop-the-azure-ssis-integration-runtime-on-a-schedule"></a>Jak uruchamiać i zatrzymywać środowiska Azure SSIS integration runtime zgodnie z harmonogramem
W tym artykule opisano, jak można zaplanować uruchamianie i zatrzymywanie środowiska Azure SSIS integration runtime (Self-HOSTED) przy użyciu usługi Azure Automation i Azure Data Factory. Uruchamianie środowiska Azure SSIS (SQL Server Integration Services) integration runtime (IR) ma koszt związany z nim. W związku z tym zazwyczaj chcesz uruchomić środowiska IR tylko wtedy, gdy należy uruchamiać pakiety usług SSIS na platformie Azure i Zatrzymaj środowisko IR, gdy nie są potrzebne. Możesz użyć interfejsu użytkownika usługi Data Factory lub programu Azure PowerShell [ręcznie rozpocząć lub zatrzymać środowisko IR Azure SSIS](manage-azure-ssis-integration-runtime.md)).

Można na przykład tworzenia działań w sieci Web przy użyciu elementów webhook do elementu runbook usługi Azure Automation PowerShell i powiązane działania wykonywanie pakietu SSIS między nimi. Działania internetowe można uruchomić i zatrzymać środowiska IR Azure-SSIS, dokładnie na czas przed i po uruchomieniu pakietu. Aby uzyskać więcej informacji o działaniu wykonywanie pakietu SSIS, zobacz [uruchamianie pakietów SSIS za pomocą działania SSIS w usłudze Azure Data Factory](how-to-invoke-ssis-package-ssis-activity.md).

## <a name="overview-of-the-steps"></a>Omówienie kroków

Poniżej przedstawiono ogólne kroki opisane w tym artykule:

1. **Tworzenie i testowanie elementu runbook usługi Azure Automation.** W tym kroku utworzysz elementu runbook programu PowerShell, za pomocą skryptu, który uruchamia lub zatrzymuje Azure SSIS IR. Następnie należy przetestować element runbook w scenariuszach zarówno uruchamianie i ZATRZYMYWANIE i upewnij się, że środowisko IR uruchomienia lub zatrzymania. 
2. **Utwórz dwa harmonogramy dla elementu runbook.** Pierwszy harmonogramu możesz skonfigurować element runbook dzięki WPROWADZENIU jako operacja. Drugi harmonogramu należy skonfigurować element runbook z STOP jako operacja. Dla obu harmonogramy należy określić tempo, w którym element runbook jest uruchomiony. Na przykład można zaplanować pierwszy z nich do uruchomienia o 8: 00 każdego dnia i drugi by było uruchamiane codziennie godzina 23. Po uruchomieniu pierwszego elementu runbook rozpoczyna się Azure SSIS IR. Gdy drugi element runbook uruchamia, zatrzymuje się Azure SSIS IR. 
3. **Utwórz dwa elementy webhook dla elementu runbook**, jeden dla operacji URUCHAMIANIA, a drugi dla operacji ZATRZYMANIA. Konfigurowanie sieci web działania w potoku usługi fabryka danych za pomocą adresów URL z tych elementów webhook. 
4. **Tworzenie potoku usługi Data Factory**. Potoku, który zostanie utworzony składa się z trzech działań. Pierwszy **Web** działania wywołuje pierwszy element webhook, aby uruchomić Azure SSIS IR. **Procedury składowanej** działanie uruchamia skrypt SQL, który uruchamia pakiet usług SSIS. Drugi **Web** działania zatrzymuje Azure SSIS IR. Aby uzyskać więcej informacji na temat wywoływania pakietu SSIS z potoku usługi fabryka danych za pomocą działania procedury składowanej, zobacz [wywołania pakietu SSIS](how-to-invoke-ssis-package-stored-procedure-activity.md). Następnie utworzysz wyzwalacz harmonogramu, aby zaplanować uruchamianie w okresach, które określisz potoku.

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli już nie przeprowadzono aprowizacji środowiska Azure SSIS integration runtime, aprowizować go, wykonując instrukcje przedstawione w [samouczek](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-test-an-azure-automation-runbook"></a>Tworzenie i testowanie elementu runbook usługi Azure Automation
W tej sekcji należy wykonać następujące czynności: 

1. Utwórz konto usługi Azure Automation.
2. Tworzenie elementu runbook programu PowerShell w ramach konta usługi Azure Automation. Skrypt programu PowerShell, skojarzony element runbook uruchamia lub zatrzymuje środowisko IR Azure SSIS, oparte na polecenie, które należy określić parametr operacji. 
3. Testowanie elementu runbook w obu menu start i Zatrzymaj scenariusze, aby upewnić się, że działa. 

### <a name="create-an-azure-automation-account"></a>Tworzenie konta usługi Azure Automation
Jeśli nie masz konta usługi Azure Automation, utwórz ją, postępując zgodnie z instrukcjami w tym kroku. Aby uzyskać szczegółowe instrukcje, zobacz [Tworzenie konta usługi Azure Automation](../automation/automation-quickstart-create-account.md). W ramach tego kroku, Utwórz **Uruchom jako platformy Azure** konta (jednostki w usłudze Azure Active Directory service) i dodaj go do **Współautor** roli w subskrypcji platformy Azure. Upewnij się, że jest taka sama jak subskrypcji, która zawiera usługi data factory, która ma Azure SSIS IR. Usługa Azure Automation używa tego konta do uwierzytelniania w usłudze Azure Resource Manager i działają na Twoich zasobów. 

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko przez przeglądarki internetowe Microsoft Edge i Google Chrome.
2. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).    
3. Wybierz **New** w menu po lewej stronie wybierz **monitorowanie + zarządzanie**i wybierz **automatyzacji**. 

    ![Nowy -> Monitorowanie + Zarządzanie -> Automatyzacja](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
2. W **Dodawanie konta usługi Automation** okna, wykonaj następujące czynności: 

    1. Określ **nazwa** dla konta usługi automation. 
    2. Wybierz **subskrypcji** zawierający fabryki danych przy użyciu usługi Azure SSIS IR. 
    3. Dla **grupy zasobów**, wybierz opcję **Utwórz nową** Aby utworzyć nową grupę zasobów lub wybierz **Użyj istniejącej** wybrać istniejącą grupę zasobów. 
    4. Wybierz **lokalizacji** dla konta usługi automation. 
    5. Upewnij się, że **Utwórz konto Uruchom jako** ustawiono **tak**. Jednostka usługi jest tworzony w usłudze Azure Active Directory. Jest ona dodawana do **Współautor** roli w subskrypcji platformy Azure
    6. Wybierz **Przypnij do pulpitu nawigacyjnego** umożliwiającego wyświetlanie na pulpicie nawigacyjnym portalu. 
    7. Wybierz pozycję **Utwórz**. 

        ![Nowy -> Monitorowanie + Zarządzanie -> Automatyzacja](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
3. Zostanie wyświetlony **stan wdrożenia** na pulpicie nawigacyjnym i w powiadomieniach. 
    
    ![Wdrażanie usługi automation](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
4. Zobaczysz stronę główną dla konta usługi automation, po jego utworzeniu pomyślnie. 

    ![Strona główna usługi Automation](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-data-factory-modules"></a>Importowanie modułów usługi Data Factory

1. Wybierz **modułów** w **zasoby UDOSTĘPNIONE** sekcji w menu po lewej stronie, a następnie sprawdź, czy masz **AzureRM.Profile** i **AzureRM.DataFactoryV2** na liście modułów.

    ![Sprawdź wymagane moduły](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Przejdź do galerii programu PowerShell dla [modułu AzureRM.DataFactoryV2](https://www.powershellgallery.com/packages/AzureRM.DataFactoryV2/), wybierz opcję **wdrażanie w usłudze Azure Automation**, wybierz konto usługi Automation, a następnie wybierz **OK**. Przejdź wstecz, aby wyświetlić **modułów** w **zasoby UDOSTĘPNIONE** sekcji w menu po lewej stronie, a następnie poczekaj na wyświetlenie **stan** z **AzureRM.DataFactoryV2** zmiana modułu **dostępne**.

    ![Zweryfikować moduł usługi Data Factory](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Przejdź do galerii programu PowerShell dla [AzureRM.Profile module](https://www.powershellgallery.com/packages/AzureRM.profile/), kliknij pozycję **wdrażanie w usłudze Azure Automation**, wybierz konto usługi Automation, a następnie wybierz **OK**. Przejdź wstecz, aby wyświetlić **modułów** w **zasoby UDOSTĘPNIONE** sekcji w menu po lewej stronie, a następnie poczekaj na wyświetlenie **stan** z **AzureRM.Profile**zmiana modułu **dostępne**.

    ![Zweryfikować moduł profilu](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-a-powershell-runbook"></a>Tworzenie elementu runbook programu PowerShell
Poniższa procedura zawiera instrukcje dotyczące tworzenia elementu runbook programu PowerShell. Skrypt skojarzony z elementem runbook albo rozpoczyna/zatrzymuje środowisko IR Azure SSIS, oparte na polecenia, należy określić dla **operacji** parametru. W tej sekcji podano wszystkich szczegółów tworzenia elementu runbook. Aby uzyskać więcej informacji, zobacz [utworzyć element runbook](../automation/automation-quickstart-create-runbook.md) artykułu.

1. Przełącz się do **elementów Runbook** , a następnie wybierz pozycję **+ Dodaj element runbook** z paska narzędzi. 

    ![Dodawanie przycisku elementu runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
2. Wybierz **Utwórz nowy element runbook**i wykonaj następujące czynności: 

    1. Aby uzyskać **nazwa**, typ **StartStopAzureSsisRuntime**.
    2. Aby uzyskać **typ elementu Runbook**, wybierz opcję **PowerShell**.
    3. Wybierz pozycję **Utwórz**.
    
        ![Dodawanie przycisku elementu runbook](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
3. Kopiuj/wklej poniższy skrypt do okna skryptu elementu runbook. Zapisz, a następnie opublikować element runbook za pomocą **Zapisz** i **Publikuj** przycisków na pasku narzędzi. 

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
6. W **uruchamianie elementu Runbook** okna, wykonaj następujące czynności: 

    1. Aby uzyskać **nazwy grupy zasobów**, wprowadź nazwę grupy zasobów z usługą data factory, która ma Azure SSIS IR. 
    2. Aby uzyskać **nazwa FABRYKI danych**, wprowadź nazwę usługi data factory, która ma Azure SSIS IR. 
    3. Aby uzyskać **AZURESSISNAME**, wprowadź nazwę Azure SSIS IR. 
    4. Aby uzyskać **operacji**, wprowadź **START**. 
    5. Kliknij przycisk **OK**.  

        ![Uruchom okno elementu runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
7. W oknie zadania wybierz **dane wyjściowe** kafelka. W oknie danych wyjściowych zadania, poczekaj, aż zostanie wyświetlony komunikat **### Ukończono ###** po stwierdzeniu **### uruchamianie ###**. Uruchamianie środowiska Azure SSIS IR trwa około 20 minut. Zamknij **zadania** oknie i mogliśmy wrócić do **Runbook** okna.

    ![Środowisko IR Azure SSIS — pracę](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
8.  Powtórz dwa poprzednie kroki, ale za pomocą **ZATRZYMAĆ** jako wartość pozycji **operacji**. Uruchom element runbook ponownie, wybierając **Start** przycisk na pasku narzędzi. Określ nazwę grupy zasobów, nazwę fabryki danych i nazwę środowiska Azure SSIS IR. Aby uzyskać **operacji**, wprowadź **ZATRZYMAĆ**. 

    W oknie danych wyjściowych zadania, poczekaj, aż zostanie wyświetlony komunikat **### Ukończono ###** po stwierdzeniu **### zatrzymywanie ###**. Zatrzymywanie środowiska Azure SSIS IR nie przyjmuje tak długo, jak uruchamianie Azure SSIS IR. Zamknij **zadania** oknie i mogliśmy wrócić do **Runbook** okna.

## <a name="create-schedules-for-the-runbook-to-startstop-the-azure-ssis-ir"></a>Tworzenie harmonogramów dla elementu runbook można uruchomić/zatrzymać środowiska Azure SSIS IR
W poprzedniej sekcji został utworzony element runbook usługi Automation, które można uruchomić lub zatrzymać Azure SSIS IR. W tej sekcji utworzysz dwa harmonogramy dla elementu runbook. Podczas konfigurowania pierwszego harmonogramu, należy określić START parametru operacji. Podobnie podczas konfigurowania harmonogramu drugiego, możesz określić ZATRZYMANIA dla tej operacji. Aby uzyskać szczegółowe instrukcje dotyczące tworzenia harmonogramów, zobacz [Utwórz harmonogram](../automation/automation-schedules.md#creating-a-schedule).

1. W **Runbook** wybierz **harmonogramy**i wybierz **+ Dodaj harmonogram** na pasku narzędzi. 

    ![Środowisko IR Azure SSIS — pracę](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
2. W **elementu Runbook z harmonogramem** okna, wykonaj następujące czynności: 

    1. Wybierz **powiązania harmonogramu z elementem runbook**. 
    2. Wybierz **Utwórz nowy harmonogram**.
    3. W **nowy harmonogram** okna, typ **Uruchom środowisko IR codziennie** dla **nazwa**. 
    4. W **rozpoczyna się w sekcji**, czas, należy określić godzinę za kilka minut późniejsza niż bieżąca godzina. 
    5. Aby uzyskać **cyklu**, wybierz opcję **cyklicznie**. 
    6. W **Powtórz co** zaznacz **dzień**. 
    7. Wybierz pozycję **Utwórz**. 

        ![Harmonogram uruchamiania środowiska Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
3. Przełącz się do **parametry i ustawienia uruchamiania** kartę. Określ nazwę grupy zasobów, nazwę fabryki danych i nazwę środowiska Azure SSIS IR. Aby uzyskać **operacji**, wprowadź **START**. Kliknij przycisk **OK**. Wybierz **OK** ponownie, aby zobaczyć harmonogramu na **harmonogramy** strony elementu runbook. 

    ![Harmonogram rozpoczęcie przeczytania artykułu środowiska Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
4. Powtórz dwa poprzednie kroki, aby utworzyć harmonogram o nazwie **zatrzymać środowisko IR codziennie**. Tym razem, określ czas, po co najmniej 30 minut od czasu określony dla **Uruchom środowisko IR codziennie** harmonogramu. Aby uzyskać **operacji**, określ **ZATRZYMAĆ**. 
5. W **Runbook** wybierz **zadania** w menu po lewej stronie. Powinien zostać wyświetlony zadań utworzonych przez harmonogramy od określonego czasu oraz ich stan. Możesz zobaczyć szczegółowe informacje o zadaniu, takich jak dane wyjściowe podobne do co wiesz już podczas testowania elementu runbook. 

    ![Harmonogram rozpoczęcie przeczytania artykułu środowiska Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
6. Po zakończeniu testowania, wyłącz harmonogramy, edytując je i wybierając **nie** dla **włączone**. Wybierz **harmonogramy** w menu po lewej stronie wybierz **Start IR codziennie/Stop środowiska IR codziennie**i wybierz **nie** dla **włączone**. 

## <a name="create-webhooks-to-start-and-stop-the-azure-ssis-ir"></a>Tworzenie elementów webhook, uruchamianie i zatrzymywanie środowiska Azure SSIS IR
Postępuj zgodnie z instrukcjami wyświetlanymi w [tworzenia elementu webhook](../automation/automation-webhooks.md#creating-a-webhook) utworzyć dwa elementy webhook dla elementu runbook. Pierwszy z nich, aby uzyskać Określ rozpoczęcia jako operacji, a dla drugiego z nich, należy określić STOP jako operacja. Zapisz w adresach URL dla obu elementów webhook gdzieś (na przykład plik tekstowy lub w notesie programu OneNote). Użyjesz tych adresów URL, podczas konfigurowania sieci Web działań w potoku usługi fabryka danych. Poniższej ilustracji przedstawiono przykład tworzenia elementu webhook, który rozpoczyna się środowiska Azure SSIS IR:

1. W **Runbook** wybierz **elementów Webhook** z menu po lewej stronie, a następnie wybierz pozycję **+ Dodaj element Webhook** na pasku narzędzi. 

    ![Elementy Webhook -> Dodaj element Webhook](./media/how-to-schedule-azure-ssis-integration-runtime/add-web-hook-menu.png)
2. W **Dodaj element Webhook** wybierz **Utwórz nowy element webhook**, i wykonaj następujące czynności: 

    1. Aby uzyskać **nazwa**, wprowadź **StartAzureSsisIR**. 
    2. Upewnij się, że **włączone** ustawiono **tak**. 
    3. Kopiuj **adresu URL** i zapisać go w innym miejscu. Ten krok jest ważny. Użytkownik nie ma adresu URL później. 
    4. Kliknij przycisk **OK**. 

        ![Nowe okno elementu Webhook](./media/how-to-schedule-azure-ssis-integration-runtime/new-web-hook-window.png)
3. Przełącz się do **parametry i ustawienia uruchamiania** kartę. Określ nazwę grupy zasobów, nazwę fabryki danych i nazwę środowiska Azure SSIS IR. Aby uzyskać **operacji**, wprowadź **START**. Kliknij przycisk **OK**. Następnie kliknij pozycję **Utwórz**. 

    ![Element Webhook — parametry i ustawienia uruchamiania](./media/how-to-schedule-azure-ssis-integration-runtime/webhook-parameters.png)
4. Powtórz poprzednie trzy kroki, aby utworzyć inny element webhook o nazwie **StopAzureSsisIR**. Należy pamiętać skopiować adres URL. Określając parametry i ustawienia uruchamiania, wprowadź **ZATRZYMAĆ** dla **operacji**. 

Musisz mieć dwa adresy URL: jeden dla **StartAzureSsisIR** elementu webhook i inne **StopAzureSsisIR** elementu webhook. Możesz wysłać żądanie HTTP POST do tych adresów URL, do uruchamiania/zatrzymywania usługi Azure SSIS IR. 

## <a name="create-and-schedule-a-data-factory-pipeline-that-startsstops-the-ir"></a>Tworzenie i zaplanować potok usługi Data Factory, który rozpoczyna/zatrzymuje środowisko IR
W tej sekcji pokazano, jak użyć działania w sieci Web w celu wywołania elementów webhook, który został utworzony w poprzedniej sekcji.

Potoku, który zostanie utworzony składa się z trzech działań. 

1. Pierwszy **Web** działania wywołuje pierwszy element webhook, aby uruchomić Azure SSIS IR. 
2. **Wykonywanie pakietu SSIS** działania lub **procedury składowanej** pakietu SSIS uruchomienia działania.
3. Drugi **Web** działania wywołuje element webhook, aby zatrzymać Azure SSIS IR. 

Po utworzeniu i przetestować potoku Tworzenie wyzwalacza harmonogramu i skojarzyć z potoku. Wyzwalacz harmonogramu definiuje harmonogramu potoku. Załóżmy, że utworzysz wyzwalacz, który jest zaplanowane do uruchomienia codziennie o godzinie 23: 00. Wyzwalacz uruchamia potok w 23: 00 każdego dnia. Potok rozpoczyna się środowiska Azure SSIS IR, wykonuje pakietu SSIS i zatrzymywany Azure SSIS IR. 

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
4. Wybierz opcję **V2** w obszarze **Wersja**.
5. Na liście **lokalizacja** wybierz lokalizację fabryki danych. Na liście są wyświetlane tylko lokalizacje obsługiwane na potrzeby tworzenia fabryk danych.
6. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.     
7. Kliknij przycisk **Utwórz**.
8. Na pulpicie nawigacyjnym jest widoczny następujący kafelek ze stanem: **Wdrażanie fabryki danych**. 

    ![kafelek Wdrażanie fabryki danych](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. Po zakończeniu tworzenia zostanie wyświetlona strona **Fabryka danych**, jak pokazano na poniższej ilustracji.
   
   ![Strona główna fabryki danych](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Kliknij pozycję **Tworzenie i monitorowanie**, aby w osobnej karcie uruchomić interfejs użytkownika usługi Data Factory.

### <a name="create-a-pipeline"></a>Tworzenie potoku

1. W **wprowadzenie** wybierz opcję **Utwórz potok**. 

   ![Strona Wprowadzenie](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
2. W **działania** przybornika, rozwiń węzeł **ogólne**, przeciąganie i upuszczanie **Web** działania na powierzchnię projektanta potoku. W **ogólne** karcie **właściwości** okna, Zmień nazwę działania na **StartIR**.

   ![Pierwsze działanie sieci Web — karta Ogólne](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-general-tab.png)
3. Przełącz się do **ustawienia** karcie **właściwości** okna, i wykonaj następujące czynności: 

    1. Aby uzyskać **adresu URL**, wklej adres URL elementu webhook, który rozpoczyna się Azure SSIS IR. 
    2. Dla **metoda**, wybierz opcję **WPIS**. 
    3. Aby uzyskać **treści**, wprowadź `{"message":"hello world"}`. 
   
        ![Pierwsze działanie sieci Web — karta Ustawienia](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-settnigs-tab.png)

4. Przeciągnij i upuść działanie wykonanie pakietu SSIS lub działanie procedury składowanej z **ogólne** części **działania** przybornika. Ustaw nazwę działania na **RunSSISPackage**. 

5. Jeśli wybierzesz działanie wykonanie pakietu SSIS, postępuj zgodnie z instrukcjami [uruchamianie pakietów SSIS za pomocą działania SSIS w usłudze Azure Data Factory](how-to-invoke-ssis-package-ssis-activity.md) aby zakończyć tworzenie działania.  Upewnij się, że podajesz wystarczającą liczbę ponownych prób, które są często oczekiwania na dostępność środowiska Azure-SSIS IR, ponieważ trwa maksymalnie 30 minut, aby rozpocząć. 

    ![Ustawienia ponawiania](media/how-to-schedule-azure-ssis-integration-runtime/retry-settings.png)

6. Jeśli wybierzesz działanie Stored Procedure, postępuj zgodnie z instrukcjami [wywoływanie pakietów SSIS za pomocą działania procedury składowanej w usłudze Azure Data Factory](how-to-invoke-ssis-package-stored-procedure-activity.md) aby zakończyć tworzenie działania. Upewnij się, że wstawić skrypt języka Transact-SQL, który oczekuje na dostępność środowiska Azure-SSIS IR, ponieważ trwa maksymalnie 30 minut, aby rozpocząć.
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

7. Połącz **sieci Web** działanie **wykonywanie pakietu SSIS** lub **procedury składowanej** działania. 

    ![Łączenie działań w sieci Web i procedury składowanej](./media/how-to-schedule-azure-ssis-integration-runtime/connect-web-sproc.png)

8. Przeciąganie i upuszczanie innego **Web** działania po prawej stronie **wykonywanie pakietu SSIS** działania lub **procedury składowanej** działania. Ustaw nazwę działania na **StopIR**. 
9. Przełącz się do **ustawienia** karcie **właściwości** okna, i wykonaj następujące czynności: 

    1. Aby uzyskać **adresu URL**, wklej adres URL elementu webhook, który zatrzymuje Azure SSIS IR. 
    2. Dla **metoda**, wybierz opcję **WPIS**. 
    3. Aby uzyskać **treści**, wprowadź `{"message":"hello world"}`.  
10. Łączenie **wykonywanie pakietu SSIS** działania lub **procedury składowanej** działania do ostatniego **Web** działania.

    ![Pełny potok](./media/how-to-schedule-azure-ssis-integration-runtime/full-pipeline.png)
11. Sprawdź poprawność ustawień potoku, klikając **weryfikacji** na pasku narzędzi. Zamknij **raport weryfikacji potoku** , klikając **>>** przycisku. 

    ![Weryfikowanie potoku](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-the-pipeline"></a>Testowe uruchamianie potoku

1. Wybierz **przebiegu testu** na pasku narzędzi dla potoku. Zostaną wyświetlone dane wyjściowe w **dane wyjściowe** okna w dolnym okienku. 

    ![Przebieg testu](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
2. W **Runbook** strona konta usługi Azure Automation, możesz sprawdzić, czy zadania zostały wykonane, aby uruchomić i zatrzymać Azure SSIS IR. 

    ![Zadania elementów Runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbook-jobs.png)
3. Uruchom program SQL Server Management Studio. W **Połącz z serwerem** okna, wykonaj następujące czynności: 

    1. Aby uzyskać **nazwy serwera**, określ  **&lt;usługi Azure SQL database&gt;. database.windows.net**.
    2. Wybierz **Opcje >>**.
    3. Aby uzyskać **Połącz z bazą danych**, wybierz opcję **SSISDB**.
    4. Wybierz przycisk **Połącz**. 
    5. Rozwiń **Integracja usług katalogów** -> **SSISDB** -> folder -> **projektów** -> Your SSIS Projekt -> **pakietów** . 
    6. Kliknij prawym przyciskiem myszy pakietu SSIS, a następnie wybierz pozycję **raporty** -> **raportów standardowych** -> **wszystkich wykonań**. 
    7. Sprawdź, czy uruchomiono pakietu SSIS. 

        ![Sprawdź wykonywania pakietów SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/verfiy-ssis-package-run.png)

### <a name="schedule-the-pipeline"></a>Zaplanować potok 
Teraz, gdy potoku działa zgodnie z oczekiwaniami, możesz utworzyć wyzwalacz służący do uruchamiania tego potoku w erze określony. Aby uzyskać szczegółowe informacje dotyczące kojarzenia wyzwalacza harmonogramu z potokiem, zobacz [wyzwalanie potoku zgodnie z harmonogramem](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

1. Na pasku narzędzi potoku wybierz **wyzwalacza**i wybierz **nowy/Edytuj**. 

    ![Wyzwalacz -> Nowy/Edycja](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)
2. W **Dodawanie wyzwalaczy** wybierz **+ nowy**.

    ![Dodawanie wyzwalaczy — nowy](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)
3. W **nowy wyzwalacz**, wykonaj następujące czynności: 

    1. Aby uzyskać **nazwa**, określ nazwę dla wyzwalacza. W poniższym przykładzie **Codzienne uruchamianie** to nazwa wyzwalacza. 
    2. Aby uzyskać **typu**, wybierz opcję **harmonogram**. 
    3. Aby uzyskać **Data rozpoczęcia**, wybierz datę i godzinę. 
    4. Aby uzyskać **cyklu**, określ tempa wyzwalacza. W poniższym przykładzie jego codziennych raz. 
    5. Dla **zakończenia**, można określić datę i godzinę, wybierając **w dniu** opcji. 
    6. Wybierz **aktywowane**. Wyzwalacz jest aktywowany po opublikowaniu rozwiązania w fabryce danych. 
    7. Wybierz opcję **Dalej**.

        ![Wyzwalacz -> Nowy/Edycja](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
4. W **Parametry uruchamiania wyzwalacza** strony, przejrzyj ostrzeżenie o, a następnie wybierz **Zakończ**. 
5. Publikowanie rozwiązania do usługi Data Factory, wybierając **Opublikuj wszystkie** w okienku po lewej stronie. 

    ![Publikuj wszystko](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-the-pipeline-and-trigger-in-the-azure-portal"></a>Monitorowanie potoku i wyzwalacza w witrynie Azure portal

1. Aby monitorować uruchomienia wyzwalacza i uruchomienia potoków, należy użyć **Monitor** karty po lewej stronie. Aby uzyskać szczegółowe instrukcje, zobacz [monitorowanie potoku](quickstart-create-data-factory-portal.md#monitor-the-pipeline).

    ![Uruchomienia potoków](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)
2. Aby wyświetlić uruchomienia działań skojarzone z uruchomieniem potoku, wybierz pierwszy link (**Wyświetl uruchomienia działań**) w **akcje** kolumny. Zobacz uruchomień działań trzy skojarzone z każdego działania w potoku (najpierw sieci Web działania, działanie Stored Procedure i drugiego działania internetowego). Aby przełączyć się ponownie, aby wyświetlić uruchomienia potoku, wybierz **potoki** link u góry.

    ![Uruchomienia działania](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)
3. Możesz również wyświetlić, wybierając pozycję uruchomienia wyzwalacza **wyzwolić uruchamianie** z listy rozwijanej, która jest obok pozycji **uruchomienia potoku** u góry. 

    ![Uruchomienia wyzwalacza](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-the-pipeline-and-trigger-with-powershell"></a>Monitorowanie potoku i wyzwalacza przy użyciu programu PowerShell

Za pomocą skryptów, jak w następujących przykładach monitorowania potoku i wyzwalacza.

1. Pobierz stan uruchomienia potoku.

  ```powershell
  Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
  ```

2. Uzyskaj informacje na temat wyzwalacza.

  ```powershell
  Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
  ```

3. Pobierz stan uruchomienia wyzwalacza.

  ```powershell
  Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
  ```

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujący wpis w blogu:
-   [Modernizacja i rozszerzanie przepływów pracy usługi ETL/ELT, za pomocą działania SSIS w potokach ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)

Zobacz następujące artykuły wchodzące w skład usług SSIS: 

- [Deploy, run, and monitor an SSIS package on Azure (Wdrażanie, uruchamianie i monitorowanie pakietu usług SSIS na platformie Azure)](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Connect to SSIS catalog on Azure (Łączenie z wykazem usług SSIS na platformie Azure)](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Schedule package execution on Azure (Planowanie wykonywania pakietów na platformie Azure)](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Connect to on-premises data sources with Windows authentication (Łączenie z lokalnymi źródłami danych przy użyciu uwierzytelniania systemu Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
