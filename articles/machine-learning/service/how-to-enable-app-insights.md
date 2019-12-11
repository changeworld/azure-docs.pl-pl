---
title: Monitorowanie i zbieranie danych z punktów końcowych usługi sieci Web Machine Learning
titleSuffix: Azure Machine Learning
description: Monitorowanie usług sieci Web wdrożonych za pomocą Azure Machine Learning platformy Azure Application Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: f4bb399e24b2c480023eba6bf5b71990f6d39c8c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978377"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Monitorowanie i zbieranie danych z punktów końcowych usługi sieci Web ML
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak zbierać dane i monitorować modele wdrożone w punktach końcowych usługi sieci Web w usłudze Azure Kubernetes Service (AKS) lub Azure Container Instances (ACI), włączając usługę Azure Application Insights. Oprócz zbierania danych wejściowych i odpowiedzi punktu końcowego można monitorować:

* Stawki żądania, czasy odpowiedzi i wskaźniki niepowodzeń
* Stawki zależności, czasy odpowiedzi i wskaźniki niepowodzeń
* Wyjątki

[Dowiedz się więcej o usłudze Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj

* Obszarem roboczym usługi Azure Machine Learning, katalog lokalny, który zawiera skrypty i zestawu SDK usługi Azure Machine Learning dla języka Python zainstalowane. Aby dowiedzieć się, jak uzyskać te wymagania wstępne, zobacz [jak skonfigurować środowisko programistyczne](how-to-configure-environment.md) .
* Model uczenia maszynowego uczonego do wdrożenia usługi Azure Kubernetes Service (AKS) lub wystąpienia kontenera platformy Azure (ACI). Jeśli go nie masz, zapoznaj się z samouczkiem dotyczącym [modelu klasyfikacji obrazów szkolenia](tutorial-train-models-with-aml.md)

## <a name="web-service-input-and-response-data"></a>Dane wejściowe i odpowiedzi usługi sieci Web

Dane wejściowe i odpowiedzi dotyczące usługi — odpowiadające danym wejściowym modelu ML i jego prognozowanie — są rejestrowane w śladach usługi Azure Application Insights w obszarze `"model_data_collection"`komunikatów. Możesz wysyłać zapytania do usługi Azure Application Insights bezpośrednio, aby uzyskać dostęp do tych danych, lub skonfigurować [ciągły eksport](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) do konta magazynu w celu dłuższego przechowywania lub dalszego przetwarzania. Dane modelu można następnie użyć w Azure Machine Learning, aby skonfigurować etykietowanie, przeszkolenie, wyjaśnienie, analizę danych lub inne użycie. 

## <a name="use-the-azure-portal-to-configure"></a>Użyj Azure Portal, aby skonfigurować

Możesz włączyć i wyłączyć usługę Azure Application Insights w Azure Portal. 

1. W [Azure Portal](https://portal.azure.com)Otwórz obszar roboczy

1. Na karcie **wdrożenia** wybierz usługę, w której chcesz włączyć Application Insights platformy Azure

   [![Lista usług na karcie wdrożenia](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Wybierz pozycję **Edytuj**

   [![Przycisk Edytuj](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. W obszarze **Ustawienia zaawansowane**zaznacz pole wyboru **Włącz diagnostykę AppInsights**

   [![Zaznaczenie pola wyboru dotyczące włączania diagnostyki](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Wybierz pozycję **Aktualizuj** w dolnej części ekranu, aby zastosować zmiany

### <a name="disable"></a>Wyłączenie

1. W [Azure Portal](https://portal.azure.com)Otwórz obszar roboczy
1. Wybierz pozycję **wdrożenia**, wybierz pozycję Usługa, a następnie wybierz pozycję **Edytuj** .

   [![Kliknij przycisk Edytuj](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. W obszarze **Ustawienia zaawansowane**wyczyść pole wyboru **Włącz diagnostykę AppInsights**

   [![Wyczyszczone pole wyboru włączenia diagnostyki](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Wybierz pozycję **Aktualizuj** w dolnej części ekranu, aby zastosować zmiany
 
## <a name="use-python-sdk-to-configure"></a>Użyj zestawu SDK języka Python, aby skonfigurować 

### <a name="update-a-deployed-service"></a>Aktualizowania wdrożonej usługi

1. Zidentyfikuj usługi w obszarze roboczym. Wartość `ws` jest nazwą obszaru roboczego

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Aktualizowanie usługi i Włączanie usługi Azure Application Insights

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Ślady niestandardowych dzienników w usłudze

Jeśli chcesz ślady niestandardowych dzienników, postępuj zgodnie z procesem standardowego wdrożenia dla usługi AKS lub ACI w [sposób wdrażania i gdzie](how-to-deploy-and-where.md) dokumentu. Następnie wykonaj następujące czynności:

1. Aktualizowanie pliku oceniania przez dodanie instrukcji Print
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Aktualizowanie konfiguracji usługi
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Tworzenie obrazu i wdrażanie go na [AKS](how-to-deploy-to-aks.md) lub [ACI](how-to-deploy-to-aci.md)

### <a name="disable-tracking-in-python"></a>Wyłącz śledzenia w języku Python

Aby wyłączyć usługę Azure Application Insights, użyj następującego kodu:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

## <a name="evaluate-data"></a>Ocena danych
Dane usługi są przechowywane na koncie usługi Azure Application Insights w ramach tej samej grupy zasobów co Azure Machine Learning.
Aby go wyświetlić:

1. Przejdź do obszaru roboczego Azure Machine Learning w programie [Azure Machine Learning Studio](https://ml.azure.com) i kliknij link Application Insights

    [![AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Wybierz kartę **Przegląd** , aby wyświetlić podstawowy zestaw metryk dla usługi

   [![Przegląd](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. Aby przyjrzeć się do ładunków danych wejściowych i odpowiedzi usługi sieci Web, wybierz pozycję **Analiza**
1. W sekcji schemat wybierz opcję **ślady** i odfiltruj ślady z komunikatem `"model_data_collection"`. W wymiarach niestandardowych można zobaczyć dane wejściowe, prognozy i inne istotne szczegóły.

   [![dane modelu](media/how-to-enable-app-insights/model-data-trace.png)](./media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. Aby zapoznać się ze śladami niestandardowymi, wybierz pozycję **Analiza**
4. W sekcji schematu wybierz **ślady**. Następnie wybierz pozycję **Uruchom** kwerendy. Dane powinny być wyświetlane w formacie tabeli i powinny być mapowane na niestandardowe wywołania w pliku oceniania

   [![Niestandardowe śledzenie](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Aby dowiedzieć się więcej na temat korzystania z usługi Azure Application Insights, zobacz [co to jest Application Insights?](../../azure-monitor/app/app-insights-overview.md).

## <a name="export-data-for-further-processing-and-longer-retention"></a>Eksportowanie danych do dalszego przetwarzania i dłuższego przechowywania

Aby wysyłać komunikaty do obsługiwanego konta magazynu, można użyć usługi Azure Application Insights " [eksport ciągły](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) ", w której można ustawić dłuższe przechowywanie. Komunikaty `"model_data_collection"` są przechowywane w formacie JSON i można je łatwo przeanalizować w celu wyodrębnienia danych modelu. Azure Data Factory, potoki Azure ML lub inne narzędzia do przetwarzania danych mogą służyć do przekształcania danych zgodnie z wymaganiami. Po przeniesieniu danych można zarejestrować je w obszarze roboczym Azure Machine Learning jako zestaw danych. Aby to zrobić, zobacz [jak utworzyć i zarejestrować zestawy danych](how-to-create-register-datasets.md).

   [![eksportu ciągłego](media/how-to-enable-app-insights/continuous-export-setup.png)](./media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>Przykład notesu

W notesie [enable-App-Insights-in-Product-Service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) przedstawiono Koncepcje opisane w tym artykule. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z artykułem [jak wdrożyć model w klastrze usługi Azure Kubernetes](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-azure-kubernetes-service) lub [jak wdrożyć model do Azure Container Instances](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-azure-container-instance) , aby wdrożyć modele w punktach końcowych usługi sieci Web, i włączyć usługę Azure Application Insights, aby korzystać z zbierania danych i monitorowania punktów końcowych.
* Zobacz [MLOps: zarządzanie, wdrażanie i monitorowanie modeli przy użyciu Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-model-management-and-deployment) , aby dowiedzieć się więcej na temat wykorzystywania danych zebranych z modeli w środowisku produkcyjnym. Takie dane mogą pomóc w nieustannym ulepszaniu procesu uczenia maszynowego
