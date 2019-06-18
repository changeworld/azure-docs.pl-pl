---
title: Uruchamianie pakietów SSIS za pomocą działania wykonywanie pakietu SSIS — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób uruchamiania pakietu usług SQL Server Integration Services (SSIS) w potoku usługi Azure Data Factory za pomocą działania wykonywanie pakietu SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 03/19/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 7287dc2fccf461cf23c45202336e3d92bc5a40aa
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66152981"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Uruchamianie pakietów SSIS za pomocą działania wykonywania pakietów SSIS w usłudze Azure Data Factory
W tym artykule opisano sposób uruchamiania pakietu SSIS w potoku usługi Azure Data Factory (ADF) za pomocą działania wykonywanie pakietu SSIS. 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tworzenie środowiska Azure-SSIS Integration Runtime (IR), jeśli nie masz już zgodnie z instrukcjami krok po kroku w [samouczka: Wdrażanie pakietów usług SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>Uruchom pakiet w witrynie Azure portal
W tej sekcji użyjesz ADF interfejsu użytkownika (UI) / aplikacji na tworzenie ADF potoku z działaniem wykonywanie pakietu SSIS, które uruchamia pakietu SSIS.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Tworzenie potoku z działaniem wykonywanie pakietu SSIS
W tym kroku używasz ADF interfejsu użytkownika/aplikacji do tworzenia potoku. Możesz dodać działanie wykonanie pakietu SSIS do potoku i skonfigurowanie pod kątem uruchamiania pakietu SSIS. 

1. Na stronie Przegląd/głównej usługi ADF w witrynie Azure portal kliknij **tworzenie i monitorowanie** Kafelek, aby uruchomić interfejs użytkownika usługi ADF/aplikację w osobnej karcie. 

   ![Strona główna fabryki danych](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Na **zaczynajmy** kliknij **Utwórz potok**: 

   ![Strona Wprowadzenie](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

2. W **działania** przybornika, rozwiń węzeł **ogólne**, przeciąganie i upuszczanie **wykonywanie pakietu SSIS** działania do powierzchni projektanta potoku. 

   ![Przeciągnij działanie wykonywanie pakietów usług SSIS na powierzchnię projektanta](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. Na **ogólne** karta działanie wykonanie pakietu SSIS, podaj nazwę i opis działania. Ustaw opcjonalny limit czasu, a następnie spróbuj ponownie wartości.

   ![Ustaw właściwości na karcie Ogólne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. Na **ustawienia** wykonywanie pakietu SSIS działania, a następnie wybierz usługi Azure-SSIS IR, który jest skojarzony z bazy danych SSISDB wdrożonym pakiecie. Jeśli pakiet korzysta z uwierzytelniania Windows, aby mieć dostęp do magazynów danych, np. plik serwerów SQL udostępnia lokalnie, usługi Azure Files, itp., sprawdź **uwierzytelniania Windows** pole wyboru i wprowadź domenę, nazwę użytkownika i hasło dla pakietu wykonanie. Jeśli pakiet wymaga 32-bitowe środowisko uruchomieniowe do uruchomienia, sprawdź **32-bitowe środowisko uruchomieniowe** pola wyboru. Aby uzyskać **poziom rejestrowania**, wybierz wstępnie zdefiniowany zakres rejestrowania dla swojej wykonanie pakietu. Sprawdź **dostosowany** pole wyboru, jeśli chcesz zamiast tego wprowadź swoją nazwę Rejestrowanie niestandardowe. Gdy środowiska IR Azure-SSIS jest uruchomiony i **ręcznego wprowadzania** pole wyboru jest zaznaczone, możesz przeglądać i wybrać istniejące foldery/projektów/pakietów/środowiska z bazy danych SSISDB. Kliknij przycisk **Odśwież** przycisk, aby pobrać nowo dodanych folderów/projektów/pakietów/środowiska z bazy danych SSISDB, dzięki czemu są one dostępne do przeglądania i wybór. 

   ![Ustaw właściwości na karcie Ustawienia — automatyczne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Gdy środowiska IR Azure-SSIS nie jest uruchomiona lub **ręcznego wprowadzania** pole wyboru jest zaznaczone, możesz wprowadzić Twoje ścieżki pakietu i środowisko z bazy danych SSISDB, bezpośrednio w następujących formatach: `<folder name>/<project name>/<package name>.dtsx` i `<folder name>/<environment name>`.

   ![Ustaw właściwości na karcie Ustawienia - Podręcznik](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

5. Na **parametry SSIS** karcie wykonywanie pakietu SSIS działania, gdy działa środowiska IR Azure-SSIS i **ręcznego wprowadzania** pole wyboru na **ustawienia** karta nie jest zaznaczone, Przypisywanie wartości do nich pojawi się istniejących parametrów SSIS do wybranego projektu/pakietu, z bazy danych SSISDB. W przeciwnym razie można je wprowadzić konflikty po kolei, aby przypisać do nich wartości ręcznie — upewnij się, że istnieją i zostaną wprowadzone poprawnie dla wykonywanie pakietu zakończyło się sukcesem. Możesz dodać zawartość dynamiczna na ich wartości, przy użyciu wyrażeń, funkcje, zmienne systemowe ADF i zmienne parametrów potoku usługi ADF. Alternatywnie można użyć kluczy tajnych przechowywanych w usługi Azure Key Vault (AKV) jako ich wartości. W tym celu należy kliknąć **usługi AZURE KEY VAULT** pole wyboru obok odpowiedniego parametru wybierz/edytowanie istniejącej usługi AKV połączone lub Utwórz nową, a następnie wybierz wpisu tajnego nazwa/wersja dla wartości parametru.  Po użytkownik Utwórz/Edytuj AKV połączone usługi, możesz wybrać/Edytuj swoje istniejące AKV lub Utwórz nową, ale Udziel dostępu tożsamości usługi ADF zarządzanych do usługi AKV Jeśli nie zostało to jeszcze zrobione. Możesz też wprowadzić klucze tajne bezpośrednio w następującym formacie: `<AKV linked service name>/<secret name>/<secret version>`.

   ![Ustaw właściwości na karcie Parametry SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. Na **Menedżerowie połączeń** karcie wykonywanie pakietu SSIS działania, gdy działa środowiska IR Azure-SSIS i **ręcznego wprowadzania** pole wyboru na **ustawienia** karta nie jest zaznaczone, istniejące Menedżerowie połączeń do wybranego projektu/pakietu, z bazy danych SSISDB, pojawi się przypisywanie wartości do ich właściwości. W przeciwnym razie można je wprowadzić konflikty po kolei, aby ręcznie przypisać wartości do ich właściwości — upewnij się, że istnieją i zostaną wprowadzone poprawnie dla wykonywanie pakietu zakończyło się sukcesem. Możesz dodać zawartość dynamiczną, ich wartości właściwości, za pomocą wyrażenia, funkcje, zmienne systemowe ADF i zmienne parametrów potoku usługi ADF. Alternatywnie można użyć kluczy tajnych przechowywanych w usługi Azure Key Vault (AKV) jako wartości ich właściwości. W tym celu należy kliknąć **usługi AZURE KEY VAULT** pole wyboru obok odpowiednie właściwości, wybierz/edytowanie istniejącej usługi AKV połączone lub Utwórz nową, a następnie wybierz wpisu tajnego nazwa/wersja dla wartości właściwości.  Po użytkownik Utwórz/Edytuj AKV połączone usługi, możesz wybrać/Edytuj swoje istniejące AKV lub Utwórz nową, ale Udziel dostępu tożsamości usługi ADF zarządzanych do usługi AKV Jeśli nie zostało to jeszcze zrobione. Możesz też wprowadzić klucze tajne bezpośrednio w następującym formacie: `<AKV linked service name>/<secret name>/<secret version>`.

   ![Ustaw właściwości na karcie Menedżerowie połączeń](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. Na **właściwość zastępuje** kartę działań wykonywanie pakietu SSIS ścieżki istniejących właściwości można wprowadzić do wybranego pakietu z bazy danych SSISDB pojedynczo, aby przypisać do nich wartości ręcznie — upewnij się, że istnieją i są poprawnie wprowadzono wykonywanie pakietu zakończyło się sukcesem, np. Aby zastąpić wartość zmiennej użytkownika, wprowadź ścieżkę w następującym formacie: `\Package.Variables[User::YourVariableName].Value`. Można również dodać zawartość dynamiczną, do ich wartości, korzystając z wyrażenia, funkcje, zmienne systemowe ADF i zmienne parametrów potoku usługi ADF.

   ![Ustaw właściwości na karcie właściwości zastąpienia](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

8. Aby sprawdzić poprawność konfiguracji potoku, kliknij przycisk **weryfikacji** na pasku narzędzi. Aby zamknąć okno **Raport weryfikacji potoku**, kliknij pozycję **>>** .

9. Opublikuj potoku usługi ADF, klikając **Opublikuj wszystkie** przycisku. 

### <a name="run-the-pipeline"></a>Uruchamianie potoku
W tym kroku możesz wyzwolić uruchomienie potoku. 

1. Aby wyzwolić uruchomienie potoku, kliknij pozycję **wyzwalacza** na pasku narzędzi, a następnie kliknij przycisk **Wyzwól teraz**. 

   ![Wyzwól teraz](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. W oknie **Uruchomienie potoku** wybierz pozycję **Zakończ**. 

### <a name="monitor-the-pipeline"></a>Monitorowanie potoku

1. Przejdź do karty **Monitorowanie** po lewej stronie. Widzisz uruchomienie potoku i jego stan, oraz inne informacje (na przykład uruchom Start czasu). Aby odświeżyć widok, kliknij przycisk **Odśwież**.

   ![Uruchomienia potoków](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Kliknij link **Wyświetl uruchomienia działania** w kolumnie **Akcje**. Zobaczysz tylko jedno działanie, Uruchom jako potok ma tylko jedno działanie (działanie wykonanie pakietu SSIS).

   ![Uruchomienia działania](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Możesz uruchomić następujące polecenia, **zapytania** względem bazy danych SSISDB bazy danych serwera Azure SQL, aby sprawdzić, czy pakiet, który został wykonany. 

   ```sql
   select * from catalog.executions
   ```

   ![Sprawdź pakiet wykonań](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Można również uzyskać identyfikator wykonywania SSISDB z danych wyjściowych uruchomienia działania potoku i użyj Identyfikatora, aby sprawdzić dzienniki wykonywania bardziej kompleksowe i komunikaty o błędach w programie SSMS.

   ![Pobierz identyfikator wykonania.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Potok za pomocą wyzwalacza harmonogramu

Można również utworzyć zaplanowane wyzwalanie dla potoku, tak aby potok jest uruchamiany zgodnie z harmonogramem (co godzinę, codziennie itp.). Aby uzyskać przykład, zobacz [tworzenie fabryki danych — interfejs użytkownika usługi Data Factory](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Uruchom pakiet przy użyciu programu PowerShell
W tej sekcji użyjesz programu Azure PowerShell do utworzenia potoku usługi ADF z działania wykonywanie pakietu SSIS, które uruchamia pakietu SSIS. 

Zainstaluj najnowsze moduły programu Azure PowerShell, postępując zgodnie z instrukcjami krok po kroku [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-an-adf-with-azure-ssis-ir"></a>Tworzenie usługi ADF za pomocą środowiska Azure-SSIS IR
Możesz użyć istniejącego ADF, który ma już Azure-SSIS IR aprowizowane lub utworzyć nowe usługi ADF środowiska Azure-SSIS IR, postępując zgodnie z instrukcjami krok po kroku w [samouczka: Wdrażanie pakietów usług SSIS na platformie Azure za pośrednictwem programu PowerShell](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Tworzenie potoku z działaniem wykonywanie pakietu SSIS 
W tym kroku utworzysz potok z działaniem wykonywanie pakietu SSIS. Działanie zostanie uruchomione pakietu SSIS. 

1. Utwórz plik JSON o nazwie **RunSSISPackagePipeline.json** w **C:\ADF\RunSSISPackage** folderze o zawartości jest podobne do poniższego przykładu:

   > [!IMPORTANT]
   > Zastąp nazwy obiektów, opisy i ścieżek, właściwości i wartości parametrów, hasła i inne wartości zmiennej przed zapisaniem pliku. 

   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "mySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "myAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyDomain",
                       "userName": "MyUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "**********"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "FolderName/ProjectName/PackageName.dtsx"
                   },
                   "environmentPath": "FolderName/EnvironmentName",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyPipelineParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MySecret"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "userName": {
                               "value": "sa"
                           },
                           "passWord": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "abc"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "userName": {
                               "value": {
                                   "value": "@pipeline().parameters.MyUsername",
                                   "type": "Expression"
                               }
                           },
                           "passWord": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyPassword",
                                   "secretVersion": "3a1b74e361bf4ef4a00e47053b872149"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

2. W programie Azure PowerShell Przełącz się do `C:\ADF\RunSSISPackage` folderu.

3. Aby utworzyć potok **RunSSISPackagePipeline**Uruchom **AzDataFactoryV2Pipeline zestaw** polecenia cmdlet.

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Oto przykładowe dane wyjściowe:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>Uruchamianie potoku
Użyj **Invoke AzDataFactoryV2Pipeline** polecenia cmdlet, aby uruchomić potok. Polecenie cmdlet zwraca identyfikator uruchomienia potoku w celu monitorowania w przyszłości.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>Monitorowanie potoku

Uruchom następujący skrypt programu PowerShell, aby stale sprawdzać stan uruchomienia potoku do momentu zakończenia kopiowania danych. Skopiuj/wklej poniższy skrypt w oknie programu PowerShell i naciśnij klawisz ENTER. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

Można również monitorować potok przy użyciu witryny Azure portal. Aby uzyskać instrukcje krok po kroku, zobacz [monitorowanie potoku](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Potok za pomocą wyzwalacza harmonogramu
W poprzednim kroku uruchomiono potoku na żądanie. Można również utworzyć wyzwalacz harmonogramu, aby uruchomić potok zgodnie z harmonogramem (co godzinę, codziennie itp.).

1. Utwórz plik JSON o nazwie **MyTrigger.json** w **C:\ADF\RunSSISPackage** folderu o następującej zawartości: 

   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
2. W **programu Azure PowerShell**, przełącz się do **C:\ADF\RunSSISPackage** folderu.
3. Uruchom **AzDataFactoryV2Trigger zestaw** polecenia cmdlet, który tworzy wyzwalacz. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
4. Domyślnie wyzwalacz jest w stanie zatrzymania. Uruchom wyzwalacz, uruchamiając **Start AzDataFactoryV2Trigger** polecenia cmdlet. 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
5. Upewnij się, że wyzwalacz jest uruchomiona, uruchamiając **Get AzDataFactoryV2Trigger** polecenia cmdlet. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
6. Uruchom następujące polecenie, po następnej pełnej godziny. Na przykład jeśli aktualna godzina jest 3:25 czasu UTC, uruchom polecenie o 16: 00 czasu UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Można uruchomić następujące zapytanie względem bazy danych SSISDB znajdujących się na serwerze Azure SQL, aby sprawdzić, czy pakiet, który został wykonany. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujący wpis w blogu:
-   [Modernizacja i rozszerzanie przepływów pracy usługi ETL/ELT, za pomocą działania SSIS w potokach ADF](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)
