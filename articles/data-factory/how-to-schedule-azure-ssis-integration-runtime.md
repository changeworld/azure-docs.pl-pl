---
title: Jak zaplanować Azure-SSIS Integration Runtime | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia harmonogramu uruchamiania i zatrzymywania Azure-SSIS Integration Runtime za pomocą usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 1/9/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 54d7979f9fbe23e9372aa2702b46e42ca64496d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60521935"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>Jak uruchamiać i zatrzymywać środowisko Azure-SSIS Integration Runtime zgodnie z harmonogramem
W tym artykule opisano sposób tworzenia harmonogramu uruchamiania i zatrzymywania środowiska Azure-SSIS Integration Runtime (IR) przy użyciu usługi Azure Data Factory (ADF). Azure-SSIS IR to ADF obliczenia zasobów dedykowanych do wykonywania pakietów usług SQL Server Integration Services (SSIS). Uruchamianie środowiska Azure-SSIS IR ma koszt związany z nim. W związku z tym zazwyczaj chcesz uruchomić środowiska IR tylko wtedy, gdy konieczne wykonywanie pakietów usług SSIS na platformie Azure i Zatrzymaj środowiska IR, gdy nie trzeba go dłużej. Możesz użyć usługi ADF interfejsu użytkownika (UI) / aplikacji lub programu Azure PowerShell [ręcznie rozpocząć lub zatrzymać środowiska IR](manage-azure-ssis-integration-runtime.md)).

