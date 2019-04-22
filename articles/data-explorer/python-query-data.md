---
title: 'Szybki start: wykonywanie zapytań o dane przy użyciu biblioteki języka Python w usłudze Azure Data Explorer'
description: Z tego przewodnika Szybki start dowiesz się, jak wykonywać zapytania o dane w usłudze Azure Data Explorer przy użyciu biblioteki języka Python.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 10/16/2018
ms.openlocfilehash: 4de8f68e0384742cea4ce50ccd23a7455b186893
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59048745"
---
# <a name="quickstart-query-data-using-the-azure-data-explorer-python-library"></a>Szybki start: wykonywanie zapytań o dane przy użyciu biblioteki języka Python w usłudze Azure Data Explorer

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. Usługa Azure Data Explorer udostępnia [bibliotekę danych klienta dla języka Python](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). Ta biblioteka umożliwia wykonywanie zapytań o dane z poziomu kodu. W tym przewodniku Szybki start nawiążesz połączenie z tabelą w *klastrze help*, który skonfigurowaliśmy, aby ułatwić naukę. Następnie możesz wykonać zapytanie względem tabeli w tym klastrze i zwrócić wyniki.

Ten przewodnik Szybki start jest również dostępny w formie [notesu platformy Azure](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueryKusto.ipynb).

## <a name="prerequisites"></a>Wymagania wstępne

* Konto e-mail organizacji, która jest członkiem usługi Azure Active Directory (AAD)

* Język [Python](https://www.python.org/downloads/) zainstalowany na komputerze deweloperskim

## <a name="install-the-data-library"></a>Instalowanie biblioteki danych

Zainstaluj program*azure-kusto-data*.

```
pip install azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>Dodawanie instrukcji importu i stałych

Zaimportuj klasy z biblioteki oraz bibliotekę analizy danych *pandas*.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
import pandas as pd
```

Usługa Azure Data Explorer korzysta z identyfikatora dzierżawy usługi AAD w celu uwierzytelnienia aplikacji. Aby odnaleźć identyfikator dzierżawy, użyj następującego adresu URL, zastępując ciąg *YourDomain* nazwą domeny.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Jeśli na przykład Twoja domena to *contoso.com*, adres URL to [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Kliknij ten adres URL, aby wyświetlić wyniki. Pierwszy wiersz wygląda w następujący sposób.

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Identyfikator dzierżawy w tym przypadku to `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. Przed uruchomieniem kodu ustaw wartość dla elementu AAD_TENANT_ID.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_CLUSTER = "https://help.kusto.windows.net/"
KUSTO_DATABASE  = "Samples"
```

Teraz możesz utworzyć parametry połączenia. W tym przykładzie w celu uzyskania dostępu do klastra używane jest uwierzytelnianie urządzenia. Możesz również użyć [certyfikatu aplikacji usługi AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24), [klucza aplikacji usługi ADD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20) oraz [użytkownika i hasła usługi AAD](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34).

```python
KCSB = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_CLUSTER)
KCSB.authority_id = AAD_TENANT_ID
```

## <a name="connect-to-azure-data-explorer-and-execute-a-query"></a>Nawiązywanie połączenia z usługą Azure Data Explorer i wykonywanie zapytania

Wykonaj zapytanie do klastra i zapisz dane wyjściowe w ramce danych. Uruchomienie tego kodu powoduje zwrócenie komunikatu, jak pokazano poniżej: *Aby się zalogować, otwórz stronę https://microsoft.com/devicelogin w przeglądarce internetowej i wprowadź kod F3W4VWZDM, aby się uwierzytelnić*. Postępuj zgodnie z instrukcjami, aby się zalogować, a następnie wróć, aby uruchomić kolejny blok kodu.

```python
KUSTO_CLIENT  = KustoClient(KCSB)
KUSTO_QUERY  = "StormEvents | sort by StartTime desc | take 10"

RESPONSE = KUSTO_CLIENT.execute(KUSTO_DATABASE, KUSTO_QUERY)
```

## <a name="explore-data-in-dataframe"></a>Eksplorowanie danych w elemencie DataFrame

Po zalogowaniu zapytanie zwraca wyniki, które są zapisywane w ramce danych. Możesz pracować z tymi wynikami tak samo, jak w przypadku każdej innej ramki danych.

```python
df = dataframe_from_result_table(RESPONSE.primary_results[0])
df
```

Powinno zostać wyświetlonych dziesięć najlepszych wyników z tabeli StormEvents.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Szybki start: pozyskiwanie danych przy użyciu biblioteki języka Python w usłudze Azure Data Explorer](python-ingest-data.md)
