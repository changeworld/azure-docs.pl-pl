---
title: Zarządzanie usługą Azure Data Lake Analytics przy użyciu języka Python
description: W tym artykule opisano, jak używać języka Python do zarządzania kontami usługi Data Lake Analytics, źródłami danych, użytkownikami & zadaniami.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: d4213a19-4d0f-49c9-871c-9cd6ed7cf731
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: d40658e1510c9ae8a2e3e1f865df7ac95f61abfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68355978"
---
# <a name="manage-azure-data-lake-analytics-using-python"></a>Zarządzanie usługą Azure Data Lake Analytics przy użyciu języka Python
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

W tym artykule opisano sposób zarządzania kontami usługi Azure Data Lake Analytics, źródłami danych, użytkownikami i zadaniami przy użyciu języka Python.

## <a name="supported-python-versions"></a>Obsługiwane wersje języka Python

* Użyj 64-bitowej wersji języka Python.
* Możesz użyć standardowej dystrybucji Pythona znajdującej się w **[Python.org pobrania](https://www.python.org/downloads/)**. 
* Wielu programistów uważa, że wygodnie jest korzystać z **[dystrybucji Anaconda Python](https://www.anaconda.com/download/)**.  
* Ten artykuł został napisany przy użyciu wersji Python 3.6 ze standardowej dystrybucji Pythona

## <a name="install-azure-python-sdk"></a>Instalowanie zestawu Azure Python SDK

Zainstaluj następujące moduły:

* Moduł **azure-mgmt-resource** zawiera inne moduły platformy Azure dla usługi Active Directory itp.
* Moduł **magazynu azure-datalake** zawiera operacje systemu plików usługi Azure Data Lake Store. 
* Moduł **azure-mgmt-datalake-store** zawiera operacje zarządzania kontem usługi Azure Data Lake Store.
* Moduł **azure-mgmt-datalake-analytics** zawiera operacje usługi Azure Data Lake Analytics. 

Najpierw upewnij się, `pip` że masz najnowsze, uruchamiając następujące polecenie:

```
python -m pip install --upgrade pip
```

Ten dokument został `pip version 9.0.1`napisany przy użyciu .

Użyj następujących `pip` poleceń, aby zainstalować moduły z linii poleceń:

```
pip install azure-mgmt-resource
pip install azure-datalake-store
pip install azure-mgmt-datalake-store
pip install azure-mgmt-datalake-analytics
```

## <a name="create-a-new-python-script"></a>Tworzenie nowego skryptu języka Python

Wklej następujący kod do skryptu:

```python
# Use this only for Azure AD service-to-service authentication
#from azure.common.credentials import ServicePrincipalCredentials

# Use this only for Azure AD end-user authentication
#from azure.common.credentials import UserPassCredentials

# Required for Azure Resource Manager
from azure.mgmt.resource.resources import ResourceManagementClient
from azure.mgmt.resource.resources.models import ResourceGroup

# Required for Azure Data Lake Store account management
from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
from azure.mgmt.datalake.store.models import DataLakeStoreAccount

# Required for Azure Data Lake Store filesystem management
from azure.datalake.store import core, lib, multithread

# Required for Azure Data Lake Analytics account management
from azure.mgmt.datalake.analytics.account import DataLakeAnalyticsAccountManagementClient
from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInformation

# Required for Azure Data Lake Analytics job management
from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

# Required for Azure Data Lake Analytics catalog management
from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

# Use these as needed for your application
import logging
import getpass
import pprint
import uuid
import time
```

Uruchom ten skrypt, aby sprawdzić, czy można zaimportować moduły.

## <a name="authentication"></a>Uwierzytelnianie

### <a name="interactive-user-authentication-with-a-pop-up"></a>Interaktywne uwierzytelnianie użytkowników za pomocą wyskakujących okienka

Ta metoda nie jest obsługiwana.

### <a name="interactive-user-authentication-with-a-device-code"></a>Interaktywne uwierzytelnianie użytkownika za pomocą kodu urządzenia

```python
user = input(
    'Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-spi-and-a-secret"></a>Uwierzytelnianie nieinteraktywne za pomocą SPI i tajnego

```python
credentials = ServicePrincipalCredentials(
    client_id='FILL-IN-HERE', secret='FILL-IN-HERE', tenant='FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-api-and-a-certificate"></a>Uwierzytelnianie nieinteraktywne za pomocą interfejsu API i certyfikatu

Ta metoda nie jest obsługiwana.

## <a name="common-script-variables"></a>Typowe zmienne skryptów

Zmienne te są używane w próbkach.

```python
subid = '<Azure Subscription ID>'
rg = '<Azure Resource Group Name>'
location = '<Location>'  # i.e. 'eastus2'
adls = '<Azure Data Lake Store Account Name>'
adla = '<Azure Data Lake Analytics Account Name>'
```

## <a name="create-the-clients"></a>Tworzenie klientów

```python
resourceClient = ResourceManagementClient(credentials, subid)
adlaAcctClient = DataLakeAnalyticsAccountManagementClient(credentials, subid)
adlaJobClient = DataLakeAnalyticsJobManagementClient(
    credentials, 'azuredatalakeanalytics.net')
```

## <a name="create-an-azure-resource-group"></a>Tworzenie grupy zasobów platformy Azure

```python
armGroupResult = resourceClient.resource_groups.create_or_update(
    rg, ResourceGroup(location=location))
```

## <a name="create-data-lake-analytics-account"></a>Tworzenie konta usługi Data Lake Analytics

Najpierw utwórz konto sklepu.

```python
adlsAcctResult = adlsAcctClient.account.create(
    rg,
    adls,
    DataLakeStoreAccount(
        location=location)
    )
).wait()
```
Następnie utwórz konto ADLA, które używa tego magazynu.

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
    print('Job is not yet done, waiting for 3 seconds. Current state: ' +
          jobResult.state.value)
    time.sleep(3)
    jobResult = adlaJobClient.job.get(adla, jobId)

print('Job finished with result: ' + jobResult.result.value)
```

## <a name="list-pipelines-and-recurrences"></a>Wyświetlanie potoków i cykli
W zależności od tego, czy zadania mają dołączone metadane potoku lub cyklu, można wyświetlić listę potoków i cykli.

```python
pipelines = adlaJobClient.pipeline.list(adla)
for p in pipelines:
    print('Pipeline: ' + p.name + ' ' + p.pipelineId)

recurrences = adlaJobClient.recurrence.list(adla)
for r in recurrences:
    print('Recurrence: ' + r.name + ' ' + r.recurrenceId)
```

## <a name="manage-compute-policies"></a>Zarządzanie zasadami obliczeniowymi

Obiekt DataLakeAnalyticsAccountManagementClient udostępnia metody zarządzania zasadami obliczeniowymi dla konta usługi Data Lake Analytics.

### <a name="list-compute-policies"></a>Lista zasad obliczeniowych

Poniższy kod pobiera listę zasad obliczeniowych dla konta usługi Data Lake Analytics.

```python
policies = adlaAccountClient.computePolicies.listByAccount(rg, adla)
for p in policies:
    print('Name: ' + p.name + 'Type: ' + p.objectType + 'Max AUs / job: ' +
          p.maxDegreeOfParallelismPerJob + 'Min priority / job: ' + p.minPriorityPerJob)
```

### <a name="create-a-new-compute-policy"></a>Tworzenie nowej zasady obliczeniowej

Poniższy kod tworzy nową zasadę obliczeniową dla konta usługi Data Lake Analytics, ustawiając maksymalną liczbę jednostek analizy dostępną dla określonego użytkownika na 50, a minimalny priorytet zadania na 250.

```python
userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde"
newPolicyParams = ComputePolicyCreateOrUpdateParameters(
    userAadObjectId, "User", 50, 250)
adlaAccountClient.computePolicies.createOrUpdate(
    rg, adla, "GaryMcDaniel", newPolicyParams)
```

## <a name="next-steps"></a>Następne kroki

- Aby wyświetlić ten samouczek przy użyciu innych narzędzi, kliknij odpowiedni selektor karty w górnej części strony.
- Aby dowiedzieć się więcej o języku U-SQL, zobacz [Wprowadzenie do języka U-SQL w usłudze Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
- Informacje o zadaniach zarządzania znajdziesz w artykule [Zarządzanie usługą Azure Data Lake Analytics przy użyciu witryny Azure Portal](data-lake-analytics-manage-use-portal.md).

