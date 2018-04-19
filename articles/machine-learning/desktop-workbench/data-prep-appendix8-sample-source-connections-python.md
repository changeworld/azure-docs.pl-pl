---
title: "Przykład dodatkowe źródła danych połączenia przy użyciu usługi Azure Machine Learning Przygotowanie danych | Dokumentacja firmy Microsoft"
description: "Ten dokument zawiera zbiór przykłady połączeń ze źródłem danych z usługi Azure Machine Learning Przygotowanie danych"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 27d72f1b49a89929098c0cc35f92d3915fc8b0b9
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2018
---
# <a name="sample-of-custom-source-connections-python"></a>Przykładowe Źródło niestandardowe połączeń (Python) 
Przed przeczytaniem tego dodatku odczytu [Przegląd rozszerzalności języka Python](data-prep-python-extensibility-overview.md).

## <a name="load-data-from-dataworld"></a>Ładowanie danych z data.world

### <a name="prerequisites"></a>Wymagania wstępne

#### <a name="register-yourself-at-dataworld"></a>Zarejestruj się w data.world
Należy token interfejsu API z data.world witryny sieci Web.

#### <a name="install-dataworld-library"></a>Zainstaluj biblioteki data.world

Otwórz interfejs wiersza polecenia Azure Machine Learning Workbench wybierając **pliku** > **Otwórz interfejs wiersza polecenia**.

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

Następnie uruchom `dw configure` w wierszu polecenia, który wyświetla monit o podanie token. Po wprowadzeniu token, .dw / katalog jest tworzony w katalogu macierzystym i token jest przechowywane.

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
Teraz można zaimportować data.world biblioteki.

#### <a name="load-data-into-data-preparation"></a>Ładowanie danych do przygotowywania danych

Utworzyć transformację przekształcenie przepływ danych (skrypt). Następnie użyj następującego skryptu do ładowania danych z data.world.

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#Load the dataset.
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset.
df = lds.dataframes['simpsons_episodes']

```
## <a name="azure-cosmos-db-as-a-data-source-connection"></a>Azure DB rozwiązania Cosmos jako połączenie ze źródłem danych
Na przykład Azure DB rozwiązania Cosmos jako połączenie danych odczytu [obciążenia bazy danych rozwiązania Cosmos Azure jako połączenie źródła danych](data-prep-load-azure-cosmos-db.md)
