---
title: Wdrażanie i użycie
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (klasyczny) służy do wdrażania przepływów pracy i modeli uczenia maszynowego jako usług sieci Web. Usługi sieci web można następnie wywołać maszyny uczenia modeli z aplikacji przez internet w celu wykonania prognoz w czasie rzeczywistym lub w trybie wsadowym.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: ff6ae0de0bbd8c47b81fa5066a97eb0b3e0cf6bc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204396"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Azure Machine Learning Studio (klasyczne) usługi sieci Web: wdrażanie i użycie

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Azure Machine Learning Studio (klasyczny) służy do wdrażania przepływów pracy i modeli uczenia maszynowego jako usług sieci Web. Usługi sieci web można następnie wywołać maszyny uczenia modeli z aplikacji przez Internet w celu wykonania prognoz w czasie rzeczywistym lub w trybie wsadowym. Ponieważ usługi sieci web są zgodne ze specyfikacją REST, można je wywoływać z różnych języków programowania i platform, takich jak .NET, Java i z aplikacji, takich jak program Excel.

W kolejnych sekcjach zawierają łącza do przewodników, kod i dokumentację ułatwiającą rozpoczęcie pracy.

## <a name="deploy-a-web-service"></a>Wdrażanie usługi internetowej

### <a name="with-azure-machine-learning-studio-classic"></a>Z Azure Machine Learning Studio (klasyczny)

Portal Studio (klasyczny) i Portal usług sieci Web Microsoft Azure Machine Learning ułatwiają wdrażanie usługi sieci Web i zarządzanie nią bez pisania kodu.

Poniższe linki zawierają ogólne informacje o sposobie wdrażania nowej usługi sieci web:

* Aby zapoznać się z omówieniem sposobu wdrażania nowej usługi sieci Web opartej na Azure Resource Manager, zobacz [wdrażanie nowej usługi sieci Web](deploy-a-machine-learning-web-service.md).
* Aby zapoznać się z przewodnikiem dotyczącym wdrażania usługi sieci Web, zobacz [wdrażanie usługi sieci web Azure Machine Learning](deploy-a-machine-learning-web-service.md).
* Aby zapoznać się z pełnym przewodnikiem dotyczącym tworzenia i wdrażania usługi sieci Web, Rozpocznij od [samouczka 1: przewidywanie ryzyka kredytowego](tutorial-part1-credit-risk.md).
* Aby uzyskać szczegółowe przykłady, które wdrażanie usługi sieci web zobacz:

  * [Samouczek 3: Wdrażanie modelu ryzyka kredytowego](tutorial-part3-credit-risk-deploy.md)
  * [Jak wdrożyć usługę sieci Web w wielu regionach](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Za pomocą dostawcy zasobów usługi sieci web API (interfejsów API usługi Azure Resource Manager)

Dostawca zasobów Azure Machine Learning Studio (klasyczny) dla usług sieci Web umożliwia wdrażanie usług sieci Web i zarządzanie nimi przy użyciu wywołań interfejsu API REST. Aby uzyskać więcej informacji, zobacz informacje dotyczące [usługi sieci Web Machine Learning (REST)](/rest/api/machinelearning/index) .

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>Za pomocą poleceń cmdlet programu PowerShell

Dostawca zasobów Azure Machine Learning Studio (klasyczny) dla usług sieci Web umożliwia wdrażanie usług sieci Web i zarządzanie nimi za pomocą poleceń cmdlet programu PowerShell.

Aby użyć poleceń cmdlet, musisz najpierw zalogować się do konta platformy Azure z poziomu środowiska programu PowerShell przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Jeśli nie znasz sposobu wywoływania poleceń programu PowerShell opartych na Menedżer zasobów, zobacz [używanie Azure PowerShell z Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md).

Aby wyeksportować eksperyment predykcyjny, użyj [tego przykładowego kodu](https://github.com/ritwik20/AzureML-WebServices). Po utworzeniu pliku .exe, od kodu, należy wpisać:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Uruchomiona jest aplikacja tworzy szablon JSON usługi sieci web. Aby użyć szablonu, aby wdrożyć usługę sieci web, należy dodać następujące informacje:

* Nazwa konta magazynu i klucza

    Możesz pobrać nazwę i klucz konta magazynu z [Azure Portal](https://portal.azure.com/).
* Identyfikator planu zobowiązania

    Identyfikator planu można uzyskać z portalu [usług sieci Web Azure Machine Learning](https://services.azureml.net) , logując się i klikając nazwę planu.

Dodaj je do szablonu JSON jako elementy podrzędne węzła *Właściwości* na tym samym poziomie, co węzeł *MachineLearningWorkspace* .

Oto przykład:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Zobacz następujące artykuły i przykładowy kod, aby uzyskać więcej informacji:

* Dokumentacja [poleceń cmdlet Azure Machine Learning Studio (klasycznych)](https://docs.microsoft.com/powershell/module/az.machinelearning) w witrynie MSDN
* Przykładowa [Przewodnik](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) w witrynie GitHub

## <a name="consume-the-web-services"></a>Korzystanie z usług sieci web

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Z usługi sieci Web Azure Machine Learning interfejsu użytkownika (testowanie)

Można testować usługi sieci web z poziomu portalu usług sieci Web Azure Machine Learning. W tym testowanie usługi odpowiedź na żądanie (RRS) i interfejsy usługę wykonywania wsadowego (BES).

* [Wdrażanie nowej usługi sieci Web](deploy-a-machine-learning-web-service.md)
* [Wdrażanie usługi sieci Web Azure Machine Learning](deploy-a-machine-learning-web-service.md)
* [Samouczek 3: Wdrażanie modelu ryzyka kredytowego](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Z poziomu programu Excel

Można pobrać szablonu programu Excel, który wykorzystuje usługę sieci web:

* [Zużywanie Azure Machine Learning usługi sieci Web z programu Excel](consuming-from-excel.md)
* [Dodatek programu Excel dla usług Azure Machine Learning Web Services](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>W kliencie oparty na protokole REST

Usługi sieci Web Azure Machine Learning to interfejsy API REST. Można korzystać z tych interfejsów API z różnych platform, takich jak .NET, Python, R, Java itp. Strona **Korzystanie** z usługi sieci Web w [portalu usług sieci Web Microsoft Azure Machine Learning](https://services.azureml.net) zawiera przykładowy kod, który może pomóc Ci rozpocząć pracę. Aby uzyskać więcej informacji, zobacz [How to consume an Azure Machine Learning Web service](consume-web-services.md) (Jak korzystać z usługi internetowej Azure Machine Learning).
