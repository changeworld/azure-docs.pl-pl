---
title: Przykład dodatkowych danych połączeń ze źródłami możliwego do przygotowania danych usługi Azure Machine Learning | Dokumentacja firmy Microsoft
description: Ten dokument zawiera zbiór przykłady połączeń ze źródłami danych, które są możliwe w przypadku przygotowywania danych usługi Azure Machine Learning
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: ed0e6bc4c506535fcb679fc2b4015a61274a77f3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967697"
---
# <a name="sample-of-custom-source-connections-python"></a>Przykładowe połączenia źródeł niestandardowych (Python) 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Przed przeczytaniem tego dodatku, przeczytaj [Przegląd rozszerzalności Python](data-prep-python-extensibility-overview.md).

## <a name="load-data-from-dataworld"></a>Ładowanie danych z usługi data.world

### <a name="prerequisites"></a>Wymagania wstępne

#### <a name="register-yourself-at-dataworld"></a>Zarejestruj się w usługi data.world
Należy uzyskać token interfejsu API z usługi data.world witryny sieci Web.

#### <a name="install-dataworld-library"></a>Zainstaluj bibliotekę usługi data.world

Otwórz interfejs wiersza polecenia usługi Azure Machine Learning Workbench, wybierając pozycję **pliku** > **Otwórz interfejs wiersza polecenia**.

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

Następnie uruchom `dw configure` w wierszu polecenia, które wyświetli monit o podanie tokenu. Po wprowadzeniu Twojego tokenu .dw / katalog jest tworzony w katalogu macierzystym i Twój token jest tam przechowywane.

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
Teraz można zaimportować data.world bibliotek.

#### <a name="load-data-into-data-preparation"></a>Ładowanie danych do przygotowywania danych

Utwórz przekształcenia Przekształć przepływ danych (skrypt). Następnie użyj następującego skryptu, aby załadować dane z usługi data.world.

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#Load the dataset.
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset.
df = lds.dataframes['simpsons_episodes']

```
## <a name="azure-cosmos-db-as-a-data-source-connection"></a>Usługa Azure Cosmos DB jako połączenie ze źródłem danych
Na przykład usługi Azure Cosmos DB jako połączenie danych odczytu [obciążenia usługi Azure Cosmos DB jako połączenie źródła danych](data-prep-load-azure-cosmos-db.md)
