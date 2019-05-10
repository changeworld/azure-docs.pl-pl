---
title: Przykład notesów programu Jupyter przy użyciu Otwórz zestaw danych agencji NOAA
titleSuffix: Azure Open Datasets
description: Aby dowiedzieć się, jak ładować otwartych zestawów danych i ich używać w celu wzbogacenia danych demonstracyjnych, należy użyć notesów programu Jupyter przykład w przypadku zestawów danych Otwórz w usłudze Azure. Techniki to m.in. Korzystanie z platformy Spark i Pandas do przetwarzania danych.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 05/02/2019
ms.openlocfilehash: b62a2690e5879e45a14d0b06a38e8c5171dda14e
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442332"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>Notesy Jupyter w przykładzie pokazano, jak Wzbogacanie danych przy użyciu otwartych zestawów danych 
Przykład aplikacji Jupyter notebooks w przypadku zestawów danych z usługami Azure Otwórz pokazują, jak załadować otwartych zestawów danych i używaj ich do Wzbogacanie danych demonstracyjnych. Techniki to m.in. Korzystanie z platformy Apache Spark i Pandas do przetwarzania danych.

>[!IMPORTANT]
>Podczas pracy w środowisku-Spark, otwórz zestawów danych umożliwia pobieranie tylko jednego miesiąca danych w czasie za pomocą niektórych klas, aby uniknąć MemoryError zostanie wygenerowany z dużymi zestawami danych.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>Ładowanie danych agencji NOAA zintegrowane powierzchni bazy danych (ISD) 
|Notes        | Opis                                    |
|----------------|------------------------------------------------|
|[Załaduj ostatnie miesiąc danych o pogodzie do elementów Pandas dataframe](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | Dowiedz się, jak załadować dane historyczne pogody do Twojego ulubionego elementów Pandas dataframe. |
|[Załaduj ostatnie miesiąc danych o pogodzie do elementów dataframe aparatu Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | Dowiedz się, jak załadować dane historyczne pogodowe do ulubionych elementów dataframe aparatu Spark.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>Dołącz do pokazu dane za pomocą danych agencji NOAA ISD 
|Notes        | Opis                                    |
|----------------|------------------------------------------------|
|[Dołącz do pokazu danych przy użyciu danych o pogodzie - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Dołącz do pokazu 1-miesięcznej zestawu danych czujnika lokalizacji za pomocą odczyty pogody w Pandas dataframe.  |
|[Dołącz do pokazu danych przy użyciu danych o pogodzie — platformy Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Dołącz do pokazu zestawu danych czujnika lokalizacji za pomocą odczyty pogody w dataframe platformy Spark. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>Dołącz dane taksówek NYC danych agencji NOAA ISD 
|Notes        | Opis                                    |
|----------------|------------------------------------------------|
|[Dane podróży taksówek wzbogacone o pogodzie - Pandas](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | Załaduj dane taksówek zielony NYC (ponad 1 miesiąc) i udoskonalanie go z danymi pogody w Pandas dataframe. W tym przykładzie zastępuje metodę `get_pandas_limit` i bilansuje wydajność ładowania danych z ilością danych.|
|[Dane podróży taksówek wzbogacone o pogodzie — platformy Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | Załadować dane taksówek zielony NYC i wzbogacanie go danymi pogody w elementów dataframe aparatu Spark.  |

## <a name="next-steps"></a>Kolejne kroki

* [Samouczek: Regresja modelowania przy użyciu uczenia maszynowego zautomatyzowane i Otwórz zestaw danych](tutorial-opendatasets-automl.md)
* [Python SDK dla otwartych zestawów danych](https://aka.ms/open-datasets-api)
* [Usługa Azure katalogu Otwórz zestawów danych](https://azure.microsoft.com/services/open-datasets/catalog/)