Alternatywnie można utworzyć działania internetowe w potokach ADF uruchamianie/zatrzymywanie środowiska IR zgodnie z harmonogramem, np. uruchamianie rano, przed wykonaniem dziennego obciążenia ETL i zatrzymując ją po południu, po wykonaniu.  Ponadto można połączyć w łańcuch działanie wykonanie pakietu SSIS między dwa działania internetowe, które uruchamiają i zatrzymują środowiska IR, dzięki czemu środowiska IR będzie uruchamianie/zatrzymywanie na żądanie w odpowiednim czasie przed lub po wykonywanie pakietu. Aby uzyskać więcej informacji o działaniu wykonywanie pakietu SSIS, zobacz [uruchamianie pakietów SSIS za pomocą działania wykonywania pakietów SSIS w potoku usługi ADF](how-to-invoke-ssis-package-ssis-activity.md) artykułu.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli nie zostały już aprowizowane środowiska IR Azure-SSIS, aprowizować go, wykonując instrukcje przedstawione w [samouczek](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>Tworzenie i planowanie potoków ADF, które uruchomić i zatrzymać środowisko Azure-SSIS IR
W tej sekcji dowiesz się, jak skorzystać z działań w sieci Web w potokach ADF uruchamianie/zatrzymywanie środowiska IR Azure-SSIS, zgodnie z harmonogramem lub uruchom i zatrzymaj ją na żądanie. Firma Microsoft przeprowadzi Cię do tworzenia trzech potoków: 

1. Pierwszy potok zawiera działanie sieci Web, który uruchamia usługi Azure-SSIS IR. 
2. Drugi potok zawiera działanie sieci Web, które zatrzymuje usługi Azure-SSIS IR.
3. Trzeci potok zawiera działanie wykonanie pakietu SSIS łańcuchowa między dwa działania internetowe, których uruchamianie/zatrzymywanie usługi Azure-SSIS IR. 

Po utworzeniu i przetestować te potoki można Tworzenie wyzwalacza harmonogramu i skojarzyć ją z dowolnym potoku. Wyzwalacz harmonogramu definiuje harmonogramu uruchamiania funkcji skojarzone potoku. 

Na przykład można utworzyć dwa wyzwalacze, pierwszy z nich jest zaplanowane do uruchomienia codziennie o godzinie 6: 00 i skojarzone z pierwszego potoku, a drugi jest zaplanowane do uruchomienia codziennie o godzinie 18: 00 i skojarzone z drugiego potoku.  Dzięki temu masz okres między 6: 00 do 18: 00 każdego dnia, gdy środowiska IR jest uruchomiona, gotowe do wykonywania codziennych obciążeń ETL.  

Jeśli utworzysz trzeci wyzwalacz, który jest zaplanowane do uruchomienia codziennie o północy i skojarzone z potoku trzeci, to potoku będzie przeprowadzana o północy każdego dnia, od środowiska IR tuż przed wykonaniem pakietu, następnie wykonywanie do pakietu i natychmiast zatrzymywanie środowiska IR tylko po wykonaniu pakietu, więc środowiska IR nie będzie uruchomiony podczas bezczynności.

### <a name="create-your-adf"></a>Tworzenie usługi ADF

1. Zaloguj się w [portalu Azure](https://portal.azure.com/).    
2. Kliknij przycisk **Nowy** w lewym menu, kliknij pozycję **Dane + analiza**, a następnie kliknij pozycję **Data Factory**. 
   
   ![Nowy-> Fabryka danych](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
   
3. W **nowa fabryka danych** wpisz **MyAzureSsisDataFactory** dla **nazwa**. 
      
   ![Strona Nowa fabryka danych](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Nazwa usługi ADF musi być globalnie unikatowa. Jeśli zostanie wyświetlony następujący błąd, Zmień nazwę usługi ADF (np. Twojanazwamyazuressisdatafactory) i spróbuj ponownie go utworzyć. Zobacz [Data Factory — reguły nazewnictwa](naming-rules.md) artykuł, aby dowiedzieć się więcej na temat reguł nazewnictwa artefaktów usługi ADF.
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. Wybieranie subskrypcji platformy Azure **subskrypcji** w ramach której chcesz utworzyć usługi ADF. 
5. W obszarze **Grupa zasobów** wykonaj jedną z następujących czynności:
     
   - Wybierz pozycję **Użyj istniejącej**, a następnie wybierz istniejącą grupę zasobów z listy rozwijanej. 
   - Wybierz **Utwórz nową**, a następnie wprowadź nazwę nowej grupy zasobów.   
         
   Aby dowiedzieć się więcej na temat grup zasobów, zobacz [używanie grup zasobów do zarządzania zasobami platformy Azure](../azure-resource-manager/resource-group-overview.md) artykułu.
   
6. Aby uzyskać **wersji**, wybierz opcję **V2** .
7. Aby uzyskać **lokalizacji**, wybierz jedną z lokalizacje obsługiwane na potrzeby tworzenia usługi ADF z listy rozwijanej.
8. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**.     
9. Kliknij pozycję **Utwórz**.
10. Na pulpicie nawigacyjnym platformy Azure zobaczysz następujący Kafelek ze stanem: **Wdrażanie fabryki danych**. 

    ![kafelek Wdrażanie fabryki danych](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
   
11. Po zakończeniu tworzenia zostanie wyświetlona strona usługi ADF, jak pokazano poniżej.
   
    ![Strona główna fabryki danych](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
   
12. Kliknij przycisk **tworzenie i monitorowanie** można uruchomić usługi ADF interfejsu użytkownika/aplikacji, w osobnej karcie.

### <a name="create-your-pipelines"></a>Tworzenie potoków

1. W **zaczynajmy** wybierz opcję **Utwórz potok**. 

   ![Strona Wprowadzenie](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
   
2. W **działania** przybornika, rozwiń węzeł **ogólne** menu, przeciągania i upuszczania **Web** działania na powierzchnię projektanta potoku. W **ogólne** karty w oknie właściwości działania, Zmień nazwę działania na **startMyIR**. Przełącz się do **ustawienia** kartę, a następnie wykonaj następujące czynności.

    1. Dla **adresu URL**, wprowadź następujący adres URL dla interfejsu API REST, który rozpoczyna się Azure-SSIS IR, zastępując `{subscriptionId}`, `{resourceGroupName}`, `{factoryName}`, i `{integrationRuntimeName}` rzeczywistymi wartościami dla środowiska IR: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01` Alternatywnie też skopiuj i wklej identyfikator zasobu środowiska IR z jego monitorowania strony w ADF interfejsu użytkownika/aplikacji, aby zastąpić następująca część powyższy adres URL: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`
    
       ![Identyfikator zasobu środowiska IR ADF SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png)
  
    2. Dla **metoda**, wybierz opcję **WPIS**. 
    3. Aby uzyskać **treści**, wprowadź `{"message":"Start my IR"}`. 
    4. Dla **uwierzytelniania**, wybierz opcję **MSI** Aby użyć tożsamości zarządzanej dla usługi ADF, zobacz [tożsamości zarządzanej przez usługę Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) artykuł, aby uzyskać więcej informacji.
    5. Aby uzyskać **zasobów**, wprowadź `https://management.azure.com/`.
    
       ![Harmonogram działania w sieci Web usługi ADF środowisko SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png)
  
3. Klonowanie pierwszego potoku, aby utworzyć drugi, zmieniając nazwę działania na **stopMyIR** i zastępuje następujące właściwości.

    1. Dla **adresu URL**, wprowadź następujący adres URL dla interfejsu API REST, który zatrzymuje Azure-SSIS IR, zastępując `{subscriptionId}`, `{resourceGroupName}`, `{factoryName}`, i `{integrationRuntimeName}` rzeczywistymi wartościami dla środowiska IR: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`
    
    2. Aby uzyskać **treści**, wprowadź `{"message":"Stop my IR"}`. 

4. Tworzenie potoku trzeci, przeciąganie i upuszczanie **wykonywanie pakietu SSIS** działanie z **działania** przybornika w Projektancie potoku powierzchni i skonfiguruj go zgodnie z instrukcjami w [ Wywoływanie pakietów SSIS za pomocą działania wykonywania pakietów SSIS w usłudze ADF](how-to-invoke-ssis-package-ssis-activity.md) artykułu.  Alternatywnie, można użyć **procedury składowanej** działania zamiast i skonfiguruj go zgodnie z instrukcjami w [wywoływanie pakietów SSIS za pomocą działania procedury składowanej w usłudze ADF](how-to-invoke-ssis-package-stored-procedure-activity.md) artykułu.  Następnie połączony działanie wykonanie procedury składowanej/pakietu SSIS między dwa działania internetowe, których uruchamianie/zatrzymywanie środowiska IR jest podobne do tych działań w sieci Web w potokach pierwszy/drugi.

   ![ADF działanie internetowe na żądanie środowisko SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png)

5. Przypisz tożsamości zarządzanej dla usługi ADF **Współautor** roli do samego siebie, więc działania internetowe w jej potoki może wywołać interfejs API REST do uruchamiania/zatrzymywania środowisk IR Azure-SSIS aprowizowane w nim.  Na stronie usługi ADF w witrynie Azure portal, kliknij **kontrola dostępu (IAM)**, kliknij przycisk **+ Dodaj przypisanie roli**, a następnie na **Dodaj przypisanie roli** blok, wykonaj następujące czynności.

    1. Aby uzyskać **roli**, wybierz opcję **Współautor**. 
    2. Aby uzyskać **Przypisz dostęp do**, wybierz opcję **użytkownika, grupy lub jednostki usługi Azure AD**. 
    3. Aby uzyskać **wybierz**, wyszukaj nazwę usługi ADF i wybierz ją. 
    4. Kliknij pozycję **Zapisz**.
    
   ![ADF zarządzanych tożsamości przypisania roli](./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png)

6. Weryfikowanie usługi ADF i wszystkie ustawienia potoku, klikając **Sprawdź, czy wszystkie / Validate** na pasku narzędzi potoku/fabryki. Zamknij **dane wyjściowe weryfikacji potoku usługi fabryka/** , klikając **>>** przycisku.  

   ![Weryfikowanie potoku](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-your-pipelines"></a>Uruchomienia potoków testu

1. Wybierz **przebiegu testu** na pasku narzędzi dla każdego potoku i zobacz **dane wyjściowe** okna w dolnym okienku. 

   ![Przebieg testu](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
    
2. Aby przetestować potoku trzeci, uruchom program SQL Server Management Studio (SSMS). W **Połącz z serwerem** okna, wykonaj następujące czynności. 

    1. Aby uzyskać **nazwy serwera**, wprowadź  **&lt;nazwę serwera usługi Azure SQL Database&gt;. database.windows.net**.
    2. Wybierz **Opcje >>**.
    3. Aby uzyskać **Połącz z bazą danych**, wybierz opcję **SSISDB**.
    4. Wybierz przycisk **Połącz**. 
    5. Rozwiń **Integracja usług katalogów** -> **SSISDB** -> folder -> **projektów** -> Your SSIS Projekt -> **pakietów** . 
    6. Kliknij prawym przyciskiem myszy określonego pakietu SSIS do uruchamiania i wybierz **raporty** -> **raportów standardowych** -> **wszystkich wykonań**. 
    7. Sprawdź, że został uruchomiony. 

   ![Sprawdź wykonywania pakietów SSIS](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-your-pipelines"></a>Planowanie potoków

Teraz, gdy potoków działa zgodnie z oczekiwaniami, można utworzyć wyzwalaczy, aby uruchamiać je w określonym odstępach czasu. Aby uzyskać szczegółowe informacje dotyczące kojarzenia wyzwalacze z potoków, zobacz [wyzwalanie potoku zgodnie z harmonogramem](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule) artykułu.

1. Na pasku narzędzi potoku wybierz **wyzwalacza** i wybierz **nowy/Edytuj**. 

   ![Wyzwalacz -> Nowy/Edycja](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)

2. W **Dodawanie wyzwalaczy** okienku wybierz **+ nowy**.

   ![Dodawanie wyzwalaczy — nowy](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)

3. W **nowy wyzwalacz** okienko, wykonaj następujące czynności: 

    1. Aby uzyskać **nazwa**, wprowadź nazwę dla wyzwalacza. W poniższym przykładzie **Codzienne uruchamianie** jest nazwa wyzwalacza. 
    2. Aby uzyskać **typu**, wybierz opcję **harmonogram**. 
    3. Aby uzyskać **Start Data (UTC)**, wprowadź datę i godzinę w formacie UTC. 
    4. Aby uzyskać **cyklu**, wprowadź tempa wyzwalacza. W poniższym przykładzie jest **codzienne** po. 
    5. Dla **zakończenia**, wybierz opcję **zakończenia nie** lub wprowadź Data i godzina zakończenia po wybraniu **w dniu**. 
    6. Wybierz **aktywowano** aktywacji natychmiast, po opublikowaniu całego ustawienia usługi ADF. 
    7. Wybierz opcję **Dalej**.

   ![Wyzwalacz -> Nowy/Edycja](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
    
4. W **Parametry uruchamiania wyzwalacza** przejrzyj wszelkie ostrzeżenia, a wybierz **Zakończ**. 
5. Publikowanie całego ustawienia usługi ADF, wybierając **Opublikuj wszystkie** na pasku narzędzi fabryki. 

   ![Publikuj wszystko](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Monitoruj potoki i wyzwalacze w witrynie Azure portal

1. Aby monitorować uruchomienia wyzwalacza i uruchomienia potoków, należy użyć **Monitor** karty w lewej części interfejsu użytkownika usługi ADF/aplikacji. Aby uzyskać szczegółowe instrukcje, zobacz [monitorowanie potoku](quickstart-create-data-factory-portal.md#monitor-the-pipeline) artykułu.

   ![Uruchomienia potoków](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)

2. Aby wyświetlić uruchomienia działań skojarzone z uruchomieniem potoku, wybierz pierwszy link (**Wyświetl uruchomienia działań**) w **akcje** kolumny. Dla potoku trzeci, widoczne będą trzy uruchomienia działania, jeden dla każdego łańcuchowych działania w potoku (działanie sieci Web można uruchomić środowiska IR, działanie Stored Procedure do wykonywania pakietu i działania w sieci Web można zatrzymać środowiska IR). Aby wyświetlić potoku zostanie ponownie uruchomione, wybierz **potoki** link u góry.

   ![Uruchomienia działania](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)

3. Aby wyświetlić uruchomienia wyzwalacza, wybierz **uruchamiany jest wyzwalacz** z listy rozwijanej w obszarze **uruchomienia potoku** u góry. 

   ![Uruchomienia wyzwalacza](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>Monitoruj potoki i wyzwalacze przy użyciu programu PowerShell

Monitoruj potoki i wyzwalacze za pomocą skryptów, jak w następujących przykładach.

1. Pobierz stan uruchomienia potoku.

   ```powershell
   Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
   ```

2. Uzyskaj informacje na temat wyzwalacza.

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
   ```

3. Pobierz stan uruchomienia wyzwalacza.

   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
   ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Tworzenie i Planowanie elementu runbook usługi Azure Automation, który rozpoczyna/zatrzymuje Azure-SSIS IR

W tej sekcji dowiesz się do tworzenia elementów runbook usługi Azure Automation, który jest wykonywany skrypt programu PowerShell, uruchamianie/zatrzymywanie środowiska IR Azure-SSIS, zgodnie z harmonogramem.  Jest to przydatne, gdy chcesz wykonać dodatkowe skrypty przed lub po uruchamianie/zatrzymywanie środowiska IR do wpisu/pre przetwarzania.

### <a name="create-your-azure-automation-account"></a>Utwórz konto usługi Azure Automation

Jeśli nie masz już konto usługi Azure Automation, utworzyć zgodnie z instrukcjami w tym kroku. Aby uzyskać szczegółowe instrukcje, zobacz [Tworzenie konta usługi Azure Automation](../automation/automation-quickstart-create-account.md) artykułu. W ramach tego kroku, Utwórz **Uruchom jako platformy Azure** konta (jednostki w usłudze Azure Active Directory service) i przypisz mu **Współautor** roli w subskrypcji platformy Azure. Upewnij się, że jest tej samej subskrypcji, który zawiera usługi ADF za pomocą usługi Azure SSIS IR. Usługa Azure Automation będzie używał tego konta do uwierzytelniania w usłudze Azure Resource Manager i działają na Twoich zasobów. 

1. Uruchom przeglądarkę internetową **Microsoft Edge** lub **Google Chrome**. ADF interfejsu użytkownika/aplikacji jest obecnie obsługiwany tylko w Microsoft Edge i przeglądarki Google Chrome.
2. Zaloguj się w [portalu Azure](https://portal.azure.com/).    
3. Wybierz **New** w menu po lewej stronie wybierz **monitorowanie + zarządzanie**i wybierz **automatyzacji**. 

   ![Nowy -> Monitorowanie + Zarządzanie -> Automatyzacja](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
    
2. W **Dodawanie konta usługi Automation** okienko, wykonaj następujące czynności.

    1. Aby uzyskać **nazwa**, wprowadź nazwę konta usługi Azure Automation. 
    2. Aby uzyskać **subskrypcji**, wybierz subskrypcję, która ma usługi ADF dzięki Azure-SSIS IR. 
    3. Dla **grupy zasobów**, wybierz opcję **Utwórz nową** Aby utworzyć nową grupę zasobów lub **Użyj istniejącej** można wybrać istniejącą grupę. 
    4. Aby uzyskać **lokalizacji**, wybierz lokalizację dla konta usługi Azure Automation. 
    5. Upewnij się, **Tworzenie konta Uruchom jako platformy** jako **tak**. Jednostka usługi zostanie utworzona w usłudze Azure Active Directory i przypisane **Współautor** roli w subskrypcji platformy Azure.
    6. Wybierz **Przypnij do pulpitu nawigacyjnego** mogą być wyświetlane trwale pulpitu nawigacyjnego platformy Azure. 
    7. Wybierz pozycję **Utwórz**. 

   ![Nowy -> Monitorowanie + Zarządzanie -> Automatyzacja](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
   
3. Zobaczysz stan wdrożenia konto usługi Azure Automation w pulpitu nawigacyjnego platformy Azure i powiadomienia. 
    
   ![Wdrażanie usługi automation](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
    
4. Po został utworzony pomyślnie, zostanie wyświetlone strony głównej swojego konta usługi Azure Automation. 

   ![Strona główna usługi Automation](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-adf-modules"></a>Importowanie modułów usługi ADF

1. Wybierz **modułów** w **zasoby UDOSTĘPNIONE** sekcji w menu po lewej stronie, a następnie sprawdź, czy masz **AzureRM.DataFactoryV2**  +   **AzureRM.Profile** na liście modułów.

   ![Sprawdź wymagane moduły](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Jeśli nie masz **AzureRM.DataFactoryV2**, przejdź do galerii programu PowerShell dla [modułu AzureRM.DataFactoryV2](https://www.powershellgallery.com/packages/AzureRM.DataFactoryV2/), wybierz opcję **wdrażanie w usłudze Azure Automation**, wybieranie subskrypcji platformy Azure Konto usługi Automation, a następnie wybierz **OK**. Przejdź wstecz, aby wyświetlić **modułów** w **zasoby UDOSTĘPNIONE** sekcji w menu po lewej stronie i zaczekaj, aż zobaczysz **stan** z **AzureRM.DataFactoryV2** Moduł zmieniony na **dostępne**.

    ![Zweryfikować moduł usługi Data Factory](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Jeśli nie masz **AzureRM.Profile**, przejdź do galerii programu PowerShell dla [AzureRM.Profile module](https://www.powershellgallery.com/packages/AzureRM.profile/), wybierz opcję **wdrażanie w usłudze Azure Automation**, Wybieranie usługi Azure Automation konta, a następnie wybierz pozycję **OK**. Przejdź wstecz, aby wyświetlić **modułów** w **zasoby UDOSTĘPNIONE** sekcji w menu po lewej stronie i zaczekaj, aż zobaczysz **stan** z **AzureRM.Profile** Moduł zmieniony na **dostępne**.

    ![Zweryfikować moduł profilu](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-your-powershell-runbook"></a>Tworzenie elementu runbook programu PowerShell

Poniższa sekcja zawiera instrukcje tworzenia elementu runbook programu PowerShell. Skrypt skojarzony z elementem runbook albo rozpoczyna/zatrzymuje Azure-SSIS IR, oparte na polecenia, należy określić dla **operacji** parametru. W tej sekcji nie przewiduje kompletne szczegóły tworzenia elementu runbook. Aby uzyskać więcej informacji, zobacz [utworzyć element runbook](../automation/automation-quickstart-create-runbook.md) artykułu.

1. Przełącz się do **elementów Runbook** kartę, a następnie wybierz pozycję **+ Dodaj element runbook** z paska narzędzi. 

   ![Dodawanie przycisku elementu runbook](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
   
2. Wybierz **Utwórz nowy element runbook** i wykonaj następujące czynności: 

    1. Aby uzyskać **nazwa**, wprowadź **StartStopAzureSsisRuntime**.
    2. Aby uzyskać **typ elementu Runbook**, wybierz opcję **PowerShell**.
    3. Wybierz pozycję **Utwórz**.
    
   ![Dodawanie przycisku elementu runbook](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
   
3. Skopiuj i wklej poniższy skrypt programu PowerShell do okna skryptu elementu runbook. Zapisz, a następnie opublikować element runbook za pomocą **Zapisz** i **Publikuj** przycisków na pasku narzędzi. 

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
        Connect-AzAccount `
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
        Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

   ![Edytuj element runbook programu PowerShell](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
    
4. Przetestuj element runbook, wybierając **Start** przycisk na pasku narzędzi. 

   ![Element runbook przycisk Start](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
    
5. W **uruchamianie elementu Runbook** okienko, wykonaj następujące czynności: 

    1. Aby uzyskać **nazwy grupy zasobów**, wprowadź nazwę grupy zasobów zawierającej usługi ADF dzięki Azure-SSIS IR. 
    2. Aby uzyskać **nazwa FABRYKI danych**, wprowadź nazwę usługi ADF dzięki Azure-SSIS IR. 
    3. Aby uzyskać **AZURESSISNAME**, wprowadź nazwę środowiska Azure-SSIS IR. 
    4. Aby uzyskać **operacji**, wprowadź **START**. 
    5. Kliknij przycisk **OK**.  

   ![Uruchom okno elementu runbook](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
   
6. W oknie zadania wybierz **dane wyjściowe** kafelka. W oknie danych wyjściowych oczekiwania na wiadomość **### Ukończono ###** po stwierdzeniu **### uruchamianie ###**. Uruchamianie środowiska Azure-SSIS IR trwa około 20 minut. Zamknij **zadania** okna i get kopii do **Runbook** okna.

   ![Środowisko IR Azure SSIS — pracę](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
    
7. Powtórz dwa poprzednie kroki, za pomocą **ZATRZYMAĆ** jako wartość pozycji **operacji**. Uruchom element runbook ponownie, wybierając **Start** przycisk na pasku narzędzi. Wprowadź grupę zasobów, ADF i Azure-SSIS IR nazwy. Aby uzyskać **operacji**, wprowadź **ZATRZYMAĆ**. W oknie danych wyjściowych oczekiwania na wiadomość **### Ukończono ###** po stwierdzeniu **### zatrzymywanie ###**. Zatrzymywanie środowiska Azure-SSIS IR nie przyjmuje tak długo, jak ją uruchomić. Zamknij **zadania** okna i get kopii do **Runbook** okna.

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Tworzenie harmonogramów dla elementu runbook można uruchomić/zatrzymać środowisko Azure-SSIS IR

W poprzedniej sekcji opisano tworzenie elementu runbook usługi Azure Automation, który może rozpocząć lub zatrzymać Azure-SSIS IR. W tej sekcji utworzysz dwa harmonogramy dla elementu runbook. Podczas konfigurowania pierwszego harmonogramu, należy określić **START** dla **operacji**. Podobnie podczas konfigurowania drugi, należy określić **ZATRZYMAĆ** dla **operacji**. Aby uzyskać szczegółowy opis kroków tworzenia harmonogramów, zobacz [Utwórz harmonogram](../automation/shared-resources/schedules.md#creating-a-schedule) artykułu.

1. W **Runbook** wybierz **harmonogramy**i wybierz **+ Dodaj harmonogram** na pasku narzędzi. 

   ![Środowisko IR Azure SSIS — pracę](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
   
2. W **elementu Runbook z harmonogramem** okienko, wykonaj następujące czynności: 

    1. Wybierz **powiązania harmonogramu z elementem runbook**. 
    2. Wybierz **Utwórz nowy harmonogram**.
    3. W **nowy harmonogram** okienku, wprowadź **Uruchom środowisko IR codziennie** dla **nazwa**. 
    4. Aby uzyskać **rozpoczyna się**, wprowadź czas, który jest za kilka minut późniejsza niż bieżąca godzina. 
    5. Aby uzyskać **cyklu**, wybierz opcję **cyklicznie**. 
    6. Aby uzyskać **Powtórz co**, wprowadź **1** i wybierz pozycję **dzień**. 
    7. Wybierz pozycję **Utwórz**. 

   ![Harmonogram uruchamiania środowiska Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
    
3. Przełącz się do **parametry i ustawienia uruchamiania** kartę. Określ grupę zasobów, ADF i Azure-SSIS IR nazwy. Aby uzyskać **operacji**, wprowadź **START** i wybierz pozycję **OK**. Wybierz **OK** ponownie, aby zobaczyć harmonogramu na **harmonogramy** strony elementu runbook. 

   ![Harmonogram rozpoczęcie przeczytania artykułu środowiska Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
    
4. Powtórz dwa poprzednie kroki, aby utworzyć harmonogram o nazwie **zatrzymać środowisko IR codziennie**. Wprowadź czas, który jest co najmniej 30 minut od czasu określony dla **Uruchom środowisko IR codziennie** harmonogramu. Dla **operacji**, wprowadź **ZATRZYMAĆ** i wybierz **OK**. Wybierz **OK** ponownie, aby zobaczyć harmonogramu na **harmonogramy** strony elementu runbook. 

5. W **Runbook** wybierz **zadania** w menu po lewej stronie. Powinien zostać wyświetlony zadań utworzonych przez harmonogramy od określonego czasu oraz ich stan. Można wyświetlić szczegóły zadania, takie jak dane wyjściowe, podobnie jak wiesz po przetestować element runbook. 

   ![Harmonogram rozpoczęcie przeczytania artykułu środowiska Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
    
6. Po zakończeniu testowania, wyłączyć harmonogramy, edytując je. Wybierz **harmonogramy** w menu po lewej stronie wybierz **Start IR codziennie/Stop środowiska IR codziennie**i wybierz **nie** dla **włączone**. 

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujący wpis w blogu:
-   [Modernizacja i rozszerzanie przepływów pracy usługi ETL/ELT, za pomocą działania SSIS w potokach ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)

Zobacz następujące artykuły wchodzące w skład usług SSIS: 

- [Deploy, run, and monitor an SSIS package on Azure (Wdrażanie, uruchamianie i monitorowanie pakietu usług SSIS na platformie Azure)](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Connect to SSIS catalog on Azure (Łączenie z wykazem usług SSIS na platformie Azure)](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Schedule package execution on Azure (Planowanie wykonywania pakietów na platformie Azure)](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Connect to on-premises data sources with Windows authentication (Łączenie z lokalnymi źródłami danych przy użyciu uwierzytelniania systemu Windows)](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
