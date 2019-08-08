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
ms.openlocfilehash: 84de9d53b19f5aa9b73570aa0d115d204e8b6596
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848213"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Wdróż model uczenia maszynowego w Azure App Service (wersja zapoznawcza)

Dowiedz się, jak wdrożyć model z usługi Azure Machine Learning jako aplikację sieci Web w programie Azure App Service.

> [!IMPORTANT]
> Chociaż usługa Azure Machine Learning i Azure App Service są ogólnie dostępne, możliwość wdrażania modelu z usługi Machine Learning na App Service jest w wersji zapoznawczej.

Za pomocą usługi Azure Machine Learning można tworzyć obrazy platformy Docker z szkoleń modeli uczenia maszynowego. Ten obraz zawiera usługę sieci Web, która odbiera dane, przesyła ją do modelu, a następnie zwraca odpowiedź. Azure App Service można użyć do wdrożenia obrazu i zapewnia następujące funkcje:

* [Obsługa protokołu SSL](/azure/app-service/app-service-web-ssl-cert-load) w celu zapewnienia bezpiecznej komunikacji między klientami a usługą.
* [Skalowanie w poziomie](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) do wielu wystąpień bez konieczności ponownego wdrażania.
* [Zaawansowane uwierzytelnianie](/azure/app-service/configure-authentication-provider-aad) na potrzeby zwiększonych zabezpieczeń.

Aby uzyskać więcej informacji na temat funkcji zapewnianych przez Azure App Service, zobacz [omówienie App Service](/azure/app-service/overview).

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Azure Machine Learning. Aby uzyskać więcej informacji, zobacz artykuł [Tworzenie obszaru roboczego](how-to-manage-workspace.md) .
* Model uczenia maszynowego zarejestrowany w Twoim obszarze roboczym. Jeśli nie masz modelu, Skorzystaj z [samouczka klasyfikacji obrazów: uczenie modelu](tutorial-train-models-with-aml.md) do uczenia i zarejestrowania go.
* Obraz platformy Docker utworzony na podstawie modelu. Jeśli nie masz obrazu, użyj [klasyfikacji obrazów: Wdróż model](tutorial-deploy-models-with-aml.md) , aby go utworzyć.

## <a name="deploy-image-as-a-web-app"></a>Wdrażanie obrazu jako aplikacji sieci Web

1. Na [Azure Portal](https://portal.azure.com)wybierz obszar roboczy Azure Machine Learning. W sekcji __Przegląd__ Użyj linku __rejestru__ , aby uzyskać dostęp do Azure Container Registry obszaru roboczego.

    ![Zrzut ekranu przedstawiający przegląd obszaru roboczego](media/how-to-deploy-app-service/workspace-overview.png)

2. Z Azure Container Registry wybierz pozycję __repozytoria__, a następnie wybierz __nazwę obrazu__ , który chcesz wdrożyć. Dla wersji, którą chcesz wdrożyć, wybierz pozycję __...__ , a następnie __Wdróż ją w aplikacji sieci Web__.

    ![Zrzut ekranu przedstawiający wdrażanie z ACR do aplikacji sieci Web](media/how-to-deploy-app-service/deploy-to-web-app.png)

3. Aby utworzyć aplikację sieci Web, podaj nazwę witryny, subskrypcję, grupę zasobów, a następnie wybierz pozycję plan/Lokalizacja usługi App Service. Na koniec wybierz pozycję __Utwórz__.

    ![Zrzut ekranu przedstawiający okno dialogowe Nowa aplikacja sieci Web](media/how-to-deploy-app-service/web-app-for-containers.png)

## <a name="use-the-web-app"></a>Korzystanie z aplikacji sieci Web

W [Azure Portal](https://portal.azure.com)wybierz aplikację sieci Web utworzoną w poprzednim kroku. W sekcji __Przegląd__ Skopiuj __adres URL__. Ta wartość jest __podstawowym adresem URL__ usługi.

![Zrzut ekranu przedstawiający przegląd aplikacji sieci Web](media/how-to-deploy-app-service/web-app-overview.png)

Usługa sieci Web, która przekazuje żądania do modelu, znajduje się `{baseurl}/score`w lokalizacji. Na przykład `https://mywebapp.azurewebsites.net/score`. Poniższy kod w języku Python pokazuje, jak przesłać dane do adresu URL i wyświetlić odpowiedź:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

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