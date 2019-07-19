---
title: Konfigurowanie Application Insights platformy Azure do monitorowania modeli ML
titleSuffix: Azure Machine Learning service
description: Monitorowanie usług sieci Web wdrożonych za pomocą usługi Azure Machine Learning na platformie Azure Application Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 07/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: 1c12f55228d77656ef57598da0fb002fdea29bd4
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871782"
---
# <a name="monitor-your-azure-machine-learning-models-with-application-insights"></a>Monitoruj swoje modele usługi Azure Machine Learning z usługą Application Insights

W tym artykule dowiesz się, jak skonfigurować usługi Azure Application Insights dla usługi Azure Machine Learning. Usługa Application Insights zapewnia możliwość monitorowania:
* Żądanie kursów, czasy reakcji i współczynniki błędów.
* Współczynniki zależności, czasy reakcji i współczynniki błędów.
* Liczba wyjątków.

[Dowiedz się więcej o usłudze Application Insights](../../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.

* Obszarem roboczym usługi Azure Machine Learning, katalog lokalny, który zawiera skrypty i zestawu SDK usługi Azure Machine Learning dla języka Python zainstalowane. Aby dowiedzieć się, jak uzyskać te wymagania wstępne, zobacz [sposób konfigurowania środowiska deweloperskiego](how-to-configure-environment.md).
* Model uczenia maszynowego uczonego do wdrożenia usługi Azure Kubernetes Service (AKS) lub wystąpienia kontenera platformy Azure (ACI). Jeśli nie masz, zobacz [Train model klasyfikacji obrazów](tutorial-train-models-with-aml.md) samouczka.


## <a name="use-sdk-to-configure"></a>Użyj zestawu SDK, aby skonfigurować 

### <a name="update-a-deployed-service"></a>Aktualizowania wdrożonej usługi
1. Zidentyfikuj usługi w obszarze roboczym. Wartość `ws` to nazwa obszaru roboczego.

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Zaktualizuj usługę, a następnie włącz usługę Application Insights. 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Ślady niestandardowych dzienników w usłudze
Jeśli chcesz ślady niestandardowych dzienników, postępuj zgodnie z procesem standardowego wdrożenia dla usługi AKS lub ACI w [sposób wdrażania i gdzie](how-to-deploy-and-where.md) dokumentu. Następnie wykonaj następujące czynności:

1. Zaktualizuj plik oceniania przez dodanie instrukcji drukowania.
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Zaktualizuj konfigurację usługi.
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Zbuduj obraz i wdróż go na [AKS](how-to-deploy-to-aks.md) lub [ACI](how-to-deploy-to-aci.md).  

### <a name="disable-tracking-in-python"></a>Wyłącz śledzenia w języku Python

Aby wyłączyć usługę Application Insights, użyj następującego kodu:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    
## <a name="use-portal-to-configure"></a>Używanie portalu do konfigurowania

Można włączyć i wyłączyć usługę Application Insights w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com), Otwórz obszar roboczy.

1. Na **wdrożeń** , a następnie wybierz usługę, której chcesz włączyć usługi Application Insights.

   [![Lista usług na karcie wdrożenia](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Wybierz pozycję **Edit** (Edytuj).

   [![Przycisk Edytuj](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. W **Zaawansowane ustawienia**, wybierz opcję **diagnostykę włączyć usługi AppInsights** pole wyboru.

   [![Zaznaczenie pola wyboru dotyczące włączania diagnostyki](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Wybierz **aktualizacji** w dolnej części ekranu, aby zastosować zmiany. 

### <a name="disable"></a>Wyłączanie
1. W [witryny Azure portal](https://portal.azure.com), Otwórz obszar roboczy.
1. Wybierz **wdrożeń**, wybierz usługę, a wybierz **Edytuj**.

   [![Kliknij przycisk Edytuj](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. W **Zaawansowane ustawienia**, wyczyść **diagnostykę włączyć usługi AppInsights** pole wyboru. 

   [![Wyczyszczone pole wyboru włączenia diagnostyki](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Wybierz **aktualizacji** w dolnej części ekranu, aby zastosować zmiany. 
 

## <a name="evaluate-data"></a>Ocena danych
Dane usługi są przechowywane na koncie usługi Application Insights w ramach tej samej grupie zasobów co usługa Azure Machine Learning.
Aby go wyświetlić:
1. Przejdź do obszaru roboczego usługi Machine Learning w [witryny Azure portal](https://portal.azure.com) i kliknij łącze usługi Application Insights.

    [![AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Wybierz **Przegląd** kartę, aby wyświetlić podstawowy zestaw metryk dla Twojej usługi.

   [![Przegląd](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. Się zapozna Twoje ślady niestandardowe, wybierz **analizy**.
4. W sekcji schematu wybierz **ślady**. Następnie wybierz pozycję **Uruchom** kwerendy. Dane powinny być wyświetlane w postaci tabeli i powinny być mapowane do niestandardowych połączeń w pliku wyników. 

   [![Niestandardowe śledzenie](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Aby dowiedzieć się więcej o tym, jak używać usługi Application Insights, zobacz [co to jest usługa Application Insights?](../../azure-monitor/app/app-insights-overview.md).
    

## <a name="example-notebook"></a>Przykład notesu

W notesie [enable-App-Insights-in-Product-Service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) przedstawiono Koncepcje opisane w tym artykule. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Następne kroki
Może również zbierać dane na modeli w produkcji. Przeczytaj artykuł [zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md). 

Przeczytaj również [Azure monitor for Containers](https://docs.microsoft.com/azure/monitoring/monitoring-container-insights-overview?toc=%2fazure%2fmonitoring%2ftoc.json).
