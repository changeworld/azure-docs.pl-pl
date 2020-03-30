---
title: Monitorowanie i zbieranie danych z punktów końcowych usługi sieci Web usługi uczenia maszynowego
titleSuffix: Azure Machine Learning
description: Monitorowanie usług sieci Web wdrożonych za pomocą usługi Azure Machine Learning przy użyciu usługi Azure Application Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 03/12/2020
ms.openlocfilehash: 464ec1fcf0986dc04bd92bbe9e31b5675e5822d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136197"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Monitorowanie i zbieranie danych z punktów końcowych usługi sieci web ml
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak zbierać dane z i monitorować modele wdrożone w punktach końcowych usługi sieci web w usłudze Azure Kubernetes Service (AKS) lub Instancja kontenerów platformy Azure (ACI), włączając usługę Azure Application Insights za pośrednictwem 
* [Azure Machine Learning Python SDK](#python)
* [Studio usługi Azure Machine Learning](#studio) pod adresem:https://ml.azure.com

Oprócz zbierania danych wyjściowych i odpowiedzi punktu końcowego można monitorować:

* Stawki żądań, czasy odpowiedzi i wskaźniki niepowodzeń
* Wskaźniki zależności, czasy odpowiedzi i wskaźniki niepowodzeń
* Wyjątki

[Dowiedz się więcej o usłudze Azure Application Insights](../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś

* Obszar roboczy usługi Azure Machine Learning, katalog lokalny zawierający skrypty i zainstalowany zestaw SDK usługi Azure Machine Learning dla języka Python. Aby dowiedzieć się, jak uzyskać te wymagania wstępne, zobacz [Jak skonfigurować środowisko programistyczne](how-to-configure-environment.md)

* Uczony model uczenia maszynowego, który ma zostać wdrożony w usłudze Azure Kubernetes Service (AKS) lub wystąpieniu kontenera platformy Azure (ACI). Jeśli go nie masz, zobacz samouczek [modelu klasyfikacji obrazów pociągu](tutorial-train-models-with-aml.md)

## <a name="web-service-metadata-and-response-data"></a>Metadane usługi sieci Web i dane odpowiedzi

>[!Important]
> Usługa Azure Application Insights rejestruje tylko ładunki o przesiewowej o biełcie do 64 kb. Jeśli ten limit zostanie osiągnięty, rejestrowane są tylko najnowsze dane wyjściowe modelu. 

Metadane i odpowiedzi na usługę — odpowiadające metadanym usługi sieci web i prognozom modelu — są `"model_data_collection"`rejestrowane w śladach usługi Azure Application Insights w komunikacie. Można wysyłać zapytania do usługi Azure Application Insights bezpośrednio, aby uzyskać dostęp do tych danych lub skonfigurować [ciągły eksport](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) na konto magazynu w celu dłuższego przechowywania lub dalszego przetwarzania. Dane modelu mogą być następnie używane w usłudze Azure Machine Learning do konfigurowania etykietowania, ponownego szkolenia, wyjaśnienia, analizy danych lub innego użycia. 

<a name="python"></a>

## <a name="use-python-sdk-to-configure"></a>Konfigurowanie konfigurowania za pomocą narzędzia Python SDK 

### <a name="update-a-deployed-service"></a>Aktualizowanie wdrożonej usługi

1. Zidentyfikuj usługę w obszarze roboczym. Wartość `ws` jest nazwą obszaru roboczego

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Aktualizowanie usługi i włączanie usługi Azure Application Insights

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Rejestrowanie niestandardowych śladów w usłudze

Jeśli chcesz rejestrować niestandardowe ślady, postępuj zgodnie ze standardowym procesem wdrażania usługi AKS lub ACI w [dokumencie Jak wdrożyć i gdzie.](how-to-deploy-and-where.md) Następnie należy wykonać następujące czynności:

1. Aktualizowanie pliku oceniania przez dodanie instrukcji drukowania
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Aktualizowanie konfiguracji usługi
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Tworzenie obrazu i wdrażanie go na [AKS lub ACI](how-to-deploy-and-where.md).

### <a name="disable-tracking-in-python"></a>Wyłączanie śledzenia w pythonie

Aby wyłączyć usługę Azure Application Insights, użyj następującego kodu:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="use-azure-machine-learning-studio-to-configure"></a>Konfigurowanie konfigurowania za pomocą usługi Azure Machine Learning studio

Możesz również włączyć usługę Azure Application Insights z usługi Azure Machine Learning studio, gdy wszystko będzie gotowe do wdrożenia modelu za pomocą tych kroków.

1. Zaloguj się do swojego obszaru roboczego pod adresemhttps://ml.azure.com/
1. Przejdź do **modeli** i wybierz model, który chcesz wdrożyć
1. Wybierz **+Wdrażanie**
1. Wypełnianie formularza **modelu wdrażania**
1. Rozwiń menu **Zaawansowane**

    ![Wdrażanie formularza](./media/how-to-enable-app-insights/deploy-form.png)
1. Wybierz **włącz diagnostykę usługi Application Insights i zbieranie danych**

    ![Włączanie statystyk aplikacji](./media/how-to-enable-app-insights/enable-app-insights.png)
## <a name="evaluate-data"></a>Ocena danych
Dane usługi są przechowywane na koncie usługi Azure Application Insights w tej samej grupie zasobów co usługa Azure Machine Learning.
Aby go zobaczyć:

1. Przejdź do obszaru roboczego usługi Azure Machine Learning w [portalu Azure](https://ms.portal.azure.com/) i kliknij łącze Usługi Application Insights

    [![Lokalizacja aplikacji](./media/how-to-enable-app-insights/AppInsightsLoc.png)](././media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Wybierz kartę **Przegląd,** aby wyświetlić podstawowy zestaw danych dla usługi

   [![Omówienie](./media/how-to-enable-app-insights/overview.png)](././media/how-to-enable-app-insights/overview.png#lightbox)

1. Aby sprawdzić metadane i odpowiedzi żądania usługi sieci web, wybierz tabelę **żądań** w sekcji **Dzienniki (Analytics)** i wybierz pozycję **Uruchom,** aby wyświetlić żądania

   [![Modelowanie danych](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. Aby przyjrzeć się śladom niestandardowym, wybierz **Analytics**
4. W sekcji schematu wybierz pozycję **Ślady**. Następnie wybierz pozycję **Uruchom,** aby uruchomić kwerendę. Dane powinny być wyświetlane w formacie tabeli i powinny być mapowane na niestandardowe wywołania w pliku oceniania

   [![Ślady niestandardowe](./media/how-to-enable-app-insights/logs.png)](././media/how-to-enable-app-insights/logs.png#lightbox)

Aby dowiedzieć się więcej o korzystaniu z usługi Azure Application Insights, zobacz [Co to jest usługa Application Insights?](../azure-monitor/app/app-insights-overview.md).

## <a name="export-data-for-further-processing-and-longer-retention"></a>Eksport danych w celu dalszego przetwarzania i dłuższego przechowywania

>[!Important]
> Usługa Azure Application Insights obsługuje tylko eksport do magazynu obiektów blob. Dodatkowe limity tej możliwości eksportu są wymienione w [obszarze Eksportuj dane telemetryczne z aplikacji App Insights](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration).

[Ciągłego eksportowania](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) usługi Azure Application Insights można używać do wysyłania wiadomości do obsługiwanego konta magazynu, w którym można ustawić dłuższe przechowywanie. Wiadomości `"model_data_collection"` są przechowywane w formacie JSON i można łatwo przeanalizować, aby wyodrębnić dane modelu. 

Usługa Azure Data Factory, usługi Azure ML Pipelines lub inne narzędzia do przetwarzania danych mogą służyć do przekształcania danych w razie potrzeby. Po przekształceniu danych można zarejestrować je w obszarze roboczym usługi Azure Machine Learning jako zestaw danych. Aby to zrobić, zobacz [Jak tworzyć i rejestrować zestawy danych](how-to-create-register-datasets.md).

   [![Ciągły eksport](./media/how-to-enable-app-insights/continuous-export-setup.png)](././media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>Przykładowy notes

Notes [enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) przedstawia pojęcia w tym artykule. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki

* Zobacz, [jak wdrożyć model w klastrze usługi Azure Kubernetes](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service) lub [jak wdrożyć model w wystąpieniach kontenera platformy Azure](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance) w celu wdrożenia modeli w punktach końcowych usługi sieci Web i włączyć usługę Azure Application Insights w celu wykorzystania zbierania danych i monitorowania punktów końcowych
* Zobacz [Mops: Zarządzanie, wdrażanie i monitorowanie modeli za pomocą usługi Azure Machine Learning,](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment) aby dowiedzieć się więcej na temat wykorzystywania danych zebranych z modeli w produkcji. Takie dane mogą pomóc w ciągłym ulepszaniu procesu uczenia maszynowego
