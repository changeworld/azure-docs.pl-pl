---
title: Rozwiązywanie problemów Azure Data Factory
description: Dowiedz się, jak rozwiązywać problemy związane z zewnętrznymi kontrolkami w Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 66590e40ff2440a166310ec3d57026f5a2cdbd1d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676845"
---
# <a name="troubleshoot-azure-data-factory"></a>Rozwiązywanie problemów Azure Data Factory

W tym artykule przedstawiono typowe metody rozwiązywania problemów z zewnętrznymi działaniami związanymi z kontrolą w Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Działanie łącznika i kopiowania

W przypadku problemów dotyczących łącznika, np. Napotkano błąd przy użyciu działania kopiowania, zobacz [Rozwiązywanie problemów Azure Data Factory łączników](connector-troubleshoot-guide.md).

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Kod błędu: 3200

- **Komunikat**: Błąd 403.

- **Przyczyna**: `The Databricks access token has expired.`

- **Zalecenie**: domyślnie token dostępu Azure Databricks jest ważny przez 90 dni. Utwórz nowy token i zaktualizuj połączoną usługę.


### <a name="error-code--3201"></a>Kod błędu: 3201

- **Komunikat**: `Missing required field: settings.task.notebook_task.notebook_path.`

- **Przyczyna**: `Bad authoring: Notebook path not specified correctly.`

- **Zalecenie**: Określ ścieżkę notesu w działaniu datakosteks.

<br/>

- **Komunikat**: `Cluster   ... does not exist.`

- **Przyczyna**: `Authoring error: Databricks cluster does not exist or has been deleted.`

- **Zalecenie**: Sprawdź, czy klaster datakostki istnieje.

<br/>

- **Komunikat**: `Invalid Python file URI.... Please visit Databricks user guide for supported URI schemes.`

- **Przyczyna**: `Bad authoring.`

- **Zalecenie**: Określ ścieżki bezwzględne dla schematów adresowania obszaru roboczego lub `dbfs:/folder/subfolder/foo.py` dla plików przechowywanych w systemie plików datakostki.

<br/>

- **Komunikat**: `{0} LinkedService should have domain and accessToken as required properties.`

- **Przyczyna**: `Bad authoring.`

