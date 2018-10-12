---
title: Włącz usługę application insights dla usługi Azure Machine Learning w środowisku produkcyjnym
description: Dowiedz się, jak skonfigurować usługę Application Insights dla usługi Azure Machine Learning Service zostały wdrożone w usłudze Azure Kubernetes Service
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 10/01/2018
ms.openlocfilehash: 45871ab515c7ffd9520b1d77d3fd1e77abcc29ef
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114570"
---
# <a name="monitor-your-azure-machine-learning-models-in-production-with-application-insights"></a>Monitorowanie usługi Azure Machine Learning modeli w środowisku produkcyjnym za pomocą usługi Application Insights

W tym artykule, możesz dowiedzieć się, jak skonfigurować **usługi Application Insights** dla Twojego **usługi Azure Machine Learning** usługi. Po włączeniu usługi Application Insights zapewnia możliwość monitorowania:
* Żądanie kursów, czasy reakcji i współczynniki błędów
* Współczynniki zależności, czasy reakcji i współczynniki błędów
* Wyjątki

Dowiedz się więcej o usłudze Application Insights [tutaj](../../application-insights/app-insights-overview.md). 

## <a name="prerequisites"></a>Wymagania wstępne
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Obszarem roboczym usługi Azure Machine Learning, zawierający skrypty i zestawu SDK usługi Azure Machine Learning dla języka Python zainstalowane katalogu lokalnego. Dowiedz się, jak uzyskać te wymagania wstępne przy użyciu [sposób konfigurowania środowiska deweloperskiego](how-to-configure-environment.md) dokumentu.
* Model uczenia maszynowego uczonego wdrażanych na platformie Azure Kubernetes Service (AKS). Jeśli nie masz, zobacz [szkolenie modeli klasyfikacji obrazów](tutorial-train-models-with-aml.md) samouczka.
* [Klastra AKS](how-to-deploy-to-aks.md).

## <a name="enable--disable-in-the-portal"></a>Włącz & wyłączyć w portalu

Można włączyć i wyłączyć usługę Application Insights w witrynie Azure portal.

### <a name="enable"></a>Włączanie

1. W [witryny Azure portal](https://portal.azure.com), Otwórz obszar roboczy.

1. Przejdź do wdrożenia, a następnie wybierz usługę, w którym chcesz włączyć usługi Application Insights.

   [![Wdrożenia](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Kliknij przycisk **Edytuj** i przejdź do **Zaawansowane ustawienia**.

   [![Edytuj](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. W **ustawienia zaawansowane** wybierz **diagnostyki Włącz usługę Application Insights**.

   [![Edytuj](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Wybierz **aktualizacji** w dolnej części ekranu, aby zastosować zmiany. 

### <a name="disable"></a>Wyłączanie
Aby wyłączyć usługę Application Insights w witrynie Azure portal, wykonaj następujące czynności:

1. Zaloguj się do witryny Azure portal pod adresem https://portal.azure.com.
1. Przejdź do swojego obszaru roboczego.
1. Wybierz **wdrożeń**, następnie **wybierz usługę**, a następnie **Edytuj**.

   [![Edytuj](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. W **Zaawansowane ustawienia**, usuń zaznaczenie opcji **diagnostykę włączyć usługi AppInsights**. 

   [![Usuń zaznaczenie pola wyboru](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Wybierz **aktualizacji** w dolnej części ekranu, aby zastosować zmiany. 

## <a name="enable--disable-from-the-sdk"></a>Włączanie i wyłączanie z zestawu SDK

### <a name="update-a-deployed-service"></a>Aktualizowania wdrożonej usługi
1. Zidentyfikuj usługi w obszarze roboczym (ws = nazwa obszaru roboczego)

    ```python
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Zaktualizuj usługę, a następnie włącz usługę Application Insights. 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Ślady niestandardowych dzienników w usłudze
Jeśli chcesz ślady niestandardowych dzienników, wykonują [procesu wdrożenia standardowego dla usługi AKS](how-to-deploy-to-aks.md) i będzie:

1. Zaktualizuj plik oceniania przez dodanie instrukcji drukowania.
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Zaktualizuj konfigurację usługi aks.
    
    ```python
    aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)
    ```

3. [Tworzenie obrazu i wdrożyć ją.](how-to-deploy-to-aks.md)  

### <a name="disable-tracking-in-python"></a>Wyłącz śledzenia w języku Python

Aby wyłączyć usługę Application Insights, użyj następującego kodu:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    

## <a name="evaluate-data"></a>Ocena danych
Dane usługi pobiera przechowywane na koncie usługi Application Insights w ramach tej samej grupie zasobów, do obszaru roboczego usługi Azure Machine Learning to w.
Aby go wyświetlić:
1. Przejdź do grupy zasobów w [witryny Azure portal](https://portal.azure.com) i klikaj zasób usługi Application Insights. 
2. **Przegląd** karta zawiera podstawowy zestaw metryk dla usługi.

   [![Przegląd](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. Zbadać, kliknij pozycję Twoje niestandardowe ślady **analizy**.
4. W ramach sekcji schematu kliknij **ślady** a następnie **Uruchom** zapytania. Dane powinny występować w postaci tabeli szczegółów poniżej i powinny być mapowane do niestandardowych połączeń w pliku wyników. 

   [![Niestandardowe śledzenie](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Kliknij przycisk [tutaj](../../application-insights/app-insights-overview.md) Aby dowiedzieć się więcej o tym, jak używać usługi Application Insights.
    

## <a name="example-notebook"></a>Przykład notesu

[00. Started/13.enable-app-insights-in-production-service.ipynb wprowadzenie](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/13.enable-app-insights) koncepcji w tym artykule pokazano, notesu.  Pobierz ten notes:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Kolejne kroki
Może również zbierać dane na modeli w produkcji. Przeczytaj artykuł [zbieranie danych dla modeli w środowisku produkcyjnym](how-to-enable-data-collection.md) 
