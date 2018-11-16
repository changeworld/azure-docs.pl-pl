---
title: Włącz usługę Application Insights dla usługi Azure Machine Learning w środowisku produkcyjnym
description: Dowiedz się, jak skonfigurować usługę Application Insights dla usługi Azure Machine Learning do wdrożenia w usłudze Azure Kubernetes Service
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 10/01/2018
ms.openlocfilehash: 71dc7c0dbb2400802235da4f1bb952c7863a1862
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51713218"
---
# <a name="monitor-your-azure-machine-learning-models-in-production-with-application-insights"></a>Monitorowanie usługi Azure Machine Learning modeli w środowisku produkcyjnym za pomocą usługi Application Insights

W tym artykule dowiesz się, jak skonfigurować usługi Azure Application Insights dla usługi Azure Machine Learning. Usługa Application Insights zapewnia możliwość monitorowania:
* Żądanie kursów, czasy reakcji i współczynniki błędów.
* Współczynniki zależności, czasy reakcji i współczynniki błędów.
* Liczba wyjątków.

[Dowiedz się więcej o usłudze Application Insights](../../application-insights/app-insights-overview.md). 

>[!NOTE]
> Kod w tym artykule został przetestowany przy użyciu zestawu SDK usługi Azure Machine Learning wersji 0.1.74


## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://aka.ms/AMLfree).
* Obszarem roboczym usługi Azure Machine Learning, katalog lokalny, który zawiera skrypty i zestawu SDK usługi Azure Machine Learning dla języka Python zainstalowane. Aby dowiedzieć się, jak uzyskać te wymagania wstępne, zobacz [sposób konfigurowania środowiska deweloperskiego](how-to-configure-environment.md).
* Model uczenia maszynowego uczonego wdrażanych na platformie Azure Kubernetes Service (AKS). Jeśli nie masz, zobacz [Train model klasyfikacji obrazów](tutorial-train-models-with-aml.md) samouczka.
* [Klastra AKS](how-to-deploy-to-aks.md).

## <a name="enable-and-disable-in-the-portal"></a>Włączanie i wyłączanie w portalu

Można włączyć i wyłączyć usługę Application Insights w witrynie Azure portal.

### <a name="enable"></a>Włączanie

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

   [![Przycisk Edytuj](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. W **Zaawansowane ustawienia**, wyczyść **diagnostykę włączyć usługi AppInsights** pole wyboru. 

   [![Wyczyszczone pole wyboru włączenia diagnostyki](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Wybierz **aktualizacji** w dolnej części ekranu, aby zastosować zmiany. 

## <a name="enable-and-disable-from-the-sdk"></a>Włączanie i wyłączanie z zestawu SDK

### <a name="update-a-deployed-service"></a>Aktualizowania wdrożonej usługi
1. Zidentyfikuj usługi w obszarze roboczym. Wartość `ws` to nazwa obszaru roboczego.

    ```python
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Zaktualizuj usługę, a następnie włącz usługę Application Insights. 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Ślady niestandardowych dzienników w usłudze
Jeśli chcesz rejestrować ślady niestandardowych, postępuj zgodnie z [procesu wdrożenia standardowego dla usługi AKS](how-to-deploy-to-aks.md). Następnie:

1. Zaktualizuj plik oceniania przez dodanie instrukcji drukowania.
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Zaktualizuj konfigurację usługi AKS.
    
    ```python
    aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)
    ```

3. [Tworzenie obrazu i wdrożyć go](how-to-deploy-to-aks.md).  

### <a name="disable-tracking-in-python"></a>Wyłącz śledzenia w języku Python

Aby wyłączyć usługę Application Insights, użyj następującego kodu:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    

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

Aby dowiedzieć się więcej o tym, jak używać usługi Application Insights, zobacz [co to jest usługa Application Insights?](../../application-insights/app-insights-overview.md).
    

## <a name="example-notebook"></a>Przykład notesu

[00. Started/13.enable-app-insights-in-production-service.ipynb wprowadzenie](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/13.enable-app-insights) koncepcji w tym artykule pokazano, notesu.  Pobierz ten notes:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Kolejne kroki
Może również zbierać dane na modeli w produkcji. Przeczytaj artykuł [zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md). 


## <a name="other-references"></a>Inne odwołania
* [Usługa Azure Monitor dla kontenerów](https://docs.microsoft.com/azure/monitoring/monitoring-container-insights-overview?toc=%2fazure%2fmonitoring%2ftoc.json)
