---
title: Opcje pozyskiwania danych
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej o opcjach pozyskiwania danych do szkolenia modeli uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.openlocfilehash: 6b1c671d2079c7d8ab59e9afe981ccef3f58ef27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086890"
---
# <a name="data-ingestion-in-azure-machine-learning"></a>Pozyskiwania danych w usłudze Azure Machine Learning

W tym artykule poznasz zalety i minusy następujących opcji pozyskiwania danych dostępnych w usłudze Azure Machine Learning. 

1. [Potoki usługi Azure Data Factory](#use-azure-data-factory)
2. [Azure Machine Learning Python SDK](#use-the-python-sdk)

Pozyskiwanie danych jest procesem, w którym nieustrukturyzowane dane są wyodrębniane z jednego lub wielu źródeł, a następnie przygotowywane do szkolenia modeli uczenia maszynowego. Jest również czasochłonne, zwłaszcza jeśli odbywa się ręcznie, a jeśli masz duże ilości danych z wielu źródeł. Automatyzacja tego wysiłku zwalnia zasoby i zapewnia, że modele korzystają z najnowszych i odpowiednich danych.

Usługa Azure Data Factory (ADF) jest specjalnie zbudowana w celu wyodrębniania, ładowania i przekształcania danych, jednak zestaw SDK języka Python umożliwia opracowanie niestandardowego rozwiązania kodu do podstawowych zadań pozyskiwania danych. Jeśli nie są to, czego potrzebujesz, możesz również użyć ADF i zestawu SDK języka Python razem, aby utworzyć ogólny przepływ pracy pozyskiwania danych, który spełnia Twoje potrzeby. 

## <a name="use-azure-data-factory"></a>Za pomocą usługi Azure Data Factory

[Usługa Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) oferuje natywną obsługę monitorowania źródeł danych i wyzwalaczy dla potoków pozyskiwania danych.  

W poniższej tabeli podsumowano zalety i wady dotyczące korzystania z usługi Azure Data Factory dla przepływów pracy pozyskiwania danych.

|Zalety|Wady
---|---
Specjalnie zbudowany do wyodrębniania, ładowania i przekształcania danych.|Obecnie oferuje ograniczony zestaw zadań potoku usługi Azure Data Factory 
Umożliwia tworzenie opartych na danych przepływów pracy do organizowania przenoszenia danych i przekształceń na dużą skalę.|Kosztowne w budowie i konserwacji. Więcej informacji można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) usługi Azure Data Factory.
Zintegrowane z różnymi narzędziami platformy Azure, takimi jak [Azure Databricks](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook) i [Azure Functions](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity) | Nie uruchamia natywnie skryptów, zamiast tego opiera się na oddzielnych obliczeniach dla przebiegów skryptu 
Natywnie obsługuje pozyskiwanie danych wyzwolone przez źródło danych| 
Procesy przygotowania danych i modelowania są oddzielne.|
Możliwości osadzonej linii danych dla przepływów danych usługi Azure Data Factory|
Zapewnia [interfejs użytkownika](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) o niskim poziomie środowiska kodu dla metod nieobjętych skryptami |

Te kroki i poniższy diagram ilustrują przepływ pracy pozyskiwania danych usługi Azure Data Factory.

1. Wyciąganie danych ze źródeł
1. Przekształcanie i zapisywanie danych w wyjściowym kontenerze obiektów blob, który służy jako magazyn danych dla usługi Azure Machine Learning
1. Po zapisaniu przygotowanych danych potok usługi Azure Data Factory wywołuje potok uczenia maszynowego szkoleniowego, który odbiera przygotowane dane do szkolenia modelu


    ![Pobędzenie danych usługi ADF](media/concept-data-ingestion/data-ingest-option-one.svg)
    
Dowiedz się, jak utworzyć potok pozyskiwania danych dla usługi Machine Learning za pomocą [usługi Azure Data Factory.](how-to-data-ingest-adf.md)

## <a name="use-the-python-sdk"></a>Korzystanie z modułu SDK języka Python 

Za pomocą [zestawu SDK języka Python](https://docs.microsoft.com/python/api/overview/azure/ml)można włączyć zadania pozyskiwania danych do kroku [potoku usługi Azure Machine Learning.](how-to-create-your-first-pipeline.md)

W poniższej tabeli podsumowano zalety i con za pomocą zestawu SDK i kroku potoków ml dla zadań pozyskiwania danych.

Zalety| Wady
---|---
Konfigurowanie własnych skryptów języka Python | Nie obsługuje natywnie wyzwalania zmiany źródła danych. Wymaga implementacji aplikacji logiki lub funkcji platformy Azure
Przygotowanie danych w ramach każdego wykonania szkolenia modelu|Wymaga umiejętności programistycznych w celu utworzenia skryptu pozyskiwania danych
Obsługuje skrypty przygotowania danych na różnych obiektach docelowych obliczeń, w tym [obliczeniach usługi Azure Machine Learning](concept-compute-target.md#azure-machine-learning-compute-managed) |Nie udostępnia interfejsu użytkownika służącego do tworzenia mechanizmu pozyskiwania

Na poniższym diagramie potok usługi Azure Machine Learning składa się z dwóch kroków: pozyskiwania danych i szkolenia modelu. Krok pozyskiwania danych obejmuje zadania, które można wykonać przy użyciu bibliotek języka Python i zestawu SDK języka Python, takie jak wyodrębnianie danych ze źródeł lokalnych/internetowych i przekształcenia danych podstawowych, takie jak brakujące przypisanie wartości. Etap szkolenia następnie używa przygotowanych danych jako dane wejściowe do skryptu szkoleniowego, aby trenować model uczenia maszynowego. 

![Potok platformy Azure + pozyskiwania danych sdk](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak utworzyć potok pozyskiwania danych dla usługi Machine Learning za pomocą [usługi Azure Data Factory](how-to-data-ingest-adf.md)
* Dowiedz się, jak zautomatyzować cykle życia programowania potoków pozyskiwania danych i zarządzać nimi za pomocą [usługi Azure Pipelines.](how-to-cicd-data-ingestion.md)
