---
title: Pozyskiwania danych za pomocą usługi Azure Data Factory
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć potok pozyskiwania danych za pomocą usługi Azure Data Factory.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 03/01/2020
ms.openlocfilehash: 5b4ed40048aab815397c9726098880b2125b732c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78274790"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Pozyskiwania danych za pomocą usługi Azure Data Factory

W tym artykule dowiesz się, jak utworzyć potok pozyskiwania danych za pomocą usługi Azure Data Factory (ADF). Ten potok służy do pozyskiwania danych do użytku z usługą Azure Machine Learning. Usługa Azure Data Factory umożliwia łatwe wyodrębnianie, przekształcanie i ładowanie danych (ETL). Po przekształceniu i załadowaniu danych do magazynu można go użyć do uczenia modeli uczenia maszynowego.

Proste przetwarzanie danych może być obsługiwane za pomocą natywnych działań ADF i instrumentów, takich jak [przepływ danych.](https://docs.microsoft.com/azure/data-factory/control-flow-execute-data-flow-activity) Jeśli chodzi o bardziej skomplikowanych scenariuszy, dane mogą być przetwarzane za pomocą kodu niestandardowego. Na przykład kod Języka Python lub R.

Istnieje kilka typowych technik korzystania z usługi Azure Data Factory do przekształcania danych podczas pozyskiwania. Każda technika ma plusy i minusy, które określają, czy jest to dobre dopasowanie do konkretnego przypadku użycia:

| Technika | Zalety | Wady |
| ----- | ----- | ----- |
| ADF + funkcje platformy Azure | Małe opóźnienia, obliczenia bezserwerowe</br>Funkcje stanowe</br>Funkcje wielokrotnego pożyciem | Tylko dobre do krótkotrwałego przetwarzania |
| ADF + komponent niestandardowy | Przetwarzanie równoległe na dużą skalę</br>Nadaje się do ciężkich algorytmów | Zawijanie kodu do pliku wykonywalnego</br>Złożoność obsługi zależności i we/wy |
| ADF + notes usługi Azure Databricks | Apache Spark</br>Natywne środowisko Języka Python | Może być kosztowne</br>Tworzenie klastrów początkowo wymaga czasu i dodaje opóźnienia

## <a name="adf-with-azure-functions"></a>ADF z funkcjami platformy Azure

![funkcja adf](media/how-to-data-ingest-adf/adf-function.png)

Usługa Azure Functions umożliwia uruchamianie małych fragmentów kodu (funkcji) bez martwienia się o infrastrukturę aplikacji. W tej opcji dane są przetwarzane za pomocą niestandardowego kodu języka Python zawiniętego w funkcję platformy Azure. 

Funkcja jest wywoływana z [aktywność funkcji platformy Azure ADF](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity). Takie podejście jest dobrym rozwiązaniem dla lekkich przekształceń danych. 

* Plusy:
    * Dane są przetwarzane na komputerze bezserwerowym o stosunkowo małym opóźnieniu
    * Potok usługi ADF może wywołać [trwałą funkcję platformy Azure,](/azure/azure-functions/durable/durable-functions-overview) która może implementować zaawansowany przepływ transformacji danych 
    * Szczegóły transformacji danych są pobierane przez funkcję platformy Azure, która może być ponownie życiówka i wywoływana z innych miejsc
* Minusy:
    * Funkcje platformy Azure muszą zostać utworzone przed użyciem z podajnikem ADF
    * Usługa Azure Functions jest dobra tylko do krótkotrwałego przetwarzania danych

## <a name="adf-with-custom-component-activity"></a>Podajnik ADF z niestandardową aktywnością składnika

![adf-customcomponent](media/how-to-data-ingest-adf/adf-customcomponent.png)

W tej opcji dane są przetwarzane za pomocą niestandardowego kodu Języka Python zawiniętego w plik wykonywalny. Jest wywoływany z [działaniem składnika niestandardowego podajnika ADF](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity). Takie podejście jest lepiej dopasowane do dużych danych niż poprzednia technika.

* Plusy:
    * Dane są przetwarzane w puli [usługi Azure Batch,](https://docs.microsoft.com/azure/batch/batch-technical-overview) która zapewnia obliczenia równoległe na dużą skalę i wysoką wydajność
    * Może być używany do uruchamiania ciężkich algorytmów i przetwarzania znacznych ilości danych
* Minusy:
    * Pula usługi Azure Batch musi zostać utworzona przed użyciem z podajnikem ADF
    * Ponad inżynierii związane z zawijania kodu Pythona do pliku wykonywalnego. Złożoność obsługi zależności i parametrów wejścia/wyjścia

## <a name="adf-with-azure-databricks-python-notebook"></a>Usługa ADF z notesem usługi Azure Databricks Python

![adf-databricks](media/how-to-data-ingest-adf/adf-databricks.png)

[Usługa Azure Databricks](https://azure.microsoft.com/services/databricks/) to platforma analityczna oparta na platformie Apache Spark w chmurze firmy Microsoft.

W tej technice transformacja danych jest wykonywana przez [notes języka Python](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook), uruchomiony w klastrze usługi Azure Databricks. Jest to prawdopodobnie najbardziej typowe podejście, które wykorzystuje pełną moc usługi Azure Databricks. Jest przeznaczony do rozproszonego przetwarzania danych na dużą skalę.

* Plusy:
    * Dane są przekształcane w najmocniejszą usługę platformy Azure przetwarzania danych, która jest wspierana przez środowisko Apache Spark
    * Natywna obsługa Języka Python wraz z platformami i bibliotekami do nauki o danych, w tym TensorFlow, PyTorch i scikit-learn
    * Nie ma potrzeby zawijania kodu Języka Python do funkcji lub modułów wykonywalnych. Kod działa tak, jak jest.
* Minusy:
    * Infrastruktura usługi Azure Databricks musi zostać utworzona przed użyciem z usługą ADF
    * Może być kosztowne w zależności od konfiguracji usługi Azure Databricks
    * Rozkręcenie klastrów obliczeniowych z trybu "zimnego" zajmuje trochę czasu, co powoduje duże opóźnienie w rozwiązaniu 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>Korzystanie z danych w potokach usługi Azure Machine Learning

![zestaw danych aml](media/how-to-data-ingest-adf/aml-dataset.png)

Przekształcone dane z potoku usługi ADF są zapisywane w magazynie danych (takich jak obiekt blob platformy Azure). Usługa Azure Machine Learning może uzyskiwać dostęp do tych danych za pomocą [magazynów danych](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores) i [zestawów danych.](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)

Za każdym razem, gdy potok ADF jest uruchamiany, dane są zapisywane w innej lokalizacji w magazynie. Aby przekazać lokalizację do usługi Azure Machine Learning, potok usługi ADF wywołuje potok usługi Azure Machine Learning. Podczas wywoływania potoku ml lokalizacja danych i identyfikator uruchamiania są wysyłane jako parametry. Potok ml można następnie utworzyć magazyn danych/zestaw danych przy użyciu lokalizacji danych. 

> [!TIP]
> Zestawy danych [obsługują przechowywanie wersji,](https://docs.microsoft.com/azure/machine-learning/how-to-version-track-datasets)dzięki czemu potok ml może zarejestrować nową wersję zestawu danych, która wskazuje najnowsze dane z potoku ADF.

Gdy dane są dostępne za pośrednictwem magazynu danych lub zestawu danych, można go użyć do uczenia modelu uczenia ml. Proces szkolenia może być częścią tego samego potoku ml, który jest wywoływany z podajnika ADF. Lub może to być oddzielny proces, takich jak eksperymenty w notatniku Jupyter.

Ponieważ zestawy danych obsługują przechowywanie wersji, a każde uruchomienie z potoku tworzy nową wersję, łatwo zrozumieć, która wersja danych została użyta do szkolenia modelu.

## <a name="next-steps"></a>Następne kroki

* [Uruchamianie notesu databricks w usłudze Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)
* [Uzyskiwanie dostępu do danych w usługach magazynu platformy Azure](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)
* [Trenuj modele z zestawami danych w usłudze Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)
* [DevOps dla potoku pozyskiwania danych](https://docs.microsoft.com/azure/machine-learning/how-to-cicd-data-ingestion)

