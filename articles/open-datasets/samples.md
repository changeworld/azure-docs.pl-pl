---
title: Przykładowe notesy Jupyter korzystające z otwartego zestawu danych NOAA
titleSuffix: Azure Open Datasets
description: Użyj przykładowych notesów Jupyter dla platformy Azure Otwórz zestawy danych, aby dowiedzieć się, jak ładować otwarte zestawy danych i używać ich do wzbogacania dane demonstracyjne. Techniki obejmują użycie platformy Spark i Pandas do przetwarzania danych.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 11/04/2019
ms.openlocfilehash: 2ccdb7adcae40d22a36fbfb47fba6e4c87b3349a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496568"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>Przykładowe notesy Jupyter pokazują, jak wzbogacać dane za pomocą otwartych zestawów danych 
Przykład Jupyter notesów dla usługi Azure Open DataSets pokazują, jak ładować otwarte zestawy danych i używać ich do wzbogacania dane demonstracyjne. Techniki obejmują używanie Apache Spark i Pandas do przetwarzania danych.

>[!IMPORTANT]
>W przypadku pracy w środowisku innym niż platforma Spark otwarte zestawy danych umożliwiają pobieranie tylko jednego miesiąca z danymi jednocześnie z określonymi klasami w celu uniknięcia MemoryError z dużymi zestawami.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>NOAA obciążenia — dane zintegrowanej bazy danych (ISD) 
|Notes        | Opis                                    |
|----------------|------------------------------------------------|
|[Załaduj jeden ostatni miesiąc danych pogody do Pandas Dataframe](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | Dowiedz się, jak ładować historyczne dane pogodowe do ulubionej ramki dataPandas. |
|[Załaduj jeden ostatni miesiąc danych pogody do ramki Dataframe platformy Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | Dowiedz się, jak ładować historyczne dane pogodowe do ulubionej ramki Dataframe.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>Dołącz dane demonstracyjne za pomocą danych NOAA ISD 
|Notes        | Opis                                    |
|----------------|------------------------------------------------|
|[Dołączanie danych demonstracyjnych za pomocą danych pogody — Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Dołącz do 1-miesięcznego demonstracyjnego zestawu danych lokalizacji czujników z odczytami pogody w Pandas Dataframe.  |
|[Dołączanie danych demonstracyjnych za pomocą danych pogody — Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Dołącz do demonstracyjnego zestawu danych lokalizacji czujników z odczytami pogody w ramce Dataframe. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>Dołącz dane z NYC taksówki przy użyciu danych NOAA ISD 
|Notes        | Opis                                    |
|----------------|------------------------------------------------|
|[Dane o podróży z taksówkami wzbogacone o dane pogodowe — Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | Ładuj NYCe dane dotyczące oddziałów zielonych (ponad 1 miesiąc) i Wzbogacaj je o dane pogodowe w ramce dataPandas. Ten przykład zastępuje metodę `get_pandas_limit` i równoważy wydajność ładowania danych z ilością danych.|
|[Dane o podróży z taksówkami wzbogacone o dane pogodowe — Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | Ładuj NYCe dane dotyczące oddziałów zielonych i Wzbogacaj je o dane pogodowe w usłudze Spark Dataframe.  |

## <a name="next-steps"></a>Następne kroki

* [Samouczek: Modelowanie regresji z automatycznym uczeniem maszynowym i otwartym zestawem danych](tutorial-opendatasets-automl.md)
* [Zestaw SDK języka Python dla otwartych zestawów danych](/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)
* [Otwórz wykaz zestawów danych platformy Azure](https://azure.microsoft.com/services/open-datasets/catalog/)
