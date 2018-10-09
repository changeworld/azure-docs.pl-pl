---
title: Przygotować dane za pomocą zestawu SDK usługi Machine Learning Data Prep dla języka Python — Azure
description: Dowiedz się, jak używać usługi Azure Machine Learning Prep zestawu SDK usługi Data dla języka Python do ładowania danych z różnych formatów, przekształcania, aby była bardziej użyteczne i zapisu danych do lokalizacji w celu swoje modele, aby uzyskać dostęp.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 83373f5d6e4a2227bcafdbc4b25b686b0b8d651d
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867191"
---
# <a name="prepare-data-for-modeling-with-azure-machine-learning"></a>Przygotowuje dane do modelowania za pomocą usługi Azure Machine Learning
 
Przygotowywanie danych stanowi ważną część przepływu pracy machine learning. Modele będą bardziej dokładne i wydajne, jeśli mają dostęp do czyszczenia danych w formacie, który jest łatwiejsze korzystanie z. 

Można przygotować swoje dane w języku Python za pomocą [zestawu SDK usługi Azure Machine Learning danych Prep](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py). 

## <a name="data-preparation-pipeline"></a>Potok przygotowywania danych

Procedury przygotowywania danych główne są:

1. [Ładowanie danych](how-to-load-data.md), które mogą znajdować się w różnych formatach
2. [Przekształcanie](how-to-transform-data.md) go do bardziej użyteczne struktury
3. [Zapis](how-to-write-data.md) tych danych do lokalizacji dostępnej dla modeli

![Proces przygotowywania danych](./media/concept-data-preparation/data-prep-process.png)

## <a name="next-steps"></a>Kolejne kroki
Przegląd [przykładzie Notes](https://github.com/Microsoft/MSDataPrepDocs/blob/master/Scenarios/GettingStarted/getting-started.ipynb) przygotowania danych przy użyciu usługi Azure Machine Learning Prep zestawu SDK usługi Data.