- **Zalecenie**: Sprawdź [definicję połączonej usługi](compute-linked-services.md#azure-databricks-linked-service).

<br/>

- **Komunikat**: `{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Przyczyna**: `Bad authoring.`

- **Zalecenie**: Sprawdź [definicję połączonej usługi](compute-linked-services.md#azure-databricks-linked-service).

<br/>

- **Komunikat**: `Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Przyczyna**: `Bad authoring.`

- **Zalecenie**: Zapoznaj się z komunikatem o błędzie. 

<br/>

### <a name="error-code--3202"></a>Kod błędu: 3202

- **Komunikat**: `There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Przyczyna**: `Too many Databricks runs in an hour.`

- **Zalecenie**: Sprawdź, czy wszystkie potoki korzystające z tego obszaru roboczego datakostki są używane na potrzeby ich tworzenia zadań.  Jeśli potoki uruchomiły zbyt wiele kostek datakostków, Migruj niektóre potoki do nowego obszaru roboczego.

<br/>

- **Komunikat**: `Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Przyczyna**: `Authoring error: No value provided for the parameter.`

- **Zalecenie**: Sprawdź kod JSON potoku i upewnij się, że wszystkie parametry w notesie baseParameters określają niepustą wartość.

<br/>

- **Komunikat**: `User: `SimpleUserContext {userId =..., name =user@company.com, orgId =...}` is not   authorized to access cluster.`

- **Przyczyna**: użytkownik, który wygenerował token dostępu, nie może uzyskać dostępu do klastra datakostks określonego w połączonej usłudze.

- **Zalecenie**: Upewnij się, że użytkownik ma wymagane uprawnienia w obszarze roboczym.


### <a name="error-code--3203"></a>Kod błędu: 3203

- **Komunikat**: `The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Przyczyna**: klaster został przerwany. W przypadku klastrów interaktywnych może to być sytuacja wyścigu.

- **Zalecenie**: najlepszym sposobem na uniknięcie tego błędu jest użycie klastrów zadań.


### <a name="error-code--3204"></a>Kod błędu: 3204

- **Komunikat**: `Job execution failed.`

- **Przyczyna**: komunikaty o błędach wskazują różne problemy, takie jak nieoczekiwany stan klastra lub określone działanie. W ogóle nie jest wyświetlany żaden komunikat o błędzie. 

- **Zalecenie**: nie dotyczy


## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

Poniższa tabela dotyczy języka U-SQL.


### <a name="error-code--2709"></a>Kod błędu: 2709

- **Komunikat**: `The access token is from the wrong tenant.`

- **Przyczyna**: nieprawidłowa dzierżawa Azure Active Directory (Azure AD).

- **Zalecenie**: nieprawidłowa dzierżawa Azure Active Directory (Azure AD).

<br/>

- **Komunikat**: `We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Przyczyna**: ten błąd jest spowodowany przez ograniczenie na Data Lake Analytics.

- **Zalecenie**: Zmniejsz liczbę przesłanych zadań do Data Lake Analytics, zmieniając wyzwalacze Data Factory i ustawienia współbieżności w działaniach. Lub Zwiększ limity Data Lake Analytics.

<br/>

- **Komunikat**: `This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Przyczyna**: ten błąd jest spowodowany przez ograniczenie na Data Lake Analytics. 

- **Zalecenie**: Zmniejsz liczbę przesłanych zadań do Data Lake Analytics, zmieniając wyzwalacze Data Factory i ustawienia współbieżności w działaniach. Lub Zwiększ limity Data Lake Analytics.


### <a name="error-code--2705"></a>Kod błędu: 2705

- **Komunikat**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Przyczyna**: Nazwa główna usługi lub certyfikat nie ma dostępu do pliku w magazynie.

- **Zalecenie**: Upewnij się, że nazwa główna usługi lub certyfikat udostępniany przez użytkownika dla zadań Data Lake Analytics ma dostęp do konta Data Lake Analytics i domyślnego wystąpienia Data Lake Storage z folderu głównego.


### <a name="error-code--2711"></a>Kod błędu: 2711

- **Komunikat**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Przyczyna**: Nazwa główna usługi lub certyfikat nie ma dostępu do pliku w magazynie.

- **Zalecenie**: Upewnij się, że nazwa główna usługi lub certyfikat udostępniany przez użytkownika dla zadań Data Lake Analytics ma dostęp do konta Data Lake Analytics i domyślnego wystąpienia Data Lake Storage z folderu głównego.

<br/>

- **Komunikat**: `Cannot find the 'Azure Data Lake Store' file or folder.`

- **Przyczyna**: ścieżka do pliku U-SQL jest niepoprawna lub poświadczenia połączonej usługi nie mają dostępu.

- **Zalecenie**: Sprawdź ścieżkę i poświadczenia podane w połączonej usłudze.


### <a name="error-code--2704"></a>Kod błędu: 2704

- **Komunikat**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Przyczyna**: Nazwa główna usługi lub certyfikat nie ma dostępu do pliku w magazynie.

- **Zalecenie**: Upewnij się, że nazwa główna usługi lub certyfikat udostępniany przez użytkownika dla zadań Data Lake Analytics ma dostęp do konta Data Lake Analytics i domyślnego wystąpienia Data Lake Storage z folderu głównego.


### <a name="error-code--2707"></a>Kod błędu: 2707

- **Komunikat**: `Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Przyczyna**: konto Data Lake Analytics w połączonej usłudze jest nieprawidłowe.

- **Zalecenie**: Sprawdź, czy odpowiednie konto zostało podane.


### <a name="error-code--2703"></a>Kod błędu: 2703

- **Komunikat**: `Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Przyczyna**: błąd pochodzi z Data Lake Analytics. 

- **Zalecenie**: błąd podobny do przykładu oznacza, że zadanie zostało przesłane do Data Lake Analytics, a skrypt nie powiódł się. Zbadaj w Data Lake Analytics. W portalu przejdź do konta Data Lake Analytics i Wyszukaj zadanie przy użyciu identyfikatora uruchomienia działania Data Factory (nie identyfikatora uruchomienia potoku). Zadanie zawiera więcej informacji o błędzie i pomaga w rozwiązywaniu problemów. Jeśli rozwiązanie nie jest wyczyszczone, skontaktuj się z zespołem pomocy technicznej Data Lake Analytics i podaj adres URL zadania, który zawiera nazwę konta i identyfikator zadania.



## <a name="azure-functions"></a>Azure Functions

### <a name="error-code--3602"></a>Kod błędu: 3602

- **Komunikat**: `Invalid HttpMethod: {method}.`

- **Przyczyna**: Metoda http określona w ładunku działania nie jest obsługiwana przez działanie funkcji platformy Azure. 

- **Zalecenie**: obsługiwane metody http to put, post, get, DELETE, Options, nagłówkowy i Trace.


### <a name="error-code--3603"></a>Kod błędu: 3603

- **Komunikat**: `Response content is not a valid JObject.`

- **Przyczyna**: wywołana funkcja platformy Azure nie zwróciła ładunku JSON w odpowiedzi. Działanie funkcji platformy Azure w Data Factory obsługuje tylko zawartość odpowiedzi JSON. 

- **Zalecenie**: zaktualizuj funkcję platformy Azure, aby zwracała prawidłowy ładunek JSON. Na przykład C# funkcja może zwrócić `(ActionResult)new<OkObjectResult("{`identyfikator \"\":\"123\"`}");`.


### <a name="error-code--3606"></a>Kod błędu: 3606

- **Komunikat**: `Azure function activity missing function key.`

- **Przyczyna**: definicja działania funkcji platformy Azure nie została ukończona. 

- **Zalecenie**: Sprawdź, czy WPROWADZONA definicja JSON działania AzureFunction ma właściwość o nazwie "functionKey".


### <a name="error-code--3607"></a>Kod błędu: 3607

- **Komunikat**: `Azure function activity missing function name.`

- **Przyczyna**: definicja działania funkcji platformy Azure nie została ukończona. 

- **Zalecenie**: Sprawdź, czy WPROWADZONA definicja JSON działania AzureFunction ma właściwość o nazwie "FunctionName".


### <a name="error-code--3608"></a>Kod błędu: 3608

- **Komunikat**: `Call to provided Azure function '{FunctionName}' failed with status-'{statusCode}' and message - '{message}'.` 

- **Przyczyna**: Szczegóły funkcji platformy Azure w definicji działania mogą być nieprawidłowe. 

- **Zalecenie**: Popraw Szczegóły funkcji platformy Azure i spróbuj ponownie.


### <a name="error-code--3609"></a>Kod błędu: 3609

- **Komunikat**: `Azure function activity missing functionAppUrl.` 

- **Przyczyna**: definicja działania funkcji platformy Azure nie została ukończona. 

- **Zalecenie**: Sprawdź, czy WPROWADZONA definicja JSON działania AzureFunction ma właściwość o nazwie "functionAppUrl".


### <a name="error-code--3610"></a>Kod błędu: 3610

- **Komunikat**: `There was an error while calling endpoint.`

- **Przyczyna**: adres URL funkcji może być nieprawidłowy.

- **Zalecenie**: Upewnij się, że wartość parametru "functionAppUrl" w kodzie JSON działania jest poprawna, i spróbuj ponownie.


### <a name="error-code--3611"></a>Kod błędu: 3611

- **Komunikat**: `Azure function activity missing Method in JSON.` 

- **Przyczyna**: definicja działania funkcji platformy Azure nie została ukończona.

- **Zalecenie**: Sprawdź, czy WPROWADZONA definicja JSON działania AzureFunction ma właściwość o nazwie "Method".


### <a name="error-code--3612"></a>Kod błędu: 3612

- **Komunikat**: `Azure function activity missing LinkedService definition in JSON.`

- **Przyczyna**: nie można ukończyć definiowania działania funkcji platformy Azure.

- **Zalecenie**: Sprawdź, czy WPROWADZONA definicja JSON działania AzureFunction zawiera szczegóły połączonej usługi.


## <a name="azure-machine-learning"></a>Azure Machine Learning


### <a name="error-code--4101"></a>Kod błędu: 4101

- **Komunikat**: `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Przyczyna**: Zły format lub Brak definicji właściwości.

- **Zalecenie**: Sprawdź, czy działanie jest zdefiniowane z prawidłowymi danymi.


### <a name="error-code--4110"></a>Kod błędu: 4110

- **Komunikat**: dla działania AzureMLExecutePipeline Brak definicji LinkedService w formacie JSON.

- **Przyczyna**: definicja działania AzureMLExecutePipeline nie została ukończona.

- **Zalecenie**: Sprawdź, czy WPROWADZONA definicja JSON działania AzureMLExecutePipeline ma szczegóły połączonej usługi.


### <a name="error-code--4111"></a>Kod błędu: 4111

- **Komunikat**: `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Przyczyna**: nieprawidłowa definicja działania.

- **Zalecenie**: Sprawdź, czy WPROWADZONA definicja JSON działania AzureMLExecutePipeline zawiera poprawne szczegóły połączonej usługi.


### <a name="error-code--4112"></a>Kod błędu: 4112

- **Komunikat**: `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Przyczyna**: Zły format lub Brak definicji właściwości.

- **Zalecenie**: Sprawdź, czy definicja połączonej usługi ma poprawne dane.


### <a name="error-code--4121"></a>Kod błędu: 4121

- **Komunikat**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Przyczyna**: poświadczenia używane do uzyskiwania dostępu do usługi Azure ml wygasły.

- **Zalecenie**: Upewnij się, że poświadczenie jest prawidłowe, i ponów próbę


### <a name="error-code--4122"></a>Kod błędu: 4122

- **Komunikat**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Przyczyna**: poświadczenie podane w połączonej usłudze Azure Service jest nieprawidłowe lub nie ma uprawnień do wykonania tej operacji.

- **Zalecenie**: Upewnij się, że poświadczenie w połączonej usłudze jest prawidłowe i ma uprawnienia dostępu do usługi Azure.


### <a name="error-code--4123"></a>Kod błędu: 4123

- **Komunikat**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Przyczyna**: `Properties of the activity such as pipelineParamters are invalid for the Azure ML pipeline.`

- **Zalecenie**: Sprawdź wartość właściwości działania, aby dopasować oczekiwany ładunek opublikowanego potoku usługi Azure ml określony w połączonej usłudze.


### <a name="error-code--4124"></a>Kod błędu: 4124

- **Komunikat**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Przyczyna**: opublikowany punkt końcowy potoku platformy Azure ml nie istnieje.

- **Zalecenie**: Upewnij się, że opublikowany punkt końcowy potoku Azure ml określony w połączonej usłudze istnieje w usłudze Azure ml.


### <a name="error-code--4125"></a>Kod błędu: 4125

- **Komunikat**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Przyczyna**: błąd serwera w usłudze Azure ml.

- **Zalecenie**: spróbuj ponownie później. Skontaktuj się z zespołem usługi Azure ML, aby uzyskać pomoc w przypadku pozostawania problemu.


### <a name="error-code--4126"></a>Kod błędu: 4126

- **Komunikat**: `AzureML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in AzureMLService for more error loggings.`

- **Przyczyna**: nie można uruchomić potoku Azure.

- **Zalecenie**: Zapoznaj się z AzureMLService, aby uzyskać więcej dzienników błędów i naprawić potok ml


## <a name="custom"></a>Niestandardowy

Poniższa tabela ma zastosowanie do Azure Batch.


### <a name="error-code--2500"></a>Kod błędu: 2500

- **Komunikat**: `Hit unexpected exception and execution failed.`

- **Przyczyna**: `Can't launch command, or the program returned an error code.`

- **Zalecenie**: Upewnij się, że plik wykonywalny istnieje. Jeśli program został uruchomiony, upewnij się, że *stdout. txt* i *stderr. txt* zostały przekazane do konta magazynu. Dobrym sposobem jest wyemitujenie dzienników copious w kodzie w celu debugowania.


### <a name="error-code--2501"></a>Kod błędu: 2501

- **Komunikat**: `Cannot access user batch account; please check batch account settings.`

- **Przyczyna**: Nieprawidłowa nazwa klucza dostępu do partii lub puli.

- **Zalecenie**: Sprawdź nazwę puli i klucz dostępu do usługi Batch w połączonej usłudze.


### <a name="error-code--2502"></a>Kod błędu: 2502

- **Komunikat**: `Cannot access user storage account; please check storage account settings.`

- **Przyczyna**: Nieprawidłowa nazwa konta magazynu lub klucz dostępu.

- **Zalecenie**: Sprawdź nazwę konta magazynu i klucz dostępu w połączonej usłudze.


### <a name="error-code--2504"></a>Kod błędu: 2504

- **Komunikat**: `Operation returned an invalid status code 'BadRequest'.` 

- **Przyczyna**: zbyt wiele plików w folderPath aktywności niestandardowej. Łączny rozmiar elementu resourceFiles nie może być większy niż 32 768 znaków.

- **Zalecenie**: usuwanie niepotrzebnych plików. Lub Dodaj do nich polecenie rozpakować, aby je wyodrębnić. Na przykład użyj `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Kod błędu: 2505

- **Komunikat**: `Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Przyczyna**: działania niestandardowe obsługują tylko konta magazynu używające klucza dostępu.

- **Zalecenie**: Zapoznaj się z opisem błędu.


### <a name="error-code--2507"></a>Kod błędu: 2507

- **Komunikat**: `The folder path does not exist or is empty: ....`

- **Przyczyna**: żadne pliki nie znajdują się na koncie magazynu w określonej ścieżce.

- **Zalecenie**: ścieżka folderu musi zawierać pliki wykonywalne, które chcesz uruchomić.


### <a name="error-code--2508"></a>Kod błędu: 2508

- **Komunikat**: `There are duplicate files in the resource folder.`

- **Przyczyna**: wiele plików o tej samej nazwie znajduje się w różnych podfolderach folderPath.

- **Zalecenie**: działania niestandardowe Spłaszcz strukturę folderów w obszarze folderPath.  Jeśli potrzebujesz zachować strukturę folderów, pliki zip i Wyodrębnij je w Azure Batch przy użyciu polecenia Rozpakuj. Na przykład użyj `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Kod błędu: 2509

- **Komunikat**: `Batch   url ... is invalid; it must be in Uri format.` 

- **Przyczyna**: adresy URL partii muszą być podobne do `https://mybatchaccount.eastus.batch.azure.com`

- **Zalecenie**: Zapoznaj się z opisem błędu.


### <a name="error-code--2510"></a>Kod błędu: 2510

- **Komunikat**: `An   error occurred while sending the request.`

- **Przyczyna**: adres URL partii jest nieprawidłowy. 

- **Zalecenie**: Sprawdź adres URL partii.


## <a name="hdinsight"></a>HDInsight

Poniższa tabela ma zastosowanie do przesyłania strumieniowego platformy Spark, Hive, MapReduce, trzody chlewnej i usługi Hadoop.


### <a name="error-code--2300"></a>Kod błędu: 2300

- **Komunikat**: `Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **Przyczyna**: podany identyfikator URI klastra jest nieprawidłowy. 

- **Zalecenie**: Upewnij się, że klaster nie został usunięty i że podany identyfikator URI jest prawidłowy. Po otwarciu identyfikatora URI w przeglądarce powinien zostać wyświetlony interfejs użytkownika Ambari. Jeśli klaster znajduje się w sieci wirtualnej, identyfikator URI powinien być prywatnym identyfikatorem URI. Aby go otworzyć, użyj maszyny wirtualnej, która jest częścią tej samej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [bezpośrednie łączenie się z usługami Apache Hadoop Services](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services).

<br/>

- **Komunikat**: `Hadoop job submission failed. Job: …, Cluster: …/. Error: A task was canceled.`

- **Przyczyna**: upłynął limit czasu przesłania zadania. 

- **Zalecenie**: przyczyną problemu może być ogólna łączność z usługą HDInsight lub łączność sieciowa. Najpierw upewnij się, że interfejs użytkownika usługi HDInsight Ambari jest dostępny z dowolnej przeglądarki. Upewnij się, że poświadczenia są nadal ważne. Jeśli używasz własnego środowiska uruchomieniowego (IR), upewnij się, że jest to możliwe z maszyny wirtualnej lub maszyny, na której zainstalowano samoobsługowe środowisko IR. Następnie spróbuj ponownie przesłać zadanie z Data Factory. Jeśli nadal się nie powiedzie, skontaktuj się z zespołem Data Factory, aby uzyskać pomoc techniczną.


- **Komunikat**: `Unauthorized: Ambari user name or password is incorrect  <br/><br/>Unauthorized: User admin is locked out in Ambari.   <br/><br/>403 - Forbidden: Access is denied.`

- **Przyczyna**: poświadczenia usługi HDInsight są nieprawidłowe lub wygasły.

- **Zalecenie**: Popraw poświadczenia i ponownie Wdróż połączoną usługę. Najpierw upewnij się, że poświadczenia działają w usłudze HDInsight, otwierając identyfikator URI klastra w dowolnej przeglądarce i próbując się zalogować. Jeśli poświadczenia nie działają, możesz je zresetować z poziomu Azure Portal.

<br/>

- **Komunikat**: `502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **Przyczyna**: ten błąd pochodzi z usługi HDInsight.

- **Zalecenie**: ten błąd pochodzi z klastra usługi HDInsight. Aby uzyskać więcej informacji, zobacz [AMBARI UI 502 Error](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 błędy połączenia z serwerem spark Thrift](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [502 błędy połączenia z serwerem Spark Thrift](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)i [Rozwiązywanie problemów z błędami bramy w Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br/>

- **Komunikat**: `Hadoop job submission failed. Job: …, Cluster: ... Error:   {\"error\":\"Unable to service the submit job request as   templeton service is busy with too many submit job requests. Please wait for some time before retrying the operation. Please refer to the config   templeton.parallellism.job.submit to configure concurrent requests. <br/><br/>Hadoop job submission failed. Job: xx, Cluster: name.   Error: {\"error\":\"java.io.IOException:   org.apache.hadoop.yarn.exceptions.YarnException: Failed to submit   application_1561147195099_3730 to YARN :   org.apache.hadoop.security.AccessControlException: Queue root.joblauncher already has 500 applications, cannot accept submission of application:   application_1561147195099_3730\`

- **Przyczyna**: zbyt wiele zadań jest przesyłanych do usługi HDInsight w tym samym czasie.

- **Zalecenie**: Rozważ ograniczenie liczby współbieżnych zadań przesyłanych do usługi HDInsight. Zapoznaj się z Data Factory współbieżności działań, jeśli zadania są przesyłane przez to samo działanie. Zmień Wyzwalacze w celu rozłożenia współbieżnych uruchomień potoków w czasie. Zapoznaj się z dokumentacją usługi HDInsight, aby dostosować `templeton.parallellism.job.submit` jak sugeruje błąd.


### <a name="error-code--2303"></a>Kod błędu: 2303

- **Komunikat**: `Hadoop job failed with exit code '5'. See   'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **Przyczyna**: zadanie zostało przesłane do usługi HDInsight i nie powiodło się w usłudze HDInsight.

- **Zalecenie**: zadanie zostało pomyślnie przesłane do usługi HDInsight. W klastrze nie powiodło się. Otwórz zadanie i dzienniki w interfejsie użytkownika usługi HDInsight Ambari lub Otwórz plik z magazynu, który sugeruje komunikat o błędzie. Plik zawiera szczegóły błędu.


### <a name="error-code--2310"></a>Kod błędu: 2310

- **Komunikat**: `Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **Przyczyna**: podany identyfikator URI klastra jest nieprawidłowy. 

- **Zalecenie**: Upewnij się, że klaster nie został usunięty i że podany identyfikator URI jest prawidłowy. Po otwarciu identyfikatora URI w przeglądarce powinien zostać wyświetlony interfejs użytkownika Ambari. Jeśli klaster znajduje się w sieci wirtualnej, identyfikator URI powinien być prywatnym identyfikatorem URI. Aby go otworzyć, użyj maszyny wirtualnej, która jest częścią tej samej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [bezpośrednie łączenie się z usługami Apache Hadoop Services](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services).

<br/>

- **Komunikat**: `502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **Przyczyna**: ten błąd pochodzi z usługi HDInsight.

- **Zalecenie**: ten błąd pochodzi z klastra usługi HDInsight. Aby uzyskać więcej informacji, zobacz [AMBARI UI 502 Error](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 błędy połączenia z serwerem spark Thrift](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [502 błędy połączenia z serwerem Spark Thrift](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)i [Rozwiązywanie problemów z błędami bramy w Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br/>

- **Komunikat**: `java.lang.NullPointerException`

- **Przyczyna**: ten błąd występuje, gdy zadanie zostanie przesłane do klastra Spark. 

- **Zalecenie**: ten wyjątek pochodzi z usługi HDInsight. Powoduje ukrycie rzeczywistego problemu. Skontaktuj się z zespołem usługi HDInsight, aby uzyskać pomoc techniczną. Podaj nazwę klastra i zakres czasu wykonywania działania.


### <a name="error-code--2347"></a>Kod błędu: 2347

- **Komunikat**: `Hadoop job failed with exit code '5'. See 'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **Przyczyna**: zadanie zostało przesłane do usługi HDInsight i nie powiodło się w usłudze HDInsight.

- **Zalecenie**: zadanie zostało pomyślnie przesłane do usługi HDInsight. W klastrze nie powiodło się. Otwórz zadanie i dzienniki w interfejsie użytkownika usługi HDInsight Ambari lub Otwórz plik z magazynu, który sugeruje komunikat o błędzie. Plik zawiera szczegóły błędu.


### <a name="error-code--2328"></a>Kod błędu: 2328

- **Komunikat**: `Internal server error occurred while processing the request. Please retry the request or contact support. `

- **Przyczyna**: ten błąd występuje na żądanie w usłudze HDInsight.

- **Zalecenie**: ten błąd występuje w usłudze HDInsight, gdy inicjowanie obsługi usługi HDInsight kończy się niepowodzeniem. Skontaktuj się z zespołem usługi HDInsight i podaj nazwę klastra na żądanie.



## <a name="web-activity"></a>Działanie internetowe

### <a name="error-code--2108"></a>Kod błędu: 2108

- **Komunikat**: `Invalid HttpMethod: '...'.`

- **Przyczyna**: działanie sieci Web nie obsługuje metody http określonej w ładunku działania.

- **Zalecenie**: obsługiwane metody http to put, post, get i DELETE.

<br/>

- **Komunikat**: `Invalid Server Error 500.`

- **Przyczyna**: błąd wewnętrzny w punkcie końcowym.

- **Zalecenie**: Użyj programu Fiddler lub Ogłoś, aby sprawdzić funkcjonalność adresu URL.

<br/>

- **Komunikat**: `Unauthorized 401.`

- **Przyczyna**: Brak prawidłowego uwierzytelniania w żądaniu.

- **Zalecenie**: token mógł wygasnąć. Podaj prawidłową metodę uwierzytelniania. Użyj programu Fiddler lub Ogłoś, aby sprawdzić funkcjonalność adresu URL.

<br/>

- **Komunikat**: `Forbidden 403.`

- **Przyczyna**: Brak wymaganych uprawnień.

- **Zalecenie**: Sprawdź uprawnienia użytkownika do dostępnego zasobu. Użyj programu Fiddler lub Ogłoś, aby sprawdzić funkcjonalność adresu URL.

<br/>

- **Komunikat**: `Bad Request 400.`

- **Przyczyna**: Nieprawidłowe żądanie HTTP.

- **Zalecenie**: Sprawdź adres URL, czasownik i treść żądania. Aby zweryfikować żądanie, użyj programu Fiddler lub programu Poster.

<br/>

- **Komunikat**: `Not found 404.` 

- **Przyczyna**: nie można odnaleźć zasobu.   

- **Zalecenie**: Użyj programu Fiddler lub Ogłoś do zweryfikowania żądania.

<br/>

- **Komunikat**: `Service unavailable.`

- **Przyczyna**: usługa jest niedostępna.

- **Zalecenie**: Użyj programu Fiddler lub Ogłoś do zweryfikowania żądania.

<br/>

- **Komunikat**: `Unsupported Media Type.`

- **Przyczyna**: typ zawartości jest niezgodny z treścią działania sieci Web.

- **Zalecenie**: Określ typ zawartości zgodny z formatem ładunku. Aby zweryfikować żądanie, użyj programu Fiddler lub programu Poster.

<br/>

- **Komunikat**: `The resource you are looking for has been removed, has had its name changed, or is temporarily unavailable.`

- **Przyczyna**: zasób jest niedostępny. 

- **Zalecenie**: Użyj programu Fiddler lub Ogłoś do sprawdzenia punktu końcowego.

<br/>

- **Komunikat**: `The page you are looking for cannot be displayed because an invalid method (HTTP verb) is being used.`

- **Przyczyna**: w żądaniu została określona niepoprawna metoda działania sieci Web.

- **Zalecenie**: Użyj programu Fiddler lub Ogłoś do sprawdzenia punktu końcowego.

<br/>

- **Komunikat**: `invalid_payload`

- **Przyczyna**: treść działania sieci Web jest niepoprawna.

- **Zalecenie**: Użyj programu Fiddler lub Ogłoś do sprawdzenia punktu końcowego.


### <a name="error-code--2208"></a>Kod błędu: 2208

- **Komunikat**: `Invoking Web Activity failed with HttpStatusCode - {0}.`

- **Przyczyna**: usługa docelowa zwróciła stan niepowodzenia.

- **Zalecenie**: Użyj programu Fiddler/Poster, aby sprawdzić poprawność żądania.


### <a name="error-code--2308"></a>Kod błędu: 2308

- **Komunikat**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Przyczyna**: może istnieć wiele przyczyn tego błędu, takich jak łączność sieciowa, błąd DNS, weryfikacja certyfikatu serwera lub przekroczenie limitu czasu.

- **Zalecenie**: Użyj programu Fiddler/Poster, aby sprawdzić poprawność żądania.


Aby użyć programu Fiddler do utworzenia sesji HTTP monitorowanej aplikacji sieci Web:

1. Pobierz, zainstaluj i Otwórz [programu Fiddler](https://www.telerik.com/download/fiddler).

1. Jeśli aplikacja sieci Web używa protokołu HTTPS, przejdź do pozycji **narzędzia** > **Opcje programu Fiddler** > **https**. Wybierz pozycję **Przechwyć połączenie HTTPS** i **Odszyfruj ruch https**. 
   
   ![Opcje programu Fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Jeśli aplikacja używa certyfikatów SSL, Dodaj certyfikat programu Fiddler do urządzenia. Przejdź do pozycji **narzędzia** > **Opcje programu fiddler** > **https** > **Akcje** > **Eksportuj certyfikat główny do pulpitu**.

1. Wyłącz przechwytywanie, przechodząc do **pliku** > **przechwytywania ruch**. Lub naciśnij klawisz **F12**.

1. Wyczyść pamięć podręczną przeglądarki, aby wszystkie elementy w pamięci podręcznej zostały usunięte i muszą zostać pobrane ponownie.

1. Utwórz żądanie: 

   a. Wybierz kartę **układacz** .

   b. Ustaw metodę HTTP i adres URL.

   d. Dodaj nagłówki i treść żądania, jeśli zachodzi taka potrzeba.

   d. Wybierz pozycję **Wykonaj**.

9. Włącz ponownie przechwytywanie ruchu i Ukończ problematyczną transakcję na stronie.

10. Przejdź do **pliku** > **Zapisz** > **wszystkie sesje**.

Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z usługą programu Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji dotyczących rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Blog dotyczący usługi Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo na platformie Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum Stack Overflow dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)



