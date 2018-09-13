---
title: Dokumentacja usługi Azure Machine Learning Model danych zbierania API | Dokumentacja firmy Microsoft
description: Dokumentacja usługi Azure Machine Learning Model danych zbierania API.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: d9fee56d7748cdfd34f982fe79467f7d61c54926
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35644734"
---
# <a name="azure-machine-learning-model-data-collection-api-reference"></a>Dokumentacja usługi Azure Machine Learning Model danych zbierania API

Zbieranie danych modelu umożliwia archiwizowanie danych wejściowych i modelu za pomocą usługi machine learning web service. Zobacz [poradnik modelu danych kolekcji](how-to-use-model-data-collection.md) Aby zrozumieć sposób instalowania `azureml.datacollector` na komputerze Windows i Linux.

W tym przewodniku dokumentacja interfejsu API używamy podejścia krok po kroku dotyczące sposobu zbierania danych wejściowych i modelu prognozy z usługi machine learning web service.

## <a name="enable-model-data-collection-in-azure-ml-workbench-environment"></a>Włącz zbieranie danych modelu w środowisku Azure ML Workbench

 Wyszukaj conda\_dependencies.yml pliku w projekcie w folderze aml_config i mają swoje conda\_dependencies.yml plik zawiera moduł azureml.datacollector w sekcji narzędzia pip, jak pokazano poniżej. Pamiętaj, że jest to tylko podsekcję pełną conda\_dependencies.yml pliku:

    dependencies:
      - python=3.5.2
      - pip:
        - azureml.datacollector==0.1.0a13

>[!NOTE] 
>Obecnie można używać moduł zbierający dane w aplikacji Azure ML Workbench według działających w trybie platformy docker. Tryb lokalny może nie działać dla wszystkich środowisk.




## <a name="enable-model-data-collection-in-the-scoring-file"></a>Włącz zbieranie danych modelu w plik oceniania

W pliku oceniania, który jest używany na potrzeby operacjonalizacji Zaimportuj moduł zbierający dane i klasy ModelDataCollector:

    from azureml.datacollector import ModelDataCollector


## <a name="model-data-collector-instantiation"></a>Tworzenie wystąpienia modułu zbierającego dane modelu
Utwórz wystąpienie nowe wystąpienie klasy ModelDataCollector:

    dc = ModelDataCollector(model_name, identifier='default', feature_names=None, model_management_account_id='unknown', webservice_name='unknown', model_id='unknown', model_version='unknown')

Zobacz szczegóły klasy i parametru:

### <a name="class"></a>Klasa
| Name (Nazwa) | Opis |
|--------------------|--------------------|
| ModelDataCollector | Klasy w przestrzeni nazw azureml.datacollector. Wystąpienie tej klasy będzie służyć do zbierania danych modelu. Pojedynczy plik oceniania może zawierać wiele ModelDataCollectors. Każde wystąpienie powinno być używane do gromadzenia danych w jednej lokalizacji dyskretnych w plik oceniania, tak, aby spójność schematu zebranych danych (czyli danych wejściowych i prognozowania)|


### <a name="parameters"></a>Parametry

| Name (Nazwa) | Typ | Opis |
|-------------|------------|-------------------------|
| nazwa_modelu | ciąg | Nazwa modelu, którego dane są zbierane dla |
| Identyfikator | ciąg | Lokalizacja w kodzie, który identyfikuje te dane, czyli "RawInput" lub "Prognozowania" |
| feature_names | Lista ciągów | Lista nazw funkcji, które stają się nagłówek csv, gdy podane |
| model_management_account_id | ciąg | Identyfikator konta zarządzania modelami, na którym są przechowywane w tym modelu. To jest wypełniane automatycznie podczas modeli jest przygotowany do działania za pośrednictwem AML |
| webservice_name | ciąg | Nazwa usługi sieci Web, na którym ten model jest obecnie wdrożona. To jest wypełniane automatycznie podczas modeli jest przygotowany do działania za pośrednictwem AML |
| model_id | ciąg | Unikatowy identyfikator dla tego modelu w kontekście konta zarządzania modelami. to jest wypełniane automatycznie podczas modeli jest przygotowany do działania za pośrednictwem AML |
| model_version | ciąg | numer wersji tego modelu w kontekście konta zarządzania modelami. To jest wypełniane automatycznie podczas modeli jest przygotowany do działania za pośrednictwem AML |



 

## <a name="collecting-the-model-data"></a>Zbieranie danych modelu

Można zebrać danych modelu za pomocą wystąpienia ModelDataCollector utworzonego powyżej.

    dc.collect(input_data, user_correlation_id="")

Zobacz szczegóły metody i parametrów:

### <a name="method"></a>Metoda
| Name (Nazwa) | Opis |
|--------------------|--------------------|
| zbieranie | Używane do zbierania danych modelu danych wejściowych i prognozowania|


### <a name="parameters"></a>Parametry

| Name (Nazwa) | Typ | Opis |
|-------------|------------|-------------------------|
| input_data | wiele typów | dane, które mają być zbierane (obecnie akceptuje listę typów, numpy.array pandas. Ramka danych, pyspark.sql.DataFrame). Dla typów ramkę danych, jeśli istnieje nagłówek nazwach funkcji te informacje znajdują się w miejscu docelowym danych (bez konieczności jawnego przesłania nazwy funkcji w Konstruktorze ModelDataCollector) |
| user_correlation_id | ciąg | Identyfikator korelacji opcjonalne, które mogą być zapewniane przez użytkownika do skorelowania tego prognoz |

