---
title: Wdróż modele ml do Azure App Service (wersja zapoznawcza)
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak za pomocą usługi Azure Machine Learning wdrożyć model w aplikacji sieci Web w programie Azure App Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/01/2019
ms.openlocfilehash: ada2a19de12c2f3f6b23fcc3d759afb0c747d37d
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897415"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Wdróż model uczenia maszynowego w Azure App Service (wersja zapoznawcza)

Dowiedz się, jak wdrożyć model z usługi Azure Machine Learning jako aplikację sieci Web w programie Azure App Service.

> [!IMPORTANT]
> Chociaż usługa Azure Machine Learning i Azure App Service są ogólnie dostępne, możliwość wdrażania modelu z usługi Machine Learning na App Service jest w wersji zapoznawczej.

Za pomocą usługi Azure Machine Learning można tworzyć obrazy platformy Docker z szkoleń modeli uczenia maszynowego. Ten obraz zawiera usługę sieci Web, która odbiera dane, przesyła ją do modelu, a następnie zwraca odpowiedź. Azure App Service można użyć do wdrożenia obrazu i zapewnia następujące funkcje:

* Zaawansowane [uwierzytelnianie](/azure/app-service/configure-authentication-provider-aad) na potrzeby zwiększonych zabezpieczeń. Metody uwierzytelniania obejmują zarówno Azure Active Directory, jak i uwierzytelnianie wieloskładnikowe.
* [Skalowanie automatyczne](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) bez konieczności ponownego wdrażania.
* [Obsługa protokołu SSL](/azure/app-service/app-service-web-ssl-cert-load) w celu zapewnienia bezpiecznej komunikacji między klientami a usługą.

Aby uzyskać więcej informacji na temat funkcji zapewnianych przez Azure App Service, zobacz [omówienie App Service](/azure/app-service/overview).

