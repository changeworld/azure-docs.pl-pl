---
title: Uruchom pakiet SSIS za pomocą działania wykonywania pakietu SSIS — platforma Azure | Microsoft Docs
description: W tym artykule opisano sposób uruchamiania pakietu SQL Server Integration Services (SSIS) w potoku Azure Data Factory przy użyciu działania wykonaj pakiet SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 09/13/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 9057cefa5108924c57dbc85bbb895b31e804a51c
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000654"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Uruchom pakiet usług SSIS za pomocą działania wykonaj pakiet SSIS w Azure Data Factory
W tym artykule opisano sposób uruchamiania pakietu SQL Server Integration Services (SSIS) w potoku Azure Data Factory (ADF) przy użyciu działania wykonaj pakiet SSIS. 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Utwórz Azure-SSIS Integration Runtime (IR), jeśli jeszcze go nie masz, postępując zgodnie z instrukcjami krok po kroku w [samouczku: Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md)aprowizacji.

## <a name="run-a-package-in-the-azure-portal"></a>Uruchom pakiet w Azure Portal
W tej sekcji jest używany interfejs użytkownika (UI)/App do tworzenia potoków ADF za pomocą działania wykonywania pakietu SSIS, które uruchamia pakiet usług SSIS.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Tworzenie potoku za pomocą działania wykonywania pakietu SSIS
W tym kroku użyjesz interfejsu użytkownika/aplikacji ADF do utworzenia potoku. Dodaj do potoku działanie wykonaj pakiet SSIS i skonfiguruj je do uruchamiania pakietu usług SSIS. 

