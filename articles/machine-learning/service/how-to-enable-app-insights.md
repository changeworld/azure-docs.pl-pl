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
ms.date: 09/25/2019
ms.custom: seoapril2019
ms.openlocfilehash: 08c891ce2702455cac9618152061e992fc81420b
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274019"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Monitorowanie i zbieranie danych z punktów końcowych usługi sieci Web ML

W tym artykule dowiesz się, jak zbierać dane i monitorować modele wdrożone w punktach końcowych usługi sieci Web w usłudze Azure Kubernetes Service (AKS) lub Azure Container Instances (ACI), włączając usługę Azure Application Insights. Oprócz zbierania danych wejściowych i odpowiedzi punktu końcowego można monitorować:
* Stawki żądania, czasy odpowiedzi i wskaźniki niepowodzeń.
* Stawki zależności, czasy odpowiedzi i wskaźniki niepowodzeń.
* Wyłączenia.

[Dowiedz się więcej o usłudze Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.

* Azure Machine Learning obszar roboczy, katalog lokalny, który zawiera skrypty, oraz zestaw Azure Machine Learning SDK dla języka Python. Aby dowiedzieć się, jak uzyskać te wymagania wstępne, zobacz [jak skonfigurować środowisko programistyczne](how-to-configure-environment.md).
* Model uczenia maszynowego, który ma zostać wdrożony w usłudze Azure Kubernetes Service (AKS) lub Azure Container Instance (ACI). Jeśli go nie masz, zapoznaj się z samouczkiem dotyczącym [modelu klasyfikacji obrazów szkolenia](tutorial-train-models-with-aml.md) .

## <a name="web-service-input-and-response-data"></a>Dane wejściowe i odpowiedzi usługi sieci Web

Dane wejściowe i odpowiedzi dotyczące usługi — odpowiadające danym wejścia do modelu ML i jego przewidywania — są rejestrowane w śladach usługi Azure Application Insights w obszarze komunikat `"model_data_collection"`. Możesz wysyłać zapytania do usługi Azure Application Insights bezpośrednio, aby uzyskać dostęp do tych danych, lub skonfigurować [ciągły eksport](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) do konta magazynu w celu dłuższego przechowywania lub dalszego przetwarzania. Dane modelu mogą być następnie używane w usłudze Azure ML w celu skonfigurowania etykietowania, przeszkolenia, wyjaśnienia, analizy danych lub innego użycia. 

## <a name="use-the-azure-portal-to-configure"></a>Użyj Azure Portal, aby skonfigurować

Możesz włączyć i wyłączyć usługę Azure Application Insights w Azure Portal. 

1. W [Azure Portal](https://portal.azure.com)Otwórz obszar roboczy.

1. Na karcie **wdrożenia** wybierz usługę, w której chcesz włączyć Application Insights platformy Azure.

   [@no__t — 1List usług na karcie wdrożenia](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Wybierz pozycję **Edit** (Edytuj).

   [@no__t — przycisk 1Edit](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. W obszarze **Ustawienia zaawansowane**zaznacz pole wyboru **Włącz diagnostykę AppInsights** .

   [![Selected — pole wyboru umożliwiające włączenie diagnostyki](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Wybierz pozycję **Aktualizuj** w dolnej części ekranu, aby zastosować zmiany. 

### <a name="disable"></a>Wyłączenie
1. W [Azure Portal](https://portal.azure.com)Otwórz obszar roboczy.
1. Wybierz pozycję **wdrożenia**, wybierz pozycję Usługa, a następnie wybierz pozycję **Edytuj**.

   [@no__t — 1Use przycisku Edytuj](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. W obszarze **Ustawienia zaawansowane**Usuń zaznaczenie pola wyboru **Włącz diagnostykę AppInsights** . 

   [![Cleared — pole wyboru umożliwiające włączenie diagnostyki](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Wybierz pozycję **Aktualizuj** w dolnej części ekranu, aby zastosować zmiany. 
 
## <a name="use-python-sdk-to-configure"></a>Użyj zestawu SDK języka Python, aby skonfigurować 

### <a name="update-a-deployed-service"></a>Aktualizowanie wdrożonej usługi
1. Zidentyfikuj usługę w obszarze roboczym. Wartość `ws` jest nazwą obszaru roboczego.

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Zaktualizuj usługę i Włącz Application Insights platformy Azure. 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Rejestruj niestandardowe ślady w usłudze
Jeśli chcesz rejestrować niestandardowe ślady, postępuj zgodnie ze standardowym procesem wdrażania dla AKS lub ACI w temacie [jak wdrożyć i gdzie](how-to-deploy-and-where.md) dokument. Następnie wykonaj następujące czynności:

1. Zaktualizuj plik oceniania przez dodanie instrukcji Print.
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Zaktualizuj konfigurację usługi.
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Utwórz obraz i Wdróż go na [AKS](how-to-deploy-to-aks.md) lub [ACI](how-to-deploy-to-aci.md).  

### <a name="disable-tracking-in-python"></a>Wyłącz śledzenie w języku Python

Aby wyłączyć usługę Azure Application Insights, użyj następującego kodu:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    
## <a name="evaluate-data"></a>Oceń dane
Dane usługi są przechowywane na koncie usługi Azure Application Insights w ramach tej samej grupy zasobów co Azure Machine Learning.
Aby ją wyświetlić:
1. Przejdź do obszaru roboczego usługi Machine Learning w [Azure Portal](https://portal.azure.com). Kliknij link Application Insights platformy Azure.

    [@no__t — 1AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Wybierz kartę **Przegląd** , aby wyświetlić podstawowy zestaw metryk dla usługi.

   [@no__t — 1Overview](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. Aby przyjrzeć się do ładunków danych wejściowych i odpowiedzi usługi sieci Web, wybierz pozycję **Analiza**
1. W sekcji schemat wybierz pozycję **ślady** i odfiltruj ślady z komunikatem `"model_data_collection"`. W wymiarach niestandardowych można zobaczyć dane wejściowe, prognozy i inne istotne szczegóły.

   [@no__t — dane 1Model](media/how-to-enable-app-insights/model-data-trace.png)](./media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. Aby zapoznać się ze śladami niestandardowymi, wybierz pozycję **Analiza**.
4. W sekcji schemat wybierz pozycję **ślady**. Następnie wybierz pozycję **Uruchom** , aby uruchomić zapytanie. Dane powinny być wyświetlane w formacie tabeli i powinny być mapowane na niestandardowe wywołania w pliku oceniania. 

   [@no__t — ślady 1Custom](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Aby dowiedzieć się więcej na temat korzystania z usługi Azure Application Insights, zobacz [co to jest Application Insights?](../../azure-monitor/app/app-insights-overview.md).

## <a name="export-data-for-further-processing-and-longer-retention"></a>Eksportowanie danych do dalszego przetwarzania i dłuższego przechowywania

Aby wysyłać komunikaty do obsługiwanego konta magazynu, można użyć usługi Azure Application Insights " [eksport ciągły](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) ", w której można ustawić dłuższe przechowywanie. Komunikaty `"model_data_collection"` są przechowywane w formacie JSON i można je łatwo przeanalizować w celu wyodrębnienia danych modelu. Azure Data Factory, potoki Azure ML lub inne narzędzia do przetwarzania danych mogą służyć do przekształcania danych zgodnie z wymaganiami. Po przeniesieniu danych można zarejestrować je za pomocą obszaru roboczego usługi Azure Machine Learning jako zestawu danych.

   [@no__t — eksport 1Continuous](media/how-to-enable-app-insights/continuous-export-setup.png)](./media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>Przykładowy Notes

W notesie [enable-App-Insights-in-Product-Service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) przedstawiono Koncepcje opisane w tym artykule. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z artykułem [jak wdrożyć model w klastrze usługi Azure Kubernetes](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-azure-kubernetes-service) lub [jak wdrożyć model w celu Azure Container Instances](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-azure-container-instance) , aby wdrożyć modele w punktach końcowych usługi sieci Web, i Włącz usługę Azure Application Insights, aby korzystać z zbierania danych i punktu końcowego. kontrolą.
* Zobacz [MLOps: zarządzanie, wdrażanie i monitorowanie modeli przy użyciu Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-model-management-and-deployment) , aby dowiedzieć się więcej na temat wykorzystywania danych zebranych z modeli w środowisku produkcyjnym. Takie dane mogą pomóc w nieustannym ulepszaniu procesu uczenia maszynowego. 
