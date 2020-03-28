---
title: Przykładowe notebooki Jupyter używające danych NOAA
titleSuffix: Azure Open Datasets
description: Użyj przykładowych notesów Jupyter dla zestawów danych Azure Open, aby dowiedzieć się, jak załadować otwarte zestawy danych i użyć ich do wzbogacenia danych demonstracyjnych. Techniki obejmują użycie spark i pandas do przetwarzania danych.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 11/04/2019
ms.openlocfilehash: 8b96a35db91a282be1fb5e4c6143e6bd0a0203f2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73606141"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>Przykładowe notesy Jupyter pokazują, jak wzbogacić dane o otwarte zestawy danych 
Przykładowe notesy Jupyter dla zestawów danych Azure Open pokazują, jak załadować otwarte zestawy danych i użyć ich do wzbogacenia danych demonstracyjnych. Techniki obejmują użycie Apache Spark i Pandas do przetwarzania danych.

>[!IMPORTANT]
>Podczas pracy w środowisku innym niż Spark, Open Datasets umożliwia pobieranie tylko jeden miesiąc danych w czasie z niektórych klas w celu uniknięcia MemoryError z dużych zestawów danych.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>Ładowanie danych zintegrowanej bazy danych powierzchni NOAA (ISD) 
|Notes        | Opis                                    |
|----------------|------------------------------------------------|
|[Załaduj jeden ostatni miesiąc danych pogodowych do ramki danych Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | Dowiedz się, jak załadować historyczne dane pogodowe do ulubionej ramki danych Pandas. |
|[Załaduj jeden ostatni miesiąc danych pogodowych do ramki danych Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | Dowiedz się, jak załadować historyczne dane pogodowe do ulubionej ramki danych Platformy Spark.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>Dołączanie danych demonstracyjnych za pomocą danych ISD NOAA 
|Notes        | Opis                                    |
|----------------|------------------------------------------------|
|[Dołącz do danych demonstracyjnych z danymi pogodowymi - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Dołącz do 1-miesięcznego zestawu danych demonstracyjnych lokalizacji czujników z odczytami pogody w ramce danych Pandas.  |
|[Dołącz do danych demonstracyjnych z danymi pogodowymi – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Dołącz do zestawu danych demonstracyjnych lokalizacji czujników z odczytami pogody w ramce danych Platformy Spark. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>Dołączanie danych taksówek w Nowym Jorku za pomocą danych ISD NOAA 
|Notes        | Opis                                    |
|----------------|------------------------------------------------|
|[Dane o podróży taksówką wzbogacone o dane pogodowe - Pandy](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | Załaduj dane zielonej taksówki NYC (ponad 1 miesiąc) i wzbogać go o dane pogodowe w ramce danych Pandas. W tym przykładzie `get_pandas_limit` zastępuje metodę i równoważy wydajność ładowania danych z ilością danych.|
|[Dane o podróży taksówką wzbogacone o dane pogodowe – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | Załaduj zielone dane taksówek w nowym Jorku i wzbogać go o dane pogodowe w ramce danych platformy Spark.  |

## <a name="next-steps"></a>Następne kroki

* [Samouczek: Modelowanie regresji z automatycznym uczeniem maszynowym i otwartym zestawem danych](/azure/machine-learning/service/tutorial-auto-train-models?context=azure/open-datasets/context/open-datasets-context)
* [Zestaw SDK języka Python dla otwartych zestawów danych](/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)
* [Katalog usługi Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/)
