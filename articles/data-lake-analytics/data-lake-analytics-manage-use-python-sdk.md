---
title: Zarządzanie Azure Data Lake Analytics przy użyciu języka Python | Dokumentacja firmy Microsoft
description: 'Dowiedz się, jak utworzyć konto usługi Data Lake Store i przesyłać zadania za pomocą języka Python. '
services: data-lake-analytics
documentationcenter: ''
author: matt1883
manager: jhubbard
editor: cgronlun
ms.assetid: d4213a19-4d0f-49c9-871c-9cd6ed7cf731
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/18/2017
ms.author: saveenr
ms.custom: devcenter
ms.openlocfilehash: 0182a14979550c880904ec829f6b59dee016cad2
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="manage-azure-data-lake-analytics-using-python"></a>Zarządzanie Azure Data Lake Analytics przy użyciu języka Python

## <a name="python-versions"></a>Wersji języka Python

* Użyj 64-bitowej wersji języka Python.
* Można użyć standardowego dystrybucję oprogramowania Python znaleźć pod adresem  **[pobiera Python.org](https://www.python.org/downloads/)**. 
* Wielu deweloperów znaleźć łatwe w użyciu  **[dystrybucję oprogramowania Python Anaconda](https://www.continuum.io/downloads)**.  
* Ten artykuł dotyczy przy użyciu języka Python w wersji 3,6 z standardowe dystrybucję oprogramowania Python

## <a name="install-azure-python-sdk"></a>Instalowanie zestawu Azure Python SDK

Instalowanie następujących modułów:

* **Zasobów azure-mgmt** moduł zawiera inne moduły Azure Active Directory itp.
* **Azure-mgmt-datalake magazynu** moduł zawiera operacji zarządzania kontem usługi Azure Data Lake Store.
* **Azure datalake magazynu** moduł zawiera operacje systemu plików usługi Azure Data Lake Store. 
* **Usługi analiza danych azure** moduł zawiera operacje usługi Azure Data Lake Analytics. 

Najpierw upewnij się, masz najnowszą `pip` , uruchamiając następujące polecenie:

```
python -m pip install --upgrade pip
```

Ten dokument został zapisany przy użyciu `pip version 9.0.1`.

Należy użyć następującego `pip` polecenia, aby zainstalować moduły w wierszu polecenia:

```
pip install azure-mgmt-resource
pip install azure-datalake-store
pip install azure-mgmt-datalake-store
pip install azure-mgmt-datalake-analytics
```

## <a name="create-a-new-python-script"></a>Utwórz nowy skrypt w języku Python

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
from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInfo

## Required for Azure Data Lake Analytics job management
from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

## Required for Azure Data Lake Analytics catalog management
from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

## Use these as needed for your application
import logging, getpass, pprint, uuid, time
```

Uruchom ten skrypt, aby zweryfikować, że można zaimportować moduły.

## <a name="authentication"></a>Authentication

### <a name="interactive-user-authentication-with-a-pop-up"></a>Interakcyjnego uwierzytelniania użytkownika z okno podręczne

Ta metoda nie jest obsługiwana.

### <a name="interactive-user-authentication-with-a-device-code"></a>Interakcyjnego uwierzytelniania użytkownika przy użyciu kodu urządzenia

```python
user = input('Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-spi-and-a-secret"></a>Uwierzytelnianie nieinteraktywne SPI i klucz tajny

```python
credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-api-and-a-certificate"></a>Uwierzytelnianie nieinteraktywne za pomocą interfejsu API i certyfikatu

Ta metoda nie jest obsługiwana.

## <a name="common-script-variables"></a>Typowe zmienne skryptu

Te zmienne są używane w przykładach.

```python
subid= '<Azure Subscription ID>'
rg = '<Azure Resource Group Name>'
location = '<Location>' # i.e. 'eastus2'
adls = '<Azure Data Lake Store Account Name>'
adla = '<Azure Data Lake Analytics Account Name>'
```

## <a name="create-the-clients"></a>Utwórz klientów

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
Następnie utwórz konto ADLA, która używa tego magazynu.

```python
adlaAcctResult = adlaAcctClient.account.create(
    rg,
    adla,
    DataLakeAnalyticsAccount(
        location=location,
        default_data_lake_store_account=adls,
        data_lake_store_accounts=[DataLakeStoreAccountInfo(name=adls)]
    )
).wait()
```

## <a name="submit-a-job"></a>Prześlij zadanie

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

## <a name="list-pipelines-and-recurrences"></a>Potoki listy i powtórzeń
W zależności od tego, czy Twoje zadania mają potoku lub ponownego metadanych dołączony, można wyświetlić listę potoki i powtórzeń.

```python
pipelines = adlaJobClient.pipeline.list(adla)
for p in pipelines:
    print('Pipeline: ' + p.name + ' ' + p.pipelineId)

recurrences = adlaJobClient.recurrence.list(adla)
for r in recurrences:
    print('Recurrence: ' + r.name + ' ' + r.recurrenceId)
```

## <a name="manage-compute-policies"></a>Zarządzanie zasadami obliczeń

Obiekt DataLakeAnalyticsAccountManagementClient zapewnia metody do zarządzania zasadami obliczeniowe dla konta usługi Data Lake Analytics.

### <a name="list-compute-policies"></a>Podać zasady obliczeń

Poniższy kod pobiera listę zasad obliczeniowe dla konta usługi Data Lake Analytics.

```python
policies = adlaAccountClient.computePolicies.listByAccount(rg, adla)
for p in policies:
    print('Name: ' + p.name + 'Type: ' + p.objectType + 'Max AUs / job: ' + p.maxDegreeOfParallelismPerJob + 'Min priority / job: ' + p.minPriorityPerJob)
```

### <a name="create-a-new-compute-policy"></a>Utwórz nowe zasady obliczeń

Poniższy kod tworzy nową zasadę obliczeniowe dla konta usługi Data Lake Analytics, ustawienie maksymalnej AUs, dostępne do określonego użytkownika do 50, a także priorytet zadania minimalna 250.

```python
userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde"
newPolicyParams = ComputePolicyCreateOrUpdateParameters(userAadObjectId, "User", 50, 250)
adlaAccountClient.computePolicies.createOrUpdate(rg, adla, "GaryMcDaniel", newPolicyParams)
```

## <a name="next-steps"></a>Kolejne kroki

- Aby wyświetlić ten samouczek przy użyciu innych narzędzi, kliknij odpowiedni selektor karty w górnej części strony.
- Aby dowiedzieć się więcej o języku U-SQL, zobacz [Wprowadzenie do języka U-SQL w usłudze Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
- Informacje o zadaniach zarządzania znajdziesz w artykule [Zarządzanie usługą Azure Data Lake Analytics przy użyciu witryny Azure Portal](data-lake-analytics-manage-use-portal.md).

