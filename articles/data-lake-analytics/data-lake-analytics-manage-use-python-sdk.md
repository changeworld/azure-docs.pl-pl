---
title: Zarządzanie przy użyciu języka Python usługi Azure Data Lake Analytics
description: W tym artykule opisano sposób zarządzania kont, źródła danych, użytkowników i zadania usługi Data Lake Analytics przy użyciu języka Python.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: d4213a19-4d0f-49c9-871c-9cd6ed7cf731
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: 82007c780a0c9ff3bb2e1a50a4826499f9df9c9f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811708"
---
# <a name="manage-azure-data-lake-analytics-using-python"></a>Zarządzanie przy użyciu języka Python usługi Azure Data Lake Analytics
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

W tym artykule opisano sposób zarządzania kontami usługi Azure Data Lake Analytics, źródła danych, użytkowników i zadań przy użyciu języka Python.

## <a name="supported-python-versions"></a>Obsługiwane wersje języka Python

* Należy użyć 64-bitowej wersji środowiska Python.
* Można użyć standardowego adrese dystrybucji Python  **[Python.org pliki do pobrania](https://www.python.org/downloads/)**. 
* Wielu programistów uznaje wygodny w użyciu  **[dystrybucji Anaconda Python](https://www.anaconda.com/download/)**.  
* W tym artykule został napisany, przy użyciu języka Python w wersji 3.6 z standardowa dystrybucji języka Python

## <a name="install-azure-python-sdk"></a>Instalowanie zestawu Azure Python SDK

Zainstaluj następujące moduły:

* **Azure-mgmt-resource** moduł obejmuje inne moduły platformy Azure dla usługi Active Directory itp.
* **Azure-datalake-store** modułu obejmuje operacje systemu plików usługi Azure Data Lake Store. 
* **Azure-mgmt-datalake-store** modułu obejmuje operacje zarządzania kontem usługi Azure Data Lake Store.
* **Azure-mgmt-datalake-analytics** moduł obejmuje operacje usługi Azure Data Lake Analytics. 

Najpierw upewnij się, że najnowsze `pip` , uruchamiając następujące polecenie:

```
python -m pip install --upgrade pip
```

Tego dokumentu została opracowana za pomocą `pip version 9.0.1`.

Należy użyć następującego `pip` polecenia, aby zainstalować moduły w wierszu polecenia:

```
pip install azure-mgmt-resource
pip install azure-datalake-store
pip install azure-mgmt-datalake-store
pip install azure-mgmt-datalake-analytics
```

## <a name="create-a-new-python-script"></a>Utwórz nowy skrypt języka Python

Wklej następujący kod do skryptu:

```python
## Use this only for Azure AD service-to-service authentication
#from azure.common.credentials import ServicePrincipalCredentials

## Use this only for Azure AD end-user authentication
#from azure.common.credentials import UserPassCredentials

## Required for Azure Resource Manager
from azure.mgmt.resource.resources import ResourceManagementClient
from azure.mgmt.resource.resources.models import ResourceGroup

## Required for Azure Data Lake Store account management
from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
from azure.mgmt.datalake.store.models import DataLakeStoreAccount

## Required for Azure Data Lake Store filesystem management
from azure.datalake.store import core, lib, multithread

## Required for Azure Data Lake Analytics account management
from azure.mgmt.datalake.analytics.account import DataLakeAnalyticsAccountManagementClient
from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInformation

## Required for Azure Data Lake Analytics job management
from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

## Required for Azure Data Lake Analytics catalog management
from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

## Use these as needed for your application
import logging, getpass, pprint, uuid, time
```

Uruchom ten skrypt, aby zweryfikować, że mogą zostać zaimportowane moduły.

## <a name="authentication"></a>Authentication

### <a name="interactive-user-authentication-with-a-pop-up"></a>Interakcyjnego uwierzytelniania użytkownika przy użyciu wyskakującego okienka

Ta metoda nie jest obsługiwana.

### <a name="interactive-user-authentication-with-a-device-code"></a>Interakcyjnego uwierzytelniania użytkownika przy użyciu kodu urządzenia

```python
user = input('Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-spi-and-a-secret"></a>Uwierzytelnianie nieinteraktywne przy użyciu SPI i klucz tajny

```python
credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-api-and-a-certificate"></a>Nieinteraktywnych uwierzytelnianie przy użyciu interfejsu API i certyfikatu

Ta metoda nie jest obsługiwana.

## <a name="common-script-variables"></a>Wspólne zmienne skryptu

Te zmienne są używane w przykładach.

```python
subid= '<Azure Subscription ID>'
rg = '<Azure Resource Group Name>'
location = '<Location>' # i.e. 'eastus2'
adls = '<Azure Data Lake Store Account Name>'
adla = '<Azure Data Lake Analytics Account Name>'
```

## <a name="create-the-clients"></a>Tworzenie klientów

```python
resourceClient = ResourceManagementClient(credentials, subid)
adlaAcctClient = DataLakeAnalyticsAccountManagementClient(credentials, subid)
adlaJobClient = DataLakeAnalyticsJobManagementClient( credentials, 'azuredatalakeanalytics.net')
```

## <a name="create-an-azure-resource-group"></a>Tworzenie grupy zasobów platformy Azure

```python
armGroupResult = resourceClient.resource_groups.create_or_update( rg, ResourceGroup( location=location ) )
```

## <a name="create-data-lake-analytics-account"></a>Tworzenie konta usługi Data Lake Analytics

Najpierw utwórz konto magazynu.

```python
adlsAcctResult = adlsAcctClient.account.create(
    rg,
    adls,
    DataLakeStoreAccount(
        location=location)
    )
).wait()
```
Następnie utwórz konto ADLA, który używa tego magazynu.

```python
adlaAcctResult = adlaAcctClient.account.create(
    rg,
    adla,
    DataLakeAnalyticsAccount(
        location=location,
        default_data_lake_store_account=adls,
        data_lake_store_accounts=[DataLakeStoreAccountInformation(name=adls)]
    )
).wait()
```

## <a name="submit-a-job"></a>Przesyłanie zadania

```python
script = """
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"""

jobId = str(uuid.uuid4())
jobResult = adlaJobClient.job.create(
    adla,
    jobId,
    JobInformation(
        name='Sample Job',
        type='USql',
        properties=USqlJobProperties(script=script)
    )
)
```

## <a name="wait-for-a-job-to-end"></a>Poczekaj na zakończenie zadania

```python
jobResult = adlaJobClient.job.get(adla, jobId)
while(jobResult.state != JobState.ended):
    print('Job is not yet done, waiting for 3 seconds. Current state: ' + jobResult.state.value)
    time.sleep(3)
    jobResult = adlaJobClient.job.get(adla, jobId)

print ('Job finished with result: ' + jobResult.result.value)
```

## <a name="list-pipelines-and-recurrences"></a>Listy potoków i cykli
Zależności, czy zadania mają metadanych potoku lub cykliczny, dołączony, możesz wyświetlić listę potoków i cykli.

```python
pipelines = adlaJobClient.pipeline.list(adla)
for p in pipelines:
    print('Pipeline: ' + p.name + ' ' + p.pipelineId)

recurrences = adlaJobClient.recurrence.list(adla)
for r in recurrences:
    print('Recurrence: ' + r.name + ' ' + r.recurrenceId)
```

## <a name="manage-compute-policies"></a>Zarządzanie zasadami obliczeń

Obiekt DataLakeAnalyticsAccountManagementClient zapewnia metody do zarządzania zasadami obliczeń dla konta usługi Data Lake Analytics.

### <a name="list-compute-policies"></a>Podać zasady obliczeń

Poniższy kod pobiera listę zasad obliczeń dla konta usługi Data Lake Analytics.

```python
policies = adlaAccountClient.computePolicies.listByAccount(rg, adla)
for p in policies:
    print('Name: ' + p.name + 'Type: ' + p.objectType + 'Max AUs / job: ' + p.maxDegreeOfParallelismPerJob + 'Min priority / job: ' + p.minPriorityPerJob)
```

### <a name="create-a-new-compute-policy"></a>Utwórz nowe zasady obliczeń

Poniższy kod tworzy nową zasadę obliczeń dla konta usługi Data Lake Analytics, ustawienie maksymalna liczba jednostek alokacji, dostępne dla określonego użytkownika na 50 i priorytet minimalny zadania do 250.

```python
userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde"
newPolicyParams = ComputePolicyCreateOrUpdateParameters(userAadObjectId, "User", 50, 250)
adlaAccountClient.computePolicies.createOrUpdate(rg, adla, "GaryMcDaniel", newPolicyParams)
```

## <a name="next-steps"></a>Kolejne kroki

- Aby wyświetlić ten samouczek przy użyciu innych narzędzi, kliknij odpowiedni selektor karty w górnej części strony.
- Aby dowiedzieć się więcej o języku U-SQL, zobacz [Wprowadzenie do języka U-SQL w usłudze Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
- Informacje o zadaniach zarządzania znajdziesz w artykule [Zarządzanie usługą Azure Data Lake Analytics przy użyciu witryny Azure Portal](data-lake-analytics-manage-use-portal.md).

