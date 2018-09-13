---
title: Łączenie z usługą Azure Cosmos DB jako źródła danych w usłudze Azure Machine Learning Workbench | Dokumentacja firmy Microsoft
description: Ten dokument zawiera przykład o tym, jak połączyć się z usługi Azure Cosmos DB za pomocą usługi Azure Machine Learning Workbench
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: 2d9562582a94b7c7aac5b972a7ef57b84eb9aa9e
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35650635"
---
# <a name="connecting-to-azure-cosmos-db-as-a-data-source"></a>Łączenie z usługą Azure Cosmos DB jako źródła danych
Ten artykuł zawiera python próbki pozwala na łączenie z usługą Cosmos DB w aplikacji Azure Machine Learning Workbench.

## <a name="load-azure-cosmos-db-data-into-data-preparation"></a>Ładowanie danych do usługi Azure Cosmos DB do przygotowywania danych

Utwórz nowy przepływ danych opartych na skryptach, a następnie użyj następującego skryptu, aby załadować dane z usługi Azure Cosmos DB. 

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

## <a name="other-data-source-connections"></a>Inne połączenia ze źródłami danych
Inne przykłady można znaleźć [przykładzie dodatkowe źródła danych połączeń](data-prep-appendix8-sample-source-connections-python.md)