> [!IMPORTANT]
> Jeśli potrzebujesz możliwości rejestrowania danych oceniających używanych przez wdrożony model lub wyniki oceny, zamiast tego należy przeprowadzić wdrożenie w usłudze Azure Kubernetes Service. Aby uzyskać więcej informacji, zobacz [zbieranie danych w modelach produkcyjnych](how-to-enable-data-collection.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz artykuł [Tworzenie obszaru roboczego](how-to-manage-workspace.md) .
* Model uczenia maszynowego zarejestrowany w Twoim obszarze roboczym. Jeśli nie masz modelu, Skorzystaj z [samouczka klasyfikacji obrazów: uczenie modelu](tutorial-train-models-with-aml.md) do uczenia i zarejestrowania go.

    > [!IMPORTANT]
    > W fragmentach kodu w tym artykule przyjęto założenie, że ustawiono następujące zmienne:
    >
    > * `ws`— Obszar roboczy Azure Machine Learning.
    > * `model`-Zarejestrowany model, który zostanie wdrożony.
    > * `inference_config`-Konfiguracja wnioskowania dla modelu.
    >
    > Aby uzyskać więcej informacji na temat ustawiania tych zmiennych, zobacz [Wdrażanie modeli za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Przygotowanie do wdrożenia

Przed wdrożeniem należy zdefiniować, co jest potrzebne do uruchomienia modelu jako usługi sieci Web. Na poniższej liście opisano podstawowe elementy, które są związane z wdrożeniem:

* __Skrypt wejściowy__. Ten skrypt akceptuje żądania, ocenia żądanie przy użyciu modelu i zwraca wyniki.

    > [!IMPORTANT]
    > Skrypt wejścia jest specyficzny dla modelu; musi on zrozumieć format danych żądania przychodzącego, format danych oczekiwanych przez model i format danych zwracanych do klientów.
    >
    > Jeśli dane żądania są w formacie, którego nie można używać w modelu, skrypt może przekształcić go w akceptowalny format. Może również przekształcić odpowiedź przed powrotem do klienta programu.

    > [!IMPORTANT]
    > Zestaw SDK Azure Machine Learning nie pozwala na dostęp usługi sieci Web do magazynu danych ani Twoich zestawów. Jeśli chcesz, aby wdrożony model miał dostęp do danych przechowywanych poza wdrożeniem, na przykład na koncie usługi Azure Storage, musisz opracować niestandardowe rozwiązanie kodu przy użyciu odpowiedniego zestawu SDK. Na przykład [zestaw SDK usługi Azure Storage dla języka Python](https://github.com/Azure/azure-storage-python).
    >
    > Kolejną alternatywą, która może posłużyć do danego scenariusza, są [przewidywania wsadowe](how-to-run-batch-predictions.md), które zapewniają dostęp do magazynów danych podczas oceniania.

    Aby uzyskać więcej informacji na temat skryptów wprowadzania, zobacz [Wdrażanie modeli za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md).

* **Zależności**, takie jak skrypty pomocnika lub pakiety Python/Conda wymagane do uruchomienia skryptu lub modelu wprowadzania

Te jednostki są hermetyzowane w __konfiguracji wnioskowania__. Konfiguracja wnioskowania odwołuje się do skryptu wejścia i innych zależności.

> [!IMPORTANT]
> Podczas tworzenia konfiguracji wnioskowania do użytku z Azure App Service należy użyć obiektu [środowiska](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) . Poniższy przykład ilustruje tworzenie obiektu środowiska i używanie go z konfiguracją wnioskowania:
>
> ```python
> from azureml.core import Environment
> from azureml.core.environment import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
> ```

Aby uzyskać więcej informacji o środowiskach, zobacz [Tworzenie środowisk i zarządzanie nimi na potrzeby szkolenia i wdrażania](how-to-use-environments.md).

Aby uzyskać więcej informacji na temat konfiguracji wnioskowania, zobacz [Wdrażanie modeli za pomocą usługi Azure Machine Learning](how-to-deploy-and-where.md).

> [!IMPORTANT]
> Podczas wdrażania programu w celu Azure App Service nie trzeba tworzyć __konfiguracji wdrożenia__.

## <a name="create-the-image"></a>Tworzenie obrazu

Aby utworzyć obraz platformy Docker wdrożony w Azure App Service, użyj [modelu model. Package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config--generate-dockerfile-false-). Poniższy fragment kodu przedstawia sposób tworzenia nowego obrazu z konfiguracji modelu i wnioskowania:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Gdy `show_output=True`są wyświetlane dane wyjściowe procesu kompilacji platformy Docker. Po zakończeniu procesu obraz został utworzony w Azure Container Registry dla obszaru roboczego.

## <a name="deploy-image-as-a-web-app"></a>Wdrażanie obrazu jako aplikacji sieci Web

1. Na [Azure Portal](https://portal.azure.com)wybierz obszar roboczy Azure Machine Learning. W sekcji __Przegląd__ Użyj linku __rejestru__ , aby uzyskać dostęp do Azure Container Registry obszaru roboczego.

    [![Zrzut ekranu przedstawiający przegląd obszaru roboczego](media/how-to-deploy-app-service/workspace-overview.png)](media/how-to-deploy-app-service/workspace-overview-expanded.png)

2. Z Azure Container Registry wybierz pozycję __repozytoria__, a następnie wybierz __nazwę obrazu__ , który chcesz wdrożyć. Dla wersji, którą chcesz wdrożyć, wybierz pozycję __...__ , a następnie __Wdróż ją w aplikacji sieci Web__.

    [![Zrzut ekranu przedstawiający wdrażanie z ACR do aplikacji sieci Web](media/how-to-deploy-app-service/deploy-to-web-app.png)](media/how-to-deploy-app-service/deploy-to-web-app-expanded.png)

3. Aby utworzyć aplikację sieci Web, podaj nazwę witryny, subskrypcję, grupę zasobów, a następnie wybierz pozycję plan/Lokalizacja usługi App Service. Na koniec wybierz pozycję __Utwórz__.

    ![Zrzut ekranu przedstawiający okno dialogowe Nowa aplikacja sieci Web](media/how-to-deploy-app-service/web-app-for-containers.png)

## <a name="use-the-web-app"></a>Korzystanie z aplikacji sieci Web

W [Azure Portal](https://portal.azure.com)wybierz aplikację sieci Web utworzoną w poprzednim kroku. W sekcji __Przegląd__ Skopiuj __adres URL__. Ta wartość jest __podstawowym adresem URL__ usługi.

[![Zrzut ekranu przedstawiający przegląd aplikacji sieci Web](media/how-to-deploy-app-service/web-app-overview.png)](media/how-to-deploy-app-service/web-app-overview-expanded.png)

Usługa sieci Web, która przekazuje żądania do modelu, znajduje się `{baseurl}/score`w lokalizacji. Na przykład `https://mywebapp.azurewebsites.net/score`. Poniższy kod w języku Python pokazuje, jak przesłać dane do adresu URL i wyświetlić odpowiedź:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat konfigurowania aplikacji sieci Web, zapoznaj się z dokumentacją [App Service w systemie Linux](/azure/app-service/containers/) .
* Aby uzyskać więcej informacji na temat skalowania, zobacz [Rozpoczynanie pracy z funkcją automatycznego skalowania na platformie Azure](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json).
* Aby uzyskać więcej informacji na temat obsługi protokołu SSL, zobacz [Używanie certyfikatu SSL w Azure App Service](/azure/app-service/app-service-web-ssl-cert-load).
* Aby uzyskać więcej informacji na temat uwierzytelniania, zobacz [Konfigurowanie aplikacji App Service do korzystania z Azure Active Directory logowania](/azure/app-service/configure-authentication-provider-aad).
* [Korzystanie z modelu uczenia Maszynowego, wdrożyć jako usługę sieci web](how-to-consume-web-service.md)