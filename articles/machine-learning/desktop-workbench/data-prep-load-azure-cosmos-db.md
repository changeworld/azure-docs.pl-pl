---
title: "Łączenie z Azure rozwiązania Cosmos bazy danych jako źródła danych w konsoli usługi Azure Machine Learning Workbench | Dokumentacja firmy Microsoft"
description: "Ten dokument zawiera przykładowy na łączenie z bazy danych rozwiązania Cosmos Azure za pomocą usługi Azure Machine Learning Workbench"
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: d36b394a528dc4bc1b6e0a9e0e5dbde728cbee1b
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2018
---
# <a name="connecting-to-azure-cosmos-db-as-a-data-source"></a>Łączenie z bazy danych rozwiązania Cosmos Azure jako źródła danych
Ten artykuł zawiera python próbki umożliwia łączenie z bazą danych rozwiązania Cosmos w konsoli usługi Azure Machine Learning Workbench.

## <a name="load-azure-cosmos-db-data-into-data-preparation"></a>Ładowanie danych z bazy danych Azure rozwiązania Cosmos do przygotowywania danych

Utwórz nowy przepływ danych opartych na skryptach, a następnie użyj następującego skryptu do ładowania danych z bazy danych usługi Azure rozwiązania Cosmos. 

```python
import pydocumentdb
import pydocumentdb.document_client as document_client

import pandas as pd

config = { 
    'ENDPOINT': '<Endpoint>',
    'MASTERKEY': '<Key>',
    'DOCUMENTDB_DATABASE': '<DBName>',
    'DOCUMENTDB_COLLECTION': '<collectionname>'
};

# Initialize the Python DocumentDB client.
client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})

# Read databases and take first since id should not be duplicated.
db = next((data for data in client.ReadDatabases() if data['id'] == config['DOCUMENTDB_DATABASE']))

# Read collections and take first since id should not be duplicated.
coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config['DOCUMENTDB_COLLECTION']))

docs = client.ReadDocuments(coll['_self'])

df = pd.DataFrame(list(docs))
```

## <a name="other-data-source-connections"></a>Inne połączeń ze źródłem danych
Inne przykłady można odczytać [przykład dodatkowe źródła danych połączenia](data-prep-appendix8-sample-source-connections-python.md)