1. Na stronie Przegląd ADF/Strona główna w Azure Portal kliknij kafelek " **autor & monitor** ", aby uruchomić interfejs użytkownika/aplikację usługi ADF na osobnej karcie. 

   ![Strona główna fabryki danych](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Na stronie wprowadzenie kliknij pozycję **Utwórz potok**: 

   ![Strona Wprowadzenie](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

2. W przyborniku **działania** rozwiń węzeł **Ogólne**, a następnie przeciągnij & upuść działanie **Wykonaj pakiet SSIS** do powierzchni projektanta potoku. 

   ![Przeciągnij działanie wykonaj pakiet SSIS na powierzchnię projektanta](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. Na karcie **Ogólne** dla działania wykonaj pakiet SSIS Podaj nazwę i opis działania. Ustaw opcjonalny limit czasu i ponów wartości.

   ![Ustawianie właściwości na karcie Ogólne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. Na karcie **Ustawienia** dla działania wykonaj pakiet SSIS wybierz Azure-SSIS IR, w którym chcesz uruchomić pakiet. Jeśli pakiet używa uwierzytelniania systemu Windows w celu uzyskania dostępu do magazynów danych, np. serwerów SQL/udziałów plików lokalnych, Azure Files itd., zaznacz pole wyboru **uwierzytelnianie systemu Windows** i wprowadź wartości poświadczeń wykonywania pakietu (**domena** Hasło użytkownika)/. / Alternatywnie możesz użyć wpisów tajnych przechowywanych w Azure Key Vault (AKV) jako ich wartości. Aby to zrobić, kliknij pole wyboru **Magazyn kluczy Azure** obok odpowiedniego poświadczenia, wybierz/Edytuj istniejącą połączoną usługę AKV lub Utwórz nową, a następnie wybierz nazwę wpisu tajnego/wersję dla swojej wartości poświadczenia.  Podczas tworzenia/edytowania połączonej usługi AKV możesz wybrać lub edytować istniejącą AKV lub utworzyć nową, ale udzielić dostępu do tożsamości zarządzanej przez usługę ADF do AKV, jeśli jeszcze tego nie zrobiono. Możesz również wprowadzić wpisy tajne bezpośrednio w następującym formacie: `<AKV linked service name>/<secret name>/<secret version>`. Jeśli pakiet wymaga 32-bitowego środowiska uruchomieniowego, zaznacz pole wyboru **32-bitowe środowisko uruchomieniowe** . 

   W obszarze **Lokalizacja pakietu**wybierz pozycję **SSISDB**, **system plików (pakiet)** lub **system plików (projekt)** . W przypadku wybrania opcji **SSISDB** jako lokalizacji pakietu, która jest automatycznie wybierana, jeśli Azure-SSIS IR została zainicjowana przy użyciu wykazu usług SSIS (SSISDB) hostowanego przez wystąpienie Azure SQL Database Server/Managed, należy określić pakiet do uruchomienia, który został wdrożone w SSISDB. Jeśli Azure-SSIS IR jest uruchomiona, a pole wyboru **wpisów ręcznych** nie jest zaznaczone, możesz przeglądać i wybierać istniejące foldery/projekty/pakiety/środowiska z SSISDB. Kliknij przycisk **Odśwież** , aby pobrać nowo dodane foldery/projekty/pakiety/środowiska z SSISDB, tak aby były dostępne do przeglądania i wyboru. Aby przeglądać/wybierać środowiska dla wykonań pakietów, musisz wcześniej skonfigurować projekty, aby dodać te środowiska jako odwołania z tych samych folderów w obszarze SSISDB. Aby uzyskać więcej informacji, zobacz [Tworzenie/mapowanie środowisk SSIS](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014).

   W obszarze **poziom rejestrowania**Wybierz wstępnie zdefiniowany zakres rejestrowania dla wykonywania pakietu. Zaznacz **niestandardowe** pole wyboru, jeśli chcesz wprowadzić dostosowanej nazwy rejestrowania zamiast tego. 

   ![Ustawianie właściwości na karcie Ustawienia — automatyczne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Jeśli Azure-SSIS IR nie jest uruchomiona lub jest zaznaczone pole wyboru **wpisy ręczne** , możesz wprowadzić swoje ścieżki pakietu i środowiska z SSISDB bezpośrednio w następujących formatach: `<folder name>/<project name>/<package name>.dtsx` i. `<folder name>/<environment name>`

   ![Ustawianie właściwości na karcie Ustawienia — ręczne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Jeśli wybrano opcję **system plików (pakiet)** jako lokalizację pakietu, która jest automatycznie wybierana, jeśli Azure-SSIS IR została zainicjowana bez SSISDB, należy określić pakiet do uruchomienia, podając ścieżkę Universal NAMING Convention (UNC) do plik pakietu (`.dtsx`) w **ścieżce pakietu**. Jeśli na przykład przechowujesz pakiet w Azure Files, jego ścieżka pakietu będzie `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`. 
   
   W przypadku skonfigurowania pakietu w osobnym pliku należy również podać ścieżkę UNC do pliku konfiguracji (`.dtsConfig`) w **ścieżce konfiguracyjnej**. Jeśli na przykład przechowujesz konfigurację w Azure Files, jej ścieżka konfiguracji będzie `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`.

   ![Ustawianie właściwości na karcie Ustawienia — ręczne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   W przypadku wybrania opcji **system plików (projekt)** jako lokalizacji pakietu należy określić, aby pakiet był uruchamiany przez podanie ścieżki UNC do pliku projektu (`.ispac`) w **ścieżce projektu** i pliku pakietu (`.dtsx`) z projektu w  **Nazwa pakietu**. Na przykład jeśli projekt jest przechowywany w Azure Files, jego ścieżka projektu będzie `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`.

   ![Ustawianie właściwości na karcie Ustawienia — ręczne](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   Następnie należy określić poświadczenia, aby uzyskać dostęp do plików projektu/pakietu/konfiguracji. Jeśli wcześniej wprowadzono wartości poświadczeń wykonywania pakietu (patrz powyżej), można użyć ich ponownie, sprawdzając, czy jest zaznaczone pole wyboru **poświadczenia wykonywania pakietu** . W przeciwnym razie musisz wprowadzić wartości poświadczeń dostępu do pakietu (**hasło** **nazwy**/**domeny**/). Jeśli na przykład przechowujesz projekt/pakiet/konfigurację w Azure Files, **domena** `Azure`to; **Nazwa użytkownika** to `<storage account name>`;, a hasło to. `<storage account key>` Alternatywnie możesz użyć wpisów tajnych przechowywanych w AKV jako ich wartości (Zobacz powyżej). Te poświadczenia będą używane w celu uzyskania dostępu do pakietu i pakietów podrzędnych w zadaniu pakietu wykonaj wszystko z własnej ścieżki/tego samego projektu, a także konfiguracji, w tym określonych w pakietach. 
   
   Jeśli używasz poziomu ochrony **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** podczas tworzenia pakietu przy użyciu narzędzi SQL Server Data Tools (SSDT), musisz wprowadzić wartość hasła podczas **szyfrowania. hasło**. Alternatywnie można użyć wpisu tajnego przechowywanego w AKV jako jego wartość (Zobacz powyżej). W przypadku korzystania z poziomu ochrony **EncryptSensitiveWithUserKey** należy ponownie wprowadzić wartości poufne w plikach konfiguracyjnych lub na właściwości**menedżerów**/połączeń **parametrów SSIS**/ **Zastąp** tabulatory (patrz poniżej). Jeśli używasz poziomu ochrony **EncryptAllWithUserKey** , nie jest to obsługiwane, więc musisz ponownie skonfigurować pakiet, aby użyć innego poziomu ochrony za pośrednictwem SSDT lub `dtutil` narzędzia wiersza polecenia. 
   
   W obszarze **poziom rejestrowania**Wybierz wstępnie zdefiniowany zakres rejestrowania dla wykonywania pakietu. Zaznacz **niestandardowe** pole wyboru, jeśli chcesz wprowadzić dostosowanej nazwy rejestrowania zamiast tego. Jeśli chcesz rejestrować wykonywanie pakietów poza przy użyciu standardowych dostawców dzienników, które można określić w pakiecie, musisz określić folder dziennika, podając jego ścieżkę UNC w **ścieżce rejestrowania**. Jeśli na przykład przechowujesz dzienniki w Azure Files, ścieżka rejestrowania będzie `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`. Podfolder zostanie utworzony w tej ścieżce dla każdego uruchomionego pakietu i nazwanego po IDENTYFIKATORze uruchomienia działania pakietu SSIS, w którym pliki dziennika będą generowane co pięć minut. 
   
   Na koniec należy również określić poświadczenia, aby uzyskać dostęp do folderu dziennika. Jeśli wcześniej wprowadzono wartości poświadczeń dostępu do pakietu (patrz powyżej), można użyć ich ponownie, sprawdzając, czy jest zaznaczone pole wyboru **poświadczenia dostępu do pakietu** . W przeciwnym razie musisz wprowadzić wartości poświadczeń dostępu do rejestrowania (**hasło** **nazwy**/**domeny**/). Na przykład w przypadku przechowywania dzienników w Azure Files **domena** `Azure`to; **Nazwa użytkownika** to `<storage account name>`;, a **hasło** to `<storage account key>`. Alternatywnie możesz użyć wpisów tajnych przechowywanych w AKV jako ich wartości (Zobacz powyżej). Te poświadczenia będą używane do przechowywania dzienników. 
   
   W przypadku wszystkich ścieżek UNC wymienionych powyżej, w pełni kwalifikowana nazwa pliku musi być krótsza niż 260 znaków, a nazwa katalogu musi być krótsza niż 248 znaków.

5. Na karcie **parametry programu SSIS** dla działania wykonywania pakietu SSIS, jeśli Azure-SSIS IR jest uruchomiona, **SSISDB** jest wybrane jako lokalizacja pakietu, a pole wyboru **wpisy ręczne** na karcie **Ustawienia** nie jest zaznaczone, istniejące SSIS parametry w wybranym projekcie/pakiecie z SSISDB zostaną wyświetlone, aby przypisać do nich wartości. W przeciwnym razie możesz wprowadzić je pojedynczo, aby przypisać do nich wartości ręcznie — upewnij się, że istnieją i zostały poprawnie wprowadzone, aby pomyślnie wykonać pakiet. 
   
   W przypadku korzystania z poziomu ochrony **EncryptSensitiveWithUserKey** podczas tworzenia pakietu za pośrednictwem **SSDT i**/systemu plików (**projektu)** jako lokalizacji pakietu należy również wprowadzić ponownie poufne parametry do przypisywania wartości do nich w plikach konfiguracyjnych lub na tej karcie. 
   
   Podczas przypisywania wartości do parametrów można dodać zawartość dynamiczną, używając wyrażeń, funkcji, zmiennych systemowych ADF i parametrów/zmiennych potoku usługi ADF. Alternatywnie możesz użyć wpisów tajnych przechowywanych w AKV jako ich wartości (Zobacz powyżej).

   ![Ustawianie właściwości na karcie Parametry SSIS](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. Na karcie **menedżerowie połączeń** dla działania wykonaj pakiet SSIS, jeśli Azure-SSIS IR jest uruchomiona, **SSISDB** jest wybrane jako lokalizacja pakietu, a pole wyboru **wpisy ręczne** na karcie **Ustawienia** nie jest zaznaczone, istniejące zostaną wyświetlone menedżerowie połączeń w wybranym projekcie/pakiecie z SSISDB, które umożliwiają przypisanie wartości do ich właściwości. W przeciwnym razie możesz wprowadzić je pojedynczo, aby przypisać wartości do swoich właściwości ręcznie — upewnij się, że istnieją i zostały poprawnie wprowadzone, aby wykonanie pakietu zakończyło się pomyślnie. 
   
   W przypadku korzystania z poziomu ochrony **EncryptSensitiveWithUserKey** podczas tworzenia pakietu za pośrednictwem **SSDT i**/systemu plików (**projektu)** jako lokalizacji pakietu należy również wprowadzić ponownie poufne właściwości Menedżera połączeń do przypisywania wartości do nich w plikach konfiguracyjnych lub na tej karcie. 
   
   Podczas przypisywania wartości do właściwości Menedżera połączeń można dodać zawartość dynamiczną przy użyciu wyrażeń, funkcji, zmiennych systemowych ADF i parametrów/zmiennych potoków ADF. Alternatywnie możesz użyć wpisów tajnych przechowywanych w AKV jako ich wartości (Zobacz powyżej).

   ![Ustawianie właściwości na karcie menedżerów połączeń](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. Na karcie **Zastępowanie właściwości** dla działania wykonaj pakiet SSIS możesz wprowadzić ścieżki istniejących właściwości w wybranym pakiecie po jednym, aby przypisać do nich wartości ręcznie — upewnij się, że istnieją i są poprawnie wprowadzone dla pakietu wykonanie powiodło się, np. Aby zastąpić wartość zmiennej użytkownika, wprowadź jej ścieżkę w następującym formacie: `\Package.Variables[User::<variable name>].Value`. 
   
   W przypadku korzystania z poziomu ochrony **EncryptSensitiveWithUserKey** podczas tworzenia pakietu za pośrednictwem **SSDT i**/systemu plików (**projektu)** jako lokalizacji pakietu należy również wprowadzić ponownie poufne właściwości do przypisywania wartości do nich w plikach konfiguracyjnych lub na tej karcie. 
   
   Podczas przypisywania wartości do właściwości można dodać zawartość dynamiczną przy użyciu wyrażeń, funkcji, zmiennych systemowych ADF i parametrów/zmiennych potoków ADF.

   ![Ustawianie właściwości na karcie Zastępowanie właściwości](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   Wartości przypisane w plikach konfiguracji i na karcie *Parametry usług SSIS* można zastąpić przy użyciu właściwości **menedżerów**/połączeń**zastępują** karty, natomiast te przypisane na karcie **menedżerowie połączeń** mogą również przesłonięte przy użyciu karty **zastąpień właściwości** .

8. Aby sprawdzić poprawność konfiguracji potoku, kliknij pozycję **Weryfikuj** na pasku narzędzi. Aby zamknąć okno **Raport weryfikacji potoku**, kliknij pozycję **>>** .

9. Opublikuj potok w usłudze ADF, klikając przycisk **Opublikuj wszystko** . 

### <a name="run-the-pipeline"></a>Uruchamianie potoku
W tym kroku zostanie wyzwolone uruchomienie potoku. 

1. Aby wyzwolić uruchomienie potoku, kliknij pozycję **Wyzwól** na pasku narzędzi, a następnie kliknij pozycję **Wyzwól teraz**. 

   ![Wyzwól teraz](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. W oknie **Uruchomienie potoku** wybierz pozycję **Zakończ**. 

### <a name="monitor-the-pipeline"></a>Monitorowanie potoku

1. Przejdź do karty **Monitorowanie** po lewej stronie. Zobaczysz uruchomienie potoku i jego stan wraz z innymi informacjami (takimi jak godzina rozpoczęcia uruchamiania). Aby odświeżyć widok, kliknij przycisk **Odśwież**.

   ![Uruchomienia potoków](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Kliknij link **Wyświetl uruchomienia działania** w kolumnie **Akcje**. Zobaczysz tylko jedno uruchomienie działania, ponieważ potok ma tylko jedno działanie (działanie wykonywania pakietu SSIS).

   ![Uruchomienia działania](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Aby sprawdzić, czy pakiet został uruchomiony, możesz uruchomić następujące **zapytanie** względem bazy danych SSISDB na serwerze Azure SQL. 

   ```sql
   select * from catalog.executions
   ```

   ![Weryfikuj wykonania pakietu](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Możesz również uzyskać identyfikator wykonywania SSISDB z danych wyjściowych uruchomienia działania potoku, a następnie użyć identyfikatora, aby sprawdzić bardziej kompleksowe dzienniki wykonywania i komunikaty o błędach w SQL Server Management Studio (SSMS).

   ![Pobierz identyfikator wykonania.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Planowanie potoku przy użyciu wyzwalacza

Możesz również utworzyć zaplanowany wyzwalacz dla potoku, aby potok działał zgodnie z harmonogramem (co godzinę, codziennie itd.). Aby zapoznać się z przykładem, zobacz temat [Tworzenie fabryki danych — Data Factory interfejsu użytkownika](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>Uruchamianie pakietu przy użyciu programu PowerShell
W tej sekcji użyjesz Azure PowerShell, aby utworzyć potok ADF przy użyciu działania wykonywania pakietu SSIS uruchamiającego pakiet usług SSIS. 

Zainstaluj najnowsze moduły Azure PowerShell, postępując zgodnie z instrukcjami krok po kroku w temacie [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-an-adf-with-azure-ssis-ir"></a>Tworzenie podajnika APD przy użyciu Azure-SSIS IR
Możesz użyć istniejącego podajnika APD, który ma już zainicjowaną Azure-SSIS IR, lub utworzyć nowy ADF, Azure-SSIS IR postępując zgodnie z instrukcjami krok po kroku w [samouczku: Wdrażaj pakiety usług SSIS na platformie](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell)Azure za pomocą programu PowerShell.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Tworzenie potoku za pomocą działania wykonywania pakietu SSIS 
W tym kroku utworzysz potok z działaniem wykonaj pakiet SSIS. Działanie uruchamia pakiet SSIS. 

1. Utwórz plik JSON o nazwie **RunSSISPackagePipeline. JSON** w folderze **C:\ADF\RunSSISPackage** o zawartości podobnej do poniższego przykładu:

   > [!IMPORTANT]
   > Przed zapisaniem pliku Zastąp nazwy obiektów/opisy/ścieżki, wartości właściwości/parametrów, hasła i inne wartości zmiennych. 

   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx",
                       "type": "SSISDB"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
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
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
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

   Aby wykonać pakiety przechowywane w systemach plików/udziałach plików/Azure Files, możesz wprowadzić wartości właściwości lokalizacji pakietu/dziennika w następujący sposób.

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "type": "File",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Aby wykonać pakiety w ramach projektów przechowywanych w systemach plików/udziałach plików/Azure Files, możesz wprowadzić wartości dla właściwości lokalizacja pakietu w następujący sposób.

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

2. W Azure PowerShell przejdź do `C:\ADF\RunSSISPackage` folderu.

3. Aby utworzyć potok **RunSSISPackagePipeline**, uruchom polecenie cmdlet **Set-AzDataFactoryV2Pipeline** .

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
Użyj polecenia cmdlet **Invoke-AzDataFactoryV2Pipeline** , aby uruchomić potok. Polecenie cmdlet zwraca identyfikator uruchomienia potoku w celu monitorowania w przyszłości.

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

Możesz również monitorować potok przy użyciu Azure Portal. Instrukcje krok po kroku znajdują się w sekcji [monitorowanie potoku](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Planowanie potoku przy użyciu wyzwalacza
W poprzednim kroku uruchomiono potok na żądanie. Możesz również utworzyć wyzwalacz harmonogramu, aby uruchomić potok zgodnie z harmonogramem (co godzinę, codziennie itd.).

1. Utwórz plik JSON o nazwie mój **Trigger. JSON** w folderze **C:\ADF\RunSSISPackage** o następującej zawartości: 

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
2. W **Azure PowerShell**przejdź do folderu **C:\ADF\RunSSISPackage** .
3. Uruchom polecenie cmdlet **Set-AzDataFactoryV2Trigger** , które tworzy wyzwalacz. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
4. Domyślnie wyzwalacz jest w stanie zatrzymanym. Uruchom wyzwalacz, uruchamiając polecenie cmdlet **Start-AzDataFactoryV2Trigger** . 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
5. Upewnij się, że wyzwalacz został uruchomiony, uruchamiając polecenie cmdlet **Get-AzDataFactoryV2Trigger** . 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
6. Uruchom następujące polecenie po następnej godzinie. Na przykład, jeśli bieżący czas to 3:25 PM UTC, uruchom polecenie o godzinie 4 PM (UTC). 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Aby sprawdzić, czy pakiet został uruchomiony, możesz uruchomić następujące zapytanie względem bazy danych SSISDB na serwerze Azure SQL. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Następne kroki
Zobacz następujący wpis w blogu:
-   [Modernizowanie i zwiększanie przepływów pracy ETL/ELT przy użyciu działań SSIS w potokach ADF](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
