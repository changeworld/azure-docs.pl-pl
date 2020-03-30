---
title: Korzystanie ze zdarzeń usługi Azure Machine Learning
titleSuffix: Azure Machine Learning
description: W tym artykule dowiesz się, jak używać usługi Azure Event Grid do subskrybowania, reagowania i anulowania subskrypcji zdarzeń generowanych przez usługę Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: andyxu
author: gogowings
ms.date: 11/04/2019
ms.openlocfilehash: d8a975487c68a21b2c8b6fa2f07d86c312243f12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139049"
---
# <a name="consume-azure-machine-learning-events-preview"></a>Korzystanie ze zdarzeń usługi Azure Machine Learning (wersja zapoznawcza)

Usługa Azure Machine Learning zarządza całym cyklem życia procesu uczenia maszynowego, w tym szkoleniem modelu, wdrażaniem modelu i monitorowaniem. Zdarzenia usługi Azure Machine Learning umożliwiają aplikacjom reagowanie na zdarzenia podczas cyklu życia uczenia maszynowego, takie jak ukończenie przebiegów szkoleniowych, rejestracja i wdrażanie modeli oraz wykrywanie dryfu danych przy użyciu nowoczesnych bezserwerowych Architektury. 

Te zdarzenia są publikowane za pośrednictwem [usługi Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Korzystając z witryny Azure portal, Powershell lub Azure CLI, klienci mogą łatwo subskrybować [zdarzenia, określając co najmniej jeden typ zdarzeń i warunki filtrowania.](/azure/event-grid/event-filtering) Klienci mają również możliwość tworzenia szerokiej gamy programów obsługi zdarzeń, takich jak usługi Azure Functions, Usługa Azure Logic Apps lub ogólne elementy webhook. Usługa Azure Machine Learning, wraz z usługą Azure Event Grid, zapewnia wysoką dostępną, niezawodną i odporną na uszkodzenia platformę dostarczania zdarzeń do tworzenia aplikacji opartych na zdarzeniach.

Aby uzyskać informacje na temat korzystania z usługi Azure Machine Learning z siatką zdarzeń, zobacz [Tworzenie przepływów pracy opartych na uczeniu maszynowym opartych na zdarzeniach (wersja zapoznawcza)](how-to-use-event-grid.md).

## <a name="the-event-model"></a>Model zdarzenia 

Usługa Azure Event Grid odczytuje zdarzenia ze źródeł, takich jak usługa Azure Machine Learning i inne usługi platformy Azure. Te zdarzenia są następnie wysyłane do programów obsługi zdarzeń, takich jak Usługi Azure Event Hubs, usługi Azure Functions, aplikacje logiki i inne. Na poniższym diagramie pokazano, jak usługa Event Grid łączy źródła i programy obsługi, ale nie jest wyczerpującą listą obsługiwanych integracji.

![Model funkcjonalny usługi Azure Event Grid](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Aby uzyskać więcej informacji na temat źródeł zdarzeń i programów obsługi zdarzeń, zobacz [Co to jest siatka zdarzeń?](/azure/event-grid/overview).

## <a name="azure-machine-learning-event-types"></a>Typy zdarzeń usługi Azure Machine Learning

Usługa Azure Machine Learning udostępnia zdarzenia w różnych punktach cyklu życia uczenia maszynowego: 

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Wywoływane po zakończeniu wykonywania eksperymentu uczenia maszynowego |
| `Microsoft.MachineLearningServices.ModelRegistered` | Wywoływane, gdy model uczenia maszynowego jest zarejestrowany w obszarze roboczym |
| `Microsoft.MachineLearningServices.ModelDeployed` | Wywoływane po zakończeniu wdrażania usługi wnioskowania z jednym lub kilkoma modelami |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Wywoływane po zakończeniu zadania wykrywania dryfu danych dla dwóch zestawów danych |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Wywoływane po zmianie stanu uruchomienia, obecnie wywoływane tylko wtedy, gdy stan uruchomienia jest "nie powiodło się" |

## <a name="subscribe-to-machine-learning-events"></a>Subskrybuj wydarzenia usługi Machine Learning

Subskrypcje zdarzeń usługi Azure Machine Learning są chronione przez kontrolę dostępu opartą na rolach (RBAC). Tylko [współautor lub właściciel](how-to-assign-roles.md#default-roles) obszaru roboczego może tworzyć, aktualizować i usuwać subskrypcje zdarzeń.

Subskrypcje zdarzeń można filtrować na podstawie różnych warunków. Filtry mogą być stosowane do subskrypcji zdarzeń podczas [tworzenia](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) subskrypcji wydarzenia lub [w późniejszym czasie.](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) 

### <a name="filter-by-event-type"></a>Filtrowanie według typu zdarzenia
Subskrypcja zdarzeń można określić jeden lub więcej typów zdarzeń usługi Azure Machine Learning.

### <a name="filter-by-event-subject"></a>Filtrowanie według tematu zdarzenia
Usługa Azure Event Grid obsługuje filtry tematów na podstawie __rozpoczyna się__ i __kończy z__ dopasowania, dzięki czemu zdarzenia z pasującym tematem są dostarczane do subskrybenta. Różne zdarzenia uczenia maszynowego mają inny format tematu.

| Typ zdarzenia | Format tematu | Przykładowy temat |
| ---------- | ----------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
| `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
| `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
| `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

### <a name="advanced-filtering"></a>Filtrowanie zaawansowane

Usługa Azure Event Grid obsługuje również zaawansowane filtrowanie na podstawie opublikowanego schematu zdarzeń. Szczegóły schematu zdarzeń usługi Azure Machine Learning można znaleźć w [schemacie zdarzeń usługi Azure Event Grid dla usługi Azure Machine Learning.](../event-grid/event-schema-machine-learning.md)

Niektóre przykładowe filtry zaawansowane, które można wykonać, obejmują:

* W `Microsoft.MachineLearningServices.ModelRegistered` przypadku zdarzenia, aby filtrować wartość znacznika modelu:

    ```
    --advanced-filter data.ModelTags.key1 StringIn ('value1')
    ```

Aby dowiedzieć się więcej o stosowaniu filtrów, zobacz [Filtrowanie zdarzeń dla siatki zdarzeń](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="consume-machine-learning-events"></a>Korzystanie ze zdarzeń uczenia maszynowego

Aplikacje obsługujące zdarzenia uczenia maszynowego powinny stosować się do kilku zalecanych praktyk:

> [!div class="checklist"]
> * Ponieważ wiele subskrypcji można skonfigurować do kierowania zdarzeń do tego samego programu obsługi zdarzeń, ważne jest, aby nie zakładać, że zdarzenia pochodzą z określonego źródła, ale aby sprawdzić temat wiadomości, aby upewnić się, że pochodzi z obszaru roboczego uczenia maszynowego, którego oczekujesz.
> * Podobnie sprawdź, czy eventType jest jednym jesteś przygotowany do przetworzenia i nie zakładaj, że wszystkie zdarzenia, które otrzymasz będą typy, których oczekujesz.
> * Ponieważ wiadomości mogą być dostarczane poza kolejnością i po pewnym opóźnieniu, użyj pól etag, aby dowiedzieć się, czy informacje o obiektach są nadal aktualne.  Ponadto użyj pól sekwencera, aby zrozumieć kolejność zdarzeń na dowolnym określonym obiekcie.
> * Ignoruj pola, których nie rozumiesz. Ta praktyka pomoże Ci zachować odporność na nowe funkcje, które mogą być dodawane w przyszłości.
> * Nieudane lub anulowane operacje usługi Azure Machine Learning nie wyzwoli zdarzenia. Na przykład jeśli wdrożenie modelu nie powiedzie się Microsoft.MachineLearningServices.ModelDeployed nie zostanie wyzwolona. Należy wziąć pod uwagę taki tryb awarii podczas projektowania aplikacji. Zawsze możesz użyć zestawu SDK, interfejsu wiersza polecenia lub portalu usługi Azure Machine Learning, aby sprawdzić stan operacji i zrozumieć szczegółowe przyczyny awarii.

Usługa Azure Event Grid umożliwia klientom tworzenie programów obsługi wiadomości oddzielonych odłączanych, które mogą być wyzwalane przez zdarzenia usługi Azure Machine Learning. Oto kilka godnych uwagi przykładów programów obsługi wiadomości:
* Azure Functions
* Azure Logic Apps
* Azure Event Hubs
* Potok fabryki danych platformy Azure
* Ogólne elementki webhook, które mogą być hostowane na platformie Azure lub w innym miejscu

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usłudze Event Grid i wypróbuj zdarzenia usługi Azure Machine Learning:

- [Event Grid — informacje](../event-grid/overview.md)
- [Schemat zdarzeń usługi Azure Event Grid dla usługi Azure Machine Learning](../event-grid/event-schema-machine-learning.md)
- [Tworzenie przepływów pracy opartych na zdarzeniach za pomocą usługi Azure Machine Learning](how-to-use-event-grid.md)
